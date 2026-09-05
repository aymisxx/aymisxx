# Bespin Cloud City Video Wallpaper Setup
## Pop!_OS 24.04 + COSMIC/Wayland + mpvpaper

Username:

```text
agniacolyte
```

Wallpaper video location:

```text
/usr/share/bespin_live_wall.mp4
```

Goal:

- MP4 as animated wallpaper
- Audio enabled
- Infinite loop
- Starts automatically after login
- Runs without terminal
- Restarts automatically if it crashes
- Easy ON/OFF commands for interviews

---

# 1. Update system

```bash
sudo apt update
```

---

# 2. Install required packages

```bash
sudo apt install git meson ninja-build libmpv-dev mpv
```

---

# 3. Download mpvpaper

```bash
cd ~
git clone --single-branch https://github.com/GhostNaN/mpvpaper
```

---

# 4. Build mpvpaper

```bash
cd ~/mpvpaper
meson setup build --prefix=/usr/local
ninja -C build
sudo ninja -C build install
```

---

# 5. Verify installation

```bash
mpvpaper --help
```

---

# 6. Place the wallpaper video in /usr/share

Place your video file here:

```text
/usr/share/bespin_live_wall.mp4
```

The final filename must be exactly:

```text
bespin_live_wall.mp4
```

Verify that it exists:

```bash
ls -lh /usr/share/bespin_live_wall.mp4
```

---

# 7. Test the video normally

```bash
mpv "/usr/share/bespin_live_wall.mp4"
```

Confirm video + audio work.

Close mpv afterward.

---

# 8. Test mpvpaper manually

```bash
mpvpaper -o "loop-file=inf --hwdec=auto" ALL "/usr/share/bespin_live_wall.mp4"
```

Expected:

- Video becomes wallpaper
- Video loops

Press:

```text
Ctrl + C
```

to stop the manual test.

---

# 9. Create systemd user-service directory

```bash
mkdir -p ~/.config/systemd/user
```

---

# 10. Create wallpaper service

```bash
nano ~/.config/systemd/user/mpvpaper.service
```

Replace the entire file with:

```ini
[Unit]
Description=MPV Bespin Cloud City Wallpaper
After=graphical-session.target

[Service]
ExecStart=/usr/local/bin/mpvpaper -o "loop-file=inf --hwdec=auto" ALL /usr/share/bespin_live_wall.mp4
Restart=always
RestartSec=2

[Install]
WantedBy=default.target
```

IMPORTANT:

The entire `ExecStart=` command must remain on ONE LINE.

Save:

```text
Ctrl + O
Enter
```

Exit:

```text
Ctrl + X
```

---

# 11. Reload systemd

```bash
systemctl --user daemon-reload
```

---

# 12. Enable automatic startup

```bash
systemctl --user enable mpvpaper.service
```

---

# 13. Start wallpaper

```bash
systemctl --user start mpvpaper.service
```

Wallpaper + music should start.

The terminal can now be closed.

---

# 14. Verify service

```bash
systemctl --user status mpvpaper.service
```

Expected:

```text
Active: active (running)
```

Press:

```text
q
```

to exit status.

---

# 15. Reboot test

```bash
sudo reboot
```

After logging into Pop!_OS, the wallpaper should start automatically.

```text
PC ON
  ↓
Pop!_OS boots
  ↓
Login
  ↓
COSMIC / Wayland starts
  ↓
systemd --user
  ↓
mpvpaper.service
  ↓
bespin_live_wall.mp4
  ↓
VIDEO + AUDIO + LOOP ∞
```

mpvpaper starts after the graphical user session because it requires the Wayland compositor.

---

# Interview / Meeting Kill Switch

Stop wallpaper + audio immediately:

```bash
systemctl --user stop mpvpaper.service
```

Start it again:

```bash
systemctl --user start mpvpaper.service
```

---

# Optional Easy Commands

Open:

```bash
nano ~/.bashrc
```

Add at the bottom:

```bash
alias walloff='systemctl --user stop mpvpaper.service'
alias wallon='systemctl --user start mpvpaper.service'
alias wallrestart='systemctl --user restart mpvpaper.service'
alias wallstatus='systemctl --user status mpvpaper.service'
```

Save and exit:

```text
Ctrl + O
Enter
Ctrl + X
```

Reload:

```bash
source ~/.bashrc
```

Now:

```bash
walloff
```

stops the wallpaper.

```bash
wallon
```

starts it.

```bash
wallrestart
```

restarts it.

```bash
wallstatus
```

checks service status.

---

# Troubleshooting

Check service status:

```bash
systemctl --user status mpvpaper.service
```

Check logs:

```bash
journalctl --user -u mpvpaper.service -n 30 --no-pager
```

Test the video directly:

```bash
mpv "/usr/share/bespin_live_wall.mp4"
```

Test mpvpaper directly:

```bash
mpvpaper -o "loop-file=inf --audio=yes --hwdec=auto" ALL "/usr/share/bespin_live_wall.mp4"
```

---

# Important Paths

Wallpaper:

```text
/usr/share/bespin_live_wall.mp4
```

mpvpaper:

```text
/usr/local/bin/mpvpaper
```

Service:

```text
/home/agniacolyte/.config/systemd/user/mpvpaper.service
```

---

# Final Commands

Normal use:

```bash
wallon
```

Interview:

```bash
walloff
```

Return to horizon:

```bash
wallon
```

☁️ Bespin Cloud City forever.