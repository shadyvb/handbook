# Pull Requests

WP-CLI follows a pull request workflow for changes to its code (and documentation). Whether you want to fix a bug or implement a new feature, the process is pretty much the same:

0. [Search existing issues](https://github.com/issues?utf8=%E2%9C%93&q=is%3Aopen+sort%3Aupdated-desc+org%3Awp-cli); if you can't find anything related to what you want to work on, open a new issue in the appropriate repository so that you can get some initial feedback.
    1. Opening an issue before submitting a pull request helps us provide architectural and implementation guidance before you spend too much time on the code.
1. Fork the repository you'd like to modify, either the framework or one of the command packages.
    1. See [Setting Up](#setting-up) for more details on configuring the codebase for development.
2. Create a branch for each issue you'd like to address. Commit your changes.
3. Push the code changes from your local clone to your fork.
4. Open a pull request. It doesn't matter if the code isn't perfect. The idea is to get it reviewed early and iterate on it.
5. Respond to [code review feedback](https://make.wordpress.org/cli/handbook/code-review/) in a timely manner, recognizing development is a collaborative process.
6. Once your pull request has passed code review, it will be merged into master and be in the pipeline for the next release.

New to WP-CLI commands? You may want to [start with the commands cookbook](https://make.wordpress.org/cli/handbook/commands-cookbook/) to learn more about how commands work.

There are three classes of repos you might want to edit:

* [wp-cli/wp-cli](https://github.com/wp-cli/wp-cli/) is the framework implementation.
* [wp-cli/scaffold-command](https://github.com/wp-cli/scaffold-command/) is an example of a command implementation. There are many others.
* [wp-cli/handbook](https://github.com/wp-cli/handbook/) contains documentation rendered in the handbook.

## Expectations

When submitting a pull request, there are several expectations to keep in mind.

**Tests are required**

Most of the time, we'll ask that functional or unit tests be added to cover the change. If it's a new feature, the pull request needs tests. If it's fixing a bug, the pull request needs tests.

See the documentation below for more information on writing and running tests.

**Follow WordPress Coding Standards**

While not yet strictly enforced, the WP-CLI project generally follows the [WordPress Coding Standards](http://make.wordpress.org/core/handbook/coding-standards/). We may ask you to clean up your pull request if it deviates too much.

**Contributions are atomic**

To make it far easier to merge your code, each pull request should only contain one conceptual change. Keeping contributions atomic keeps the pull request discussion focused on one topic and makes it possible to approve changes on a case-by-case basis.

If you submit a pull request with multiple conceptual changes, we'll ask you to resubmit as separate pull requests.

**Make regular process on your contribution**

Through [our code review process](https://make.wordpress.org/cli/handbook/code-review/), we'll work with you to make sure your pull request is ready for merge. But if changes are needed and we haven't heard from you in **two weeks**, we'll consider the pull request abandoned. Someone else may pick it up and make the changes required. Or it may be closed.

If you need to step away for any reason, make a comment on the pull request or the related issue so we can pick things up or put things on hold when needed.

## Setting up

If you haven't submitted a pull request before, you'll want to install WP-CLI for local development:

1. Install [Composer](https://getcomposer.org/) and [hub](https://hub.github.com/) if you don't already have them.
2. Clone the WP-CLI git repository to your local machine: `git clone git@github.com:wp-cli/wp-cli.git ~/wp-cli`
3. Change into the cloned directory and fork WP-CLI: `cd ~/wp-cli; hub fork`
4. Install all Composer dependencies: `composer install --prefer-source`
5. Alias the `wp` command to your new WP-CLI install: `alias wp=~/wp-cli/bin/wp`
6. Verify WP-CLI was installed properly: `wp --info`

Commands bundled with WP-CLI (e.g. `wp scaffold plugin`) will be editable from the `vendor/wp-cli` directory (e.g. `vendor/wp-cli/scaffold-command`). The `--prefer-source` flag when installing WP-CLI ensures each command is installed as a Git clone, making it easier to commit to.

Commands available for standalone installation (e.g. `wp dist-archive`) can be installed from source (e.g. `wp package install git@github.com:wp-cli/dist-archive-command.git`). Run `wp package path <package-name>` to find the appropriate directory to edit.

Importantly, you'll need to fork each repository in order to have an `origin` to push to. Run `hub fork` to fork a repository from the command-line:

    $ cd vendor/wp-cli/scaffold-command
    $ hub fork
    Updating danielbachhuber
    From https://github.com/wp-cli/scaffold-command
     * [new branch]      master     -> danielbachhuber/master
    new remote: danielbachhuber
    $ git remote -v
    danielbachhuber git@github.com:danielbachhuber/scaffold-command.git (fetch)
    danielbachhuber git@github.com:danielbachhuber/scaffold-command.git (push)

Once you've done so, you'll have a fork in your GitHub account and new remote you can push to. If you didn't install `hub`, you'll need to fork the target repo through the web UI and manually add your fork as a remote.

## Running and writing tests

There are two types of automated tests:

* functional tests, implemented using [Behat](http://behat.org)
* unit tests, implemented using [PHPUnit](http://phpunit.de/)

### Functional tests

The functional test files for each WP-CLI repository are in the `features/` directory. Each `.feature` file comprises one or more functional tests for a given feature (roughly organized by command).

A functional test can be as simple as:

```
Feature: Evaluating PHP code and files.

  Scenario: Basics
    Given a WP install

    When I run `wp eval 'var_dump(defined("WP_CONTENT_DIR"));'`
    Then STDOUT should contain:
      """
      bool(true)
      """
```

Functional tests typically follow this pattern:

* **Given** some background,
* **When** a user performs a specific action,
* **Then** the end result should be X (and Y and Z).

Before running the functional tests, you'll need a MySQL (or MariaDB) user called `wp_cli_test` with the password `password1` that has full privileges on the MySQL database `wp_cli_test`. Running the following as root in MySQL should do the trick:

    GRANT ALL PRIVILEGES ON wp_cli_test.* TO "wp_cli_test"@"localhost" IDENTIFIED BY "password1";

Then, to run the entire test suite:

    ./vendor/bin/behat --expand

Or to test a single feature:

    ./vendor/bin/behat features/core.feature

More info can be found by using `./vendor/bin/behat --help`.

Each repository is configured to run its tests on every code push. The [wp-cli/automated-tests](https://github.com/wp-cli/automated-tests) repository runs all tests for all repositories on a regular basis.

### Unit tests

The unit test files are in the `tests/` directory.

To run the unit tests, just execute:

    ./vendor/bin/phpunit

## Finally...

Thanks! Hacking on WP-CLI should be fun. If you find any of this hard to figure out, let us know so we can improve our process or documentation!
