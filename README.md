# Patchwork -- Dependency Monitoring for the Front Team

## Factual Hackathon 2017

Project to help Front (& potentially other teams) manage versioning upates and security vulnerabilities in 3rd party package dependencies.

## Setup Patchwork

### Create VersionEye Account

[Signup](https://www.versioneye.com/signup?utf8=%E2%9C%93) for an account at https://www.versioneye.com/signup?utf8=%E2%9C%93. Make note of your API Key and organization name.
The organization name is found at the top of your VersionEye dashboard when logged in and will be something like `[username]_orga`.

### Create a SlackBot

Create a new app at https://api.slack.com/apps. Title it 'Patchwork'.

In 'Add Features and Functionality', click 'Incoming Webhooks'. At the bottom, click 'Add New Webhook to Team' and follow the instructions to choose the channel the app will post to. Make note of the generated webhook URL.

Upload the Patchwork logo from `patchwork/assets/patchwork.png` under "Basic Information > Display Information". I chose "Notices for Package Updates & Security Vulnerabilities" for the description and `#c55100` for the background color.

### Create your config file

In `patchwork/src`, copy `config.json.example` to `config.json` and edit the resulting file as necessary.
The API key, organization name, and Slack webhook URL you just generated are required.
Delete any lines of this file that you do not want - they will override the default values if they are set.

#### Other Config Options

##### `directory`

str: Absolute path of the directory to search for outdated dependencies

##### `directory_name`

str: Name of the directory to search for outdated dependencies. Used in place of the absolute path in reporting: for example, if a dependency is at `/Users/yourName/repo_name/dependency.type` and directory_name is set to `repo_name`, Slack and email notifications will refer to the file by `repo_name/dependency.type` instead of the full path on your machine.

##### `traversal_depth`

int: represents how deeply to search for dependency files. For example, the default `traversal_depth = 0` represents only searching in the top-level directory and will return files like `repository/package.json` but not `repository/src/package.json`.

##### `subdirectory_blacklist`

str[]: Patchwork will ignore any subdirectories with the included names. By default, Patchwork blacklists `node_modules` subdirectories so you don't waste API calls looking up your dependencies' dependencies. For an even more thorough search that includes all these files, you can override this setting in your config file with `subdirectory_blacklist: []`.

##### `data_directory`

str: If using the `-s` or `--save` option to save your dependency reports as JSON, this specifies the directory to use. By default, `data_directory` is `patchwork/data/`, meaning reports will be saved to `patchwork/data/<timestamp_of_request_initialization>/`. The timestamp-based subdirectories allow you to find reports from a given day or time easily.

You can override with any absolute path if you wish to save the reports outside of the Patchwork folder.

##### `test_webhook`

str: If using the `-t` or `--test` option, sends Slack notifications to this webhook. This option allows you to post to a different channel (I recommend your own slackbot channel) when testing so you don't overwhelm the real channel.

##### `dependency_file_types`

str[]: Patchwork searches for and uploads to VersionEye only those files that match a file name in this array. Default is `['package.json']`. See below for more options:

###### Supported Dependency Options

Files managed by the following package managers can be parsed with Patchwork through the VersionEye API. For example, both `Gemfile`s and `Gemfile.lock`s are supported under the Ruby Bundler.

- Composer (PHP)
- Bundler (Ruby)
- PIP (Python)
- NPM (Node.JS)
- Yarn (Node.JS)
- Bower (JavaScript)
- CocoaPods (Objective-C)
- Maven (Java)
- SBT (Scala)
- Gradle (Groovy)
- Leiningen (Clojure)
- Nuget (Microsoft .NET platform)
- Cargo (Rust)
- Biicode (C/C++)
- Berkshelf (Chef)
- Hex (Elixir)
- Cpan (Perl)

### Install Requirements

Patchwork requires python3.
Install python3 from https://www.python.org/downloads/.
Patchwork does not require any nonstandard libraries.

If your copy of python3 is anywhere other than `usr/local/bin/python3`, you may have to update the shebang on line 1 of `patchwork/src/patchwork.py` with the path to your copy. The path can be found with `which python3`.

## Run Patchwork

`cd` into `patchwork/src` and run `chmod +x patchwork.py` to make the dependency checker executable.
Run via `./patchwork.py`, or `./patchwork.py -v` for verbose mode.

## Types of Notifications
### Versioning

![Versioning Notification](https://user-images.githubusercontent.com/10542153/28694551-2936dc10-72df-11e7-9f27-8e223754fd03.png)

Distinguishes between minor releases, middling releases, and new major version numbers.

### Security

![Security Notification](https://user-images.githubusercontent.com/10542153/28694576-58d56a86-72df-11e7-86ea-bf0a1eb7d925.png)

Orange for security vulnerabilities published more than 2 weeks ago, red if published within the last two weeks (most recent sprint).
