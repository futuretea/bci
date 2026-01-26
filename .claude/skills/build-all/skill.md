# Build All Docker Images

## Description
Build all Docker images in the repository in the correct dependency order. This is useful when you've made changes to base images and need to rebuild everything.

## Instructions

Follow these steps to build all Docker images:

### 1. Gather Required Information

Use the AskUserQuestion tool to prompt the user for:

**Question 1: What tag to use?**
- Header: "Tag"
- Options:
  - local: "Tag all images as 'local' for quick testing (Recommended)"
  - latest: "Tag all images as 'latest'"
  - custom: "Specify a custom tag"

If the user selects "custom", ask them to provide the custom tag name in the "Other" field.

**Question 2: Multi-platform build?**
- Header: "Platform"
- Options:
  - no: "Single-platform build (current platform only) (Recommended)"
  - yes: "Multi-platform build (linux/amd64 and linux/arm64)"

### 2. Validate Prerequisites

Before building, check:

1. **Docker is running:**
   ```bash
   docker info
   ```
   If this fails, inform the user that Docker is not running.

2. **If multi-platform, check buildx:**
   ```bash
   docker buildx version
   ```
   If this fails and multi-platform was selected, inform the user that buildx is required.

3. **Verify all image directories exist:**
   Check that these directories exist: bookworm/, go-operator/, netshoot/, node/, server-base/

### 3. Build Images in Dependency Order

Build the images in the following order to respect dependencies:

**Phase 1: Independent base images (can be built in parallel)**
- bookworm
- go-operator
- netshoot
- server-base

**Phase 2: Dependent images**
- node (depends on bookworm:master-head)

For each image, execute the appropriate build command:

**Single-platform:**
```bash
docker build -t futuretea/<image-name>:<tag> <image-name>/
```

**Multi-platform:**
```bash
docker buildx build --platform linux/amd64,linux/arm64 -t futuretea/<image-name>:<tag> <image-name>/
```

### 4. Track Build Progress

As you build each image:

1. Display which image is currently building:
   ```
   🔨 Building bookworm...
   ```

2. If a build fails, note it but continue with other images:
   ```
   ❌ Failed to build bookworm
   ```

3. If a build succeeds, note it:
   ```
   ✅ Successfully built bookworm
   ```

### 5. Handle node Dependency

Before building node:

1. **If single-platform:** Check if bookworm was successfully built in Phase 1
2. **If multi-platform:** Tag the bookworm image as master-head:
   ```bash
   docker tag futuretea/bookworm:<tag> futuretea/bookworm:master-head
   ```

If bookworm failed to build, skip node and note the dependency issue.

### 6. Report Final Results

After all builds complete, display a summary table:

```
📊 Build Summary
================

Image         Status    Tag
---------     ------    ---
bookworm      ✅        local
go-operator   ✅        local
netshoot      ✅        local
server-base   ✅        local
node          ✅        local

5/5 images built successfully
```

Or if there were failures:

```
📊 Build Summary
================

Image         Status    Tag
---------     ------    ---
bookworm      ❌        local
go-operator   ✅        local
netshoot      ✅        local
server-base   ✅        local
node          ⏭️        local (skipped - bookworm dependency failed)

3/5 images built successfully
2 failed or skipped
```

## Example Usage

```
User: /build-all
Claude: [Prompts for tag]
User: [Selects "local"]
Claude: [Prompts for platform]
User: [Selects "no" for single-platform]
Claude: [Builds all images in order]
Claude: [Displays summary table]
```

## Notes

- Images are built in dependency order: bookworm must complete before node starts
- If bookworm fails, node will be skipped automatically
- Independent images (go-operator, netshoot, server-base) can fail without affecting each other
- Multi-platform builds take significantly longer than single-platform builds
- The build continues even if individual images fail, so you can see all results
- For multi-platform builds, images are not automatically pushed to Docker Hub
