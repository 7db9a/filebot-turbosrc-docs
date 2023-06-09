## Adding and endpoint or feature to Turbosrc.

When creating an a new endpoint for turbosrc-service, you must update one of the following subystems
- turbosrc-engine
- turbosrc-namespace
in addition to updating turbosrc-service.

### Example new endpoint

***getVotes**

There is the diff for the commit for turbosrc-engine.

```
commit cc63cb930cd42880051d800e3144cfc8efe02b5f
Author: Turbosrc Engineer <>
Date:   Wed May 3 12:21:36 2023 -0400

    getting votes for a pr

diff --git a/lib/getVotes.js b/lib/getVotes.js
new file mode 100644
index 0000000..1d8bb17
--- /dev/null
+++ b/lib/getVotes.js
@@ -0,0 +1,29 @@
+const { PullRequest, Vote } = require("../server/db");
+
+async function getVotes(
+  /*repo:*/ repo_id,
+  /*defaultHash:*/ defaultHash,
+) {
+  try {
+    let pr = await PullRequest.findOne({
+      where: { repo_id: repo_id, defaultHash: defaultHash },
+      include: { model: Vote, order: [["createdAt", "DESC"]] },
+    });
+      // order by newest first
+    let json = JSON.stringify(pr, null, 2);
+    let data = JSON.parse(json);
+    let votes = data.votes.map((vote) => {
+        return {
+            contributor_id: vote.contributor_id,
+            // votepower: vote.votepower,
+            side: vote.side,
+            createdAt: vote.createdAt,
+        }
+    })
+    return { status: 200, votes: votes }
+  } catch (error) {
+    console.log(error);
+    return error.status
+  }
+}
+module.exports = getVotes;
diff --git a/lib/index.js b/lib/index.js
index 49085ae..6bd9460 100644
--- a/lib/index.js
+++ b/lib/index.js
@@ -16,6 +16,8 @@ const getQuorum = require("./state/getQuorum");
 const setQuorum = require("./state/setQuorum");
 const getPRvoteTotals = require("./state/getPRvoteTotals");
 const getRepo = require("./getRepo");
+const getVotes = require("./getVotes");
+
 module.exports = {
   createPullRequest,
   createLinkedPullRequest,
@@ -35,4 +37,5 @@ module.exports = {
   getQuorum,
   setQuorum,
   getPRvoteTotals,
+  getVotes
 };
diff --git a/server/index.js b/server/index.js
index e74ad21..09365cc 100644
--- a/server/index.js
+++ b/server/index.js
@@ -22,6 +22,7 @@ const {
   setQuorum,
   getQuorum,
   setVote,
+  getVotes
 } = require("../lib");

 var schema = buildSchema(`
