# ansible-role-centos-ami-builder

## Overview
Ansible role to build a custom CentOS 7.x AMI for AWS. Most other AMI creators
work by deriving an existing AMI and adding extra packages on the top of it, and
also update existing configuration files. This AMI creator on the other hand is
designed to create a new CentOS AMI from scratch to be able to use a custom disk
layout based on LVM (Logical Volume Manager).

## How it works
This Ansible role builds a minimal CentOS 7 Amazon Machine Image (AMI) on a
small EBS Volume with a small boot partition and a root volume in LVM and
cloud-init installed so an ssh public key can be configured at launch time for
the "admin" user. The disk is automatically going to be grown at launch time but
the space will remain unallocated in the LVM Volume-Group so users can use the
space to grow any Logical-Volume they want.

## How to use it
   * Verify the values specified in the vars/main.yml file are valid
   * Manually create an EC2 t2.micro instance from an official CentOS AMI
   * Create a secondary 3GB EBS Volume and attach it as /dev/xvdf
   * Attach an Elastic IP to the instance and put it in the hosts file
   * Run ansible-playbook using this role and make sure it works
   * Manually create an EBS Snapshot of the secondary 3GB EBS Volume
   * Create an AMI from this snapshot using HVM and rootdev=/dev/xvda
