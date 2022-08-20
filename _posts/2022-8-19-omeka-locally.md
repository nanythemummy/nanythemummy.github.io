---
title: Running Omeka on Your Own Computer
categories: [Digital Humanities]
author: Kea
---

_Psst! Hey, you!_  
_Yes, you._    

 Want to run Omeka on a local server and have your own private archive? Like, maybe you want to learn Omeka but you don't want to be limited by a free trial or Omeka's cloud hosting service? Or maybe you have a bunch of pictures you want to access quickly for your dissertation, but don't have permission or legal rights to put online? Or Maybe you don't want to pony up the money to host an archive that only you are using? Or maybe you are working in the field or on the go and want to have your very own private database that isn't Filemaker? Well, in the last case, you might want to check out Django, Rails, or even Drupal on a similar local web-stack, but today I'm going to talk about how to run Omeka with Apache 2 and Mysql on a Virtual Machine that is running on your desktop or laptop.  
<!--more-->
After hearing a lot about Omeka as a possible solution for building online DH archives without code, I though I'd try it.  However, most of the tutorials I found entailed paying for hosting and setting up the Omeka site after hosting was aquired (Omeka's own site gives a 404 for the installation instructions. *Cry.*) I just wanted to play with the service and didn't want to hand out my credit-card number, so I thought that I could set it up locally. Omeka is marketed as a no-code solution for building digital archives, so I though it would be fairly easy to set up on a local stack.  

 _I was wrong._  

Omeka kind of assumes that you have hosting already, preferably with Omeka's cloud service. Once up and running, Omeka gives you a digital archive with <a href="#asterisk"> dublin-core standard metadata.*</a> You can just add your archive items and enter metadata to your heart's content. You don't need to code. But the catch is that the sysadmin work needed to get it running isn't trivial--it requires the Apache2 webserver and the Mysql database server (rather than nginx and various other db options) as well as Apache's mod-rewrite. It is actually trickier to set up locally than Drupal is.  

It took me a couple of hours to figure out how get it running on a Debian VM, and I figured that my fellow digital humanists would rather not. So I spent today writing a long shellscript to set it all up for you, with the help of Vagrant--a command-line tool for downloading and making virtual machines. You can find my vagrant file and provisioning script  <a href="https://github.com/nanythemummy/omeka_vm"> on my github page</a>. The fact that I got the local instance running at all owes a lot to the <a href="#reeves_cit"> tutorial written by Jonathan Reeve on installing Omeka.</a> Even though Reeves' tutorial doesn't deal with running Omeka on a local server, it gave a lot of valuable advice on which files needed to be tweaked.  

## What is Vagrant, and What is a Virtual Machine? 

VMs are a confusing concept for a lot of people who aren't used to using them. Basically you can think of a *V*irtual *M*achine as a whole new machine that is using your computer's resources to run. Your computer is the host, and the VM is the guest. Using a piece of software called a Hypervisor, some of the RAM, disk-space, networking, etc. of your computer can be cordoned off and made to behave like a second computer, running its own operating system and programs. These have many uses, In fact, many sites on the internet run on VMs or on similar stripped-down programs called "containers." They can also be used for running old, obsolete software. I like to use a Windows 95 VM to play my old games.  
The good thing about running a VM is that your computer is in charge, and the VM is throwaway. So, if you mess something up beyond repair on the VM, you can just delete it and remake it. This makes VMs especially good for developing and isolating new and experimental code from other code with which it might conflict.  These can be made and configured programmatically with the right software and files loaded on the fly. One of the pieces of software which can do this is called Vagrant.  

## Setting up a Virtual Machine Using the Vagrantfile and Provisioining Script
In this tutorial, we will be setting up a VM with Omeka, a web-server (Apache2), and a database (Mysql). To do this, we will need:

