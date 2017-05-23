---
layout: post
title: Create password-protected folders in OSX
tags:
- Tips
---

Actually, you can’t set password for folder in Mac OSX. But there is a workaround to protect you files/folders, it’s
creating a password-protected disk image.

A disk image is a single file (.dmg) that can contains files and folders. When mounted, it becomes a folder in your
filesystem, and you can manipulate it like normal folder. You can imagine it’s just a USB disk.

Here is how to create and set password for that disk image file.
1. Open `Disk Utility` (located in `/Applications/Utilities/`). Click the `New Image` button, and input these information:
- `Save As`: set the name you want for the disk image (.dmg) file
- `Where`: set the path to where you want to keep disk image
- `Size`: maximum size of disk image. It will be the limitation of files in your disk image, so set it large enough.
- `Encryption`: choose 128-bit AES encryption (and/or 256-bit AES in Mac OS X v10.5 or later). **Important:** you must
choose an encryption in order to set password for your disk image.
2. Click the `Create` button.
3. Set password in the dialog window that appears. **Important:** uncheck *"Remember password (add to keychain)"* option.
If it’s checked, your content will be revealed. If you forget the password, data stored in the encrypted disk image
cannot be retrieved.
4. Click OK.

That’s it, your password-protected storage is ready to used. To open the password-protected folder, just double click
the created disk image file (.dmg), and enter the password. It will be mounted as an disk drive, in left panel in
Finder. All your content are there.

Remember to eject that disk after finished working.