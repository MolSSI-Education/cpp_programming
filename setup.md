---
title: Setup
---

# Setup
Following this tutorial will require use of your own personal computer or laptop and installation of some software.

Please follow the instructions given here to make sure you have the necessary software installed. 

First see the appropriate section in the Operating System Specific Instructions, then everyone should do the portion in "Installation Instructions for Everyone".

## Operating System Specific Installation Instructions
Pick the appropriate operating system and follow these instructions
1. [MacOS compiler installation](#mac-os)
1. [Linux compiler installation](#linux)
1. [Windows compiler installation](#windows)

## Mac OS

### Compilers
MacOS users should [install XCode](https://developer.apple.com/xcode/). An easy way to install XCode is through the [Mac App Store](https://apps.apple.com/us/app/xcode/id497799835?mt=12).

## Linux

### Compilers
Use this command to install compilers
~~~
sudo apt install build-essential
~~~
{: .language-bash}

## Windows
If your computer uses the Windows operating system, we require installing Windows Subsystem for Linux (WSL). Follow the installation instructions at [this link](https://docs.microsoft.com/en-us/windows/wsl/install-win10). If you don’t have a preference on Linux distribution, we recommend installing Ubuntu 20.04. 

Once WSL is installed, open your ‘Start’ menu and choose ‘Ubuntu’. This should open a terminal window. The first time you have opened Ubuntu, you may see a message which says “Installing, this may take a few minutes…”. After the installation is done, you will have to create a username and password. After these are created, you should be able to use the terminal.

The Windows Subsystem for Linux is like running another computer inside your computer. It is a different operating system and has different software installed than your Windows computer. This means that software installed on one operating system is not available in the other.

### Compilers
After installing WSL, ou will need to install C++ compilers. Use this command to install compilers
~~~
sudo apt install build-essential
~~~
{: .language-bash}

## Text Editor
Everyone should have a text editor they can use to their code. If you do not have a preference for text editors, we recommend [Visual Studio Code](https://code.visualstudio.com/). If you are using WSL, see [these instructions](https://code.visualstudio.com/docs/remote/wsl) for installing Visual Studio Code for use with WSL.





