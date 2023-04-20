# legion-amd-7-16-16arha7-linux

Linux related tweaks and settings to ensure the best possible experience when running:

- Fedora
- Voidlinux (coming soon)

## Software

- https://github.com/johnfanv2/LenovoLegionLinux (Lenovo Legion Linux (LLL) brings additional drivers and tools for Lenovo Legion series laptops to Linux. It is the alternative to Lenovo Vantage or Legion Zone (both Windows only). It allows to control a few features like fan curve and power mode.)

## Bug Fixes, addressing incompatiblities by kernel version

### Linux Kernel 6.2.x

Kernel 6.2.x requires the following patches.

- fixing screen flickering white and/or fully white (https://gitlab.freedesktop.org/drm/amd/-/issues/2352, https://gitlab.freedesktop.org/drm/amd/-/issues/2352#note_1776812)

```
diff --git a/drivers/gpu/drm/amd/display/amdgpu_dm/amdgpu_dm.c b/drivers/gpu/drm/amd/display/amdgpu_dm/amdgpu_dm.c
index 037af1997b27a83d16d88dccd3d8c658d557e9a8..c40cbfb758b53d849b1fbd173d487301f8b22063 100644
--- a/drivers/gpu/drm/amd/display/amdgpu_dm/amdgpu_dm.c
+++ b/drivers/gpu/drm/amd/display/amdgpu_dm/amdgpu_dm.c
@@ -1553,8 +1553,6 @@ static int amdgpu_dm_init(struct amdgpu_device *adev)
 				init_data.flags.gpu_vm_support = true;
 			break;
 		case IP_VERSION(3, 0, 1):
-		case IP_VERSION(3, 1, 2):
-		case IP_VERSION(3, 1, 3):
 		case IP_VERSION(3, 1, 6):
 			init_data.flags.gpu_vm_support = true;
 			break;
```




### Linux Kernel 6.3.x

I'm not certain that the patch above was included in 6.3, it's likely that this is still a pending issue.

## Battery Savings

running smart shift to push more towards apu instead of dgpu

https://dri.freedesktop.org/docs/drm/gpu/amdgpu.html#gpu-smartshift-information
https://fedoraproject.org/wiki/QA:Testcase_vga_switcheroo
https://www.kernel.org/doc/html/latest/gpu/vga-switcheroo.html

-100 sets maximum preference to APU and 100 sets max perference to dGPU

```
sudo su -c "echo -100 > /sys/bus/pci/devices/0000:03:00.0/smartshift_bias"
```

The amdgpu driver provides a sysfs API for adjusting certain power related parameters. The file power_dpm_force_performance_level is used for this. It accepts the following arguments:

```
    auto
    low
    high
    manual
    profile_standard
    profile_min_sclk
    profile_min_mclk
    profile_peak
```

```
sudo su -c "echo 'low' > /sys/bus/pci/devices/0000:03:00.0/power_dpm_force_performance_level"
```

The dGPU is woken up too often, lets change it from 5 seconds (5000) to 60 seconds 60000

```
sudo bash -c "echo 60000 > /sys/bus/pci/devices/0000:03:00.0/power/autosuspend_delay_ms"
```

**CPU configuration**

power management
https://wiki.archlinux.org/title/Power_management#Kernel_parameters
https://www.kernel.org/doc/html/latest/admin-guide/pm/amd-pstate.html

use ```cpupower``` to set the frequency.

check the power state settings:
```
# https://www.kernel.org/doc/html/latest/admin-guide/pm/amd-pstate.html#user-space-interface-in-sysfs
ls /sys/devices/system/cpu/cpufreq/policy0/*amd*
ls /sys/devices/system/cpu/cpufreq/policy0/*amd* | xargs cat
```

**ensure that the following is installed**

```
sudo dnf install microcode_ctl
```


**active state power management**

```echo powersave > /sys/module/pcie_aspm/parameters/policy```


