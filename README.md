# Reusable GitHub workflow for Bruxless

## Workflow available

<!-- TOC -->
* [Reusable GitHub workflow for Bruxless](#reusable-github-workflow-for-bruxless)
  * [Workflow available](#workflow-available)
  * [Usage](#usage)
    * [Java Micro-Service CI](#java-micro-service-ci)
      * [Parameters](#parameters)
      * [Example](#example)
    * [Java Micro-Service Release](#java-micro-service-release)
      * [Parameters](#parameters-1)
      * [Example](#example-1)
    * [Javascript web Micro-Service CI](#javascript-web-micro-service-ci)
      * [Parameters](#parameters-2)
      * [Example](#example-2)
    * [Javascript Test CI](#javascript-test-ci)
      * [Parameters](#parameters-3)
      * [About ENV_FILE](#about-env_file)
      * [Example](#example-3)
<!-- TOC -->

## Usage

### Java Micro-Service CI

[Source](.github/workflows/java-microservice-ci-default.yml)

#### Parameters

| Parameter Name              | Type   | Required | Description                                                                                |
|-----------------------------|--------|----------|--------------------------------------------------------------------------------------------|
| java_distribution           | String | false    | The java distribution (default to temurin)                                                 |
| java_version                | String | false    | The java version (default to 17)                                                           |
| slack_channel_id_ci         | String | true     | The CI Slack channel's id                                                                  |
| slack_channel_id_ci_release | String | true     | The CI release Slack channel's id                                                          |
| sonar_organization          | String | true     | The sonar organization                                                                     |
| sonar_host_url              | String | true     | The sonar host url                                                                         |
| sonar_project_key           | String | true     | The sonar project key                                                                      |
| project_name                | String | true     | The project name                                                                           |
| aws_region                  | String | true     | The AWS region (default to eu-west-1)                                                      |
| ecr_url                     | String | true     | The ECR url                                                                                |
| chart_museum_url            | String | false    | The ChartMuseum URL (DEPRECATED)                                                           |
| trivy_lib_vuln_severity     | String | false    | Trivy vulnerabilities levels for libraries (default to 'UNKNOWN,LOW,MEDIUM,HIGH,CRITICAL') |
| trivy_docker_vuln_severity  | String | false    | Trivy vulnerabilities levels for dockerfiles (default to 'CRITICAL,HIGH')                  |
| enable_database_docker      | String | false    | Enable the database docker build (default to true)                                         |
| SLACK_BOT_TOKEN             | Secret | true     | The slack bot token                                                                        |
| SONAR_TOKEN                 | Secret | true     | The sonar token                                                                            |
| AWS_ACCESS_KEY_ID           | Secret | true     | The AWS access key                                                                         |
| AWS_SECRET_ACCESS_KEY       | Secret | true     | The AWS secret access key                                                                  |

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

| Parameter Name              | Type   | Required | Description                                                                                |
|-----------------------------|--------|----------|--------------------------------------------------------------------------------------------|
| node_version                | String | false    | The Node version (default to 16)                                                           |
| slack_channel_id_ci         | String | true     | The CI Slack channel's id                                                                  |
| slack_channel_id_ci_release | String | true     | The CI release Slack channel's id                                                          |
| sonar_organization          | String | true     | The sonar organization                                                                     |
| sonar_host_url              | String | true     | The sonar host url                                                                         |
| sonar_project_key           | String | true     | The sonar project key                                                                      |
| project_name                | String | true     | The project name                                                                           |
| aws_region                  | String | true     | The AWS region (default to eu-west-1)                                                      |
| ecr_url                     | String | true     | The ECR url                                                                                |
| chart_museum_url            | String | false    | The ChartMuseum URL (DEPRECATED)                                                           |
| trivy_lib_vuln_severity     | String | false    | Trivy vulnerabilities levels for libraries (default to 'UNKNOWN,LOW,MEDIUM,HIGH,CRITICAL') |
| trivy_docker_vuln_severity  | String | false    | Trivy vulnerabilities levels for dockerfiles (default to 'CRITICAL,HIGH')                  | 
| SLACK_BOT_TOKEN             | Secret | true     | The slack bot token                                                                        |
| SONAR_TOKEN                 | Secret | true     | The sonar token                                                                            |
| AWS_ACCESS_KEY_ID           | Secret | true     | The AWS access key                                                                         |
| AWS_SECRET_ACCESS_KEY       | Secret | true     | The AWS secret access key                                                                  |

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

### Javascript Test CI

[Source](.github/workflows/js-qa-ci-default.yml)

#### Parameters

| Parameter Name      | Type   | Required | Description                                                          |
|---------------------|--------|----------|----------------------------------------------------------------------|
| slack_channel_id_qa | String | true     | The CI Slack channel's id                                            |
| runner              | String | true     | Runner to use to run tests (usually 'bruxless-runner')               |
| container           | String | true     | Container to use to run tests                                        |
| environment         | String | true     | Tested environment (qa, staging or prod)                             |
| SLACK_BOT_TOKEN     | Secret | true     | The slack bot token                                                  |
| ENV_FILE            | Secret | true     | The env file, containing variables and secrets for the environments  |

#### About ENV_FILE
It must be a secret, named '<env>_env_file' (for exemple 'qa_env_file').
It must contain all variable for the given env, even if they are not secrets.
Content exemple: 
```
ENV=qa
AUTH_URL=https://auth.dev.bruxless.rocks/
API_URL=https://api.dev.bruxless.rocks/
WEBCONSOLE_URL=https://webconsole.dev.bruxless.rocks/
USERNAME_PATIENT=patient
PASSWORD_PATIENT=patient
USERNAME_ADMINFULL=admin_full
PASSWORD_ADMINFULL=admin_full
CLIENT_ID=api_test
CLIENT_SECRET=toto
```

#### Example

```yaml
name: API acceptance tests

on:
  workflow_dispatch:
    inputs:
      environment:
        description: "Tested environment"
        required: true
        default: 'qa'
        type: choice
        options:
          - qa
          - staging
          - prod

jobs:
  test:
    uses: bruxless/sre-github-workflow/.github/workflows/js-qa-ci-default.yml@main
    with:
      slack_channel_id_qa: "C076K1E0F7F"
      runner: bruxless-runners
      container: mcr.microsoft.com/playwright:v1.44.1-jammy
      environment: ${{ inputs.environment }}
    secrets:
      SLACK_BOT_TOKEN: ${{ secrets.SLACK_BOT_TOKEN }}
      ENV_FILE: ${{ secrets[format('{0}_env_file', github.event.inputs.environment)] }}
```
