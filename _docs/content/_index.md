---
title: drone-docker-buildx
---

[![GitHub contributors](https://img.shields.io/github/contributors/dhpollack/drone-docker-buildx)](https://github.com/dhpollack/drone-docker-buildx/graphs/contributors)
[![Source: GitHub](https://img.shields.io/badge/source-github-blue.svg?logo=github&logoColor=white)](https://github.com/dhpollack/drone-docker-buildx)
[![License: MIT](https://img.shields.io/github/license/dhpollack/drone-docker-buildx)](https://github.com/dhpollack/drone-docker-buildx/blob/main/LICENSE)

Drone plugin to build and publish multiarch Docker images with buildx.

<!-- prettier-ignore-start -->
<!-- spellchecker-disable -->
{{< toc >}}
<!-- spellchecker-enable -->
<!-- prettier-ignore-end -->

## Versioning

The tags follow the major version of Docker, e.g. `20`, and the minor and patch parts reflect the `version` of the plugin. A full example would be `20.12.5`. Minor versions can introduce breaking changes, while patch versions can be considered non-breaking.

## Usage

{{< hint type=important >}}
Be aware that the this plugin requires [privileged](https://docs.drone.io/pipeline/docker/syntax/steps/#privileged-mode) capabilities, otherwise the integrated Docker daemon is not able to start.
{{< /hint >}}

```yaml
kind: pipeline
name: default

steps:
  - name: docker
    image: dhpollack/drone-docker-buildx:23
    privileged: true
    settings:
      username: octocat
      password: secure
      repo: octocat/example
      tags: latest
```

### Parameters

<!-- prettier-ignore-start -->
<!-- spellchecker-disable -->
{{< propertylist name=drone-docker-buildx.data sort=name >}}
<!-- spellchecker-enable -->
<!-- prettier-ignore-end -->

### Examples

#### Push to other registries than DockerHub

If the created image is to be pushed to registries other than the default DockerHub, it is necessary to set `registry` and `repo` as fully-qualified name.

**GHCR:**

```yaml
kind: pipeline
name: default

steps:
  - name: docker
    image: dhpollack/drone-docker-buildx:23
    privileged: true
    settings:
      registry: ghcr.io
      username: octocat
      password: secret-access-token
      repo: ghcr.io/octocat/example
      tags: latest
```

**AWS ECR:**

```yaml
kind: pipeline
name: default

steps:
  - name: docker
    image: dhpollack/drone-docker-buildx:23
    privileged: true
    environment:
      AWS_ACCESS_KEY_ID:
        from_secret: aws_access_key_id
      AWS_SECRET_ACCESS_KEY:
        from_secret: aws_secret_access_key
    settings:
      registry: <account_id>.dkr.ecr.<region>.amazonaws.com
      repo: <account_id>.dkr.ecr.<region>.amazonaws.com/octocat/example
      tags: latest
```

## Build

Build the binary with the following command:

```shell
export GOOS=linux
export GOARCH=amd64
export CGO_ENABLED=0
export GO111MODULE=on

make build
```

Build the Docker image with the following command:

```shell
docker build --file docker/Dockerfile.amd64 --tag dhpollack/drone-docker-buildx .
```

## Test

```shell
docker run --rm \
  -e PLUGIN_TAG=latest \
  -e PLUGIN_REPO=octocat/hello-world \
  -e DRONE_COMMIT_SHA=00000000 \
  -v $(pwd):$(pwd) \
  -w $(pwd) \
  --privileged \
  dhpollack/drone-docker-buildx --dry-run
```
