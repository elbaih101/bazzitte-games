# **VoiceStudio on Bazzite – AMD ROCm Setup Summary**

Working successfully

Successfully configured **VoiceStudio 0.5.8** to detect and execute GPU computations on the AMD Radeon RX 6650 XT using ROCm.

## **1. Hardware and software environment**

| Component        | Configuration         |
| ---------------- | --------------------- |
| OS               | Bazzite GNOME         |
| GPU              | AMD Radeon RX 6650 XT |
| GPU architecture | `gfx1032`             |
| GPU vendor ID    | `1002:73ef`           |
| VoiceStudio      | 0.5.8                 |
| PyTorch          | 2.10.0+rocm7.1        |
| HIP runtime      | 7.1.25424             |
| System ROCm      | 7.2                   |
| Installation     | AppImage              |

## **2. Problems encountered**

1. VoiceStudio initially detected the AMD GPU but couldn't successfully execute GPU computations.
2. `torch.cuda.is_available()` returned `True`, but that alone didn't prove GPU acceleration was working.
3. GPU computation initially caused a segmentation fault.
4. The RX 6650 XT (`gfx1032`) needed a compatible architecture override for the installed PyTorch ROCm build.

## **3. Changes made**

### **A. Installed ROCm system packages**

Because Bazzite is an image-based Fedora system, we used `rpm-ostree` rather than `dnf`.

Installed:

```
sudo rpm-ostree install rocm-hip7.2
sudo rpm-ostree install rocminfo7.2
```

The packages were added to the active deployment.

### **B. Verified GPU detection**

Executed:

```
/usr/lib64/rocm/rocm-7.2/bin/rocminfo
```

Confirmed:

- GPU detected successfully.
- Architecture: `gfx1032`.
- Marketing name: `AMD Radeon RX 6650 XT`.
- Device type: GPU.
- Kernel dispatch supported.

### **C. Verified PyTorch ROCm**

VoiceStudio's Python environment was located at:

```
/var/mnt/58ef1e2b-aa6a-40fb-a2c9-2057ca6af2ca/voice studio/VoiceStudio/project/.venv
```

Verified:

- PyTorch 2.10.0+rocm7.1.
- HIP 7.1.
- GPU count: 1.

### **D. Applied the GPU architecture override**

This was the critical fix:

```
HSA_OVERRIDE_GFX_VERSION=10.3.0
```

The RX 6650 XT belongs to the `gfx103x` family. The override allowed the installed ROCm PyTorch build to execute GPU operations successfully.

### **E. Verified actual GPU computation**

Executed a matrix multiplication test:

```
HSA_OVERRIDE_GFX_VERSION=10.3.0 \
"/var/mnt/58ef1e2b-aa6a-40fb-a2c9-2057ca6af2ca/voice studio/VoiceStudio/project/.venv/bin/python" \
-u -c "import torch; print('PyTorch:',torch.__version__); print('GPU:',torch.cuda.get_device_name(0)); x=torch.randn(1024,1024,device='cuda'); y=x@x; torch.cuda.synchronize(); print('GPU computation SUCCESS:',y[0][0].item())"
```

Test passed

Output:

```
/opt/amdgpu/share/libdrm/amdgpu.ids: No such file or directory
GPU: AMD Radeon Graphics
GPU computation SUCCESS: -48.9686279296875
```

The `amdgpu.ids` warning remained, but it did not prevent GPU computation.

## **4. Configured permanent GPU launch**

Created a GNOME application launcher:

```
~/.local/share/applications/voicestudio-rocm.desktop
```

With the following content:

```
[Desktop Entry]
Version=1.0
Type=Application
Name=VoiceStudio (AMD GPU)
Comment=VoiceStudio with AMD ROCm acceleration
Exec=/usr/bin/env HSA_OVERRIDE_GFX_VERSION=10.3.0 OMNIVOICE_TORCH_VARIANT=rocm /home/elbaih/AppImages/voicestudio.appimage
Terminal=false
Categories=AudioVideo;Utility;
StartupNotify=true
```

Now VoiceStudio can be launched from GNOME Activities without manually entering environment variables.

## **5. Final launch configuration**

These are the two important environment variables:

| Variable                          | Purpose                                           |
| --------------------------------- | ------------------------------------------------- |
| `HSA_OVERRIDE_GFX_VERSION=10.3.0` | Provides the compatible GPU architecture override |
| `OMNIVOICE_TORCH_VARIANT=rocm`    | Selects the ROCm PyTorch runtime                  |

## **6. Current status and remaining considerations**

ROCm GPU detection

Working

PyTorch GPU computation

Working

VoiceStudio GPU self-test

Passed

Permanent launcher

Configured

All engines using GPU

Not verified

**Important:** GPU detection and successful PyTorch computation do not guarantee that every VoiceStudio engine uses the GPU. Individual engines may still use CPU depending on their backend and hardware support.

The Compute-time budget settings discussed later control generation timeouts, not GPU priority.

**Bottom line:** The key to getting your RX 6650 XT working was installing the ROCm system runtime, verifying GPU detection with `rocminfo`, and launching VoiceStudio with `HSA_OVERRIDE_GFX_VERSION=10.3.0` and `OMNIVOICE_TORCH_VARIANT=rocm`.
