# Linux GUI Setup Guide (WSL2)

This guide provides a comprehensive, professional walkthrough for setting up a lightweight **Linux GUI environment** using **XRDP** and **XFCE4**, enabling secure **Remote Desktop Access** to your Linux system.

---

## 📋 Prerequisites

- A running Ubuntu/Debian-based Linux server
- Root or sudo privileges
- Stable internet connection

---

## 📦 Step 1: Install XRDP and XFCE4

Update packages and install XRDP with XFCE4 (a lightweight desktop environment):

```bash
sudo su
apt update && apt upgrade -y
apt install -y xrdp xfce4
```

---

## ⚙️ Step 2: Configure XRDP Server

### Backup the default XRDP config:
```bash
cp /etc/xrdp/xrdp.ini /etc/xrdp/xrdp.ini.bak
```

### Modify configuration:

- Change port from `3389` to `3390`
- Increase color depth to `128 bpp`

```bash
sed -i 's/3389/3390/g' /etc/xrdp/xrdp.ini
sed -i 's/max_bpp=32/#max_bpp=32\nmax_bpp=128/g' /etc/xrdp/xrdp.ini
sed -i 's/xserverbpp=24/#xserverbpp=24\nxserverbpp=128/g' /etc/xrdp/xrdp.ini
```

---

## 🖥️ Step 3: Set XFCE as the Default Session

Set XFCE4 as the default session for XRDP:

```bash
echo xfce4-session > ~/.xsession
```

---

## 🛠️ Step 4: Edit XRDP Startup Script

Edit the `startwm.sh` file to launch XFCE:

```bash
nano /etc/xrdp/startwm.sh
```

Replace the content with:

```bash
# published under The MirOS Licence

# Rely on /etc/pam.d/xrdp-sesman using pam_env to load both
# /etc/environment and /etc/default/locale to initialise the
# locale and the user environment properly.

if test -r /etc/profile; then
        . /etc/profile
fi

if test -r ~/.profile; then
        . ~/.profile
fi

#test -x /etc/X11/Xsession && exec /etc/X11/Xsession
#exec /bin/sh /etc/X11/Xsession
#xfce
startxfce4
```

### Save and Exit:
- `Ctrl + S` → Save changes  
- `Ctrl + X` → Exit nano

---

## 🔎 Step 5: Verify Network & Ports

### Check server IP:
```bash
hostname -I
```

### Confirm XRDP is listening:
```bash
sudo netstat -tulpn | grep xrdp
```

Expected Output:
```
tcp6       0      0 ::1:3350                :::*                    LISTEN      <PID>/xrdp-sesman
tcp6       0      0 :::3390                 :::*                    LISTEN      <PID>/xrdp
```

---

## 🖧 Step 6: Connect Using Remote Desktop

On your client (Windows/macOS/Linux), open **Remote Desktop Connection** or **Remmina**, and use:

```
Username: [Your Linux Username]
Password: [Your Linux Password]
```

---
## 🧯 Troubleshooting Tips (Extra)

- 🛑 If the session closes immediately, ensure:
  - XRDP and XFCE4 are properly installed
  - `.xsession` contains only `xfce4-session`
- 🔁 Restart XRDP if you update configuration:
  ```bash
  sudo systemctl restart xrdp
  ```
- 🔒 Disable firewall temporarily to test:
  ```bash
  sudo apt  install ufw -y
  sudo ufw disable
  ```

---

## ✅ Secure XRDP with UFW

```bash
sudo ufw allow 3390/tcp
sudo ufw enable
```
---

## 📚 References

- [XFCE](https://xfce.org/)
- [Ubuntu](https://help.ubuntu.com/)
- [Linux Kernel](https://docs.kernel.org/)
- [XRDP Official Code](https://github.com/neutrinolabs/xrdp)
- [Remote Desktop Docs](https://support.microsoft.com/en-us/windows/how-to-use-remote-desktop-5fe128d5-8fb1-7a23-3b8a-41e636865e8c)
- [Windows Subsystem for Linux (WSL2)](https://learn.microsoft.com/en-us/windows/wsl/)

---

© 2025 | Linux Remote Access Guide  
Author: [sadbinsiddique](https://github.com/sadbinsiddique)
