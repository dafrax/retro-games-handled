# Download Google Drive Shared Folders with rclone

Download large folders from Google Drive **Shared with me** using `rclone` on Linux.

This method is useful when downloading a large folder directly from the Google Drive web interface repeatedly results in `Canceled`.

## Supported Linux Distributions

- RHEL
- Rocky Linux
- AlmaLinux
- CentOS Stream
- Debian
- Ubuntu

## 1. Install rclone

### RHEL / Rocky Linux / AlmaLinux / CentOS Stream

Install the required packages:

```bash
sudo dnf install -y curl unzip
```

Install the latest rclone:

```bash
curl https://rclone.org/install.sh | sudo bash
```

For older RHEL-based systems using `yum`:

```bash
sudo yum install -y curl unzip
curl https://rclone.org/install.sh | sudo bash
```

Verify:

```bash
rclone version
```

### Debian / Ubuntu

```bash
sudo apt update
sudo apt install -y rclone
```

Verify:

```bash
rclone version
```

## 2. Create a Google Drive Remote

Run:

```bash
rclone config
```

Select:

```text
n
```

Enter a name:

```text
name> gdrive_roms
```

Select **Google Drive** as the storage type.

For example:

```text
Storage> 18
```

### Client ID

Leave it empty:

```text
client_id>
```

Press `Enter`.

### Client Secret

Leave it empty:

```text
client_secret>
```

Press `Enter`.

### Scope

Select full access:

```text
scope> 1
```

### Service Account

Leave it empty:

```text
service_account_file>
```

Press `Enter`.

### Advanced Config

Select:

```text
n
```

### Browser Authentication

If rclone is running on a Linux desktop with a browser:

```text
y
```

A browser will open.

Sign in with the Google account that has access to the shared folder and authorize rclone.

### Shared Drive

If the folder is a normal folder under **Shared with me**, select:

```text
n
```

Only select `y` if the folder is actually located in a Google Workspace Shared Drive.

### Save the Remote

When prompted:

```text
Keep this remote?
y) Yes this is OK
e) Edit this remote
d) Delete this remote
```

Select:

```text
y
```

Exit:

```text
q
```

## 3. Find Folders Shared with You

List folders shared with your Google account:

```bash
rclone lsd gdrive_roms: --drive-shared-with-me
```

Example:

```text
          -1 2023-10-16 17:50:27        -1 RG35XX-Dual system20231014
```

## 4. Check the Folder Contents

For example:

```bash
rclone lsd "gdrive_roms:RG35XX-Dual system20231014" \
    --drive-shared-with-me
```

Example output:

```text
          -1 2023-07-03 20:23:04        -1 TF2 128G ROM
          -1 2023-07-03 18:22:55        -1 system + 64G ROM
```

## 5. Download the Entire Folder

Go to the destination directory:

```bash
cd /path/to/destination
```

For example:

```bash
cd /media/dfxpc/ARSIP/ROMS/RG35XX
```

Download the entire folder:

```bash
rclone copy "gdrive_roms:RG35XX-Dual system20231014" . \
    --drive-shared-with-me \
    -P \
    --transfers 4 \
    --checkers 8
```

The directory structure will be preserved:

```text
RG35XX/
├── TF2 128G ROM/
└── system + 64G ROM/
```

## 6. Command Options

| Option | Description |
|---|---|
| `rclone copy` | Copy files from Google Drive |
| `gdrive_roms:` | Configured Google Drive remote |
| `.` | Current local directory |
| `--drive-shared-with-me` | Access files shared with your Google account |
| `-P` | Display download progress |
| `--transfers 4` | Download up to 4 files simultaneously |
| `--checkers 8` | Use 8 concurrent file checkers |

## 7. Stop the Download

Press:

```text
Ctrl + C
```

This stops the current rclone process.

Already downloaded files remain on disk.

## 8. Resume the Download

Run the same command again:

```bash
rclone copy "gdrive_roms:RG35XX-Dual system20231014" . \
    --drive-shared-with-me \
    -P \
    --transfers 4 \
    --checkers 8
```

rclone will skip files that are already present and match the source.

This allows large downloads to be resumed after:

- Network interruptions
- Terminal interruptions
- Reboots
- Manually stopping the process

## 9. For an Unstable Connection

Use fewer simultaneous transfers and more retries:

```bash
rclone copy "gdrive_roms:RG35XX-Dual system20231014" . \
    --drive-shared-with-me \
    -P \
    --transfers 2 \
    --checkers 8 \
    --retries 10 \
    --low-level-retries 20
```

For large ROM collections, `--transfers 2` can be a good starting point.

## 10. Check Remote Folder Size

```bash
rclone size "gdrive_roms:RG35XX-Dual system20231014" \
    --drive-shared-with-me
```

## 11. Check Local Folder Size

```bash
du -sh .
```

Or:

```bash
du -sh *
```

## 12. Verify the Download

Compare the local files against the Google Drive source:

```bash
rclone check \
    "gdrive_roms:RG35XX-Dual system20231014" \
    . \
    --drive-shared-with-me
```

This can identify files that are missing or different.

## 13. Quick Start

Once the Google Drive remote has been configured:

```bash
cd /path/to/destination

rclone copy "gdrive_roms:RG35XX-Dual system20231014" . \
    --drive-shared-with-me \
    -P \
    --transfers 4 \
    --checkers 8
```

Stop the download:

```text
Ctrl + C
```

Resume the download by running the same command again.

## References

- [rclone Installation](https://rclone.org/install/)
- [rclone Google Drive](https://rclone.org/drive/)
- [rclone Documentation](https://rclone.org/docs/)
- [Google Drive — Shared with me](https://support.google.com/drive/answer/2375057)