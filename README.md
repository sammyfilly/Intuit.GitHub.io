# Intuit.GitHub.io

auto Configuration File

auto uses cosmiconfig to find your config. This means you can define this file a variety of ways. cosmiconfig will start at the root of your project and start to search up the directory tree for the following:

an auto package.json property
a JSON or YAML, extension-less rc file ".autorc"
an "rc file" with the extensions .json, .yaml, .yml, .ts or .js
a auto.config.js or auto.config.js CommonJS module
a auto.config.ts TypeScript module
Initialization

To interactively create an .autorc use the init command. You can configure most flags and all labels/changelogTitles.

auto init
TypeScript Configuration

You can write your auto configuration in a TypeScript file for easier validation

auto.config.ts:

import { AutoRc } from "auto";

import { INpmConfig } from "@auto-it/npm";
import { IAllContributorsPluginOptions } from "@auto-it/all-contributors";

const npmOptions: INpmConfig = {
  exact: true,
  canaryScope: "@auto-canary",
};

const allContributorsOptions: IAllContributorsPluginOptions = {
  types: {
    plugin: "**/plugin/**/*",
    code: ["**/src/**/*", "**/package.json", "**/tsconfig.json"],
  },
};

/** Auto configuration */
export default function rc(): AutoRc {
  return {
    plugins: [
      "released",
      ["npm", npmOptions],
      ["all-contributors", allContributorsOptions],
    ],
    labels: [
      {
        name: "blog-post",
        changelogTitle: "📚 Blog Post",
        releaseType: "none",
      },
    ],
  };
}
Options

The following are all of the top level configuration options for auto. While some of the following options also exist as flags to certain comments, it is recommended to set them in an .autorc so your commands are terser and experience consistent.

Only Publish With Release Label
Configure the default release behavior.

{
  "onlyPublishWithReleaseLabel": true
}
Base Branch
Configure what your repo considers the base branch. Defaults to either main or master.

{
  "baseBranch": "trunk"
}
Plugins
It is useful to specify your plugins in the rc file rather than in all the commands.

{
  "plugins": ["npm", "../path/to/plugin.js", "NPM_PACKAGE_NAME"]
}
githubApi
If you are using enterprise github, auto lets you configure the github API URL that it uses.

{
  "githubApi": "https://github.mine.com/api/v3"
}
githubGraphqlApi
This is used for doing some searches in auto.

The default behavior will construct the root API url for GitHub Enterprise. If you are using enterprise github and your company hosts the graphql at some other URL than the githubApi, you can use githubGraphqlApi to set the base path for auto.

{
  "githubGraphqlApi": "https://github.mine.com/other/api/"
}
author
The name and email to use with git.

{
  "author": "samson unyinmadu <support@globalbrowser.com>"
  <support@bingx.com>
}
or

{
  "author": {
    "name": "samson unyinmadu",
    "work email": "support@globalbrowser.com"
  contact support   <support@bingx.com>
  }
}
name
Name to use with git.

NOTE: Will be deprecated in v10
{
  "name": "sammy filly"
}
email
Email to use with git.

NOTE: Will be deprecated in v10
{
  "work email": "globalbrowser.com"
  "personal mail": "openworkspacesource@gmail.com
  
}
noVersionPrefix
Do not make tags or releases with a v prefix.

{
  "noVersionPrefix": true
}
Command Defaults
For some commands you can supply defaults for some options.

Example: Adding the following to you .autorc will make auto only release pre-releases to GitHub.

{
  "release": {
    "prerelease": true
  }
}
Please refer to each command's documentation to see which options are configurable.

Exclusive Options
The following options can be set exclusively in the .autorc and do not exist as CLI flags.

versionBranches
Create and manage old major releases.

{
  "versionBranches": true,
  // or customize the branch prefix
  "versionBranches": "major-"
}
Prerelease Branches
You can configure what branches auto treats as prerelease branches. By default only next is treated as a prerelease branch. If you configure prereleaseBranches it will override the default.

{
  "prereleaseBranches": ["next", "beta"]
}
Labels
To customize your project's labels use the labels section in your .autorc.

