---

# CI

Reusable **GitHub Actions** and **CI building blocks** shared across multiple repositories.

This repository centralizes common CI logic (Docker builds, pushes, metadata, caching, etc.) so individual projects stay clean, minimal, and consistent.

---

## 🎯 Purpose

The goal of this repository is to:

* Avoid duplicating GitHub Actions workflows across repos
* Enforce consistent Docker build & push behavior
* Centralize updates and improvements
* Provide short, readable `uses:` syntax for consumers

Typical usage:

```yaml
uses: antonisin/CI/docker-build-push@v1
```

---

## 📦 What’s inside

```
CI/
├── docker-build-push/
│   ├── action.yml
│   └── README.md
└── README.md   ← this file
```

### Available actions

| Action              | Description                                                   |
|---------------------|---------------------------------------------------------------|
| `docker-build-push` | Build & push multi-arch Docker images with cache and metadata |

More actions may be added over time (tests, releases, base images, etc.).

---

## 🚀 Usage

### Docker Build & Push

Build and push a Docker image to your registry.

#### Example workflow

```yaml
name: Build Service

on:
  push:
    branches: [ main ]
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: antonisin/CI/docker-build-push@v1
        with:
          image_name: redirector_auth_service
          docker_registry: ${{ secrets.DOCKER_REGISTRY }}
          docker_username: ${{ secrets.DOCKER_USERNAME }}
          docker_token: ${{ secrets.DOCKER_TOKEN }}
```

---

## 🔧 Inputs

### `docker-build-push`

| Input             | Required | Default                   | Description                          |
|-------------------|----------|---------------------------|--------------------------------------|
| `image_name`      | ✅        | —                         | Docker image name (without registry) |
| `docker_registry` | ✅        | —                         | Registry URL                         |
| `docker_username` | ✅        | —                         | Registry username                    |
| `docker_token`    | ✅        | —                         | Registry password / token            |
| `dockerfile`      | ❌        | `Dockerfile`              | Path to Dockerfile                   |
| `context`         | ❌        | `.`                       | Build context                        |
| `target`          | ❌        | `prod`                    | Docker build target                  |
| `platforms`       | ❌        | `linux/amd64,linux/arm64` | Target platforms                     |
| `push_latest`     | ❌        | `true`                    | Push `latest` tag                    |

---

## 🏷 Tags produced

The action automatically generates:

* `latest` (optional)
* `sha-<commit>`
* `<branch-name>`

Example:

```
latest
sha-a1b2c3d
main
feature-login
```

---

## 🧠 Caching

Uses **registry-based BuildKit cache**:

* `:buildcache` tag
* Shared across runs
* Speeds up rebuilds significantly

No extra setup required.

---

## 🔐 Secrets

Secrets **are not inherited automatically** and must be passed explicitly:

```yaml
docker_registry: ${{ secrets.DOCKER_REGISTRY }}
docker_username: ${{ secrets.DOCKER_USERNAME }}
docker_token: ${{ secrets.DOCKER_TOKEN }}
```

Works with **private repos** under the same GitHub user or organization.

---

## 🏷 Versioning (important)

Always pin to a version tag:

```yaml
uses: antonisin/CI/docker-build-push@v1
```

* `v1` → stable
* `v2` → breaking changes
* `v1.1` → backward-compatible improvements

Avoid using `@main` in production.

---

## 🧩 Design philosophy

* Composite actions for **short, clean syntax**
* No hidden magic
* Caller controls:

    * `runs-on`
    * `concurrency`
    * job structure
* CI logic lives in one place

---

## 🔮 Planned additions

Possible future actions:

* Base image builder
* PR-only dry builds
* Node / PNPM test runners
* Release tagging
* Cosign image signing
* SBOM generation

---

## 🛠 Maintenance

This repo is intended to evolve gradually.
Changes should be versioned and backward compatibility respected whenever possible.

---
