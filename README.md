# ansible-role-centos-ami-builder

## Overview
This Ansible role provides a fully automated way to create a custom CentOS 7 AMI
for the AWS Cloud environment. Unlike most other AMI creation programs this role
does not derive an existing AMI where additional packages and configuration
files would be added. The AMI is created from scratch by partitioning a disk and
installing the operating system using packages from the official CentOS
repositories. This allows to have a custom disk layout with LVM (Logical Volume
Manager) and to control all packages which are going to be installed in order to
get a really minimal system.

This role only works with CentOS v7 and it has been tested with CentOS v7.9.
Please use the following role to build a Rocky Linux v8 AMI:
https://github.com/fdupoux/ansible-role-rockylinux-ami-builder

## Contents
The disk layout is based on LVM as it is very useful for allocating the disk
space and it can be quite important on stateful instances such as database
servers to make sure there is still space for data even if logs are growing a
lot. The image will come with cloud-init in order to configure the SSH key which
is selected at launch time. Also it will automatically grow the LVM
Physical-Volume so all the disk space in the EBS volume of the instance can be
allocated to grow the root file system or create other file systems with LVM.
Important AWS utilities such as awscli and python2-boto are also installed as
they are very often involved in the bootstapping process.

## Requirements
This Ansible role comes with the following requirements:
   * Ansible 2.1.0 or more recent
   * An AWS Account where to execute the creation of the AMI
   * An access key pair with enough privileges to execute ec2 and cloudformation
     operations
   * Permission to launch an official CentOS AMI from the AWS Marketplace
     The best thing to do is to manually create a t2.nano instance and to
     destroy it immediately as this process will ask for the permission to
     launch such instances in your AWS Account:
     https://wiki.centos.org/Cloud/AWS

## How it works
This Ansible role works by first creating a temporary stack in your AWS Account
which contains a VPC and an EC2 instance. This instance is running the official
CentOS image and the new CentOS operating system will be installed on a
secondary empty EBS volume. At the end of the installation the instance is
stopped and the new AMI is created as an image of the secondary volume. The
stack and all its resources are destroyed and only the new AMI and its related
EBS snapshot remain.

## How to use it
   * Install this role using ansible-galaxy
     https://galaxy.ansible.com/fdupoux/centos-ami-builder/
   * Manually create an EC2 t2.micro instance from an official CentOS AMI to
     authorize the use of these AMI in your AWS account
   * Use this role from an ansible playbook and make sure you pass all the
     important parameters which are defined in the defaults/main.yml file
