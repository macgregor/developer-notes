# Installing Unity on Linux (Fedora 23)
This one wasnt too bad, you will need to create a Unity account and sign in to
use Unity.

## Dependencies
none

## Install
```bash
wget http://download.unity3d.com/download_unity/unity-editor-installer-5.1.0f3+2015091501.sh
sudo bash unity-editor-installer-5.1.0f3+2015091501.sh
mkdir /usr/share/unity-editor
cp -R unity-editor-5.1.0f3/* /usr/share/unity-editor
sudo chmod 4755 /usr/share/unity-editor/Editor/chrome-sandbox
```

add `/usr/share/unity-editor/Editor` to path
run: `Unity`

## Troubleshooting

### libgpg-error starting unity
```bash
./Unity: /usr/share/unity-editor/Editor/Data/Tools/libgpg-error.so.0: no version information available (required by /lib64/libgcrypt.so.20)
./Unity: relocation error: /lib64/libgcrypt.so.20: symbol gpg_err_set_errno, version GPG_ERROR_1.0 not defined in file libgpg-error.so.0 with link time reference
```
Solution: remove `/usr/share/unity-editor/Editor/Data/Tools/libgpg-error.so.0`

### chome-sandbox permission error starting unity
```
Chromium Embedded Framework requires that the file '/usr/share/unity-editor/Editor/chrome-sandbox' is owned by root and has permissions 4755.
```
Solution: `sudo chmod 4755 /usr/share/unity-editor/Editor/chrome-sandbox`
