# Fooocus-Linux-Containerization-AMD
Guide on how to run Fooocus on Linux in container based environments on AMD GPUs.

Hi.

Recently, I set about trying to set-up Fooocus in a containerized environment on Linux. I have managed to set this environment up and run it locally on my pc. For context, these are the specifications of my computer:

OS: Arch Linux 
Kernel: x86_64 Linux 6.12.10-arch1-1
CPU: AMD Ryzen 7 3700X 8-Core @ 16x 4.426GHz
GPU: AMD Radeon RX 6600 (radeonsi, navi23, LLVM 19.1.7, DRM 3.59, 6.12.10-arch1-1)
GPU Drivers Installed: vulkan-radeon, vulkan-amdgpu-pro, xf86-video-amdgpu

**Dockerfile Setup:**
[dockerfile.txt](https://github.com/user-attachments/files/18541358/dockerfile.txt)

**Building The Container**

`podman build -t fooocus .`

**Starting The Container:**

`podman run -it --rm \
  -v fooocus-models:/app/models \
  --device=/dev/kfd \
  --device=/dev/dri \
  -p 7865:7865 \
  fooocus
`

**Downloading Models**

**1. Option A: Let Fooocus Handle It:**

After starting Fooocus, it will automatically download the required models to **_/app/models/checkpoints._** If you mapped the fooocus-models volume, these models will persist across container runs.

**2. Option B: Manual Download:**

Download the default models from the Fooocus repository and place them in the mounted volume:
`podman volume inspect fooocus-models`

Copy the downloaded models into the **_models/checkpoints_** directory inside the volume.

**Access Fooocus:**

`http://localhost:7865`

**Optional: Launch Preset Modes:**

**- Anime Edition:**

`podman run -it --rm \
  -v fooocus-models:/app/models \
  --device=/dev/kfd \
  --device=/dev/dri \
  -p 7865:7865 \
  fooocus --preset anime
`

**- Realistic Edition:**

`podman run -it --rm \
  -v fooocus-models:/app/models \
  --device=/dev/kfd \
  --device=/dev/dri \
  -p 7865:7865 \
  fooocus --preset realistic
`

**Notes And Troubleshooting:**

**1. Verify GPU Access:** Ensure that _**/dev/kfd**_ and _**/dev/dri**_ are accessible and that your user is in the video group:
`sudo usermod -aG video $USER`
`newgrp video`

**2. Model Volume:** Use the **_fooocus-models_** volume to store models persistently across container runs.

**3. AMD GPU Beta Support:** Fooocus for AMD GPUs is in beta.

That is mostly it! This basic containerization template should get you going on Linux with AMD GPUs.
Suggestions and improvements are always welcome!
