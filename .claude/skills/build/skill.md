# Build Docker Image (Local Platform)

## Description
Build a single Docker image for the current platform. This is the fastest way to build and test images locally.

## Instructions

Follow these steps to build a Docker image:

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
  - local: "Tag as 'local' for quick local testing (Recommended)"
  - latest: "Tag as 'latest'"
  - custom: "Specify a custom tag"

If the user selects "custom", ask them to provide the custom tag name in the "Other" field.

### 2. Validate Prerequisites

Before building, check:

1. **Docker is running:**
   ```bash
   docker info
   ```
   If this fails, inform the user that Docker is not running and they need to start Docker Desktop or the Docker daemon.

2. **Image directory exists:**
   Verify that the directory for the selected image exists (e.g., `bookworm/`, `netshoot/`, etc.)

3. **Handle node dependency:**
   If the user selected "node", check if the bookworm:master-head image exists:
   ```bash
   docker images futuretea/bookworm:master-head --format "{{.Repository}}:{{.Tag}}"
   ```

   If it doesn't exist, warn the user:
   > "⚠️ The node image depends on futuretea/bookworm:master-head, which doesn't exist locally. You should build bookworm first with tag 'master-head', or this build will fail."

   Ask if they want to continue anyway or build bookworm first.

### 3. Build the Image

Execute the docker build command:

```bash
docker build -t futuretea/<image-name>:<tag> <image-name>/
```

Replace `<image-name>` with the selected image and `<tag>` with the selected/custom tag.

### 4. Report Results

After the build completes:

**On Success:**
```
✅ Successfully built futuretea/<image-name>:<tag>

You can now run the image with:
docker run -it futuretea/<image-name>:<tag>

To see the image details:
docker images futuretea/<image-name>:<tag>
```

**On Failure:**
```
❌ Build failed for futuretea/<image-name>:<tag>

Common issues:
- Check the Dockerfile syntax in <image-name>/Dockerfile
- Ensure base images are available
- Check Docker daemon logs for more details
```

## Example Usage

```
User: /build
Claude: [Prompts for image selection]
User: [Selects netshoot]
Claude: [Prompts for tag]
User: [Selects "local"]
Claude: [Runs docker build -t futuretea/netshoot:local netshoot/]
Claude: ✅ Successfully built futuretea/netshoot:local
```

## Notes

- This skill only builds for the current platform (typically linux/amd64 on most development machines)
- For multi-platform builds, use the `/build-multiplatform` skill instead
- The build output will show all Docker build steps and any errors
- Built images are stored locally and not pushed to any registry
