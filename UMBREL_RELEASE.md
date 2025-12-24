# How to create an Umbrel App Release

## Build image

### Use BuildKit for multi-arch builds

    docker buildx create --use

### Helper command: Get all existing tags for image

    curl -s "https://hub.docker.com/v2/repositories/mauricewipf/fizzy/tags/?page_size=100" | jq -r '.results[].name'

### Build, tag and push image

    docker buildx build \
      --platform linux/amd64,linux/arm64 \
      -t mauricewipf/fizzy:latest \
      -t mauricewipf/fizzy:1.0.0 \
      -t mauricewipf/fizzy:$(git rev-parse --short HEAD) \
      --push \
      --provenance=false \
      .

### Build, tag and push image with Github Actions

    git checkout umbrel
    git fetch upstream --tags
    git fetch origin
    git fetch upstream main:main
    git push origin main
    git merge origin/main
    git push origin

    # Get all version tags:
    git ls-remote --tags --sort=-committerdate origin | head -10

    # Get latest release tag from upstream which was merged into origin
    git describe --tags --abbrev=0 $(git merge-base origin/umbrel upstream/main)

    # Get latest commit from upstream which was merged into origin
    git rev-parse --short=7 $(git merge-base origin/umbrel upstream/main)

    # Create a tag with the 7-digit commit hash
    git tag "$(git rev-parse --short=7 "$(git merge-base origin/umbrel upstream/main)")"

    # Check tag creation
    git tag --sort=-committerdate | head -10

    git push origin umbrel --tags

    # publish-image-qemu.yml is running...
    # Open https://github.com/mauricewipf/fizzy-umbrel/actions

Visit URL to see all tagged image versions:
https://github.com/mauricewipf/fizzy-umbrel/pkgs/container/fizzy-umbrel/versions
