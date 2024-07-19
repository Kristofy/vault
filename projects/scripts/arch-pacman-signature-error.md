Run as root
```bash
$ rm -R /etc/pacman.d/gnupg/
$ rm -R /root/.gnupg/ 
$ gpg --refresh-keys
$ pacman-key --init && pacman-key --populate archlinux
```
