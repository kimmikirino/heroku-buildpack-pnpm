# PNPM compatible Buildpack for Heroku and Node.js

This is an unofficial for of the official [Heroku buildpack](http://devcenter.heroku.com/articles/buildpacks) for Node.js apps.

It supports pnpm in addition to yarn/npm. As long as the root of your project contains a `pnpm-lock.yaml` file, PNPM will be used to install all dependencies.

## Documentation

This will run a regular `pnpm install` in the Heroku environment. It will install dev depdencies. Once node env is commented in the code

The pnpm store will automatically be cached, you do not need to specify additional cache directories manually.

For more information about using this Node.js buildpack on Heroku, see these Dev Center articles:

- [Heroku Node.js Support](https://devcenter.heroku.com/articles/nodejs-support)
- [Getting Started with Node.js on Heroku](https://devcenter.heroku.com/articles/nodejs)
- [Troubleshooting Node.js Deploys](https://devcenter.heroku.com/articles/troubleshooting-node-deploys)

For more general information about buildpacks on Heroku:

- [Buildpacks](https://devcenter.heroku.com/articles/buildpacks)
- [Buildpack API](https://devcenter.heroku.com/articles/buildpack-api)

## Using the PNPM Heroku Node.js buildpack

It's suggested that you use the latest version of the release buildpack. You can set it using the `heroku-cli`.

```sh
heroku buildpacks:set https://github.com/unfold/heroku-buildpack-pnpm
```

Your builds will always used the latest published release of the buildpack.

If you need to use the git url, you can use the `latest` tag to make sure you always have the latest release. **The `main` branch will always have the latest buildpack updates, but it does not correspond with a numbered release.**

```sh
heroku buildpacks:set https://github.com/unfold/heroku-buildpack-pnpm#latest -a my-app
```

### Chain Node with multiple buildpacks

This buildpack automatically exports node, pnpm, npm, and any node_modules binaries
into the `$PATH` for easy use in subsequent buildpacks.

## Feedback

Having trouble? Dig it? Feature request?

- [help.heroku.com](https://help.heroku.com/)
- [GitHub issues](https://github.com/heroku/heroku-buildpack-nodejs/issues)

## Development

### Prerequisites

For local development, you may need the following tools:

- [Docker](https://hub.docker.com/search?type=edition&offering=community)
- [Go 1.14](https://golang.org/doc/install#install)
- [upx](https://upx.github.io/)

### Deploying an app with a fork or branch

To make changes to this buildpack, fork it on GitHub.
Push up changes to your fork, then create a new Heroku app to test it,
or configure an existing app to use your buildpack:

```
# Create a new Heroku app that uses your buildpack
heroku create --buildpack <your-github-url>

# Configure an existing Heroku app to use your buildpack
heroku buildpacks:set <your-github-url>

# You can also use a git branch!
heroku buildpacks:set <your-github-url>#your-branch
```

### Downloading Plugins

In order to download the latest plugins that have been released, run the following:

```
plugin/download.sh v$VERSION
```

Make sure the version is in the format `v#`, ie. `v7`.

## Tests

The buildpack tests use [Docker](https://www.docker.com/) to simulate
Heroku's stacks.

To run the test suite:

```
make test
```

Or to just test a specific stack:

```
make heroku-18-build
make heroku-20-build
```

The tests are run via the vendored
[shunit2](https://github.com/kward/shunit2)
test framework.

### Debugging

To display the logged build outputs to assist with debugging, use the "echo" and "cat" commands. For example:

```sh
test() {
  local log_file var

  var="testtest"
  log_file=$(mktemp)
  echo "this is the log file" > "$log_file"
  echo "test log file" >> "$log_file"

  # use `echo` and `cat` for printing variables and reading files respectively
  echo $var
  cat $log_file

  # some cases when debugging is necessary
  assertEquals "$var" "testtest"
  assertFileContains "test log file" "$log_file"
}
```

Running the test above would produce:

```log
testtest
this is the log file
test log file
```

The test output writes to `$STD_OUT`, so you can use `cat $STD_OUT` to read output.
