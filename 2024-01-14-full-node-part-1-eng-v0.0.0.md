# My First Full Node — Bitcoin Base

> This is a series composed of three articles: Bitcoin Base, Lightning Network, and BTCPay Server.

If you've come this far, you've probably already asked yourself:

#### How can I become an individual with a high degree of financial sovereignty in this new technological and economic paradigm that we are living in and helping to build?

One of the advanced steps in understanding and using this system is the creation and management of a full node. Remembering that there are [numerous ways](https://bitcoiner.guide/node/diy/) to run a Bitcoin node, including manually setting it up by downloading the official repositories of the protocols on GitHub. This tutorial can be useful for Jr. developers and technically curious individuals, but who are still beginners in Bitcoin and want to explore the benefits of running full nodes at home for the first time. This solution is especially appealing if you like tinkering with the web5 ecosystem, Nostr, and other decentralized applications.

In this first article, I will share with you a real-life experience on how to create and maintain a complete Bitcoin node. In the second article, we will learn how to make Lightning transactions by directly communicating with your node and its channels, and in the third article, we will reach the point of using it to receive payments for some website on the internet with BTCPay Server.

I've had some experiences setting up and running nodes, with varying levels of success. If there's one piece of advice I wish had been given to me more firmly in the past, it's to stay away from Raspberry Pis. It's subpar hardware. It takes almost 5 days for the Initial Block Download of Bitcoin, that is, that first download and indexing of the entire history of the Bitcoin blockchain since 2009. If you already have a busy life, don't waste time and money on this. It's not worth the wait.

Today, if I were pressed for time but still wanted to set up a node anyway, I would buy directly from the Umbrel website. At the moment it seems to be worth it, I just don't know the situation regarding taxes on this amount.

![node-umbrel-plug-play](https://github.com/biohazel/tech-writing-illustrations/blob/master/node-umbrel-plug-play.jpg?raw=true)

See, I have a Pi 4, look at the performance difference. Feel this pain.

![cpu-performance](https://github.com/biohazel/tech-writing-illustrations/blob/master/cpu-performance.png?raw=true)

At the time, the cost was BRL 3,500, and I was afraid that the federal revenue service would tax my product. So, I decided it would be safer to buy the parts and assemble an equivalent machine.

In my case, when I bought the hardware, I looked at the Umbrel website to see which processor was coming in their mini servers and bought the same. Mine is a Beelink, this one here. 

![mini-pc-beelink](https://github.com/biohazel/tech-writing-illustrations/blob/master/mini-pc-beelink.png?raw=true)

I also bought a 2TB SSD, opened it, and installed it. You need those precision screwdrivers for electronics. The Bitcoin blockchain is one of the things that takes up the most space. Now it's February 2024, and it's approximately ~549 GB.

![beelink-ssd](https://github.com/biohazel/tech-writing-illustrations/blob/master/beelink-ssd.jpg?raw=true)

To start with the software part, we need to prepare a flash drive with the operating system we will use to install Umbrel, which will be Ubuntu Server. Let's go to the Ubuntu website and download an ISO image to record on the flash drive.

An ISO image is a file that contains an exact copy of a file system. In the context of Ubuntu Server, the ISO contains everything necessary to install and run the operating system, preserving the structure and files exactly as they should be on the disk or flash drive.

Recording an ISO image on a flash drive goes beyond simply copying files. It's necessary to write the image in such a way that the flash drive is bootable. This means configuring the flash drive so that the computer can start or boot from it and access the operating system installer.

Let's download Ubuntu Server. Access the official website and download the LTS (Long-Term Support) version.

![ubuntu-server-software](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-software.jpg?raw=true)

Next, it's time to prepare the software we will use to record the image on the flash drive. Programs like Balena Etcher for MacOS automate this process, ensuring that the drive is correctly formatted and prepared to boot the system on your mini PC. If you are using Windows or Linux, you can ask ChatGPT to give you alternatives to Balena Etcher.

Download and install Balena Etcher.

![balena-etcher-1](https://github.com/biohazel/tech-writing-illustrations/blob/master/balena-etcher-1.jpg?raw=true)

Now, insert the flash drive and with Balena Etcher, copy the ISO image to the flash drive.

![balena-etcher-2](https://github.com/biohazel/tech-writing-illustrations/blob/master/balena-etcher-2.jpg?raw=true)
![balena-etcher-3](https://github.com/biohazel/tech-writing-illustrations/blob/master/balena-etcher-3.jpg?raw=true)
![balena-etcher-4](https://github.com/biohazel/tech-writing-illustrations/blob/master/balena-etcher-4.jpg?raw=true)

Device ready, it's time to connect a keyboard and a monitor to the mini PC to access the BIOS and configure a boot from this device.

To access the BIOS (or UEFI, which is the more modern version of BIOS) of a mini PC, you usually need to be physically present and use a keyboard directly connected to the device. This is because the BIOS is accessed during the hardware's boot process, before the operating system or any network software, such as an SSH (Secure Shell) server, is loaded. Therefore, the BIOS operates outside the environment where remote commands or network connections would be available.

Let's make it happen. This is my setup. Since I have this spare TV in the room where the internet modem is, I connected it there. Remember that we will need to connect a network cable to your node.

![setup-hardware](https://github.com/biohazel/tech-writing-illustrations/blob/master/setup-hardware.jpg?raw=true)

Notice that I also have an uninterruptible power supply (UPS). Something important when managing a Lightning node is ensuring uptime health, the least possible offline time during the life of the channels. If you live in a place with many power spikes, it's good to have a UPS and connect your internet and your node to it.

Insert the flash drive with Ubuntu Server into the mini PC and turn it on. Press Del—depending on the machine it could be another key—to enter the BIOS. Mine was already configured to read from the flash drive first.

You need to point Boot Option #1 to your USB drive where the Ubuntu Server ISO is located. This ensures that, upon booting, the system will go through it first and follow the installation instructions of this Operating System.

![ubuntu-server-1](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-1.jpg?raw=true)

Now, save and exit, and it will start by reading the USB drive that I inserted.

![ubuntu-server-2](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-2.jpg?raw=true) 

Now starting...

![ubuntu-server-3](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-3.jpg?raw=true) 

See this sequence of screens, it's quite intuitive. First, it will ask you to press some keys to identify the configuration of your keyboard.

![ubuntu-server-4](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-4.jpg?raw=true) 

![ubuntu-server-5](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-5.jpg?raw=true) 

Now let's install the default Ubuntu Server.

![ubuntu-server-6](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-6.jpg?raw=true) 

![ubuntu-server-7](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-7.jpg?raw=true) 

![ubuntu-server-8](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-8.jpg?raw=true) 

![ubuntu-server-9](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-9.jpg?raw=true) 

![ubuntu-server-10](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-10.jpg?raw=true) 

![ubuntu-server-11](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-11.jpg?raw=true) 

Here we will choose Continue.

![ubuntu-server-12](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-12.jpg?raw=true) 

Time to set your node's credentials. The name you choose in Your server's name is the hostname that will appear linked to its IP in the internal network.

![ubuntu-server-13](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-13.jpg?raw=true) 

Instead of writing down your credentials in a place that can be accessed by others, consider using a secure password manager, which can encrypt and store complex passwords, making it unnecessary to remember each one of them. Bitwarden is an interesting distributed solution, but do your research first.

![ubuntu-server-14](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-14.jpg?raw=true) 

Let's skip Ubuntu Pro.

![ubuntu-server-15](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-15.jpg?raw=true) 

Here, select to install the OpenSSH server, a software that implements the SSH protocol so you can access your node remotely from other machines.

![ubuntu-server-16](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-16.jpg?raw=true) 

Continue following the sequence until it's time to restart. In this case, we won't install anything else. Afterward, Umbrel will take care of it.

![ubuntu-server-17](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-17.jpg?raw=true) 

![ubuntu-server-18](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-18.jpg?raw=true) 

![ubuntu-server-19](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-19.jpg?raw=true) 

Here you can remove your flash drive. I removed it, and even so, it took me to a screen that warns you need to remove it. Either way, it will work out. Remove it and press Enter. Then press Enter again to request your login credentials.

![ubuntu-server-20](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-20.jpg?raw=true) 

![ubuntu-server-21](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-21.jpg?raw=true) 

![ubuntu-server-22](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-server-22.jpg?raw=true) 

All set! Ubuntu Server is installed and ready to be managed remotely.

Let's do the management and installation of Umbrel from afar. I know I did the part with Balena Etcher on a Mac, but now I will operate the rest of the node's configurations from a Linux machine.

We assume that you are already connected via an ethernet cable. Let's find out the IP address of your machine. It's time to look under your router for its own IP address and access credentials. Within the router's system, we will find out the IP of your node. Just type the IP of your router into any web browser and follow the login instructions. It's easy to identify because the name we gave to the node in the first access credentials appears listed among the IPs active on this network. 

![ip-node](https://github.com/biohazel/tech-writing-illustrations/blob/master/ip-node.png?raw=true) 

Now, let's access this node remotely. Open your Terminal (on Linux or macOS systems) or PowerShell (on Windows systems). Try accessing your node using the command:

```ssh usuario@endereco_ip```

Make sure to replace usuario with your actual username and endereco_ip with the IP address of the node you want to access. In my case, it is:

```ssh scalar@192.168.15.55```

![ubuntu-1](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-1.png?raw=true) 

Since I've already had a server setup on this IP in the past, it's identifying a conflict of SSH keys. More specifically, the server's name has changed.

![ubuntu-2](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-2.png?raw=true) 

So, we need to remove the old access key and create a new one. This is one way to remove the key directly. 
 
 ```ssh-keygen -R 192.168.15.55```

Or.

 ```ssh-keygen -f "/home/ubuntu/.ssh/known_hosts" -R "192.168.15.55```

With the key removed, just try to log in again.

![ubuntu-3](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-3.png?raw=true) 

Now is the time to prepare the SSD, format it, and mount it. Then add the mount point to /etc/fstab so that it persists across multiple reboots.

Start with the command: ```lsblk``` 

![ubuntu-4](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-4.png?raw=true) 

In my case, I can see that it is in sdb1. Now let's prepare this disk by formatting the sdb1 partition with ext4.

```sudo mkfs.ext4 /dev/sdb1```

![ubuntu-5](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-5.png?raw=true) 

Done, now let's create the directory that will be the mount point.

```sudo mkdir -p /mnt/umbrel```

Next, perform the mount.

```sudo mount /dev/sdb1 /mnt/umbrel```

![ubuntu-6](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-6.png?raw=true) 

Note the UUID of your partition. To ensure the SSD is automatically mounted at startup, you can add an entry in /etc/fstab.

Use the UUID (specific to your system) to add a line similar to this one in the /etc/fstab file. First, you need to open /etc/fstab with a text editor. In this case, I will use Vim.

```sudo vim /etc/fstab```

Inside Vim, press the letter i to enter INSERT mode, where you can write. Now add the line below.

```UUID=seu-uuid-aqui /mnt/umbrel ext4 defaults 0 2```

```UUID=c4852d3e-6435-45c5-805f-420a959a1568 /mnt/umbrel ext4 defaults 0 2```

![ubuntu-7](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-7.png?raw=true) 

To save, press Esc, then type ```:w``` and ```:q``` in sequence.

Now let's use the command ```sudo mount -a``` to mount all the partitions listed in /etc/fstab that are not yet mounted.

To ensure there were no errors in your /etc/fstab entry, let's check with ```lsblk```.

![lsbk-final](https://github.com/biohazel/tech-writing-illustrations/blob/master/lsblk-final.png?raw=true) 

Now let's locate exactly where the disk is mounted to install Umbrel.

```cd /mnt/umbrel```

Please check the Umbrel website for the current installation guidelines for Linux. There's a new version of their OS coming out in March 2024!

![umbrel-0](https://github.com/biohazel/tech-writing-illustrations/blob/master/umbrel-0.png?raw=true) 

It's time to use the installation command for Linux.

```curl -L https://umbrel.sh | bash```

![ubuntu-8](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-8.png?raw=true) 

And voilá!

![ubuntu-9](https://github.com/biohazel/tech-writing-illustrations/blob/master/ubuntu-9.png?raw=true) 

Now comes the magical moment. Play the music [Umbrella da Ember Island](https://open.spotify.com/intl-pt/album/14qmH55ZpVHTRn67ONh5J6) and type your server's .local name in your browser. In my case, it is:

```scalar.local```

![umbrel-1](https://github.com/biohazel/tech-writing-illustrations/blob/master/umbrel-1.png?raw=true) 

Now fill in with a username and password.

![umbrel-2](https://github.com/biohazel/tech-writing-illustrations/blob/master/umbrel-2.png?raw=true) 

![umbrel-3](https://github.com/biohazel/tech-writing-illustrations/blob/master/umbrel-3.png?raw=true) 

Let's choose our first application, Bitcoin Core. We need it as a prerequisite to install the Lightning protocol on top and the BTCPay Server afterwards.

![umbrel-4](https://github.com/biohazel/tech-writing-illustrations/blob/master/umbrel-4.png?raw=true) 

Now, click install. And there we go!

![umbrel-5](https://github.com/biohazel/tech-writing-illustrations/blob/master/umbrel-5.png?raw=true) 

![umbrel-6](https://github.com/biohazel/tech-writing-illustrations/blob/master/umbrel-6.png?raw=true) 

Now let's wait for the IBD, the initial download of the Bitcoin blockchain. With this processor, it should take about 15 hours. But let's count. This photo of when I started downloading Bitcoin Core 26.0.0 was taken on February 14, 2024, at 7:06 PM. As soon as it finishes downloading, we will continue to part 2 of this article: installing a Lightning node, opening channels, and managing them remotely via Zeus. A word of adoration for Zeus who helped me close my channels and recover my funds when I broke my old node by tinkering too much.

Remember that in this repository there is a translation made with 💖 and ✨ of [Mastering the Lightning Network](https://github.com/biohazel/lnbook-pt-br).

Congratulations on making it this far! Enjoy your sovereignty. ⚡️