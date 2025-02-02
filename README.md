# Guide to install orbisdev and how to use it with orbislink

## Requirements

To install the orbisdev toolchain, you must meet the following requirements:



  Windows Subsystem for Linux (Debian/Ubuntu):

  ```bash
  wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb
  sudo dpkg -i packages-microsoft-prod.deb
  sudo apt update
  sudo apt install git gcc llvm libnfs-dev vim libnfs-utils texinfo wget patch cmake clang m4 flex bison build-essential dotnet-runtime-3.1 dotnet-sdk-3.1
  ```

- You will need `libScePigletv2VSH.sprx` and `libSceShaccVSH.sprx` libraries. You can get them by loading RetroArch R4 and download the libraries from the `sce_module` folder mounted in the sandbox through FTP.

- An NFS server: explained later for windows ...

- Have orbislink.pkg installed: [download](https://github.com/orbisdev/orbisdev-orbislink/raw/master/pkg/IV0003-BIGB00004_00-ORBISLINK0000000.pkg) (no need to repackage the .pkg, it's universal)

* A modified version of `orbislink_config.ini` with the NFS address and IP for debugnet. You can get it from [here](https://github.com/orbisdev/orbisdev-orbislink/blob/master/pkg/orbislink_config.ini).

## Installation of the SDK

1. Add the following variable and the bin folder to your login script (i.e ~/.bashrc)

   ```bash
   export ORBISDEV=~/orbisdev
   export PATH=$ORBISDEV/bin:$PATH
   ```

   To do this automatically you can run:

   ```bash	
   echo "export ORBISDEV=~/orbisdev" | tee -a ~/.bashrc && echo 'export PATH=$ORBISDEV/bin:$PATH' | tee -a ~/.bashrc && source ~/.bashrc
   ```

2. Clone the repository:

   ```bash
   git clone https://github.com/orbisdev/orbisdev ~/orbisdev
   ```

3. Start the installation:

   ```bash
   cd ~/orbisdev && chmod +x build-all.sh && bash build-all.sh
   ```

   In my case, I ran it on Ubuntu 20.04 with an Intel i9 9900k and it took me about 5 minutes, it's a quick install :stuck_out_tongue:

## Set up the NFS server in Windows

contains socat and nfs server for windows.
Extract the files in "C:/"
![image](https://user-images.githubusercontent.com/17991404/146659053-bdf35446-60e0-4d72-8ddc-38af918d4f33.png)

## Compiling a example

1. Clone any example. In my case, I'll use masterzorag's example which you can download from [here](https://github.com/masterzorag/orbisdev-samples)

2. Compile it following these steps:

   ```bash
   git clone https://github.com/masterzorag/orbisdev-samples
   cd orbisdev-samples/basic
   make
   make oelf
   make eboot
   # optional: just if u wanna make it pkg
   make pkg_build
   ```

   Known issues: if you get an error during the `make eboot` step check, if you're using Python 2.7. In case you aren't, follow this extra step:

   ```bash
   sudo apt install python 2.7
   sed -i 's/python/python2.7/g' Makefile
   ```

3. Done. You compiled your first example.
   In case u wanna use it with orbislink, remember to move the `homebrew.self` into the root of your NFS shared dir!

## Installation of orbislink and loading a test app

1. Download orbislink.pkg from orbisdev [repository](https://github.com/orbisdev/orbisdev-orbislink/blob/master/pkg/IV0003-BIGB00004_00-ORBISLINK0000000.pkg).

2. Modify `orbislink_config.ini` for your setup. Leave it in an accessible place since we will use it later. You can download it from [here](https://github.com/orbisdev/orbisdev-orbislink/blob/master/pkg/orbislink_config.ini). 

   Following the upper NFS server installation, an example for it'd be like this:

   ```ini
   [debugnet]
   serverIp=192.168.0.2
   debugPort=18194
   level=3
   [orbisNfs]
   enabled=1
   nfsurl=nfs://192.168.0.2/share

   ```

3. Check if you have following files to your shared directory:

   ```
   C:\NFS\share
   ├── homebrew.self
   ├── libScePigletv2VSH.sprx
   └── libSceShaccVSH.sprx
   ```

   - In case you're missing `libScePigletv2VSH.sprx` or `libSceShaccVSH.sprx`, check [this requirement](https://github.com/OpenPS4/guide-to-install-orbisdev#requirements)
   - In case you're missing the `homebrew.self`, check [this step](https://github.com/OpenPS4/guide-to-install-orbisdev#compiling-a-example)

4. Load HEN with the patches mentioned in the requirements and install the .pkg.

5. Have a socat session ready to listen the debugnet (Download in the Github). Examples:

   ```
   Execute Start NFS.bat
   Execute Start SOCAT.bat
   ```

4. Open orbislink app and send the config to it using this command: 

   ```bash
   # we send the config to orbislink. Remember to be in the same folder as the config is!!
   socat -u FILE:orbislink_config.ini TCP:YOURPLAYSTATIONIP:18193
   ```

5. If it was correct, after sending the config you should have had some return in the debugnet and the application should have started.

   In case you used the example we compiled above, you should get a beep. If after the first beep you get three beeps, that means something went wrong.
   By pressing the different buttons you will see in the debugnet that you get a return that they have been pressed. If you hit the triangle, you will exit the application.

   ** Remember that you must rerun the debugnet session after it loads since the socket is closed and another is opened **
   
## Contributors

This guide could not be possible without the work of:
- psxdev aka bigboss
- hitodama
- flatz
- maxton
- masterzorag
- fjtrujy
- frangar
- The rest of OrbisDev team
- Many more I'm missing!
