# MegaSync-Alternative: Mounting Mega.nz with Rclone on Incompatible Systems

## Description
This guide will show you how to use `rclone` to mount your Mega.nz account as a local directory on Linux systems where `MegaSync` is unsupported or not functioning correctly. This allows you to access Mega.nz files directly from the local filesystem and makes file synchronization straightforward.

## Prerequisites
- A Linux system (this guide has been tested on Rocky Linux 9 but should work on other distributions).
- A Mega.nz account.
- Administrative privileges (for configuring automatic mounts at boot).

## Installing Rclone

1. **Update repositories and install Rclone:**
    ```bash
    sudo dnf install rclone
    ```

2. **Configure Rclone for Mega.nz:**
    - Start the `rclone` configuration:
      ```bash
      rclone config
      ```
    - Select `n` to create a new remote.
    - Provide a name for your remote (e.g., `mega`).
    - Choose `mega` as the storage type.
    - Enter your Mega.nz credentials.
    - Follow the on-screen instructions to complete the configuration.

## Manual Mounting of Mega.nz

1. **Create a mount directory:**
    ```bash
    mkdir -p ~/megamount
    ```

2. **Mount the Mega.nz directory:**
    ```bash
    rclone mount mega:/ ~/megamount --daemon
    ```

   You can now access Mega.nz files in the `~/megamount` directory.

## Automating Mounting at Boot

### Option 1: Using `/etc/rc.local`

1. **Edit the `/etc/rc.local` file:**
    ```bash
    sudo nano /etc/rc.local
    ```

2. **Add the mount command before the `exit 0` line:**
    ```bash
    rclone mount mega:/ /home/$USER/megamount --daemon --allow-other
    ```

3. **Make `/etc/rc.local` executable:**
    ```bash
    sudo chmod +x /etc/rc.local
    ```

### Option 2: Creating a `systemd` Service

1. **Create a service file for Rclone:**
    ```bash
    sudo nano /etc/systemd/system/rclone-mega.service
    ```

2. **Insert the following content into the file:**
    ```ini
    [Unit]
    Description=Mount Mega.nz using Rclone
    After=network-online.target

    [Service]
    User=<your-username>
    ExecStart=/usr/bin/rclone mount mega:/ /home/<your-username>/megamount --daemon --allow-other
    ExecStop=/bin/fusermount -u /home/<your-username>/megamount
    Restart=on-failure

    [Install]
    WantedBy=multi-user.target
    ```

3. **Reload `systemd` and enable the service:**
    ```bash
    sudo systemctl daemon-reload
    sudo systemctl enable rclone-mega
    ```

4. **Start the service immediately (optional):**
    ```bash
    sudo systemctl start rclone-mega
    ```

## Manually Unmounting the Directory

If you need to unmount the Mega.nz directory manually, run:

```bash
fusermount -u ~/megamount
```

***If this guide has been helpful to you, consider supporting my work with a donation. You can donate using the following methods:***
```bash
Lightning Network (LN): asyscom@sats.mobi
Bitcoin (BTC): bc1qcsqgmch9mr76w9ngaxys70xssjmgspvv687j37
```
