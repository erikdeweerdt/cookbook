# Ubuntu
## Non-snap packages
### Firefox
Available in (official) PPA, so relatively easy:
```bash
sudo add-apt-repository ppa:mozillateam/ppa
echo '
Package: *
Pin: release o=LP-PPA-mozillateam
Pin-Priority: 1001
' | sudo tee /etc/apt/preferences.d/mozilla-firefox
```

### Chromium
Not available in any trustworthy PPA, so get it from Debian.
Below works for Ubuntu 22.04LTS, you may have to use newer Debian versions if you read this in the future.

`/etc/apt/sources.list.d/debian.bullseye.list`:
```
deb [arch=amd64 signed-by=/usr/share/keyrings/debian-bullseye.gpg] http://deb.debian.org/debian bullseye main
deb [arch=amd64 signed-by=/usr/share/keyrings/debian-bullseye.gpg] http://deb.debian.org/debian bullseye-updates main
deb [arch=amd64 signed-by=/usr/share/keyrings/debian-security-bullseye.gpg] http://deb.debian.org/debian-security bullseye-security main
```

`/etc/apt/preferences.d/chromium`:
```
# Note: 2 blank lines are required between entries
Package: *
Pin: release a=eoan
Pin-Priority: 500

Package: *
Pin: origin "deb.debian.org"
Pin-Priority: 300

# Pattern includes 'chromium', 'chromium-browser' and similarly
# named dependencies:
Package: chromium*
Pin: origin "deb.debian.org"
Pin-Priority: 700
```

Import the [archive signing keys](https://ftp-master.debian.org/keys.html):
```bash
gpg --keyserver hkps://keyserver.ubuntu.com --recv-keys '1F89 983E 0081 FDE0 18F3 CC96 73A4 F27B 8DD4 7936'
gpg --keyserver hkps://keyserver.ubuntu.com --recv-keys 'AC53 0D52 0F2F 3269 F5E9 8313 A484 4904 4AAD 5C5D'
gpg --output - --export '1F89 983E 0081 FDE0 18F3 CC96 73A4 F27B 8DD4 7936' | sudo tee /usr/share/keyrings/debian-bullseye.gpg > /dev/null
gpg --output - --export 'AC53 0D52 0F2F 3269 F5E9 8313 A484 4904 4AAD 5C5D' | sudo tee /usr/share/keyrings/debian-security-bullseye.gpg > /dev/null
```