@@ -56,6 +57,17 @@ var schema = buildSchema(`
     quorum: String!
   }

+  type Vote {
+    contributor_id: String!
+    side: String!
+    createdAt: String!
+  }
+
+  type VoteData {
+    status: Int!
+    votes: [Vote]!
+  }
+
   type Query {
     createRepo(owner: String, repo: String, defaultHash: String, contributor_id: String, side: String): String,
     getRepo(repo: String): Repo,
@@ -74,6 +86,7 @@ var schema = buildSchema(`
     getPRvoteTotals(owner: String, repo: String, defaultHash: String, contributor_id: String, side: String): String,
     getPRvoteYesTotals(owner: String, repo: String, defaultHash: String, contributor_id: String, side: String): String,
     getPRvoteNoTotals(owner: String, repo: String, defaultHash: String, contributor_id: String, side: String): String,
+    getVotes(repo: String, defaultHash: String): VoteData,
   }
 `);

@@ -226,6 +239,12 @@ var root = {
       args.side
     );
   },
+  getVotes: async (args) => {
+    return await getVotes(
+      args.repo,
+      args.defaultHash,
+    );
+  },
 };

 const port = 4002;
diff --git a/src/requests.js b/src/requests.js
index 15bb29b..1b28d58 100644
--- a/src/requests.js
+++ b/src/requests.js
@@ -250,6 +250,17 @@ var root = {
     const json = JSON.parse(res.text);
     return json.data.getPRvoteNoTotals;
   },
+  getVotes: async (repo, defaultHash) => {
+    const res = await superagent
+      .post(`${port}/graphql`)
+      .send({
+        query: `{ getVotes(repo: "${repo}", defaultHash: "${defaultHash}") { status, votes { contributor_id, side, createdAt } } }`,
+      })
+      .set("accept", "json");
+    const json = JSON.parse(res.text);
+    console.log('json.data.getVotes', json.data.getVotes)
+    return json.data.getVotes;
+  },
 };

 module.exports = root;
diff --git a/test-server/getVotes.js b/test-server/getVotes.js
new file mode 100644
index 0000000..1df8e58
--- /dev/null
+++ b/test-server/getVotes.js
@@ -0,0 +1,26 @@
+const assert = require("assert");
+const { getVotes } = require("../src/requests");
+
+describe("get votes", function () {
+  it("should get all the votes for a pull request and their relevant data, contributor_id, votepower, side, timestamp", async function () {
+    const voteData = await getVotes(
+      /*repo:*/ "joseph/demo",
+      /*defaultHash:*/ "defaultHash2",
+    );
+    assert.deepEqual(
+      voteData.status,
+       200,
+       "Fail to get votes for a PR"
+     );
+    assert.deepEqual(
+      voteData.votes[0].contributor_id,
+       '0x0c55D3B26A1229B9D707a4272F55E66103301858',
+       "Fail to get votes for a PR"
+     );
+    assert.deepEqual(
+      voteData.votes[0].side,
+      'yes',
+      "Fail to get votes for a PR"
+    );
+});
+});
diff --git a/test-server/run-tests.sh b/test-server/run-tests.sh
index 7b9edf5..128d4aa 100755
--- a/test-server/run-tests.sh
+++ b/test-server/run-tests.sh
@@ -1 +1 @@
-npm test test-server/postCreateRepo.js test-server/getRepoStatus.js test-server/postTransferTokens.js test-server/postGetContributorTokenAmount.js test-server/postGetAuthorizedContributor.js test-server/postCreatePullRequest.js  test-server/createLinkedPullRequest.js test-server/voteToMerge.js test-server/votePullRequestNotFound test-server/updatePullRequest test-server/voteToClose.js test-server/voteOpen.js test-server/invalidTransfer.js test-server/duplicateVote.js test-server/voteAfterMerge.js test-server/voteConflict.js test-server/voteConflictLinks.js test-server/voteUpdate.js test-server/voteUpdateLinks.js test-server/getMostRecentLinkedPullRequest.js
+npm test test-server/postCreateRepo.js test-server/getRepoStatus.js test-server/postTransferTokens.js test-server/postGetContributorTokenAmount.js test-server/postGetAuthorizedContributor.js test-server/postCreatePullRequest.js  test-server/createLinkedPullRequest.js test-server/voteToMerge.js test-server/votePullRequestNotFound test-server/updatePullRequest test-server/voteToClose.js test-server/voteOpen.js test-server/invalidTransfer.js test-server/duplicateVote.js test-server/voteAfterMerge.js test-server/voteConflict.js test-server/voteConflictLinks.js test-server/voteUpdate.js test-server/voteUpdateLinks.js test-server/getMostRecentLinkedPullRequest.js test-server/getVotes.js
```

Here is the diff for turbosrc-service:

```
commit 7ffd927b1ee149118c7946bfd8dbeb85302ffb30
Author: Turbosrc Engineer <>
Date:   Wed May 3 13:01:54 2023 -0400

    feature: getVotes for a pr

diff --git a/server.js b/server.js
index 35125eb..067bc40 100755
--- a/server.js
+++ b/server.js
@@ -31,7 +31,8 @@ const {
   checkRejectPullRequestHistory,
   getContributorTokenAmount,
   getUser,
-  findOrCreateUser
+  findOrCreateUser,
+  getVotes
 } = require('./src/lib/actions')
 const {
        getGitHubPullRequest,
@@ -112,6 +113,18 @@ var schema = buildSchema(`
     push_permissions: Boolean!
   }
 
