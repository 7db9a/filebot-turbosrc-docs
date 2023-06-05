**tsrc-dev** cli development tool to make it easy to launch Turbosrc services and to do end-to-end tests.

Start.

```
./tsrc-dev start
```

Stop turbosrc.

```
./tsrc-dev stop
```

Restart turbosrc.

```
./tsrc-dev restart
```

Test turbosrc

```
./tsrc-dev test <username> <repository> execute_all
````

Example usage with user "turbo-src" and repo "demo". Essentially, just replace your username and keep everything as is for basic usage.

```
./tsrc-dev test turbo-src demo execute_all
````

`username` is your GH personal username name. `repository` is the repo forked from turbo-src for testing.

### More on end-to-end tests with tsrc-dev

`tsrc-dev test` is a subcommand.

Build test environment and run all tests.

```
./tsrc-dev test <username> <repository> execute_all
````

There are various other commands with `tsrc-dev test`. For further usage just enter `tsrc-dev test`.

## Other commands

Here is a sample below for more granular control over testing. For testing Turbosrc, please make username is `turbo-src` and repository is `demoo`.

Delete a fork on Github.

```
./tsrc-dev test <username> <repository> delete_fork
````

Fork a repo on Github. For Turbosrc testing, `

```
./tsrc-dev test <username> <repository> fork_repo
````
Create pull requests (only works against `testrepo` on `turbo-src`).

```
./tsrc-dev test <username> <repository> create_pull_request
````

Run all the tests without building or re-building test environment.

```
./tsrc-dev test <username> <repository> run_tests
```