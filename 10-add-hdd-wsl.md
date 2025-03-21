# Add HDD to WSL

## GET-CimInstance -query "SELECT * from Win32_DiskDrive"
## wsl.exe --mount \\.\PHYSICALDRIVE1 --bare
## mount /<drive> (check with sudo fdisk) /<mount path>
