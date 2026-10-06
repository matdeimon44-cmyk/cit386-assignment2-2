
# CIT 386 - Module 3 - Assignment 3.1
## Hyper-V Build Log and Disk Behavior

**Student:** Matthew Espinel

## 1. Hyper-V Virtual Machine Configuration

I created and configured a virtual machine in Hyper-V using the following settings:

| Setting | Configuration |
|---|---|
| VM Name | matthew-espinel |
| Generation | Generation 2 |
| Memory | 4096 MB (4 GB) |
| Dynamic Memory | Disabled |
| Virtual Processors | 14 |
| Network | Not Connected |
| Operating System | Ubuntu Server 24.04.3 LTS |
| Primary Disk Format | VHDX |
| Primary Disk Type | Dynamically Expanding |
| Maximum Disk Size | 127 GB |
| Secure Boot | Disabled for Ubuntu installation |

The primary virtual hard disk is located at:

`C:\ProgramData\Microsoft\Windows\Virtual Hard Disks\matthew-espinel.vhdx`

## 2. Dynamically Expanding VHDX Test

I inspected the dynamically expanding VHDX from the Hyper-V host and recorded its file size.

### Initial Measurement

- Maximum virtual disk size: **127 GB**
- Current VHDX file size: **5.79 GB**

I then created a 2 GiB test file inside the Ubuntu virtual machine using:

`dd if=/dev/zero of=~/testfile.bin bs=1M count=2048 status=progress`

The command successfully wrote:

**2,147,483,648 bytes (2.0 GiB)**

### Measurement After Writing Data

After writing the test data, I inspected the VHDX again from the Hyper-V host.

- Current VHDX file size: **5.82 GB**

I then deleted the test file from Ubuntu using:

`rm ~/testfile.bin`

### Measurement After Deleting Data

After deleting the 2 GiB test file, I inspected the VHDX again.

- Current VHDX file size: **5.82 GB**

The VHDX did not automatically return to its previous size after the file was deleted.

## 3. Fixed-Size VHDX Test

I created a second virtual hard disk with the following configuration:

| Setting | Configuration |
|---|---|
| File Name | matthew-espinel-fixed.vhdx |
| Format | VHDX |
| Type | Fixed Size |
| Maximum Disk Size | 4 GB |
| Initial File Size | 4 GB |

The fixed disk was attached to the Ubuntu VM as `/dev/sdb`.

I formatted it with the ext4 filesystem and mounted it at:

`/mnt/fixed`

Before writing the test data, Ubuntu reported approximately **3.9 GB** total filesystem capacity with approximately **3.7 GB available**.

I then wrote 2 GiB of data to the fixed disk using:

`sudo dd if=/dev/zero of=/mnt/fixed/testfile.bin bs=1M count=2048 status=progress`

The operation successfully wrote **2.0 GiB** of data.

I deleted the test file and unmounted the disk.

### Final Fixed-Disk Measurement

I inspected `matthew-espinel-fixed.vhdx` again from the Hyper-V host.

- Current File Size: **4 GB**
- Maximum Disk Size: **4 GB**

## 4. Disk Behavior Comparison

| Disk Type | Initial Size | Data Written | Size After Write | Size After Delete |
|---|---:|---:|---:|---:|
| Dynamically Expanding | 5.79 GB | 2.0 GiB | 5.82 GB | 5.82 GB |
| Fixed Size | 4 GB | 2.0 GiB | 4 GB | 4 GB |

The dynamically expanding disk allocates host storage as the virtual disk requires additional blocks. Deleting files inside the guest operating system does not automatically shrink the VHDX file on the Hyper-V host.

The fixed-size disk behaves differently because its full configured capacity is allocated when the VHDX is created. Therefore, the fixed VHDX remained at 4 GB before and after writing and deleting the test data.

## 5. Reflection

This lab helped me understand the practical difference between dynamically expanding and fixed-size virtual disks in Hyper-V. A dynamically expanding disk can conserve host storage because the VHDX does not initially occupy its entire maximum capacity. However, deleting files inside the guest does not necessarily reduce the physical size of the VHDX.

A fixed-size disk reserves its configured storage capacity immediately. In my test, the fixed disk remained at 4 GB even after writing 2 GiB of data and deleting it.

This experiment demonstrated why administrators need to consider both the virtual capacity presented to a VM and the actual storage consumed by VHDX files on the Hyper-V host.


Crear imágenes
Reimagina, ilustra y edita
