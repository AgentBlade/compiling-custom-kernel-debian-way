# Compile a custom kernel "the Debian way"

**[Associated tutorial video](https://www.youtube.com/watch?v=81BORKzvDOw)**

**Build dependencies.**

Install build tools.
Code (as root or sudo):

    apt build-dep linux

After that drop administrative privileges, you don't need to use sudo or the root user.

**Download the kernel source.**

You can either stick with the version that your distro is currently providing by running:
    
    apt install linux-source

The source files will be stored in '/usr/src/'

OR

Visit [kernel.org](https://kernel.org) and download the version you prefer. 


Untar the archive using:

    tar -xf linux-4.x.x.tar.xz

Remove the compressed archive:

    rm -rf linux-4.x.x.tar.xz

**Set up the environment where to compile in.**

(We're using the home directory for convenience).
Create a dedicated directory in the home called “kernel”:

    mkdir /home/$USER/kernel

Move the extracted sources into the newly created directory:

    mv /home/$USER/Downloads/linux-4.x.x /home/$USER/kernel/

Create a symbolic link called “linux” for the source directory within the parent “kernel” directory:

    ln -s linux-4.x.x linux

Enter in it:

    cd linux

Create a configuration file based on the current kernel's one and rename it “.config”:

    cp /boot/config-$(uname -r) .config

**Configure the new kernel.**

run

    make menuconfig

A pseudo-graphical configuration interface will appear.

***Keys are:***

    · Arrow keys to move/select;
    · Y key to set a feature as built in;
    · M key to set a feature as a module;
    · N key to remove a feature;
    · ENTER to interact with the menu;
    · ESC key to perform an “emergency exit”, in case you modify something unwillingly and you don't know how to restore it.

You can choose not to touch anything in order to get a newer version with no effort or learn how to recognize your hardware components [here](https://wiki.gentoo.org/wiki/Hardware_detection).

Save and exit the configuration menu.

**Compile the kernel and create two .deb packages for an easy installation.**

    fakeroot make deb-pkg -j $(($(nproc)+1)) 

***using the menu***

    · The “make-kpkg” command ensures the creation of a binary .deb installer;
    · the “-j” parameter tells the compiler how many threads to perform simultaneously 
      (usually for a faster compilation time we specify the number of virtual and physical cores,
      which you can find out with the “nproc” command, +1), but is up to you;
    · “–initrd” tells the compiler to create a ramdisk for the new kernel;
    · “kernel_headers” and “kernel_image” are the two packages we need to create and install to get a functioning kernel.

Depending on your computational power and number of threads this could take up to 30 minutes.
Take it easy.

**Install the newly compiled kernel:**

do:
   
    cd ..

then as root (or using sudo)
    
    dpkg -i *.deb

Reboot.
