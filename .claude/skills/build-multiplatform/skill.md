# Build Multi-Platform Docker Image

## Description
Build a Docker image for multiple platforms (linux/amd64 and linux/arm64) using Docker buildx. Optionally push the image to Docker Hub.

## Instructions

Follow these steps to build a multi-platform Docker image:

### 1. Gather Required Information

Use the AskUserQuestion tool to prompt the user for:

**Question 1: Which image to build?**
- Header: "Image"
- Options:
  - bookworm: "Base Debian Bookworm image with Docker client and common utilities"
  - go-operator: "Go 1.25 development environment for Kubernetes operators with tools like controller-gen, ginkgo, helm, kind, and golangci-lint"
  - netshoot: "Network troubleshooting container with comprehensive networking tools (tcpdump, wireshark, iperf, nmap, etc.)"
  - node: "Node.js v20.18.3 development environment built on top of bookworm base"
  - server-base: "SUSE BCI base image with tini, curl, and NFS client"

**Question 2: What tag to use?**
- Header: "Tag"
- Options:
  - latest: "Tag as 'latest' (Recommended)"
  - master-head: "Tag as 'master-head' (used for dependencies)"
  - custom: "Specify a custom tag"

If the user selects "custom", ask them to provide the custom tag name in the "Other" field.

**Question 3: Push to Docker Hub?**
- Header: "Push"
- Options:
  - no: "Build only (don't push to registry) (Recommended)"
  - yes: "Build and push to Docker Hub (requires authentication)"

### 2. Validate Prerequisites

Before building, check:

1. **Docker is running:**
   ```bash
   docker info
   ```
   If this fails, inform the user that Docker is not running.

2. **Docker buildx is available:**
   ```bash
   docker buildx version
   ```
   If this fails, inform the user:
   > "❌ Docker buildx is not available. Please ensure you have Docker Desktop installed or set up buildx manually."

3. **Image directory exists:**
   Verify that the directory for the selected image exists.

4. **Handle node dependency:**
   If the user selected "node", warn them:
   > "⚠️ The node image depends on futuretea/bookworm:master-head. Make sure bookworm has been built and pushed with the 'master-head' tag first, or this build will fail."

5. **Check Docker Hub authentication (if pushing):**
   If the user selected "yes" to push, check if they're logged in:
   ```bash
   docker info | grep Username
   ```
   If not logged in, inform them:
   > "⚠️ You don't appear to be logged in to Docker Hub. Run 'docker login' first, or the push will fail."

   Ask if they want to continue anyway.

### 3. Build the Image

Execute the appropriate docker buildx command:

**Without push:**
```bash
docker buildx build --platform linux/amd64,linux/arm64 -t futuretea/<image-name>:<tag> <image-name>/
```

**With push:**
```bash
docker buildx build --platform linux/amd64,linux/arm64 -t futuretea/<image-name>:<tag> --push <image-name>/
```

Replace `<image-name>` with the selected image and `<tag>` with the selected/custom tag.

### 4. Report Results

After the build completes:

**On Success (without push):**
```
✅ Successfully built multi-platform image futuretea/<image-name>:<tag>

Platforms: linux/amd64, linux/arm64

Note: The image was built but not pushed to Docker Hub.
To push it later, run:
docker buildx build --platform linux/amd64,linux/arm64 -t futuretea/<image-name>:<tag> --push <image-name>/
```

**On Success (with push):**
```
✅ Successfully built and pushed futuretea/<image-name>:<tag>

Platforms: linux/amd64, linux/arm64

The image is now available on Docker Hub:
https://hub.docker.com/r/futuretea/<image-name>
```

**On Failure:**
```
❌ Multi-platform build failed for futuretea/<image-name>:<tag>

Common issues:
- Check the Dockerfile syntax in <image-name>/Dockerfile
- Ensure base images are available for both platforms
- If pushing, verify Docker Hub authentication
- Check that buildx is properly configured
```

## Example Usage

```
User: /build-multiplatform
Claude: [Prompts for image selection]
User: [Selects bookworm]
Claude: [Prompts for tag]
User: [Selects "master-head"]
Claude: [Prompts for push option]
User: [Selects "yes"]
Claude: [Checks prerequisites, runs docker buildx build with --push]
Claude: ✅ Successfully built and pushed futuretea/bookworm:master-head
```

## Notes

- Multi-platform builds take longer than single-platform builds
- When building without push, the image is stored in the buildx cache but not loaded into local Docker images
- The node image requires bookworm:master-head to be available (either locally or on Docker Hub)
- Pushing requires authentication to Docker Hub with the futuretea organization
- Both linux/amd64 and linux/arm64 platforms are built simultaneously
