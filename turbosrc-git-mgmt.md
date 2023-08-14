# TurboSRC Git Management

This document provides instructions on how to manage the TurboSRC repository and its submodules.

## jClone the Repository
To clone the TurboSRC repository along with all of its submodules, use the following command:

```
git clone --recurse-submodules https://github.com/turbo-src/turbo-src.git
```

This will clone the main repository and all of the submodules at the same time.

## Update the Submodules

If you have already cloned the repository and want to update the submodules, use the following commands:

```
git submodule init
git submodule update
```

To update the submodules to their latest commit on the remote branches, use the following command:

```
git submodule update --remote
```

## Add a Submodule

To add a submodule, navigate to the root directory of the main repository and use the following command:

```
git submodule add <repo-url>
```

Replace <repo-url> with the URL of the repository that you want to add as a submodule.

## Delete a Submodule

To delete a submodule, follow these steps:

1. Delete the relevant section from the .gitmodules file.
2. Delete the relevant section from .git/config.
3. Run git add .gitmodules.
4. Remove the submodule directory and delete the submodule files from the Git index: `git rm --cached <submodule-name> and rm -rf <submodule-name>`.
5. Commit the changes.
6. Delete the untracked submodule files: `rm -rf .git/modules/<submodule-name>`.

Replace `<submodule-name>` with the name of the submodule that you want to delete.

## Other Useful Commands

* To check the status of all submodules: `git submodule status`
* To pull all changes in the submodules: `git submodule update --remote --merge1`
* To push all changes in the submodules: `git push --recurse-submodules=on-demand`

Remember to regularly pull changes from the remote branches to keep your local copies up-to-date.

#### Get lastest changes

`git pull` and `git submodule update --remote`

### Make changes

Here is an example

**1. Checkout feature branch in the turbo-src**

```git checkout -b myFeature```

**2. Checkout feature branch in the targeted submodule**

```
cd turbosrc-engine
git checkout -b myFeature
```

**3. Stage and commit in both the submodule and monorepo after you make changes.**

`turbosrc-engine`

```
git add <file>
git commmit -m "New feature!"
```

`turbo-src`

```
git add <submodule>
git commmit -m "New feature!"
```

**4. Push changes to both the submodule and the monorepo.**

`turbosrc-engine`

```
git push origin myFeature
```

`turbo-src`

```
git push origin myFeature
```

**5. Pull in new changes form monorepo and submodules**

`turbo-src`

```
git pull origin myFeature
```

**6. Go back to master and forget about myFeature**

`turbo-src`

```
git checkout master
git submodule update --init --recursive
```

**7. You want to go back into myFeature branch.**

`turbo-src`

```
git checkout myFeature
git submodule update --init --recursive
```

**8. Let's say another developer wants to experiment with this new feature.**

`turbo-src`

```
git fetch origin myFeature
git checkout myFeature
git submodule update --init --recursive
```
