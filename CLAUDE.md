# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This repository contains a collection of base container images (BCI) for various development and operational purposes. Each subdirectory contains a Dockerfile for a specific purpose:

- **bookworm**: Base Debian Bookworm image with Docker client and common utilities
- **go-operator**: Go 1.25 development environment for Kubernetes operators with tools like controller-gen, ginkgo, helm, kind, and golangci-lint
- **netshoot**: Network troubleshooting container with comprehensive networking tools (tcpdump, wireshark, iperf, nmap, etc.)
- **node**: Node.js development environment (v20.18.3) built on top of bookworm base
- **server-base**: SUSE BCI base image with tini, curl, and NFS client

## Building Images

Images are built using Docker buildx for multi-platform support (linux/amd64, linux/arm64). The GitHub Actions workflow `.github/workflows/build-and-push.yml` handles automated builds.

### Manual Build

To build an image locally:

```bash
# Build for local platform
docker build -t futuretea/<image-name>:local <image-name>/

# Build multi-platform (requires buildx)
docker buildx build --platform linux/amd64,linux/arm64 -t futuretea/<image-name>:tag <image-name>/
```

### Automated Build via GitHub Actions

Trigger the workflow manually with:
- Docker Image Name (e.g., "netshoot", "go-operator")
- Docker Image Tag (e.g., "v1.0.0", "master-head")

Images are pushed to Docker Hub under the `futuretea` organization.

## Image Dependencies

- **node** depends on **bookworm** (uses `FROM futuretea/bookworm:master-head`)
- All other images are independent base images

When updating bookworm, consider rebuilding node image as well.

## Issue Tracking with Beads

This repository uses Beads for issue tracking. Issues are stored in `.beads/issues.jsonl` and synced via git.

### Common Beads Commands

```bash
# Create new issue
bd create "Issue description"

# List all issues
bd list

# View issue details
bd show <issue-id>

# Update issue status
bd update <issue-id> --status in_progress
bd update <issue-id> --status done

# Sync with remote
bd sync
```

## Version Management

- Go version is specified in go-operator/Dockerfile (currently 1.25)
- Node version is specified in node/Dockerfile (currently v20.18.3)
- Tool versions are pinned in Dockerfiles for reproducibility

When updating versions, update the FROM statement or ENV variables in the respective Dockerfile.