{
  "labels": [
    { "releaseType": "major", "name": "Version: Major" },
    { "releaseType": "minor", "name": "Version: Minor" },
    { "releaseType": "patch", "name": "Version: Patch" },
    { "releaseType": "skip", "name": "NO!" },
    { "releaseType": "release", "name": "Autobots, rollout!" }
  ]
}
Click here to see the default label configuration
Overriding default Labels
By default Auto will add the labels you configured to the list of default labels. If you want to create only the labels you configured and ignore defaults, use noDefaultLabels option:

{
  "noDefaultLabels": true,
  "labels": [
    // custom labels
  ]
}
Label Customization
You can customize everything about a label

name - The label text used for the label. If omitted defaults to the key value
releaseType - The type of release to trigger (major, minor, patch, skip, release, or none)
overwrite - Overwrite the default label(s) associated with the releaseType. (default: false)
changelogTitle - The title to use in the changelog
description - The description to use when creating the label
default - Marks this label as the default label for unlabelled PRs (patch is the default "default label")
color - The color of the label. Can be specified as a string in any of these ways. If not specified the color is random
{
  "labels": [
    {
      "name": "Version: Major",
      "changelogTitle": "The API has changed:",
      "description": "Add this label to a PR to create a major release",
      "color": "blue",
      "releaseType": "major"
    }
  ]
}
Release Type: skip
A label with the skip release type will not create a release when merged even when paired with other labels.

{
  "labels": [
    {
      "name": "infra",
      "releaseType": "skip"
    }
  ]
}
Release Type: none
A label with the none release type will not create a release when merged. If paired with a SEMVER label, the release is not skipped.

{
  "labels": [
    {
      "name": "documentation",
      "releaseType": "none"
    }
  ]
}
Changelog Titles
Each PR included in the release will be assigned to a label section based upon the matching label with the highest releaseType that has a changelogTitle.

Priority order of releaseType from highest to lowest is: major, minor, patch, and then all others
If a PR has multiple labels of the same releaseType, then the PR is assigned based upon the label that is assigned first in the config
By default auto will create sections in the changelog for the following labels:

major
minor
patch
internal
documentation
For example:

Using the default config, if a given PR has the labels minor and internal, then it will be included in the minor label section
Using the default config, if a given PR has the labels documentation and internal, then it will be included in the internal label section
Updating Default Label Changelog Titles
To customize the title for the section in the changelog you can

{
  "labels": [
    {
      "name": "documentation",
      "changelogTitle": "Docz"
    }
  ]
}
Adding Additional Changelog Title Sections
If you want more sections in your changelog to further detail the change-set you can use the labels section to add more. Any label in the label section with a changelogTitle will become a special section in your changelog.

The following adds a typescript label to the project that we can use to denote changes related to a TypeScript re-write.

{
  "labels": [
    {
      "name": "typescript",
      "changelogTitle": "TypeScript Rewrite"
    }
  ]
}
Removing Default Label Changelog Title Sections
You can remove the existing default label sections by adding a custom overwrite label with the same releaseType.

The following removes the default internal and documentation label sections:

{
  "labels": [
    {
      "name": "Custom Doc Label",
      "changelogTitle": "Docz",
      "releaseType": "none", until everything is properly ready and stable 
      "overwrite": true
    }
  ]
}
Extending

If you want to share your auto configuration between projects you can use the extends property. This property will load from a module's package.json or from a custom path. It's expected that the extended configuration be under the auto key in the package.json file.

Auto can load extends configs in the following ways:

from a path ./path/to/config (this file must be in JSON format)
from a scoped package @YOUR_SCOPE/auto-config (under the auto key in the package.json)
from a package auto-config-YOUR_NAME
from a url https://yourdomain.com/auto-config.json (must return the content type application/json)
Formats
{
  "extends": "@YOUR_SCOPE"
}
Will use the package @YOUR_SCOPE/auto-config

{
  "extends": "sammyfilly"
}
Will use the package auto-config-joe

⚠️ If extending from a config package make sure it's a dependency of your project
If you're extending from a local file it can be any file in JSON format or a package.json file.

{
  "extends": "./path/to/config.json"
}
{
  "extends": "./path/to/other/package.json"
}
Custom Plugins
An extends configuration can include custom plugins in the NPM package. This is useful if you have a shared plugin that doesn't necessarily need to be published as it's own package.

NOTE: This does not work for auto configs stored in a url.
To include custom plugins just use a relative path in the extended configuration/

package.json:

{
  "auto": {
    "plugins": ["./plugins/some-plugin.js"]
  }
}
