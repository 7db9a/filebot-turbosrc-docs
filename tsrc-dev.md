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

Example usage with user "7db9a" and repo "demo". Essentially, just replace your username and keep everything as is for basic usage.

```
./tsrc-dev test 7db9a demo execute_all
````

`username` is your GH personal username name. `repository` is the repo forked from turbo-src for testing.

### More on end-to-end tests with tsrc-dev

`tsrc-dev test` is a subcommand.

Build test environment and run all tests.

```
./tsrc-dev test <username> <repository> execute_all
````

Delete a fork.

```
./tsrc-dev test <username> <repository> delete_fork
````

Fork a repo.

```
./tsrc-dev test <username> <repository> fork_repo
````

Create pull requests.

```
./tsrc-dev test <username> <repository> create_pull_request
````

Run tests
```
./tsrc-dev test <username> <repository> run_tests
```