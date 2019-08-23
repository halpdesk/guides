# NVIDIA drivers

Most recent guide to follow:
[https://linuxconfig.org/how-to-install-nvidia-driver-on-debian-10-buster-linux]()

## nvidia reports missing firmware

Article about missing formware:
[https://askubuntu.com/questions/832524/possible-missing-frmware-lib-firmware-i915/832528]()

Firmware download site:
[https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git/tree/i915]()

The error looks like this:

```error
W: Possible missing firmware /lib/firmware/i915/bxt_dmc_ver1_07.bin for module i915
W: Possible missing firmware /lib/firmware/i915/skl_dmc_ver1_27.bin for module i915
W: Possible missing firmware /lib/firmware/i915/kbl_dmc_ver1_04.bin for module i915
W: Possible missing firmware /lib/firmware/i915/cnl_dmc_ver1_07.bin for module i915
W: Possible missing firmware /lib/firmware/i915/glk_dmc_ver1_04.bin for module i915
W: Possible missing firmware /lib/firmware/i915/kbl_guc_ver9_39.bin for module i915
W: Possible missing firmware /lib/firmware/i915/bxt_guc_ver9_29.bin for module i915
W: Possible missing firmware /lib/firmware/i915/skl_guc_ver9_33.bin for module i915
W: Possible missing firmware /lib/firmware/i915/kbl_huc_ver02_00_1810.bin for module i915
W: Possible missing firmware /lib/firmware/i915/bxt_huc_ver01_07_1398.bin for module i915
W: Possible missing firmware /lib/firmware/i915/skl_huc_ver01_07_1398.bin for module i915
```

Here is what to do:

```sh
wget https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git/tree/i915/bxt_dmc_ver1_07.bin
wget https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git/tree/i915/skl_dmc_ver1_27.bin
wget https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git/tree/i915/kbl_dmc_ver1_04.bin
wget https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git/tree/i915/cnl_dmc_ver1_07.bin
wget https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git/tree/i915/glk_dmc_ver1_04.bin
wget https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git/tree/i915/kbl_guc_ver9_39.bin
wget https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git/tree/i915/bxt_guc_ver9_29.bin
wget https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git/tree/i915/skl_guc_ver9_33.bin
wget https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git/tree/i915/kbl_huc_ver02_00_1810.bin
wget https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git/tree/i915/bxt_huc_ver01_07_1398.bin
wget https://git.kernel.org/pub/scm/linux/kernel/git/firmware/linux-firmware.git/tree/i915/skl_huc_ver01_07_1398.bin

sudo mkdir /lib/firmware/i915/
sudo cp bxt_dmc_ver1_07.bin /lib/firmware/i915/
sudo cp skl_dmc_ver1_27.bin /lib/firmware/i915/
sudo cp kbl_dmc_ver1_04.bin /lib/firmware/i915/
sudo cp cnl_dmc_ver1_07.bin /lib/firmware/i915/
sudo cp glk_dmc_ver1_04.bin /lib/firmware/i915/
sudo cp kbl_guc_ver9_39.bin /lib/firmware/i915/
sudo cp bxt_guc_ver9_29.bin /lib/firmware/i915/
sudo cp skl_guc_ver9_33.bin /lib/firmware/i915/
sudo cp kbl_huc_ver02_00_1810.bin /lib/firmware/i915/
sudo cp bxt_huc_ver01_07_1398.bin /lib/firmware/i915/
sudo cp skl_huc_ver01_07_1398.bin /lib/firmware/i915/

sudo update-initramfs -u
```

## Two graphic cards on the same system - optirun

**Optirun** is used when another video card is on the same system.
`nvidia-settings` detects the primary card and might fail with error "unable to load infor form any available system".

Solution is to install optirun (bumblebee) from primus package: `apt install primus`

The the nvidia settings works as follows:

`sudo optirun nvidia-smi`

or

`sudo optirun nvidia-settings -c :8`

> Note: the -c parameter is appended to `nvidia-settings`, and decides which control display to use. Often times 8.

```sh
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 418.74       Driver Version: 418.74       CUDA Version: N/A      |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  GeForce MX250       On   | 00000000:3C:00.0 Off |                  N/A |
| N/A   42C    P8    N/A /  N/A |      6MiB /  2002MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|    0     17947      G   /usr/lib/xorg/Xorg                             5MiB |
+-----------------------------------------------------------------------------+
```

> Above is sample output by `nvidia-smi`
