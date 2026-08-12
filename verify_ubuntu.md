# How to verify an Ubuntu .iso file

Since you could be downloading Ubuntu on any machine, we cannot assume the instructions will be for a Linux user. Choose your operating system from the list below and follow the instructions.

## Linux users

```
wget https://releases.ubuntu.com/26.04/SHA256SUMS
wget https://releases.ubuntu.com/26.04/SHA256SUMS.gpg
gpg --keyid-format long --keyserver hkp://keyserver.ubuntu.com --recv-keys 0x46181433FBB75451 0xD94AA3F0EFE21092
gpg --keyid-format long --verify SHA256SUMS.gpg SHA256SUMS
sha256sum -c SHA256SUMS 2>&1 | grep OK
```

## MacOS users

Download gnupg with brew

`brew install gnupg`

```
curl -O https://releases.ubuntu.com/26.04/SHA256SUMS
curl -O https://releases.ubuntu.com/26.04/SHA256SUMS.gpg
gpg --keyid-format long --keyserver hkp://keyserver.ubuntu.com --recv-keys 0x46181433FBB75451 0xD94AA3F0EFE21092
gpg --keyid-format long --verify SHA256SUMS.gpg SHA256SUMS
shasum -a 256 -c SHA256SUMS 2>&1 | grep OK
```

## Windows users

install [gpg4win](https://gpg4win.org)

```
curl.exe -O https://releases.ubuntu.com/26.04/SHA256SUMS
curl.exe -O https://releases.ubuntu.com/26.04/SHA256SUMS.gpg
gpg --keyid-format long --keyserver hkp://keyserver.ubuntu.com --recv-keys 0x46181433FBB75451 0xD94AA3F0EFE21092
gpg --keyid-format long --verify SHA256SUMS.gpg SHA256SUMS
certUtil -hashfile ubuntu-26.04-desktop-amd64.iso SHA256
```

eyeball that hash against the matching line in SHA256SUMS...open it with

`type SHA256SUMS`

