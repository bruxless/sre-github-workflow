# Reusable Github workflow for Bruxless

## Workflow available

- [Java docker build](#java-docker-build)
- [Link PR to Asana Ticket](#link-pr-to-asana-ticket)
- [Push docker image on AWS registry](#push-docker-image-on-aws-registry)

## Usage

### Java docker build

[Source](.github/workflows/java_docker_build.yml)

#### Parameters

| Parameter Name        | Type   | Required                   | Description                                      |
|-----------------------|--------|----------------------------|--------------------------------------------------|
| `docker_context_path` | string | true                       | The path of the docker context.                  |
| `dockerfile_path`     | string | true                       | The path to Dockerfile (e.g foo/bar/Dockerfile). |
| `java_version`        | string | false : default value `17` | The java version used to build java project.     |

#### Example

```
name: Build
#Might be updated to :
# on:
#  pull_request:
#    types:
#       - opened
#       - synchronize
#       - reopened
#  on:
#    push:
#      branches:
#        - "develop"
#        - "main"
#        - "main/**"
#        - "develop/**"

on:
  push:
    branches:
      - "**"

jobs:
  build_test:
    uses: bruxless/sre-github-workflow/.github/workflows/java_docker_build.yml@main
    with:
      docker_directory: 'foo-service/target'
      dockerfile_path: 'foo-service/target'
      # java_version: '17'
```

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

### Push docker image on AWS registry

[Source](.github/workflows/push_docker_image.yml)

#### Parameters

| Parameter Name          | Type    | Required                      | Description                                                                                            |
|-------------------------|---------|-------------------------------|--------------------------------------------------------------------------------------------------------|
| `docker_context_path`   | string  | true                          | The path of the docker context.                                                                        |
| `dockerfile_path`       | string  | true                          | The path to Dockerfile (e.g foo/bar/Dockerfile).                                                       |
| `java_version`          | string  | false : default value `17`    | The java version used to build java project.                                                           |
| `java_project`          | boolean | false : default value `false` | If true, this job package mvn project, in order to build docker image. Otherwise java build is skipped |
| `ecr_repository`        | string  | true                          | The registry arn (eg `00000000.dkr.ecr.eu-west-3.amazonaws.com/registry_name`)                         |
| `AWS_ACCESS_KEY_ID`     | secret  | true                          | The AWS access key id                                                                                  |
| `AWS_SECRET_ACCESS_KEY` | secret  | true                          | The AWS secret access id                                                                               |

#### Example

```
name: Push docker image
on:
  workflow_run:
    workflows: ["Build"]
    branches:
      - "main"
      - "main/**"
    types:
      - completed
jobs:
  deploy:
    if: ${{ github.event.workflow_run.conclusion == 'success' }}
    uses: bruxless/sre-github-workflow/.github/workflows/push_docker_image.yml@main
    with:
      docker_directory: 'foo-service/target'
      dockerfile_path: 'foo-service/target'
      ecr_repository: '0000000.dkr.ecr.eu-west-1.amazonaws.com/foo-service'
      java_project: true
    secrets:
      AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
      AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}

```

### Create releases 

[Source](.github/workflows/maven_release.yml)

#### Parameters

| Parameter Name          | Type    | Required                       | Description                                                                |
|-------------------------|---------|--------------------------------|----------------------------------------------------------------------------|
| `source_branch`         | string  | false: default value `develop` | The source branch, ie. the branch that contains next code.                 |
| `destination_branch`    | string  | false: default value `main`    | The destination branch, ie. the branch that will contains new code.        |
| `source_version`        | string  | false : default value ``       | Used to force the version of the source branch AFTER release process.      |
| `destination_version`   | boolean | false : default value ``       | Used to force the version of the destination branch AFTER release process. |
| `java_version`          | string  | false : default value `17`     | The java version used to build java project.                               |

#### Example

```

```