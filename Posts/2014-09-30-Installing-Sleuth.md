---
title: Installing Sleuth Kit on Ubuntu
date: 2014-09-30 10:43
---

Time to talk about something about digital forensics! As a graduate student in this area, I think it is very important to try some different tool other than those famous commercial software like FTK or EnCase. Open source software is even greater since it is possible to look at the inside mechanism of digital forensic tools, which is definitely a great learning experience.

[The Sleuth Kit](http://www.sleuthkit.org/index.php) &reg; is such a kind of open source digital forensic tools. It is a set of useful programs designed for basic digital forensic investigation. The main developer of this software is Brian Carrier. One thing worth noting is that he's also the author of File System Forensic Analysis, a great book explaining file systems in details. This book can be found [here](http://www.amazon.com/System-Forensic-Analysis-Brian-Carrier/dp/0321268172/ref=sr_1_1?ie=UTF8&qid=1412053246&sr=8-1&keywords=File+System+Forensic+Analysis).

Below is a my experience about setting up the environment and installing the Sleuth Kit (TSK) in a Debian-based Linux system such as Ubuntu. The actual process may be different on different machines. The operation system I used is Ubuntu 14.04. I hope the difference of OS version and packages won't bring too many troubles.
<!--excerpt-->

**Here I will offer two methods to install TSK: the easy one, and the hard one.** The difference of products by these two methods is version. The easy option #1 installs TJK 2.3.2 automatically. The difficult option #2 installs latest version (which is 4.1.3 at the time of writing). If you just want to try TSK, use the easy option. If you want to use some of TSK's latest features or want to dig deep into its source codes, use the difficult one. You decide.

## Option #1

The simplest way to install is typing command `sudo apt-get install sleuthkit`. The corresponding packages will be located, downloaded and installed automatically.The version of TSK installed with this method is 2.3.2. 

After installation, run `mmls -V`. The message `The Sleuth Kit ver 2.3.2` should appear.

## Option #2
If you want to install the latest version, which is 4.1.3 at the time of writing this post, along with some additional support function, use this one.

Before installing TSK, it is critical to set up basic environments like interpreters and compilers for multiple languages. Recommended languages are C/C++, Java, Python, Perl, Ruby. The following commands are helpful:  
`sudo apt-get install g++ jre python perl ruby`

To obtain TSK, go the [download page](http://www.sleuthkit.org/sleuthkit/download.php). The download link will direct you to sourceforge.net, click the sleuthkit-4.1.3.tar.gz to download the compressed file to a folder. Go to that folder, use `tar xzf sleuthkit-4.1.3.tar.gz` command to extract files to a folder. Right now the folder contains the following files:

![unzipped](http://blob.syang.io/web-images/Pictures/Blog/unzipped.png) 

If you have Linux experience, you should know that installing open source software is very different as installing software on Windows system. In a nutshell, the process has three stages:

./configure --> make --> make install

You can find an detailed explanation of this process [here](http://www.thegeekstuff.com/2012/06/install-from-source/). The INSTALL file inside the folder is also recommended to read.

To start the installation process, use command `./configure`. The ending part of output may look like this:

![nosup](http://blob.syang.io/web-images/Pictures/Blog/nosupport.png)

Here several building supports are missing. Zlib, which is used to handle zipped files, can be install by running `sudo apt-get install zlib1g`. Both afflib and libewf are libraries of formats (AFF and EWF) to store disk images. Adding these two supports is very useful. So it would be a better idea to enable them.

Codes of libewf can be found in [this Google code repository](https://code.google.com/p/libewf/).

Installing libewf requires openssl library. Run `sudo apt-get install libssl-dev`. Then in libewf folder, use `./configure`, `make` and `sudo make install` in order to install the library.

Installing afflib is similar. Codes of afflib can be downloaded via [this link](https://github.com/downloads/simsong/AFFLIBv3/afflib-3.7.1.tar.gz). Once downloaded and unpacked, use the same three commands to install.

The Java/JNI support is not so important so far for command line tools. I just leave it as it is.

Eventually, you can get the `./configure` result as:

![sup](http://blob.syang.io/web-images/Pictures/Blog/sup.png)

When it is ready, the Makefile should be generated in the folder. Run `make` command to compile the whole kit. Be patient, it make take some time. After compilation, use `sudo make install` to finish installation.

To test whether TSK is successfully installed, rum `mmls -V` (mmls is one of the tools in TSK for displaying partition table). The version of TSK should appear:

![suc](http://blob.syang.io/web-images/Pictures/Blog/success.png)

Once installed, the TSK can be used to perform many basic digital forensics exploration. I will post some experience about it in the following articles.

## Thanks for reading!
