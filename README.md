# Samsung Fullscreen AOD Enabler

<p align="center">
  <b>Restore Full Screen AOD wallpapers on unsupported Samsung Galaxy devices.</b>
</p>

<p align="center">
  A Magisk/KernelSU module for Samsung Galaxy devices where the Full Screen AOD wallpaper feature is present in the firmware but disabled by Samsung.
</p>

---

## ✨ Features

- 🖼️ Restores the **Wallpaper on AOD** option
- 📱 Intended for **unsupported Samsung Galaxy devices**, including devices up to the **Galaxy S23 series**
- 🔧 Uses the **original `floating_feature.xml` from your own firmware**
- 🎯 Modifies **only** `SEC_FLOATING_FEATURE_LCD_CONFIG_AOD_FULLSCREEN`
- 🛡️ Does **not** ship a pre-modified `floating_feature.xml`
- ⚡ Designed for **temporary root** environments
- 🔄 Supports activation after a **soft / userspace reboot**
- ♻️ Includes a separate module for restoring the stock runtime state

---

## 💡 Why this module?

Samsung has disabled the Full Screen AOD wallpaper feature on some Galaxy devices, even though the required functionality is still present in the software.

There are already modules that enable this feature by providing a modified `floating_feature.xml`.

The problem is that `floating_feature.xml` contains many settings specific to a particular device and firmware. Using an XML file taken from another device or firmware can therefore introduce unnecessary compatibility problems.

### This module takes a different approach.

It does **not** include its own static XML.

Instead, on each activation it reads the **original `floating_feature.xml` from your own device**, creates a temporary patched copy, changes only the required value, verifies the result, and then bind-mounts that patched copy over the original path.

```text
Your device's original floating_feature.xml
                  │
                  ▼
        Create a temporary copy
                  │
                  ▼
Change only SEC_FLOATING_FEATURE_LCD_CONFIG_AOD_FULLSCREEN
                  │
                  ▼
          Verify the modification
                  │
                  ▼
       Bind-mount the patched file
```

Nothing else in the XML is intentionally changed.

---

## 🔍 What exactly is changed?

The module looks for:

```xml
<SEC_FLOATING_FEATURE_LCD_CONFIG_AOD_FULLSCREEN>0</SEC_FLOATING_FEATURE_LCD_CONFIG_AOD_FULLSCREEN>
```

and changes only that value to:

```xml
<SEC_FLOATING_FEATURE_LCD_CONFIG_AOD_FULLSCREEN>1</SEC_FLOATING_FEATURE_LCD_CONFIG_AOD_FULLSCREEN>
```

If the expected value is not found, the module **refuses to modify or mount the file**.

This helps prevent accidental changes when the firmware does not match the expected configuration.

---

## ⚡ Temporary Root & Soft Reboot

The module is specifically designed with **temporary-root users** in mind.

It applies the runtime modification through a bind mount rather than permanently modifying `/system` on disk. The module runs its patching logic from both `post-fs-data.sh` and `service.sh`, allowing it to re-apply the modification in supported soft / userspace reboot environments.

This is especially useful for devices where the bootloader cannot be permanently unlocked but temporary root is available through a kernel exploit.

> **Important:** behaviour after a soft/userspace reboot can depend on the specific temporary-root implementation and One UI version.

---

## ♻️ Restoring the Stock Interface

A separate module, **AODInterfaceRestore**, is included for restoring the normal system state without requiring a full reboot.

### Recommended procedure

**1. Disable the main `SamFullAOD` module.**

**2. Install `AODInterfaceRestore.zip`.**

**3. Perform a soft / userspace reboot.**

The restore module:

- removes the runtime bind mount created by the AOD patch;
- deletes the temporary patched copy;
- restarts the AOD service, SystemUI and Settings when appropriate.

After the soft reboot, the system should once again see the **original `floating_feature.xml` from your firmware**.

**4. The restore module can then be removed or disabled.**

### Why is this useful?

This is primarily intended for temporary-root users who **do not want to perform a full device reboot**, because a full reboot may cause them to lose their temporary root access.

> **Note:** the restore module does not modify `/system` on disk. If Samsung framework components have already cached the modified feature state and the interface remains abnormal, the restore module's own documentation recommends a **full power-off / power-on** as a last resort.

---

## 📱 Compatibility

This project is intended for Samsung Galaxy devices where the Full Screen AOD wallpaper functionality is implemented in the firmware but disabled or hidden.

The main target is the range of devices **up to the Galaxy S23 series** where Samsung removed this option.

Compatibility still depends on the exact:

- Device model
- One UI version
- Firmware build
- Temporary-root implementation
- Presence and format of the required `floating_feature.xml` entry

### Tested devices

> Add confirmed test results here.

| Device | One UI | Status |
|---|---|---|
| Galaxy S23 | — | Working ✅️ |
| Galaxy S22 | — | Working ✅️ |
| Galaxy S21 | — | Working ✅️ |
| Galaxy S20 | — | Working ✅️ |

---

## 🚀 Installation

1. Download the latest release from **[Releases](../../releases)**.
2. Open **Magisk**.
3. Go to **Modules**.
4. Choose **Install from storage**.
5. Select the `SamFullAOD` ZIP.
6. Complete the installation.
7. Perform a reboot supported by your temporary-root environment.
8. Check the AOD settings.

On supported firmware, the Full Screen AOD wallpaper option should now be available.

---

## 🛠️ How the module works

The main module contains no static `floating_feature.xml`.

At runtime it:

1. Locates `/system/etc/floating_feature.xml`.
2. Checks for the expected AOD value.
3. Copies the current file to a temporary working location.
4. Changes only the Full Screen AOD feature value.
5. Verifies that the change was successful.
6. Creates a bind mount over the original path.
7. Restarts the Samsung AOD service.

Because the bind mount is temporary, the original file on `/system` is not permanently overwritten.

---

## ❌ What this module does NOT do

- It does **not** add a new AOD engine.
- It does **not** permanently edit `/system/etc/floating_feature.xml`.
- It does **not** ship a universal pre-modified XML file.
- It does **not** intentionally change unrelated `floating_feature.xml` values.

It simply exposes a Samsung feature that is already present in supported firmware but disabled for the device.

---

## 📦 Releases

Download the latest version here:

**[GitHub Releases](../../releases)**

The release may contain:

- `SamFullAOD` — main AOD Enabler module
- `AODInterfaceRestore` — runtime restore module

---

## ⚠️ Disclaimer

Use these modules at your own risk.

They interact with Samsung's system configuration at runtime. Although the main module does not permanently modify `/system`, compatibility is not guaranteed for every device, firmware or root implementation.

Always keep a way to disable the module if something goes wrong.

---

## ⭐ Support

If this project helped restore Full Screen AOD on your Samsung Galaxy device, consider giving the repository a ⭐.

<p align="center">
  <b>Made for the Samsung Galaxy modding community.</b>
</p>
