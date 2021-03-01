---
title: Install EVE-NG in Google Cloud (2021 Edition)
layout: post
post-image: "/assets/images/posts/2021-02-28-eve-ng-in-gcp/part-1a.png"
description: This blog outlines the process for installing EVE-NG in Google Cloud
  in 15 minutes or less. It is the first post to accompany the video series on Network
  Collective YouTube channel.
tags:
- EVE-NG
- Google Cloud
---

This is an update from the content I released back in 2018. Thanks to the EVE-NG development team this process can be completed in just 15 minutes. I released a video series on Network Collective covering this topic and other EVE-NG Tips and Tricks.

#### Process Summary
1. Login to Google Cloud using your existing Google account or create a new account
2. Create a new project
3. Create the nested virtualization Ubuntu VM image
4. Create a new VM instance using the new image
5. Run the installation script
6. Run the setup wizard

<center><iframe width="560" height="315" src="https://www.youtube.com/embed/sMYtz-bSZLQ" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe></center>

## 1. Log In To Google Cloud
You can use your existing Google or Gmail account or create a new account for google cloud. [https://cloud.google.com](https://cloud.google.com)
If this is your first time Google will give you a $300 free credit. This is more than enough to test this process and practice a bunch of labs.

## 2. Create A New Project

## 3. Create The New VM Image
Navigate to: **Compute Engine --> VM instances** and switch to your new Project from the upper left hand drop down.

**NOTE:** This takes a few minutes.

Once the Compute Engine is ready, click on the **"Launch Cloud Shell"** icon (in the upper right).

Make sure your Cloud Shell session is set to your newly created project. (It should have your project name in the command prompt.)

Enter the command: (this is 1 long command)
```
gcloud compute images create nested-virt-ubuntu --source-image-project=ubuntu-os-cloud --source-image-family=ubuntu-1604-lts --licenses="https://www.google.com/compute/v1/projects/vm-options/global/licenses/enable-vmx"
```
**NOTE:** It could take a few minutes to return to an interactive prompt. Be patient.
You are ready when the shell returns "STATUS READY":
```
user@cloudshell:~ (test-305418)$ gcloud compute images create nested-virt-ubuntu --source-image-project=ubuntu-os-cloud --source-image-family=ubuntu-1604-lts --licenses="https://www.google.com/compute/v1/projects/vm-options/global/li
censes/enable-vmx"
Created [https://www.googleapis.com/compute/v1/projects/test-305418/global/images/nested-virt-ubuntu].
NAME                PROJECT      FAMILY  DEPRECATED  STATUS
nested-virt-ubuntu  test-305418                      READY
user@cloudshell:~ (test-305418)$
```
Now, close the Cloud Shell terminal.

## 4. Create The New VM instace
Click on the "Create" button in the VM instances frame in the center of the screen.

- **Name:** Anything you want (cannot change)
- **Labels:** (optional)
- **Region:** Choose the region nearest you (cannot change)
- **Zone:** (cannot change)
- **Machine Family:** General Purpose
- **Series:** Must choose "Intel Cascade Lake" or "Intel Skylake"
- **Machine Type:** This is going to depend on what you need for labbing. You cannot change this unless you power-off your VM. Just to get started I choose "n2-standard-4".
- **Confidential VM service:** Unchecked
- **Container:** Unchecked
- **Boot Disk:** Choose the image we created from the cloud shell. 
    - It will be under **"Custom images"**:
    - Show images from: ***Your new project name***
	  - **Image:** the image name we created earlier
	  - **Boot Disk Type:** SSD
	  - **Size:** This is going to be determined by you on how you intend to use the VM for labbing. To get started I select 50GB.
- **Identity and API access:** leave default
- **Firewall:** Choose "Allow HTTP traffic"

Click **"Create"** at the bottom.

Wait for your VM to be provisioned, this could a  minute or two.

## 5. Run The Installation Script
Once your VM is provisioned it will automatically be started. Use the built-in SSH feature to connect to the VM's console.

Once you're SSH'd into the VM become root, grab the installation script, update the package manager and upgrade current packages.
```
sudo -i

wget -O - http://www.eve-ng.net/repo/install-eve.sh | bash -i

apt update

apt upgrade
```
Reboot the VM.

## 6. Run The Setup Wizard
Connect to it again via the built in SSH.

You will be presented with a configuration wizard.

# STOP!

When you are greeted with the wizard to enter a root password, **don't!**

Hold **`CTRL`** and press **`C`**, become root **`sudo -i`**

This will restart the wizard and allow you to change root's password.

Follow the initial configuration wizard.

- **Enter Root Password:**
- **Enter Root Password Again:**
- **Hostname:** anything you want (I leave default)
- **DNS Domain Name:** anything you want (I leave default)
- **DHCP/Static IP:** Choose DHCP/Static
- **NTP:** (leave empty)
- **Proxy:** Choose "Direct connection"

After hitting enter, the setup wizard will kick you out.

## 7. You're Finished / What's Next?
At this point the installation is finished. You should have a working EVE-NG server in Google Cloud.

Next, you should:
- Start uploading the images you need for labbing
- Start importing your already created labs
- Start thinking securing the ingress and egress traffic of your EVE-NG lab.
- Check out my other blogs and videos for more EVE-NG: Tips & Tricks