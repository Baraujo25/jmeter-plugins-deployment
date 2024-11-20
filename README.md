# Publish JMeter Plugin Action

This GitHub Action automates the process of publishing a plugin to the
[JMeter Plugins Manager repository](https://github.com/undera/jmeter-plugins). It generates the
required metadata from the latest GitHub release of your plugin and creates a pull request to the
target repository.

## Features

- Automatically fetches the latest GitHub release of your plugin.
- Generates a release object and updates the appropriate JSON file inside the repository.
- Updates the target repository with the new release details.
- Handles forking, branching, and creating a pull request to the upstream repository.

## Inputs

The action requires the following inputs:

| Input                  | Description                                                                                              | Required | Default                                        |
| ---------------------- | -------------------------------------------------------------------------------------------------------- | -------- | ---------------------------------------------- |
| `upstream-repository`  | URL of the target JMeter Plugins repository to send the PR to.                                           | No       | `https://github.com/undera/jmeter-plugins.git` |
| `forked-repository`    | URL of your forked repository used to send the PR from.                                                  | Yes      | -                                              |
| `plugin-artifact-name` | Prefix of the plugin artifact name used to identify the JAR file in the GitHub release.                  | Yes      | -                                              |
| `plugin-id`            | Registered ID for identifying the plugin in the JMeter Plugins repository.                               | Yes      | -                                              |
| `changes`              | Release note line describing the update.                                                                 | Yes      | -                                              |
| `ignore-dependencies`  | Comma-separated prefixes of dependencies to ignore in the release metadata.                              | No       | -                                              |
| `token`                | GitHub token used for authentication to perform API operations such as fetching assets and creating PRs. | Yes      | -                                              |

## Usage

Here’s how to set up and use this action in your workflow:

```yaml
name: Publish to JMeter Plugins

on:
  workflow_dispatch:
    inputs:
      changes:
        description: 'Release notes for the update'
        required: true

jobs:
  publish:
    runs-on: ubuntu-latest
    steps:
      - name: Run Publish JMeter Plugin Action
        uses: abstracta/publish-jmeter-plugin@v1
        with:
          forked-repository: https://github.com/Abstracta/jmeter-plugins.git
          plugin-artifact-name: prefix-of-example-plugin-name
          plugin-id: example-plugin-id
          changes: ${{ inputs.changes }}
          token: ${{ secrets.GITHUB_TOKEN }}
```

## How It Works

1. _Input Validation_: The action ensures all required inputs are provided.
1. _Repository Setup_: It checks out the forked repository and configures upstream and origin
   remotes.
1. _Metadata Generation_: Extracts version and library details from the latest GitHub release of the
   plugin.
1. _Update_: Locates the correct section using plugin-id and appends the new release details.
1. _Branching and PR_: Creates a new release branch, commits the changes, and pushes the branch to
   the forked repository. A pull request is then created to the upstream repository using the
   `token`.

## Notes

- Ensure your plugin JAR and all required dependencies are correctly published in the GitHub release
  of your repository.
- Pull request is created in a draft mode working as a stagging area for manually validation
- Use a
  [GitHub PAT token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#creating-a-personal-access-token-classic)
  with sufficient permissions for repository and PR actions.
- Customize ignore-dependencies if some libraries should not be included in the release metadata.
- Field `"depends"` of the release JSON object is grabbed from the latest release.
  - Desire of updates requires a manual modification over the PR
