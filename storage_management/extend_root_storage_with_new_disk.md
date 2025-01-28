# How to Extend Root Storage with a New Disk in Linux

This tutorial explains how to extend your root directory (`/`) by adding a new disk and allocating all its space to your existing storage setup. We’ll do this using **LVM (Logical Volume Manager)** in Linux. Let’s dive in! 😊

---

## 🛠️ Prerequisites
- A Linux system with root access.
- A new disk (e.g., `/dev/sdb`) added to your system.
- The root filesystem (`/`) is already managed by **LVM**.

---

## 🚀 Step-by-Step Guide

### **Step 1: Verify the New Disk**
First, check if the new disk has been detected by the system.

#### Command:
```bash
lsblk
```

#### Expected Output:
```plaintext
NAME         MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda            8:0    0   20G  0 disk
├─sda1         8:1    0    1M  0 part
├─sda2         8:2    0  1.8G  0 part /boot
└─sda3         8:3    0 18.2G  0 part
  └─ubuntu-lv
sdb            8:16   0   20G  0 disk
```
- `sda` is your primary disk.
- `sdb` is the newly added disk, unpartitioned and unused.

---

### **Step 2: Create a Physical Volume (PV)**
Convert the new disk (`/dev/sdb`) into an **LVM Physical Volume**.

#### Command:
```bash
sudo pvcreate /dev/sdb
```

#### Output:
```plaintext
Physical volume "/dev/sdb" successfully created.
```

#### Explanation:
- This prepares the disk for LVM by writing metadata to it.

---

### **Step 3: Extend the Volume Group (VG)**
Add the new physical volume (`/dev/sdb`) to your existing volume group (`ubuntu-vg`).

#### Command:
```bash
sudo vgextend ubuntu-vg /dev/sdb
```

#### Output:
```plaintext
Volume group "ubuntu-vg" successfully extended.
```

#### Explanation:
- A **Volume Group (VG)** is a pool of storage created from one or more physical volumes. Adding the new disk increases the available storage in `ubuntu-vg`.

---

### **Step 4: Extend the Logical Volume (LV)**
Grow the logical volume (`ubuntu-lv`) to use all the additional storage.

#### Command:
```bash
sudo lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
```

#### Output:
```plaintext
Size of logical volume ubuntu-vg/ubuntu-lv changed from 10.00 GiB to 30.00 GiB.
```

#### Explanation:
- The **Logical Volume (LV)** is where the filesystem resides. This step increases the LV size by allocating all available space in the VG.

---

### **Step 5: Resize the Filesystem**
After extending the logical volume, resize the filesystem to use the additional space.

#### For `ext4` Filesystem:
```bash
sudo resize2fs /dev/ubuntu-vg/ubuntu-lv
```

#### For `xfs` Filesystem:
```bash
sudo xfs_growfs /
```

#### Output:
```plaintext
Resized filesystem to 30G.
```

#### Explanation:
- Resizing the filesystem makes the extra space usable for the root directory (`/`).

---

### **Step 6: Verify the Changes**
Confirm that the root directory has been extended.

#### Command:
```bash
df -h
```

#### Expected Output:
```plaintext
Filesystem                     Size  Used Avail Use% Mounted on
/dev/mapper/ubuntu--vg-ubuntu--lv   30G   12G   18G  40% /
```

---

## 🎉 You’re Done!
Your root directory (`/`) has been successfully extended to include the new disk. The process dynamically added storage to your system without downtime.

---

## 📝 Summary of Commands
1. **Check Disks**:
   ```bash
   lsblk
   ```
2. **Create Physical Volume**:
   ```bash
   sudo pvcreate /dev/sdb
   ```
3. **Extend Volume Group**:
   ```bash
   sudo vgextend ubuntu-vg /dev/sdb
   ```
4. **Extend Logical Volume**:
   ```bash
   sudo lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
   ```
5. **Resize Filesystem**:
   - For `ext4`: `sudo resize2fs /dev/ubuntu-vg/ubuntu-lv`
   - For `xfs`: `sudo xfs_growfs /`
6. **Verify Changes**:
   ```bash
   df -h
   ```

---

## 🌟 Why This Approach?
- **Non-Disruptive**: No need to unmount or reboot the system.
- **Flexible**: LVM allows for easy resizing and storage management.
- **Scalable**: Add more disks in the future without hassle.

