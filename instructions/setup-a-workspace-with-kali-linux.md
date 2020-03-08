# Setting up a workspace with Kali Linux and Virtualbox

Kali linux is a debian based linux distribution which comes preinstalled with hundreds of tools designed for penetration testing.

While these tools are readily available for installation on most linux istributions, kali linux comes pre installed with all of the most common tools and is the fastest and easiest way to get started.

The kali images we will use are ready to use, prebuilt, virtual machine images from the official kali website.

## Steps to install

Download and install Oracle Virtualbox<br>
https://www.virtualbox.org/wiki/Downloads

Download a Kali Linux Virtualbox Appliance<br>
https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/#1572305786534-030ce714-cc3b

Import Kali Linux to virtualbox 
  - Click **File** > **Import**.<br>![0](img/kali-vbox-setup/0.png)
  - Find the downloaded image and click **Open**.<br>![1](img/kali-vbox-setup/1.png)
  - Click **Next**.<br>![2](img/kali-vbox-setup/2.png)
  - Click **Import** to accept the default settings. You can modify settings such as CPU and Memory limits here or you can do it in the settings later.<br>![3](img/kali-vbox-setup/3.png)
  - Wait for the appliance to finish importing.<br>![4](img/kali-vbox-setup/4.png)

Open the settings for the VM by selecting the VM and clicking **Settings**<br>![6](img/kali-vbox-setup/6.png)

Go to 'USB', change the USB controller to `USB 1.1 (OHCI) Controller` and click **Ok**.<br>(otherwise you will get [this error](img/kali-vbox-setup/5.png))![7](img/kali-vbox-setup/7.png)

Start the virtual machine.<br>![8](img/kali-vbox-setup/8.png)

Press **&lt;enter&gt;** to select `Kali GNU/Linux` (hightlighed).<br>![9](img/kali-vbox-setup/9.png)

If you are presented with a pop up like this Click **Capture**.<br>![10](img/kali-vbox-setup/10.png)

Login to the virtual machine. The default username and password depends on the when you downloaded Kali and what the latest version was at the time.

Version|Username|Password|Note|
|---|---|---|---|
|Kali Rolling (2019.4) x64|`root`|`toor`|Root user - you do not need to use `sudo` command for anything|
|Kali Rolling (2020.1) x64|`kali`|`kali`|Standard privileged user - become root using `sudo -s`|

![11](img/kali-vbox-setup/11.png)


**Welcome to kali linux**

>"The quieter you become, the more you are able to hear"


## Next Steps

 * [Setup a nat network](setup-nat-network.md) and configure VM to use it.
 * [Enable the eth0 interface for Kali Linux](kali-enable-eth0.md)
