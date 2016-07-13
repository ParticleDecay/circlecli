# CircleCLI

Unofficial CircleCI cross-platform CLI tool, written in Python.

![PyPI Version](https://img.shields.io/pypi/v/circlecli.svg) ![PyPI License](https://img.shields.io/pypi/l/circlecli.svg) ![CircleCI Status](https://img.shields.io/circleci/project/TheRealJoeLinux/circlecli.svg) ![Codecov Status](https://img.shields.io/codecov/c/github/TheRealJoeLinux/circlecli.svg)

**_Note: This project is currently in beta. There will be bugs._**

I wrote this tool because I realized there wasn't anything cross-platform, that you could easily use on Linux, OSX, or Windows (if you are one of the rare ones using Windows CLI anyway). Python comes pre-installed on most Linux distros, and OSX, so it should be easy to get up and running.

CircleCLI supports most of what the official API supports (new features are being added), and it also supports a little bit of what isn't supported by CircleCI just yet (namely, adding multiple environment variables in a single command and displaying concise response info).

See the [Examples](#examples) section for common types of usage.

## Installation
```bash
pip install circlecli
```

## Configuration
CircleCLI requires an API token from your CircleCI [Account Settings](https://circleci.com/account/api) page.
You may set this token as an environment variable named `CIRCLE_TOKEN` (good for scripting), or CircleCLI will interactively ask you for this token on first run if no configuration file is present.
If you let CircleCLI prompt you for info, then it will create a configuration file named `.circlecli.ini` in your user's home directory that looks like this:
```
[circlecli]
token = XXXXX
default_username = XXXXX
```
You can always edit your own values in this configuration file.

## Usage
```
circlecli [--help] [--project PROJECT] [--username USERNAME]
          [--build-num BUILD_NUM] [--config] [--verbose]
          [--set ENVVARS]
          [action] [help]
  
Execute CircleCI REST API commands from the CLI.
  
positional arguments:
  action                An action to perform (see below)
  help                  Get help on a particular action
  
optional arguments:
  --help, -h            Display this help text
  --project PROJECT, -p PROJECT
                        The target project name
  --username USERNAME, -u USERNAME
                        The username of the project/repo owner
  --build-num BUILD_NUM, -b BUILD_NUM
                        The target build number
  --config, -c          Only print saved configuration values
  --verbose, -v         Return original full output from CircleCI
  --set ENVVARS, -s ENVVARS
                        Set a variable (used with 'env' action)
  
available actions:
  me            Provide information about the signed in user.
  projects      List of all the projects you're following on CircleCI.
  builds        Last 30 build summaries for the account (or for a project).
  artifacts     List the artifacts produced by a given build.
  retry         Retry a given build.
  add-key       Add a user to the build's SSH permissions.
  clear-cache   Clear the cache for a project.
  env           List or add environment variables for a project.
  <cmd> help    Display help text for a particular action
```
CircleCLI by default displays formatted, concise data for each action.
To display the original response from CircleCI, simply add `-v` to your command.

## Examples
#### Print information about your account
```bash
me@foobox:~$ circlecli me
Name: Barack Obama
Emails: president@whitehouse.gov
Sign-In Count: 36
Heroku API Key: None
Containers: 1
Parallelism: 1
Username: TheRealBarack
Admin: False
Projects: https://github.com/whitehouse/constitution, https://github.com/whitehouse/education, https://github.com/whitehouse/us_web_design_standards
```
#### Print detected configuration and exit
```bash
me@foobox:~$ circlecli config
token = THISISNOTAREALTOKEN
default_username = 
```
#### List projects you follow
```bash
me@foobox:~$ circlecli projects
whitehouse/constitution
whitehouse/education
whitehouse/us_web_design_standards
```
#### Filter only projects that build on OSX
```bash
me@foobox:~$ circlecli projects -f feature_flags.osx=true
whitehouse/education
```
#### List builds for the account
```bash
me@foobox:~$ circlecli builds
...
Build# : 11
Author : Barack Obama <president@whitehouse.gov>
Branch : master
Queued : Fri, Jul 08, 2016 08:09PM EDT
Trigger: github
URL    : https://circleci.com/gh/whitehouse/us_web_design_standards/11
Result : canceled

Build# : 12
Author : None
Tag    : v1.1.1
Queued : Fri, Jul 08, 2016 08:42PM EDT
Trigger: github
URL    : https://circleci.com/gh/whitehouse/us_web_design_standards/12
Result : success
```
#### Filter builds by a specific user
```
me@foobox.com:~$ circlecli builds -f user.login=TheRealBarack
Build# : 11
Author : Barack Obama <president@whitehouse.gov>
Branch : master
Queued : Fri, Jul 08, 2016 08:09PM EDT
Trigger: github
URL    : https://circleci.com/gh/whitehouse/us_web_design_standards/11
Result : canceled
```
#### View a specific build's details
```bash
me@foobox:~$ circlecli builds -u foo_user -p foo_project -b 12
Build# : 12
Author : None
Tag    : v1.1.1
Queued : Fri, Jul 08, 2016 08:42PM EDT
Trigger: github
URL    : https://circleci.com/gh/whitehouse/us_web_design_standards/12
Result : success
```
#### Retry a build
```bash
me@foobox:~$ circlecli retry -u foo_user -p foo_project -b 12
Build# : 13
Author : Barack Obama <president@whitehouse.gov>
Branch : master
Queued : Sun, Jul 10, 2016 12:17AM EDT
Trigger: retry
URL    : https://circleci.com/gh/whitehouse/us_web_design_standards/13
Result : None
```
#### Cancel a build (using the default username)
```bash
me@foobox:~$ circlecli cancel -p foo_project -b 12
Build# : 13
Author : Barack Obama <president@whitehouse.gov>
Branch : master
Queued : Sun, Jul 10, 2016 12:17AM EDT
Trigger: retry
URL    : https://circleci.com/gh/whitehouse/us_web_design_standards/13
Result : canceled
```
#### Clear a project's cache
```bash
me@foobox:~$ circlecli clear-cache -p foo_project
status: build dependency caches deleted
```
#### List a project's environment variables (using the default username)
```bash
me@foobox:~$ circlecli env -p foo_project
DEPLOY_ENV: xxxxg
```
#### Set an environment variable
```bash
me@foobox:~$ circlecli env -u foo_user -p foo_project -s TEST_ENV=stage
TEST_ENV: xxxxge
```
#### Set multiple environment variables (using the default username)
```bash
me@foobox:~$ circlecli env -p foo_project -s FOO=BAR -s BAZ=QUX
FOO: xxxxR
BAZ: xxxxX
```
