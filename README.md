**Welcome to my Github Page**
*Table of Contents*
1. Creating an Arch Linux Installation
2. Setting up Docker
3. Creating a Wireguard Server


**1. Creating an Arch Linux Installation**

**Downloading The ISO**
1. Download the Arch ISO off of the wiki, in this case use *Adectra*.
2. [Index of /archlinux/iso/2024.10.01/](https://mirror.adectra.com/archlinux/iso/2024.10.01/)

**Creating the VM in Virtualbox**
1. Boot into Virtualbox and select new.
2. Create a name, select the ISO image you downloaded, and for Type select `Linux` and for Version select `Arch Linux (64-bit)`.
3. Select the Hardware dropdown and select the memory and processors you would like on your VM. In this case, used 4096mb of Memory and 4 processors.
4. Next, select the Hard Disk dropdown and select how much hard disk you would like to give your VM. In this case, used 32gb.
5. Make sure you check the `EFI` box here as well.
6. Select `Finish` to save your changes, and then hit start to boot into your new VM.


**Setting Up Your Arch Installation**

1. First, you will need to set your keyboard layout and font, to do so run these commands.
>`loadkeys us'
>`setfont ter-124n'

2. Next, you will need to ensure your Installation has connection to the internet. If you are running a VM on a device with network connection it should already be connected, to test this, run.
> `ip addr show`	
	If you are on your own device, or this is not connected, you can use the `iwctl` command to connect to wireless internet. (This will not work on VirtualBox)

3. Next, you will need to set your systems time and date, to do this run this command.
> `timedatectl`


**Partitioning Your Drive**

1. You will first need to locate you primary drive, in VMs, expect it to be the `sda` drive. To see all your drives, run this command.
> `lsblk`


2. Next you will need to go into *CFdisk*, to start run this.
> `cfdisk` 

3. From here click `GPT`

4. Next, create the partitions. The first will be 100mb, the second will be 4gb, and the third will be everything else. Once this is done, write the partitions and exit `CFDisk`.

5. Now to name and format the partitions. To do this run these commands.
> `mkfs.ext4 /dev/sda3`
> `mkfs.fat -F 32 /dev/sda1`
> `mkswap /dev/sda2`

6. You are also going to need to mount the partitions, to do this run these commands.
> `mount /dev/sda3 /mnt`
> `mkdir -p /mnt/boot/efi`
> `mount /dev/sda1 /mnt/boot/efi`
> `swapon /dev/sda2`


**Installation**

1. Now its time to begin installing things! Important things you are going to want to install are:
> `pacstrap -K /mnt base linux linux-firmware` Others you can add include: `sof-firmware base-devel grub efibootmgr nano networkmanager`

**System Configuration**

1. First, you will need to genersate an `Fstab` file/
> `genfstab /mnt`
> `genfab /mnt > /mnt/etc/fstab`
	You Can check this is correct with:
	`/mnt/etc/fstab`

2. Now you can use the system! you can begin with this:
> `arch-chroot /mnt`
> `ln -sf /usr/share/zoneinfo/"Region"(America)/"City(Chicago)" /etc/localtime`
> `hwclock --systohc`

3. Next it is time to localize your Arch Install. The easiest way to do this will be through `nano`.
> `nano /etc/locale.gen`
	From here, delete the `#` in front of your location, in this case it will be `en_US.UTF-8`
	Then hit `CTRL + O + Enter` and the `CTRL + X` to exit nano.
	Finally, run:
	`locale-gen`

4. Now configure in `locale.conf`
> `nano /etc/locale.conf`
	`lang=en_US.UTF-8`
	Then hit `CTRL + O + Enter` and the `CTRL + X` to exit nano.

5. Next up Hostname.
> `nano /etc/hostname
	`YourName`
	Then hit `CTRL + O + Enter` and the `CTRL + X` to exit nano.

6. Make a root password.
> `passwd` 
> `"YourPassword"`


**Create the Users**

1. You are going to need to create the users for your Arch, to do that follow these steps.
> `useradd -m -G wheel (IF you want them to be an admin) -s /bin/"YourShell" "YourName"`
> `passwd "YourName"`
> If you want the password to have to be changed by the user on next login, run:
> `chage -d 0 "Username"`

2. Next you are going to want to give `sudo` to the users that need it, to do this run this command.
> `EDITOR=nano visudo`
> Once in here, uncomment the section with the group wheel.
> Then hit `CTRL + O + Enter` and the `CTRL + X` to exit nano.


**Rebooting into the System**

1. First, you'll want to enable `NetworkManager`.
> `systemctl enable NetworkManager`

2. Next, you will need to set up your BootLoader
> `grub-install /dev/sda`
> `grub-mkconfig -o /boot/grub/grub.cfg`
> `exit`
> `reboot`


**Graphics**

1. This documentation is going to install KDE Plasma, follow these commands to get it working.
> `sudo pacman -S plasma sddm`
> Press `Enter`, `Enter`, `Enter`
> `sudo  pacman -S konsole kate firefox`
> `sudo systemctl enable --now sddm`


**Installing Zsh**

1. This step is entirely optional, but if you would like Zsh on your system do this.
> `sudo pacman -S zsh`

2. Now, if yuo want to set Zsh as your default shell, run this.
> `chsh -s /bin/zsh`

3. You can switch back using this.
> `chsh -s /bin/bash`


**Setting up SSH**

1. This is important if you want remote access to your machine, first install `openssh`
> `sudo pacman -S openssh`

2. Now you can enable and start `openssh`
> `systemctl enable sshd`
> `systemctl start sshd`

3. You can check status with this command:
> `systemctl status sshd`


**Colors**

1. To add colors across the system, edit the `/etc/bash.bashrc` file. To edit them for one specific user, edit the `home/.bashrc`

2. You might download files online for this step, in which case they might be zipped, to do this, make sure you download ZIP.
> `sudo  pacman -S zip`



**Aliases**

1. This step is, like the colors, entirely customizable. The syntax to set aliases is: `alias "Name"="Command/Directory"`. An example is here, this will make the word `root` switch you to the root user.
> `alias root='su'`


**Congrats!**
And with that, you have finished a simple install of Arch Linux with plenty of bells and whistles!
  
  
  
**2. Setting up Docker**
    

  

**Install Docker and Compose**
*This documentation is for Ubuntu*
1. Install Docker and ensure it has Docker Compose.
> `sudo apt install docker.io`
> `sudo apt update`
> `sudo apt install docker-compose-plugin`

2. Get docker running.
> `sudo service docker start`
> `sudo docker run -d -p 443:443 --name openvas mikesplain/openvas`

3. Go to Openvas
>`On a web browser, go to https://localhost`
>Username and password are both `admin`

4. Congrats, you now have a container running Openvas, feel free to mess around and learn the program!

    
   
     
**3. Creating a Wireguard Server**
1. Setup Your Server
 Sign up for DigitalOcean using this link: [DigitalOcean | Cloud Infrastructure for Developers](https://www.digitalocean.com/?refcode=d33d59113ab6&utm_campaign=Referral_Invite&utm_medium=Referral_Program&utm_source=CopyPaste). This will allow you to get 200 dollars of credit which will be enough to set up wireguard for the context of this lab.  
 Next, you will need to create a droplet, `buy the $6 one, Ubuntu 24.04, Basic, Regular Intel CPU, Normal SSD`. You can also set up SSH or a password, or use the online console for the case of this guide. 
 
2. Get Wireguard set up
Run these commands to get everything ready to run Wireguard.
> `mkdir -p ~/wireguard/
   `mkdir -p ~/wireguard/config/
   `nano ~/wireguard/docker-compose.yml`

3. Set up the `docker-compose file`
After running the pervious commands you have created the file, paste this into Nano in order to get it set up.
*Note: You might need to change your timezone and you will for sure need to change the SERVERURL. You can find the server IP in Digitalocean to paste in this field.*

>version: '3.8'
>services:
  wireguard:
    container_name: wireguard
    image: linuxserver/wireguard
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/Chicago
      - SERVERURL=1.2.3.4
      - SERVERPORT=51820
      - PEERS=pc1,pc2,phone1
      - PEERDNS=auto
      - INTERNAL_SUBNET=10.0.0.0
    ports:
      - 51820:51820/udp
    volumes:
      - type: bind
        source: ./config/
        target: /config/
      - type: bind
        source: /lib/modules
        target: /lib/modules
    restart: always
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    sysctls:
      - net.ipv4.conf.all.src_valid_mark=1

4.  Install Docker
Run these commands to quickly install Docer and docker-compose to finish this lab.
> `sudo apt install apt-transport-https ca-certificates curl software-properties-common -y`
> 
> `curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -`
> 
> `sudo add-apt-repository \
   `"deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   `$(lsb_release -cs) \
   `stable"
   >
> `apt-cache policy docker-ce`
> 
> `sudo apt install docker-ce -y`

5. Install Compose
This is an addon for Docker which is necessary to finish this lab. Run these commands to install it.
> `sudo curl -L "https://github.com/docker/compose/releases/download/1.27.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose`
> 
> `sudo chmod +x /usr/local/bin/docker-compose`

6. Start Wireguard
Now you will need to start up Wireguard!
> `cd ~/wireguard/`
> `docker-compose up -d`

7. Set up Wireguard on a phone
Download the Wireguard app, from here you can scan the QR code given to you by typing in the command.
> `docker-compose logs -f wireguard`
From here you can check `ipleak.net` to make sure your IP switched!

8. Set up Wireguard on a laptop
Download Wireguard from this link: `https://www.wireguard.com/install/`.
From here, download the config file, you can find this file using this command.
>`cd ~wireguard/config/wg_confs`
Now just download the file, put it on Wireguard, and everything should work.

**Congrats, you should have a Wireguard srver going!**
