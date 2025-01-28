# How to Permanently Mount a New Disk in Linux

This guide walks you through the steps to add a new disk (e.g., a 20 GB hard drive) to your Linux system and permanently mount it in your filesystem. The instructions are written with a friendly tone to make it approachable. 😊

---

## 🛠️ Prerequisites
- A virtual or physical machine running Linux.
- A newly attached disk (e.g., `/dev/sdb`) that you want to use.

---

## 🚀 Step-by-Step Guide

### **Step 1: Attach the New Disk**
1. Add a new hard disk to your virtual machine (via VirtualBox, VMware, etc.).
2. Boot up your Linux system and verify the disk is recognized.

### **Step 2: Verify the Disk**
Run the following command:
```bash
lsblk
```

You should see the new disk, likely labeled as `/dev/sdb`, in the output:
```plaintext
NAME         MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sda            8:0    0   20G  0 disk
├─sda1         8:1    0    1M  0 part
├─sda2         8:2    0  1.8G  0 part /boot
└─sda3         8:3    0 18.2G  0 part
sdb            8:16   0   20G  0 disk
```
---

### **Step 3: Partition the Disk**
1. Open the partitioning tool:
   ```bash
   sudo fdisk /dev/sdb
   ```
2. Inside `fdisk`:
   - Press `n` to create a new partition.
   - Choose `primary` and accept the defaults to use the full disk.
   - Press `w` to save changes and exit.

---

### **Step 4: Format the Partition**
Format the newly created partition (`/dev/sdb1`) with the `ext4` file system:
```bash
sudo mkfs.ext4 /dev/sdb1
```

This creates a filesystem that can store your data.

---

### **Step 5: Create a Mount Point**
Create a directory where the disk will be mounted:
```bash
sudo mkdir -p /mnt/newdisk
```

---

### **Step 6: Temporarily Mount the Disk**
Mount the partition to the directory:
```bash
sudo mount /dev/sdb1 /mnt/newdisk
```

Verify the mount:
```bash
df -h
```

Expected output:
```plaintext
Filesystem      Size  Used Avail Use% Mounted on
/dev/sdb1        20G   44M   19.9G  1% /mnt/newdisk
```

---

### **Step 7: Make the Mount Permanent**
To ensure the disk is mounted after reboot:
1. Edit the `/etc/fstab` file:
   ```bash
   sudo nano /etc/fstab
   ```
2. Add the following line to the end of the file:
   ```plaintext
   /dev/sdb1  /mnt/newdisk  ext4  defaults  0  2
   ```

Alternatively, you can use the disk’s **UUID** for reliability:
```bash
blkid /dev/sdb1
```
Copy the UUID and use it in `/etc/fstab`:
```plaintext
UUID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  /mnt/newdisk  ext4  defaults  0  2
```

Save and exit the file.

---

### **Step 8: Test the Configuration**
1. Unmount the disk:
   ```bash
   sudo umount /mnt/newdisk
   ```
2. Remount all entries in `/etc/fstab`:
   ```bash
   sudo mount -a
   ```
3. Verify the mount again:
   ```bash
df -h
```

---

### **You’re Done! 🎉**
Your new 20 GB disk is now permanently mounted at `/mnt/newdisk`. You can use it for storing data, hosting applications, or anything else you need. The configuration in `/etc/fstab` ensures it stays mounted even after a reboot.

---

### **Optional: Advanced Features**
- **Resize the Partition**: If you expand the disk size later, you can resize the partition and filesystem.
- **Use LVM**: For even more flexibility, consider adding the disk to an LVM setup.

Let me know if you have questions or need help with further customization!
