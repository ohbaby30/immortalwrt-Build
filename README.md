# ImmortalWrt GitHub Actions Builder

This repository builds ImmortalWrt firmware for both x86_64 and FriendlyARM NanoPi R4S with GitHub Actions.

## Targets

- `Build ImmortalWrt x86_64`
  - Config: `configs/x86_64.config`
  - Custom files: `files-x86_64/`
  - First boot LAN IP: `10.10.10.123/24`
  - Release firmware: `immortalwrt-x86-64-generic-squashfs-combined-efi.img.gz`

- `Build ImmortalWrt R4S`
  - Config: `configs/r4s.config`
  - Custom files: `files-r4s/`
  - First boot LAN IP: `192.168.50.254/24`
  - Release firmware: `immortalwrt-rockchip-armv8-friendlyarm_nanopi-r4s-squashfs-sysupgrade.img.gz`

Both targets build LuCI with Chinese language support, Passwall2 with Xray core only, LuCI Wake on LAN, `nano`, `luci-theme-openwrt-2020`, and `luci-theme-material`.

## Usage

1. Upload the contents of this `build/` directory to a GitHub repository root.
2. The repository root should contain `.github/`, `configs/`, `files-x86_64/`, `files-r4s/`, and `README.md`.
3. Open the repository `Actions` page.
4. Choose `Build ImmortalWrt x86_64` or `Build ImmortalWrt R4S`.
5. Click `Run workflow`.
6. Download the firmware from GitHub Releases after the build finishes.

Do not upload the outer `build/` folder itself as a subdirectory if you want GitHub Actions to detect the workflows. Upload the files and folders inside `build/` to the repository root.
