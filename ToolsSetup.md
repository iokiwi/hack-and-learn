# Setting Up Kali Linux with Virtualbox

Kali linux is a debian based linux distribution which comes preinstalled with hundreds of tools designed for penetration testing.

While these tools are readily available on most distributions, kali linux is the fastest and easiest way to get started.

The kali images we will use are ready to use, prebuilt, virtual machine images from the official kali website. This saves us the the effort of installing kali from scratch in a virtual machine.


## Steps to install

1. Downlaod and install Oracle Virtualbox
https://www.virtualbox.org/wiki/Downloads

2. Download Kali Linux Virtualbox Appliance
https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/#1572305786534-030ce714-cc3b

4. Import Kali Linux to virtualbox
  - Click **Import**.<br>![0](img/kali-vbox-setup/0.png)
  - Find the downloaded image and click **Open**.<br>![1](img/kali-vbox-setup/1.png)
  - Click **Next**.<br>![2](img/kali-vbox-setup/2.png)
  - Click **Import** to accept the default settings. You can modify settings such as CPU and Memory limits here or you can do it in the settings later.<br>![3](img/kali-vbox-setup/3.png)
  - Wait for the appliance to finish importing.<br>![4](img/kali-vbox-setup/4.png)
  - If you attempt to start the image now you may see the following error message.<br>![5](img/kali-vbox-setup/5.png).<br>In this case, open settings.<br>![6](img/kali-vbox-setup/6.png)
  - Go to 'USB', change the USB controller `USB 1.1 (CHCI) Controller` and click **Ok**.<br>![7](img/kali-vbox-setup/7.png)

5. Start the virtual machine.<br>![8](img/kali-vbox-setup/8.png)

6. Press **&lt;enter&gt;** to select `Kali GNU/Linux` (hightlighed).<br>![9](img/kali-vbox-setup/9.png)

7. If you are presented with a pop up like this Click **Capture**.<br>![10](img/kali-vbox-setup/10.png)

8. Login to the virtual machine<br>
**Username:** `root`<br>
**Password:** `toor`<br>
![11](img/kali-vbox-setup/11.png)

**Welcome to kali linux**

*'The quieter you become, the more you are able to hear"*