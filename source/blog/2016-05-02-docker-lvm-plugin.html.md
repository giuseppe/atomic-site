---
title: Working with Docker LVM Plugin
date: 2016-05-01 13:55:00 UTC
author: shishir
tags: docker, lvm
published: true
comments: true
---
Recently Docker introduced a volume command (`docker volume –help` for more information) to allow users to create a logical volume and then bind-mount it inside the container at container creation/runtime.  You can achieve this by:

![local_volume_foobar](./local_volume_foobar_1.png)

This will create a logical volume `foobar` mounted at filesystem location `/var/lib/docker/volumes`.

Now, you can bind mount the `foobar` volume into the `/run` directory inside the container.

![bind_mount_foobar_local](./bind_mount_foobar_local_2.png)

READMORE

While this is all good, Docker does not allow us to put a restriction on the size of the volume created. For use cases like OpenShift, where the Platform-as-a-service (PAAS) system wants to create a size-adjustable container, the docker volume command might not be good enough.

So we decided to write a docker volume plugin, which would allow us to create a size-adjustable volume and then bind-mount it inside the container.

## Docker LVM Plugin

`docker-lvm-plugin` is a volume plugin, which creates logical volumes using LVM2. LVM2 is a userspace toolset that provides logical volume management in Linux.



	```
  where `/dev/hda` is your partition or whole disk on which physical volumes were created.

Update your volume group name in `/etc/docker/docker-lvm-plugin`.

That’s it! You are all set to use the plugin now.

## Examples

To explore how this plugin will work, here are some use-case examples.

### Volume Creation

![lvm_volume_foobar_create](./lvm_volume_foobar_create_3.png)

This will create a lvm volume named foobar with a size of 208 MB (0.2 GB).

### Volume List

![lvm_volume_list](./lvm_volume_list_4.png)

This will list volumes created by all docker drivers, including the default driver (local).

### Volume Inspect

![lvm_volume_inspect](./lvm_volume_inspect_5.png)

This will inspect foobar and return a JSON.

### Volume Removal

![lvm_volume_rm](./lvm_volume_rm_6.png)

This will remove the lvm volume foobar.

### Bind Mount lvm volume Inside the Container

![bind_mount_foobar_lvm](./bind_mount_foobar_lvm_7.png)

This will bind mount the logical volume foobar into the home directory of the container.

Hope you will enjoy playing around with this plugin!