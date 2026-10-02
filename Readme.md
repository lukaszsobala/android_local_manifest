### Device specific configuration to build AOSP Android 17 for Orange Pi 5 series boards with rk3588s

***

### Radxa ROCK 5A (Android TV) — this fork

This branch points `device/opi/opi5_pro` and `device/opi/opi5_pro-kernel` at ROCK 5A forks
(branch `android-17.0-rock5a`). The product names (`aosp_opi5_pro_tv`, `opi5_pro-mkimg.sh`) are unchanged.

| Repo (fork) | Branch | ROCK 5A changes |
|---|---|---|
| [android_kernel_rk_opi](https://github.com/lukaszsobala/android_kernel_rk_opi/tree/android-17.0-6.18-rock5a) | `android-17.0-6.18-rock5a` | Ethernet (stmmac) built in, RTL8852BE Wi-Fi + BT for the Radxa A8 module, firmware in-tree, HDMI audio in the DTS |
| [android_kernel_manifest](https://github.com/lukaszsobala/android_kernel_manifest/tree/android-17.0-rock5a) | `android-17.0-rock5a` | `common-android17-6.18` + the kernel fork above |
| [android_device_opi_opi5_pro-kernel](https://github.com/lukaszsobala/android_device_opi_opi5_pro-kernel/tree/android-17.0-rock5a) | `android-17.0-rock5a` | Mainline U-Boot for ROCK 5A, ROCK 5A DTB, boot script that boots from SD or eMMC. See its `ROCK5A.md`. |
| [android_device_opi_opi5_pro](https://github.com/lukaszsobala/android_device_opi_opi5_pro/tree/android-17.0-rock5a) | `android-17.0-rock5a` | Copies all DTBs to the boot partition, `IMGSIZE` override, Radxa branding |

1. Build the kernel (see the kernel manifest README): `tools/bazel build --config=fast --config=stamp //common:opi5_pro`
2. Copy `bazel-bin/common/opi5_pro/arch/arm64/boot/Image` and `.../dts/rockchip/rk3588s-rock-5a.dtb` into
   `device/opi/opi5_pro-kernel/`, replacing the files there (commit them to the fork to keep them).
3. Sync and build Android as below, using `https://raw.githubusercontent.com/lukaszsobala/android_local_manifest/ccr-c4aa7773-ghw425/manifest_rk_opi.xml` and `lunch aosp_opi5_pro_tv-cp2a-userdebug`.
4. Make the image sized for your media, e.g. a 16 GB eMMC: `IMGSIZE=14GiB ./opi5_pro-mkimg.sh`
5. Flash the `*_gpt.img` to SD (test) or eMMC. U-Boot tries SD before eMMC.

***

### How to build (Ubuntu 24.04 LTS):

1. Establish [Android build environment](https://source.android.com/docs/setup/start/requirements).

2. Make sure to run the below commands to install some dependencies

```
sudo apt-get install dosfstools e2fsprogs fdisk kpartx mtools rsync
sudo pip3 install meson mako jinja2 ply pyyaml dataclasses
```

3. Initialize repo:

```
repo init -u https://android.googlesource.com/platform/manifest -b android-17.0.0_r1
curl -o .repo/local_manifests/manifest_rk_opi.xml -L https://raw.githubusercontent.com/dvab-sarma/android_local_manifest/android-17.0/manifest_rk_opi.xml --create-dirs
```

Or optionally, you can reduce download size by creating a shallow clone and removing unneeded projects:

```
repo init -u https://android.googlesource.com/platform/manifest -b android-17.0.0_r1 --depth=1
curl -o .repo/local_manifests/manifest_rk_opi.xml -L https://raw.githubusercontent.com/dvab-sarma/android_local_manifest/android-17.0/manifest_rk_opi.xml --create-dirs
curl -o .repo/local_manifests/remove_projects.xml -L https://raw.githubusercontent.com/dvab-sarma/android_local_manifest/android-17.0/remove_projects.xml
```

4. Sync source code:

```
repo sync -j$(nproc)
```

5. Setup Android build environment:

```
. build/envsetup.sh
```

6. Select the device (`opi5_pro` ) and build target (tablet UI, `tv` for Android TV, or `car` for Android Automotive):


```
lunch aosp_opi5_pro-cp2a-userdebug
```
```
lunch aosp_opi5_pro_tv-cp2a-userdebug
```
```
lunch aosp_opi5_pro_car-cp2a-userdebug
```


7. Compile:

```
make bootimage systemimage vendorimage -j$(nproc)
```

8. Make flashable image for the device
 (`opi5 series boards`): 

```
./opi5_pro-mkimg.sh
```
***
### UPDATE FOR RK3566 (Orange pi 3b v2.1)

- Currently, this Android-17 support is only for Rockchip's rk3588 SoC, Would be releasing for rk3566 in near future. For AOSP 16 on rk3566, Please use the android-16.0 branch of this project. 

***


Also look into [Linux kernel build instructions](https://github.com/dvab-sarma/android_kernel_manifest/tree/android-16.0).

The rockchip drm was patched in this kernel attached in this github.  So, it is recommended to use the kernel in this github.
***

### Issues:
- Camera doesn't work
- 3.5 mm port doesn't work.
- Ethernet port isn't working.
- [Android](https://github.com/dvab-sarma/android_local_manifest/issues)
- [Linux kernel](https://github.com/dvab-sarma/android_kernel_manifest/issues)

***
### Prebuilt Images
- You can download the android 15 / 16 / 17 prebuilt images for various boards from this google drive link. [Here](https://drive.google.com/drive/folders/1d5ifTQ6-efLuzAD8wl57woRwLUpaYbc-)

***

### Wiki:

- [Main Wiki](https://github.com/dvab-sarma/android_local_manifest/wiki)
- [Mesa3D](https://github.com/dvab-sarma/android_local_manifest/wiki/Mesa3D)



### TODO:
- Camera drivers need to be developed for rockchip based devices.

***
**Credits:**
- The android userspace code is based on KonstaKang's raspberry-vanilla aosp project. A huge thanks to KonstaKang and raspberry-vanilla team.
- A huge thanks to Masayuki Araki ([Misaka](https://github.com/misakazip)) for his contribution in developing and testing  this build for Orange Pi 5.