* A working internet connection, at least for the installation. After that, it doesn't matter.
* A PC or an older mac with a chipset that isn't Apple Silicon (Virtualbox isn't compatible with Apple Silicon, meaning these instructions won't work with a Mac M1 or M2. VMWare nexus is compatible, but I haven't yet built support for that into the Vagrant instructions-- _I_ have an Intel Mac, so you fancy people will just have to deal.)
* Vagrant, which can be downloaded from the [Vagrant Webpage](https://www.vagrantup.com/downloads "Vagrant") 
* [Virtual Box](https://www.virtualbox.org/) , which can be obtained by giving your contact info to Oracle who will then [resell it to any opressive or shady entity which will pay.](https://www.lawfareblog.com/data-brokers-and-national-security) Remember, lying is a sin unless it's to a databroker.
* The provisioining script and vagrant file, which are  [on my github page](https://github.com/nanythemummy/omeka_vm "Kea's Github") Clone or unzip this into a directory of your choice, but remember where it is. I've saved mine in ~/projects/omeka_vm and that is the directory I'll use in this tutorial. So, if you saved your vagrantfile and script somewhere different, substitute that directory for ~/projects/omeka_vm wherever that occurs.  

Install and set up Virtualbox, and install vagrant with the installer. Make sure you have disk space on your main hard drive. This is going to take ~ 2 gigs. Open up the terminal.  
Before you boot up your machine, you may want to change the name and password which omeka will use to access the database. To do this, open up the file called vagrantfile that you downloaded from my omeka_vm repo. Open it in Notepad on pc or textedit on Mac. Replace the contents of the "" on the first two lines with your prefered username and password. If you would like a newer or particular version of Omeka or Mysql, enter these in the appropriate spaces here as well.
### On Mac 

Click the magnifying glass up in the right hand corner next to the clock and type "terminal" to start the terminal. Then, type:  

    cd ~/projects/omeka_vm
    vagrant up

### On PC 
Click the Windows button and type "command line". Open up the "Windows Command Line". Type:  

    cd $HOME\projects\omeka_vm 
    vagrant up  
 
On some PCs, running VMs (AKA Virtualization) might be disabled in the BIOS of the PC. To enable virtualization, see the [Microsoft instructions for Virtualization on Popular PCs ](https://support.microsoft.com/en-us/windows/enable-virtualization-on-windows-11-pcs-c5578302-6e43-4b4b-a449-8ced115f58e1 "Virtualization Instructions") instructions from Microsoft. Once you've done that and rebooted, try running "vagrant up" again. 

Vagrant will download a template virtual machine running Debian Linux 10. It will run a script on that machine which does the following.

## What the Provisioining Script Does
The provisioining script is a shell script, or set of programmatic instructions, that gets sent over to your virtual machine and run with full permissions once your virtual machine boots. It downloads and configures your omeka installation and your database and webserver. The result is that you will have your own private Omeka installation running on your computer in a virtual machine. Youc an access your Omeka archive through your browser by typing [http://localhost:8070/omeka/install/install.php](http://localhost:8070/omeka/install/install.php "The Install script running on your local server") when the provisioining script finishes.  
When you reboot your computer, you will have to restart the virtual machine by entering the directory ~/projects/omeka_vm via the command line and then running 'vagrant up' again. Note that nobody else outside of your computer will be able to access the site. It will not be available on the internet, but will be availabe without an internet connection for personal use.  
Here are the specifics on what the provisioning script does--what you would have to do manually to get omeka to run on your mac or on your VM if you were to install it by hand. You don't have to know this, but I'm writing it down so if I ever have to do it by hand again, I can reference this document. 

1. Installs various utilities for encryption, downloading and unzipping files: gnupg, wget, and unzip.
2.  Installs the Apache2 webserver. Installs the Apache2 plugin mod_rewrite, and enables it. 
3.  Installs the Php programming language and interpreter and the Apache plugin mod_php, as well as php_xml and php_mysql.
4.  Configure  Mysql's special code repository from the Oracle Mysql site, and install mysql-server from it. 
5. Set up a new database called omeka_db, create the specified username and password for the db, and give them full permissions to use the omeka_db database.
6. Download the version of Omeka specified in the vagrant file to /var/www/http/, which is the directory from which Apache serves web documents. 
7.  Unzip Omeka to the folder /var/www/http/omeka and set the owner to www-data.
8.  Opens the file /var/www/http/omeka/db.ini and configures it to use the username and password specified in the vagrant file for the database omeka_db running on localhost, ie. the database you just set up, running on this very computer.
9. Open the apache config file and allow rewrites for all files and subdirectories in /var/www/http/omeka
10.  Installs the library imagemagick, which is used to generate thumbnails of the pictures in your archive.

## Setting Up your Omeka Site for the First Time
Assuming the provisioining script runs successfully,  fire up your browser and navigate to: [http://localhost:8070/omeka/install/install.php](http://localhost:8070/omeka/install/install.php "The omeka install script running on your own server!") and enter the relevant information about your archive. Create the archive by clicking the button at the bottom of the form. Congratulations! You now have a working local Omeka installation with its own database and webserver!

## Logging into your vagrant box 
If you ever need to log into your vagrant box, you can open the terminal, navigate to the directory where your vagrant file lives (~/projects/omekea_vm) and log in with the following command:  

    vagrant ssh  

You might not ever need to do this, but if you ever want to get your hands dirty with database configuration, or you want to dump your database to move it elsewhere, you may have to do this.

## Cleanup 
If you would like to shut down your vm, you can open the terminal as descripbed above for windows and mac and navigate to ~/projects/omeka_vm/. To shut down the vm, type:

    vagrant halt

You can also open up virtualbox and right click on the vm to pull up a menu from which you can control it. Remember, though, that if you shut down your vm, you will have to restart it to access your Omeka site again. If you would like to destroy your vm along with your omeka site, you can use the terminal to navigate to the directory with your vagrant file, as described above, and you can type:

    vagrant destroy

This will delete your vagrant box and all the stuff installed on it. Goodbye, Omeka.

## References
* ‘Enable Virtualization on Windows 11 PCs’ <a href="https://support.microsoft.com/en-us/windows/enable-virtualization-on-windows-11-pcs-c5578302-6e43-4b4b-a449-8ced115f58e1"> <https://support.microsoft.com/en-us/windows/enable-virtualization-on-windows-11-pcs-c5578302-6e43-4b4b-a449-8ced115f58e1></a> [accessed 20 August 2022]
* Kans, Michael, ‘Data Brokers and National Security - Lawfare’, Lawfare, 2021 <a href="https://www.lawfareblog.com/data-brokers-and-national-security"> <https://www.lawfareblog.com/data-brokers-and-national-security></a> [accessed 20 August 2022]
* <span id="reeves_cit"> Reeve, Jonathan, ‘Installing Omeka’, Programming Historian, 2016 <a href="https://programminghistorian.org/en/lessons/installing-omeka"> <https://programminghistorian.org/en/lessons/installing-omeka> </a> [accessed 20 August 2022] </span>

<div id="asterisk">
* Omeka clains to produce Dublin-Core metadata. However, there seems to be a lot of confusion in the DH lit about what metadata is and what it is for. To say that it is "Data about data" is at best facile, and conceals the multiple ways in which the term is used by people from different backgrounds. The bottom line is that to get the most out of dublin-core, metadata adhering to the standard must be recorded consistently and exposed in the generated HTML/JSON/RDF/XML in such a way that metadata can be interpreted by a program crawling your site. For all the talk of the importance of metadata, few sites actually seem to expose it in a way that would be machine readable without a large ammount of effort and mapping between terms on the part of the person who writes the program which crawls for this data. This is a long post in and of itself, which I may write once I dig a little deeper into how well Omeka actually acheives this goal.
</div>