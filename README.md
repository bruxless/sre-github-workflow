# Reusable Github workflow for Bruxless

## Workflow available

<!-- TOC -->
* [Reusable Github workflow for Bruxless](#reusable-github-workflow-for-bruxless)
  * [Workflow available](#workflow-available)
  * [Usage](#usage)
    * [Link PR to Asana Ticket](#link-pr-to-asana-ticket)
      * [Parameters](#parameters)
      * [Example](#example)
    * [Java Micro-Service CI](#java-micro-service-ci)
      * [Parameters](#parameters-1)
      * [Example](#example-1)
    * [Java Micro-Service Release](#java-micro-service-release)
      * [Parameters](#parameters-2)
      * [Example](#example-2)
<!-- TOC -->
## Usage

### Link PR to Asana Ticket

[Source](.github/workflows/link-pr-with-asana.yml)

#### Parameters

| Parameter Name | Type   | Required | Description                   |
|----------------|--------|----------|-------------------------------|
| `ASANA_SECRET` | secret | true     | The secret key given by Asana |

#### Example

```
name: Link PR with Asana

on:
  pull_request:
    types:
    - opened
    - reopened

jobs:
  asana_link_with_pr:
    # Asana link is disabled when we do not found asana link in PR's body
    if: contains(github.event.pull_request.body, 'https://app.asana.com')
    uses: bruxless/sre-github-workflow/.github/workflows/link-pr-with-asana.yml@main
    secrets:
      ASANA_SECRET: ${{ secrets.ASANA_SECRET }}
```

### Java Micro-Service CI

[Source](.github/workflows/java-microservice-ci-default.yml)

#### Parameters

| Parameter Name              | Type   | Required | Description                                                                                 |
|-----------------------------|--------|----------|---------------------------------------------------------------------------------------------|
| java_distribution           | String | false    | The java distribution (default to temurin)                                                  |
| java_version                | String | false    | The java version (default to 17)                                                            |
| helm_version                | String | false    | The helm version (default to 3.12.0)                                                        |
| slack_channel_id_ci         | String | true     | The CI Slack channel's id                                                                   |
| slack_channel_id_ci_release | String | true     | The CI release Slack channel's id                                                           |
| sonar_organization          | String | true     | The sonar organization                                                                      |
| sonar_host_url              | String | true     | The sonar host url                                                                          |
| sonar_project_key           | String | true     | The sonar project key                                                                       |
| project_name                | String | true     | The project name                                                                            |
| aws_region                  | String | true     | The AWS region (default to eu-west-1)                                                       |
| ecr_url                     | String | true     | The ECR url                                                                                 |
| chart_museum_url            | String | true     | The ChartMuseum URL                                                                         |
| enable_trivy_exit           | String | false    | Enable Trivy failure when CVE are found, you can deactivate this behavior (default to true) |
| enable_database_docker      | String | false    | Enable the database docker build (default to true)                                          |
| SLACK_BOT_TOKEN             | Secret | true     | The slack bot token                                                                         |
| SONAR_TOKEN                 | Secret | true     | The sonar token                                                                             |
| AWS_ACCESS_KEY_ID           | Secret | true     | The AWS access key                                                                          |
| AWS_SECRET_ACCESS_KEY       | Secret | true     | The AWS secret access key                                                                   |

#### Example

```yaml
name: Build
run-name: Build ${{ github.repository }} for branch ${{ github.ref_name }}

on:
  push:
    branches:
      - "**"

permissions:
  # In order to give release creation right.
  contents: write

jobs:
  build:
    uses: bruxless/github_workflow/.github/workflows/java-microservice-ci-default.yml@main
    with:
      slack_channel_id_ci: "slack_channel_id_ci"
      slack_channel_id_ci_release: "slack_channel_id_ci"
      sonar_organization: "sonar_organization"
      sonar_host_url: "sonar_host_url"
      sonar_project_key: "sonar_project_key"
      project_name: "project_name"
      ecr_url: "ecr_url"
      chart_museum_url: "chart_museum_url"
    secrets:
      SLACK_BOT_TOKEN: ${{ secrets.SLACK_BOT_TOKEN }}
      SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
      AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
      AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
```

### Java Micro-Service Release

[Source](.github/workflows/java-microservice-release-default.yml)

#### Parameters

| Parameter Name      | Type   | Required | Description                                                                               |
|---------------------|--------|----------|-------------------------------------------------------------------------------------------|
| java_distribution   | String | false    | The java distribution (default to temurin)                                                |
| java_version        | String | false    | The java version (default to 17)                                                          |
| source_branch       | String | true     | The source branch, that will contains the next release (default to develop)               |
| destination_branch  | String | true     | The destination branch, that will contain the created release (default to main)           |
| destination_version | String | false    | The version of the destination branch AFTER release (if not set SNAPSHOT will be removed) |
| source_version      | String | false    | The version of the source branch AFTER release (if not set : X.Y.Z+1-SNAPSHOT)            |
| dry_run             | String | false    | Do not really push (default to true)                                                      |
| github_app_id       | String | true     | GitHub app id                                                                             |
| BRUXLESS_GH_APP_KEY | Secret | true     | The Bruxless GitHub App key                                                               |

#### Example

```yaml
name: Release

on:
  workflow_dispatch:
    inputs:
      source_branch:
        description: 'The source branch, that will contains the next release'
        type: string
        required: true
        default: 'develop'
      destination_branch:
        description: 'The destination branch, that will contain the created release'
        type: string
        required: true
        default: 'main'
      destination_version:
        description: 'The version of the destination branch AFTER release (if not set SNAPSHOT will be removed)'
        type: string
      source_version:
        description: 'The version of the source branch AFTER release (if not set : X.Y.Z+1-SNAPSHOT)'
        type: string
      dry_run:
        description: 'Do not really push'
        type: boolean
        default: true

concurrency: release-${{ github.ref }}

jobs:
  build:
    uses: bruxless/github_workflow/.github/workflows/java-microservice-release-default.yml@main
    with:
      source_branch: ${{ inputs.source_branch }}
      source_version: ${{ inputs.source_version }}
      destination_branch: ${{ inputs.destination_branch }}
      destination_version: ${{ inputs.destination_version }}
      dry_run: ${{ inputs.dry_run }}
      github_app_id: "github_app_id"
    secrets:
      BRUXLESS_GH_APP_KEY: ${{ secrets.BRUXLESS_GH_APP_KEY }}
```


### Javascript web Micro-Service CI

[Source](.github/workflows/js-web-ci-default.yml)

#### Parameters

| Parameter Name              | Type   | Required | Description                                                                                 |
|-----------------------------|--------|----------|---------------------------------------------------------------------------------------------|
| node_version                | String | false    | The Node version (default to 16)                                                            |
| helm_version                | String | false    | The helm version (default to 3.12.0)                                                        |
| slack_channel_id_ci         | String | true     | The CI Slack channel's id                                                                   |
| slack_channel_id_ci_release | String | true     | The CI release Slack channel's id                                                           |
| sonar_organization          | String | true     | The sonar organization                                                                      |
| sonar_host_url              | String | true     | The sonar host url                                                                          |
| sonar_project_key           | String | true     | The sonar project key                                                                       |
| project_name                | String | true     | The project name                                                                            |
| aws_region                  | String | true     | The AWS region (default to eu-west-1)                                                       |
| ecr_url                     | String | true     | The ECR url                                                                                 |
| chart_museum_url            | String | true     | The ChartMuseum URL                                                                         |
| enable_trivy_exit           | String | false    | Enable Trivy failure when CVE are found, you can deactivate this behavior (default to true) |
| SLACK_BOT_TOKEN             | Secret | true     | The slack bot token                                                                         |
| SONAR_TOKEN                 | Secret | true     | The sonar token                                                                             |
| AWS_ACCESS_KEY_ID           | Secret | true     | The AWS access key                                                                          |
| AWS_SECRET_ACCESS_KEY       | Secret | true     | The AWS secret access key                                                                   |

#### Example

```yaml
name: Build
run-name: Build ${{ github.repository }} for branch ${{ github.ref_name }}

on:
  push:
    branches:
      - "**"

permissions:
  # In order to give release creation right.
  contents: write

jobs:
  build:
    uses: bruxless/github_workflow/.github/workflows/js-web-ci-default.yml@main
    with:
      slack_channel_id_ci: "slack_channel_id_ci"
      slack_channel_id_ci_release: "slack_channel_id_ci"
      sonar_organization: "sonar_organization"
      sonar_host_url: "sonar_host_url"
      sonar_project_key: "sonar_project_key"
      project_name: "project_name"
      ecr_url: "ecr_url"
      chart_museum_url: "chart_museum_url"
    secrets:
      SLACK_BOT_TOKEN: ${{ secrets.SLACK_BOT_TOKEN }}
      SONAR_TOKEN: ${{ secrets.SONAR_TOKEN }}
      AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
      AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
```
