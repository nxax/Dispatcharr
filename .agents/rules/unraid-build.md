# Unraid Custom Docker Build Workflow

The user deploys custom builds of Dispatcharr on Unraid using locally built Docker images built directly on the server or pushed from their repository `https://github.com/nxax/Dispatcharr.git`.

## Unraid Build Instructions (Method 2 - Local Build on Server)
- Repository: `https://github.com/nxax/Dispatcharr.git`
- Custom Docker Tag: `nxax/dispatcharr:custom`

### Command Sequence on Unraid (via SSH / Terminal):
```bash
# 1. Navigate to the repository directory on Unraid
cd /mnt/user/appdata/dispatcharr-custom

# 2. Pull latest code updates
git pull origin main

# 3. Build the custom Docker image locally into Unraid's Docker daemon
docker build -f docker/Dockerfile -t nxax/dispatcharr:custom .
```

### Unraid Docker Container Configuration:
- **Repository**: `nxax/dispatcharr:custom`
- After building the image, restart/reapply the container in the Unraid Docker tab.