+  type Vote {
+    contributor_id: String!
+    side: String!
+    votePower: String!
+    createdAt: String!
+  }
+
+  type VoteData {
+    status: Int!
+    votes: [Vote]!
+  }
+
   type Query {
     createTsrcPullRequest(owner: String, repo: String, defaultHash: String, childDefaultHash: String, head: String, branchDefaultHash: String, remoteURL: String, baseBranch: String, fork_branch: String, title: String): String,
     getContributorTokenAmount(owner: String, repo: String, defaultHash: String, contributor_id: String, side: String, token: String): ContributorTokenAmount,
@@ -132,6 +145,7 @@ var schema = buildSchema(`
     newPullRequest(owner: String, repo: String, defaultHash: String, contributor_id: String, side: String): String,
     getPullRequest(owner: String, repo: String, defaultHash: String, contributor_id: String, side: String): PullRequest,
     getGitHubPullRequest(owner: String, repo: String, defaultHash: String, contributor_id: String): ghPullRequest,
+    getVotes(repo: String, defaultHash: String): VoteData,
     getPRvoteTotals(owner: String, repo: String, defaultHash: String, contributor_id: String, side: String): String,
     getPRvoteYesTotals(owner: String, repo: String, defaultHash: String, contributor_id: String, side: String): String,
     getPRvoteNoTotals(owner: String, repo: String, defaultHash: String, contributor_id: String, side: String): String,
@@ -359,6 +373,12 @@ var root = {
     const voteTotals = getPRvoteTotals(fakeTurboSrcReposDB, args)
     return voteTotals.percentVotedQuorum
   },
+  getVotes: async (args) => {
+    return await getVotes(
+      args.repo,
+      args.defaultHash,
+    );
+  },
   getPRvoteYesTotals: async (args) => {
     const voteYesTotals = await getPRvoteYesTotals(args)
 
diff --git a/src/lib/actions.js b/src/lib/actions.js
index 09858ec..0a15e39 100644
--- a/src/lib/actions.js
+++ b/src/lib/actions.js
@@ -29,6 +29,7 @@ const {
   postGetPRvoteTotals,
   postGetAuthorizedContributor,
   getRepoStatus,
+  getVotes
 } = require("./../utils/engineRequests");
 const {
   postCreateUser,
@@ -224,6 +225,13 @@ const root = {
 
     return voteYes;
   },
+  getVotes: async (args) => {
+    const resGetVotes = await getVotes(
+      args.repo,
+      args.defaultHash,
+    );
+    return resGetVotes;
+  },
   getPRvoteNoTotals: async function (args) {
     console.log('no:', args.contributor_id)
     const convertedHashes = await convertDefaultHash(args.owner, args.repo, args.defaultHash, false, args.contributor_id)
diff --git a/src/utils/engineRequests.js b/src/utils/engineRequests.js
index 86dd53f..a406cb9 100644
--- a/src/utils/engineRequests.js
+++ b/src/utils/engineRequests.js
@@ -291,6 +291,17 @@ var root = {
     const json = JSON.parse(res.text);
     return json.data.getPRvoteNoTotals;
   },
+  getVotes: async (repo, defaultHash) => {
+    const privateStore = await getServiceEndpoint("offchain")
+    const res = await superagent
+      .post(privateStore)
+      .send({
+        query: `{ getVotes(repo: "${repo}", defaultHash: "${defaultHash}") { status, votes { contributor_id, side, votePower, createdAt } } }`,
+      })
+      .set("accept", "json");
+    const json = JSON.parse(res.text);
+    return json.data.getVotes;
+  },
 };
 
 module.exports = root;

```