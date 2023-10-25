# Windows

Windows support is courtesy of [cassowary](https://github.com/casualsnek/cassowarys).

Before you set up windows, you will need to make QEMU run as non-root. To do this, use the preconfigured just runner by running `just qemu-non-root`. Then you can follow below excerpt from [cassowary](https://github.com/casualsnek/cassowary):
### Download Windows .iso image and VirtIO Drivers for Windows

We will need either Windows 7, 8, or 10 Pro, Enterprise or Server to use RDP apps.  
VirtIO driver will improve the VM performance while having lowest overhead.

- Download Windows [10](https://www.microsoft.com/en-us/software-download/windows10) or [11](https://www.microsoft.com/software-download/windows11).

- Download latest VirtIO driver iso images from: [HERE](https://fedorapeople.org/groups/virt/virtio-win/direct-downloads/stable-virtio/virtio-win.iso)

and save them in a convenient location.

### Creating a Virtual Machine

- Open virt-manager from your application menu

- On virt-manager click on **Edit** -> **Preferences**, check **Enable XML editing** then click Close;  
  
  <img src="https://github.com/casualsnek/cassowary/raw/main/docs/img/virt-manager-0.png" alt="virt-manager-0">

- From virt-manager menu bar select **File** and then **New Virtual Machine**;

- On the New VM window select **Local Install media** and click Next;  
  
  <img src="https://github.com/casualsnek/cassowary/raw/main/docs/img/virt-manager-1.png" alt="virt-manager-1">

- Browse and select the Windows 10 iso you downloaded on install media then click Next again;

- Set the CPU cores (2 recommended), Memory (4096 MB recommended) and Disk Space as per your preferences;  
  
  <img src="https://github.com/casualsnek/cassowary/raw/main/docs/img/virt-manager-2.png" alt="virt-manager-2">
  <img src="https://github.com/casualsnek/cassowary/raw/main/docs/img/virt-manager-3.png" alt="virt-manager-3">

- Give a name to your vm such as `Win10` and check **Customize configuration before install** then click on Finish!;  
  
  <img src="https://github.com/casualsnek/cassowary/raw/main/docs/img/virt-manager-4.png" alt="virt-manager-4">

- In the CPU tab make sure **Copy host configuration** is checked;  
  
  <img src="https://github.com/casualsnek/cassowary/raw/main/docs/img/virt-manager-5.png" alt="virt-manager-5">

- Goto XML tab of CPU and replace the section:

```xml
<clock offset="localtime">
    .......
    .......
</clock>
```

with:

```xml
<clock offset="localtime">
  <timer name="hpet" present="yes"/>
  <timer name="hypervclock" present="yes"/>
</clock>
```

- In the Memory tab set the **Current allocation** to **1024**, so the VM won't use 4GiB of memory directly but it will range from 1GiB to 4GiB;  
  
  <img src="https://github.com/casualsnek/cassowary/raw/main/docs/img/virt-manager-6.png" alt="virt-manager-6">

- In the Boot Options tab you could check **Start the virtual machine on host bootup** if you would like the VM to boot automatically at your PC boot;

- In the SATA Disk 1 tab set the **Disk bus** to **VirtIO**;  

> If you are using Windows 7 skip this step as VirtIO is not supported.

<img src="https://github.com/casualsnek/cassowary/raw/main/docs/img/virt-manager-7.png" alt="virt-manager-7">

- Move over to NIC section and set **Device model** to **virtio**;  
  
  <img src="https://github.com/casualsnek/cassowary/raw/main/docs/img/virt-manager-8.png" alt="virt-manager-8">

- Click on **Add hardware** at the bottom left, select **Storage** then choose **Select or Create custom storage**; click on **Manage**, browse and select the downloaded virtio-win driver iso. Finally set the device type to **CDROM** and click on Finish;  
  
  <img src="https://github.com/casualsnek/cassowary/raw/main/docs/img/virt-manager-9.png" alt="virt-manager-9">

- Click **Begin Installation** on top left;

- Follow the installation instructions for Windows 10 and when choosing a Custom installation you will get no drives to install Windows 10 on. To make the VirtIO drive works you will have to click on **Load Driver**, then choose **OK** and finally select the driver for Windows 10;

> If no drivers are loaded or shown, let Windows search for them inside the `amd64` folder of the VirtIO disk.

<img src="https://github.com/casualsnek/cassowary/raw/main/docs/img/virt-manager-10.png" alt="virt-manager-10">

- After that your drive will show and you can continue like a normal Windows 10 installation;
- After some time you will get to "Let's connect to internet page", click on **I don't have internet** at bottom left and continue with limited setup;
- Set your username and password. The Password is not allowed to be blank;
- After you get to Windows 10 desktop open This PC and browse to virtio-win CD drive and install **virtio-win-gt-x64.exe**;
- It's also suggested to install the [spice guest tools](https://www.spice-space.org/download/windows/spice-guest-tools/spice-guest-tools-latest.exe) to also enable copy-paste between host and guest;
- Shut down the VM and from the menubar select **View** and then **Details**;
- Go to Display Spice section and set **Listen Type** to **None**; also check the OpenGL option and click Apply;
- Go to Video QXL section and set **Model** to **VirtIO** and check the 3D acceleration option;

> If you get a black screen after those changes, revert those changes. This could happen with nvidia graphics card;

- Start the VM by clicking the play button on the top left (you may need to click the Monitor icon to show the VM screen ). Login to desktop;
- Open up edge and browse to this page and continue the instructions for installing cassowary.

## On Windows (guest)

- Open Settings, click on System, scroll to bottom and click on Remote desktop then click on Enable Remote Desktop and click confirm!
- Open this page and download latest .zip from the [cassowary release page](https://github.com/casualsnek/cassowary/releases/)
- Extract the downloaded .zip file
- Double click on `setup.bat` located on extracted directory
- Logout and login again to windows session
- After you have logged in continue the instructions below

## On Linux (host)

Here we will be using Arch Linux. You can easily find equivalent commands for your Linux distro.

- Go to the [cassowary release page](https://github.com/casualsnek/cassowary/releases/) and download latest `.whl`;  
- Open terminal on the location where you downloaded the `.whl` file;  
- Install `python3`, `python3-pip`, `freerdp2`, `libvirt-python3` packages and dependencies by running following commands on terminal:

```bash
$ sudo pacman -S python3 python3-pip freerdp libvirt-python
$ pip3 install PyQt5
```

- Install the downloaded `.whl` file by running:

```bash
$ pip install cassowary*
```

> If you get any warning about the folder `/home/$USER/.local/bin` not in your PATH, you can easily add it by adding it to your `$HOME/.profile` or `$HOME/.bash_profile`:
> 
> ```
> echo "PATH=\$PATH:$HOME/.local/bin" >> $HOME/.profile
> ```

- Launch cassowary configuration utility with:

```bash
$ python3 -m cassowary -a
```

- Head over to Misc tab and click on **"Setup Autostart"** and **"Create"**. This will bring cassowary to your application menu and setup background service autostart;
- Enter the VM name from the VM setup step; in this case `Win10`;
- Click on "Save changes" and then on **"Autodetect"**, this should automatically fill up the VM IP;
- Click "Save changes" again then click **"Autofill host/username"** then enter the password you set during the windows setup. Then click "Save changes" again;
- Now goto **"Guest app"** tab and create shortcut for any application you want.

Now you can find application on your application menu which you can use to launch apps easily
You can explore other commandline usage of cassowary by running:

```bash
$ python -m cassowary -h
```
For advanced configuration or FAQ, check the [configuration page of cassowary](https://github.com/casualsnek/cassowary/blob/main/docs/3-faq.md).