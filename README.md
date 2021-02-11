Fast Xcode installation and update
==================================

If you are using developing iOS application using VMWare on Windows, you probably have experienced the 12-hour process of installing Xcode from the App Store.
 * To make the matter worse, every time iOS is updated, you need to update Xcode to test your app and there is no such thing as an incremental update: **Every update requires a redownload of (currently) 11.66GB.** (Note that, Apple uses the storage unit 1GB = 1,000,000,000 bytes as opposed to the world 1GB = 2^30 bytes.)
 * And downloading is not the end of it since there is another long "Preparing Xcode" post-download
 * And usually, on the first run you could see the _"Verifying Xcode installation"_ dialog that doesn't seem to be progressing. Users have been reporting the dialog might take as long as 20-30 minutes to complete; sometimes, with a restart.

> To this end, we would like to call on Apple to
> **make Xcode more modular** so that we download only what is actually needed, e.g. people who don't need iPhone simulators shouldn't have to download them; likewise, if I am not developing for Apple TV or Apple Watch, I shouldn't be forced to install the SDK.
> And **make update incremental**.
> It is certainly not good for the environment if **millions of developers** have to download many gigabytes frequently, not to count the long running post-download installation process.

And if you install Xcode on a virtual machine, it's best to run the update before you go to sleep.

Thankfully, there is a method to escape this hell:

 1. (~40 minutes, depending on connection speed) Download Xcode (xip file) from [Apple Developer Portal](https://developer.apple.com/download/more/?q=xcode).
Note that there is no need to purchase a full Developer account: You can log in with your Apple ID.

 2. (~5 minutes) Extract the xip file with the excellent [7-zip](https://www.7-zip.org/) command line:
```
"C:\Program Files\7-Zip\7z.exe" x Xcode_12.4.xip -slt -t*
```
Thanks [Rafael](https://sourceforge.net/p/sevenzip/discussion/45798/thread/a83977fdde/) for the idea.
Once you are done, there are 3 files: `Metadata`, `Content` and `[TOC].xml`.

 3. (~20 minutes) Extract the PBZX stream file `Content` using our pbzx stream parser tool (compile yourself using Visual Studio or grab our [prebuilt binary](/release)):
```
PBZXParser.exe -n Content
```
After extraction, you will see a new file `Content.cpio`, roughly 30GB.

 4. (~2 hours) _On your Mac_: Open the **Terminal** and run the command
```
ditto -x Content.cpio /Applications
```
(Running the command with verbosity option `-v -V` takes us 3.5 hours from 4:14pm to 7:48pm.)

Now, we are done!

As you can see, only the final step needs to be done on a Mac; the other steps could be done on a Windows/Linux machine if you are using Mac on a Virtual Machine.

## pbzx stream parser

Modification of Niklas Rosenstein's [pbzx stream parser](https://github.com/NiklasRosenstein/pbzx) to provide Windows support:
 * Add GCC __builtin_bswap64 implementation
 * Disable XAR support
 * Write output to file (instead of standard output) to avoid Windows new line issue with stream redirection

We use the [Unofficial LibLZMA](https://github.com/ShiftMediaProject/liblzma) built for Visual Studio.
Download and copy `lzma.lib` to the `lib/` subfolder and we are ready to build (Release X64 Configuration only) with Visual Studio.
Then copy `lzma.dll` to the executable folder.

Usage:
```
    PBZXParser.exe -n "FileName"
```

## License

Copyright (C) 2020  Lightech
Copyright (C) 2017  Niklas Rosenstein
Copyright (C) 2014  PHPdev32

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <http://www.gnu.org/licenses/>.
