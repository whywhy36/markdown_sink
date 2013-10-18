vchs-mssqlaas-devbox
====================

This repository provides the development environment based on Vagrant. 

## Get Started

### Install Vagrant
Our dev box is based on vagrant of which provider is VirtualBox. Refer to [Vagrant](http://docs.vagrantup.com/v2/) for further information.

### Directory Layout
This repository contains multiple Vagrant boxes and some common tools. Each vagrant box environment is one subdirectory that contains one Vagrantfile.

#### Dev box

This sub-directory is one standard vagrant workspace. It must contain one Vagrantfile to support one standard Vagrant environment.
This environment will start 3 Windows boxes and 1 Linux box. The basic information of the boxes are:

Windows box:
* Windows 2008 R2 with prepared SQL Server 2012 image
* Provision process will complete installation of SQL Server 2012, start the node & worker services and do something necessary stuffs.
* Your dev box directory is real-time synced with one folder( C:\vagrant ) in your Windows boxes. You do not have to copy & paste your codes, just focus on the editor and terminal in your host.

Linux box:
* Ubuntu Precise
* Provision process will prepare the Ruby runtime, start the CloudFoundry NATS and Health Manager services.

The provision procedure of Windows box is defined in the provision.cmd. Modify this file to do your customized provision.



#### Add YOUR box and summary here

## Others

### winrm-cli
This command line tool features your ability to run Windows cmd/powershell commands without loging into the Windows guest boxes. You can also manage multiple boxes at same time.
```bash
$ ./winrm-cli

Commands:
  winrm-cli add NODE        # connect to the specific node
  winrm-cli cmd COMMAND     # execute the normal commandline(cmd) command
  winrm-cli delete NODE     # disconnect to the specific node
  winrm-cli help [COMMAND]  # Describe available commands or one specific command
  winrm-cli info            # show current winrm status
  winrm-cli ps COMMAND      # execute the powershell(ps) command
```
Suppose you have two boxes (default, test) in your dev environment, you can:
####add box
```bash
winrm-cli add -a="192.168.0.1" -u="admin" -p="pwd" -n="default"
winrm-cli add -a="192.168.0.2" -u="admin" -p="pwd" -n="test"
```
####list boxes
```bash
winrm-cli status
```
####run cmd command in one box
```bash
winrm-cli cmd dir
```
or
```bash
winrm-cli cmd -n=test dir
```
####run power shell command 
```bash
winrm-cli ps ls
```
####delete one box
```bash
winrm-cli delete test
```
