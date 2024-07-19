Set up ydotool on fedora
```bash
sudo dnf install ydotool
sudo tee /etc/profile.d/ydotool.sh << "EOF" > /dev/null
export YDOTOOL_SOCKET="/tmp/.ydotool_socket"
EOF
sudo mkdir -p /etc/systemd/system/ydotool.service.d
sudo tee /etc/systemd/system/ydotool.service.d/override.conf << EOF > /dev/null
[Service]
Group=input
ExecStart=
ExecStart=ydotoold -P 660
EOF
sudo systemctl daemon-reload
sudo systemctl enable ydotool.service
sudo systemctl restart ydotool.service
sudo usermod -a -G input ${USER}
```
