# Building and Deploying the Homebox HA Addon

This guide covers multiple methods to build and deploy the Homebox addon to your Home Assistant instance.

## Method 1: Local Development Mode (Easiest for Testing)

This method lets Home Assistant build the addon locally, perfect for development and testing.

### Steps:

1. **Comment out the image in config.yaml** to enable local builds:
   ```yaml
   # image: "ghcr.io/Oddiesea/homebox-ha-addon"
   ```

2. **Add your local repository to Home Assistant**:
   - Go to **Settings** → **Add-ons** → **Add-on Store**
   - Click the **⋮** (three dots) menu in the top right
   - Select **Repositories**
   - Click **Add** and enter one of:
     - **Local path** (if HA has access): `/config/addons/homebox-ha-addon`
     - **Git URL**: `https://github.com/yourusername/homebox-ha-addon.git`
     - **Local git**: `file:///path/to/homebox-ha-addon`

3. **Install the addon**:
   - The addon should appear in the Add-on Store
   - Click **Homebox Addon** → **Install**
   - Home Assistant will build it automatically

## Method 2: Build Locally and Push to Registry

For production deployments, build the image and push it to a container registry.

### Steps:

1. **Install Home Assistant Builder** (if not already installed):
   ```bash
   docker pull ghcr.io/home-assistant/builder:2024.08.2
   ```

2. **Build the addon image**:
   ```bash
   cd /Users/liamjones/dev/personal/homebox-ha-addon
   docker run --rm \
     --privileged \
     -v "$(pwd)":/data \
     -v "/var/run/docker.sock:/var/run/docker.sock" \
     ghcr.io/home-assistant/builder:2024.08.2 \
     --aarch64 \
     --target /data/homebox \
     --image "ghcr.io/yourusername/homebox-ha-addon" \
     --docker-hub "ghcr.io/yourusername" \
     --addon
   ```

3. **Update config.yaml** with your image:
   ```yaml
   image: "ghcr.io/yourusername/homebox-ha-addon"
   ```

4. **Login to GitHub Container Registry**:
   ```bash
   echo $GITHUB_TOKEN | docker login ghcr.io -u YOUR_USERNAME --password-stdin
   ```

5. **Tag and push the image**:
   ```bash
   docker tag ghcr.io/yourusername/homebox-ha-addon:latest ghcr.io/yourusername/homebox-ha-addon:2.2.0
   docker push ghcr.io/yourusername/homebox-ha-addon:latest
   docker push ghcr.io/yourusername/homebox-ha-addon:2.2.0
   ```

6. **Add repository to Home Assistant**:
   - Add your GitHub repository URL in the Add-on Store

## Method 3: Use GitHub Actions (Automatic)

If you push to GitHub, the workflow will automatically build and push images.

### Steps:

1. **Push your changes to GitHub**:
   ```bash
   git add .
   git commit -m "Add ingress support"
   git push origin main
   ```

2. **Wait for GitHub Actions to build** (check Actions tab)

3. **Update config.yaml** if the image name changed:
   ```yaml
   image: "ghcr.io/Oddiesea/homebox-ha-addon"
   ```

4. **Make the image public** (if private):
   - Go to your GitHub repository
   - Click **Packages** → Find your image → **Package settings** → Change visibility to **Public**

5. **Add repository to Home Assistant**:
   - Settings → Add-ons → Add-on Store → Repositories
   - Add: `https://github.com/Oddiesea/homebox-ha-addon`

## Method 4: Local Repository via Network Share

If your Home Assistant can access a network share:

1. **Copy repository to network share**:
   ```bash
   cp -r /Users/liamjones/dev/personal/homebox-ha-addon /path/to/network/share/
   ```

2. **Add repository in Home Assistant**:
   - Use the network path or SMB path

## Quick Local Testing Command

For quick local builds during development:

```bash
# Build for aarch64 (Raspberry Pi 4, etc.)
docker run --rm --privileged \
  -v "$(pwd)":/data \
  -v "/var/run/docker.sock:/var/run/docker.sock" \
  ghcr.io/home-assistant/builder:2024.08.2 \
  --aarch64 \
  --target /data/homebox \
  --image "local/homebox-addon" \
  --docker-hub "local" \
  --addon \
  --test
```

## Troubleshooting

- **"Image not found"**: Make sure the image name in config.yaml matches what you built/pushed
- **"Build failed"**: Check that Docker has enough resources and /var/run/docker.sock is accessible
- **"Repository not found"**: Verify the repository path is correct and accessible
- **"Permission denied"**: Make sure the repository directory has correct permissions

## Recommended Workflow

For active development:
1. Use **Method 1** (local development) to test changes quickly
2. Once stable, use **Method 3** (GitHub Actions) for production builds

