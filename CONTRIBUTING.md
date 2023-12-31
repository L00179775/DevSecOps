# Contribution Guide

## If you have not contributed to this repo before:

- If you find a bug in a test suite or in an individual project test, please
  open an issue.
- If the bug can be confirmed, we will let you know, and if you would like to
  begin working on fixing the bug, please follow the guidelines below.

### In general

- Fork, clone locally, and install dependencies using `yarn install`.
- Please make all PRs against a branch you have created specifically to address the issue you are fixing.
- Be sure to test all your changes to the bundle locally by running `yarn test`.
  Make sure all tests pass before you create a PR. See the section below
  about [Automated Testing](#automated-testing).

### For fixing project test suite bugs / adding new project test suites

- All tests suites should use only existing dependencies (Mocha, Chai).
- We are using Chai's `assert` library.
- **BREAKING CHANGES:**
  - Please test all of your changes against example projects for whatever project you are working on. If the changes you make cause the example project to fail any tests, this is considered a breaking change, as it could cause other Campers' past solutions to fail. This should, in general, be avoided once freeCodeCamp is no longer in beta, as we do not want to break too many past projects. If you feel that this is a change that is ABSOLUTELY NECESSARY, please discuss this with @no-stack-dub-sack or @QuincyLarson.
  - If the new freeCodeCamp curriculum is still in beta and you make a breaking change, but please be sure to include in your PR a forked version of the official project with updated code that passes all new and existing tests. Advise one of the above people so that the official project can be changed accordingly if your changes are accepted.
- Each exported test suite must be imported into `project-tests/index.js`, and a corresponding key, value must be added to the exported object.
- Please follow naming conventions for naming files and functions.

### For adding functionality to the project test suite itself

- All "utility functions" are in `index.js` and are called from `test-suite-skeleton.js`, again, through the `FCC_Global` object.
- Take a look at how everything works... If you think you have a feature that would improve functionality, go ahead and open an issue and/or implement it on your end and see how it works.
- If it looks good to you and you think this represents an improvement, go ahead and submit a PR.

### Bundle CDN

- https://cdn.freecodecamp.org/testable-projects-fcc/v1/bundle.js

### Automated testing

Running tests locally, summary:

```sh
yarn build && yarn test
```

If this is your first time testing, you will need to do some setup before the
above command will work (see the [Setup section](#setup)).

#### Motivation

To better understand why we automated the testing of this project, you could
optionally read the section below on
[To test your code locally](#to-test-your-code-locally). The steps there only
test one CodePen sample project and it will easily take you more than ten
minutes to do this manually. If you were to make a change in one of the
programs shared by all the projects, you would have to repeat those steps more
than a dozen times, and it would take a good hour or so of your time.

To further motivate you, sometimes when testing the code manually and
locally, your changes will work but then fail in the CodePen environment!

By using the automated tests, you can make sure your changes work for all the
sample CodePens in the CodePen environment and in less than five minutes for
_all_ the projects, and while working on something else.

So it is well worth it to go through the setup steps below, most of which only
need to be run one time.

#### Setup

1. You should have already run `yarn` per the forking instructions.
1. Make sure you have the Chrome browser installed. You should have version
   59.0.3071.115 or later.
1. You need to configure your test environment by setting up the `.env`
   file (see below).

**Configuring your test environment with .env**

If you do not already have a `.env` file, copy the example file to get
you started. From the project root directory:

```sh
cp sample.env .env
```

Now you can edit the `.env` file and to reflect your environment. It
is well documented inside the file and the defaults should work but at the very
least you will have to choose a values for `SELENIUM_BROWSER` and `BROWSER_PATH`.

(The `.env` file is ignored by git, so you can be sure it won't be
overwritten by other contributers once you have created it).

**Testing using your local bundle.js file**

This is the most common scenario and requires all of the previously listed steps.
To build `bundle` and `example projects` and start watching your files for changes run:

```sh
yarn start
```

Leave that running. And in a new window, you can run the tests:

```sh
yarn test
```

You will find screenshots of all the test results in the `test/screenshots`
directory (or the directory you configured in your `test/setup.js` file).

#### Testing tips

**Running just one test**

Many times you are only changing the tests for one CodePen sample project. You
can run the tests for one project by using the Mocha grep command line flag. For
example, the following will run only the D3 Tree Map test:

```sh
yarn mocha -g 'D3 Tree'
```

For the names used by the grep option, see the `name` attribute in the default
export object of the `src/project-tests/index.js` file.

**Running tests without headless mode**

By default tests run in the headless mode. If you need to see how it works on
your screen, you need to disable the headless mode. For this uncomment next line
in `.env` file:

```sh
HEADLESS=0
```

and run tests:

```sh
yarn test
```

If everything is set up correctly, you should see the following happen
automatically:

1. Browser starts.
1. The URL for one of the sample projects loads.
1. The "Run Tests" button is clicked and the script waits for test to complete.
1. The "Tests" button is clicked to see the test output.
1. A screenshot of the results is saved.
1. Move on to the next project by returning to step 1.

**Running tests on a virtual screen**

If for some reason headless mode doesn't work for you, you can use the tried
and trusted method of running a virtual screen via the `Xvfb` command.
You may need to install the command first.

In a separate window run the following command:

```sh
Xvfb :99 -screen 0 1920x1080x16
```

And then using the `:99` and (-screen) `0` values from above you run the tests
like this:

```sh
DISPLAY=':99.0' yarn test
```

The tests will now run on the virtual display without popping up the browser.

Running a single test works similarly:

```sh
DISPLAY=':99.0' yarn mocha -g 'D3 Tree'
```

**Capturing video of the tests**

If you use a virtual display, it's pretty easy to capture everything on video,
which can be useful for debugging when the screenshots don't tell you enough.
Run the following just before you start the tests. Note the `:99.0` from above:

```sh
ffmpeg -video_size 1920x1080 -framerate 10 -f x11grab -i :99.0 -crf 36 test-output.mp4
```

Remember to kill the command (ctrl-c?) when your tests finish!

The framerate is set low and the compression high to keep the video output file
small, but if the video quality is not good enough, you can increase the
`-framerate` value and / or lower the `-crf` value.

#### Port 8080

Note that the `yarn develop` command may choose a different port
if 8080 is already in use. You will either have to adjust your `test/setup.js`
file to reflect what port the develop script uses, or make sure port
8080 is not in use before running the script.

#### Mac Retina display

On Mac Retina displays, the value your system gives you for display is
double what you need to put in the `.env` file. For example, if your
system settings say your resolution is 2880×1800 then you would put the
following in your `.env`:

```sh
BROWSER_MAX_WIDTH=1440
BROWSER_MAX_HEIGHT=900
```

#### Dependencies

Much like the project tests, we use Mocha and Chai. For automating the browser
we also use Selenium, chromedriver, geckodriver, webpack-dev-server and
live-server. Please be cautious about adding new dependencies unless absolutely
needed.

#### Travis CI

The automated tests have been setup to run on Travis CI when you create a PR.
PRs should not be merged until they pass the tests (along with a code review
and the usual PR approval and merge process - passing the tests on Travis CI is
only one criteria for merging).

You can see the success / fail status for Travis directly on the PR itself on
github, as well as in the README.md file on the main page of the project.

See the `.travis.yml` file for how the tests are set up to run on Travis CI.

#### Useful references

https://trac.ffmpeg.org/wiki/Capture/Desktop

https://docs.travis-ci.com/user/uploading-artifacts/

https://atasteofsandwich.wordpress.com/2014/05/04/visual-regression-tests-with-travis-ci-and-github/

https://docs.travis-ci.com/user/gui-and-headless-browsers/

http://seleniumhq.github.io/selenium/docs/api/javascript/

https://github.com/SeleniumHQ/selenium

https://github.com/SeleniumHQ/selenium/wiki

https://groups.google.com/forum/#!forum/selenium-users

https://github.com/SeleniumHQ/selenium/wiki/Selenium-Help

That's the end of the automated testing section. If you want to understand how
to do it manually, see below. But be forewarned that the manual testing will
eventually add up to a lot of lost time, so it's worth it to go through the
one-time local set up and use the automated tests instead.

That is not to say that the local tests are never useful. If someone reports a
bug that effects their local project it can be useful to copy their code for
local testing and debugging. There may be other use cases too.

### To test your code on CodePen project

Start the live-server with the following command:

```sh
yarn develop
```

You should see something like the following:

```sh
> webpack-dev-server --https
Project is running at https://127.0.0.1:8080/
...

...
Compiled successfully.
```

Add a link to `https://127.0.0.1:8080/bundle.js` into your CodePen project.

Leave this command running, and every time you change files in `src/`,
it will automatically detect the change, compile and reload `bundle.js`.

### To test your code locally

Import the code from the your project that you are creating/editing the test
suite for into the `src/projects/local_test` directory:

- Create index.html and paste your HTML code here, include any other external
  resources you need (e.g. React, jQuery, D3, FontAwesome, etc.).
- Create files for your code, i.e. JS, CSS, SCSS, SASS etc.
- Include the JS & CSS in the files you created in `local_test`.
- Run `yarn start` to start watching your files for changes and serve.
- Now your changes to the test files will be automatically bundled by webpack
  and served in the project that is running locally.
