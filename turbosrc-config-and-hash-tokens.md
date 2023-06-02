### Purpose 

How to set up your `.config.json` and to how to secure you github key of the Turbosrc launcher/hoster. The secret is stored the `turbosrc-service/.config.json`.

### Install

Clone github.com/turbo-src/jwt_hash_decrypt and run `npm install` in the cloned project's root directory. Should work on node version v10.0 but if in doubt use v12.22.0.

### encrypt

```
node jwt_hash_decrypt.js --secret=<secret> --string='{"githubToken": "ghp_123..."}'
```
It will encrypt your github token.

### decrypt

```
node jwt_hash_decrypt.js --secret=<secret> --token=<token_to_decrypt>
```
It will return your original, unhashed github token.

## Config
You'll need a `.config.json` file in the root directory.

- Replace YOUR_USERNAME with your Github username
- Replace JWT with your signing key
- Replace YOUR_ENCRYPTED_TOKEN with the JWT string from above

```
{
    "github": {
        "organization": "turbo-src",
        "user": "YOUR_USERNAME",
        "apiToken": "YOUR_ENCRYPTED_TOKEN"
    },
    "turbosrc": {
        "endpoint": {
          "mode": "online",
           "url": "http://localhost:4000/graphql"
        },
        "jwt": "JWT",
        "store": {
            "repo": {
                "addr": "REPO_ADDR",
                "key": "REPO_KEY"
            },
            "contributor": {
                "addr": "YOUR_ADDR",
                "key": "YOUR_KEY"
            }
        }
    },
    "offchain": {
        "endpoint": {
          "mode": "online",
          "url": "http://localhost:4002/graphql"
        }
    },
    "namespace": {
        "endpoint": {
          "mode": "online",
          "url": "http://localhost:4003/graphql"
        }
    },
    "gh": {
        "endpoint": {
          "mode": "online",
          "url": "http://localhost:4004/graphql"
        }
    },
    "testers": {
        "a": {
          "user": "tsrctester1",
          "key": "TEST_KEY_1",
	      "apiToken": "ENCRYPTED_TOKEN_1"
        },
        "b": {
          "user": "tsrctester2",
          "key": "TEST_KEY_2",
	      "apiToken": "ENCRYPTED_TOKEN_2"
        },
        "c": {
          "user": "tsrctester3",
          "key": "TEST_KEY_3",
	      "apiToken": "ENCRYPTED_TOKEN_3"
        },
        "d": {
          "user": "tsrctester4",
          "key": "TEST_KEY_4",
	      "apiToken": "ENCRYPTED_TOKEN_4"
        },
        "e": {
          "user": "tsrctester5",
          "key": "TEST_KEY_5",
	      "apiToken": "ENCRYPTED_TOKEN_5"
        },
        "f": {
          "user": "tsrctester6",
          "key": "TEST_KEY_6",
	      "apiToken": "ENCRYPTED_TOKEN_6"
        },
        "g": {
          "user": "tsrctester7",
          "key": "TEST_KEY_7",
	      "apiToken": "ENCRYPTED_TOKEN_7"
        },
        "h": {
          "user": "tsrctester8",
          "key": "TEST_KEY_8",
	      "apiToken": "ENCRYPTED_TOKEN_8"
        },
        "i": {
          "user": "tsrctester9",
          "key": "TEST_KEY_9",
	      "apiToken": "ENCRYPTED_TOKEN_9"
        },
        "j": {
          "user": "tsrctester10",
          "key": "TEST_KEY_10",
	      "apiToken": "ENCRYPTED_TOKEN_10"
        },
        "k": {
          "user": "tsrctester11",
          "key": "TEST_KEY_11",
	      "apiToken": "ENCRYPTED_TOKEN_11"
        },
        "l": {
          "user": "tsrctester12",
          "key": "TEST_KEY_12",
	      "apiToken": "ENCRYPTED_TOKEN_12"
        }
    }
}
```
