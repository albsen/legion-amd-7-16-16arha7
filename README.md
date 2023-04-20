# legion-amd-7-16-16arha7-linux linux tweaks targeting mainly fedora and or voidlinux where applicable 

## Bug Fixes, addressing incompatiblities by kernel version

### Linux Kernel 6.2.x

Kernel 6.2.x requires the following patches:



### Linux Kernel 6.3.x



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


