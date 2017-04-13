---
layout: post
section-type: post
title: Live Resizing LVM partition on Linux by using parted
category: sysadmin
tags: [ 'sysadmin', 'linux', 'lvm', 'partition', 'resizing' ]
--- 

Sometimes, our linux instance is running out of space and we have to resize the partion. We also can use Gparted which a GUI for resizing partion but somehow we cannot use Gparted and want to live resizing then using parted is the best solution.

First thing first, we need to expand the HDD. In my case, I am using Xenserver and my linux instance have an extended partition that contains the LVM partition.

I'm using parted to size my partition. First up is to start parted on /dev/xvda

<pre><code data-trim class="yaml">
#parted /dev/xvda
</code></pre>

Then use the print free command to see where the partition needs to end to fill up the free space
<pre><code data-trim class="yaml">
(parted) print free
Model: Xen Virtual Block Device (xvd)
Disk /dev/xvda: 322GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Disk Flags:

Number  Start   End     Size    Type      File system  Flags
        32.3kB  1049kB  1016kB            Free Space
 1      1049kB  512MB   511MB   primary   ext2
        512MB   513MB   1048kB            Free Space
 2      513MB   221GB   221GB   extended
 5      513MB   221GB   221GB   logical                lvm
        221GB   321GB   100GB             Free Space

</code></pre>

To resize it, a simple resizepart command is used

<pre><code data-trim class="yaml">
(parted) resizepart 2 321GB
</code></pre>

In the case of an extended partition containing the LVM partition, the command above just needs to be run against the 2 partition numbers, with the extended partition being extended first, and the LVM partition being extended second with the ending bytes being one less than the extended partition. In the above example, partition 2 is the extended partition, and partition 5 is the LVM partition.

<pre><code data-trim class="yaml">
(parted) resizepart 2 321GB
(parted) resizepart 5 321GB
</code></pre>

<strong>Resizing Physical Volume</strong>

After resizing the partition, we'll need to resize the physical volume
<pre><code data-trim class="yaml">
# pvresize /dev/xvda1
</code></pre>

Replace /dev/xvda1 with the partition that has just been extended
<strong>Resizing Logical Volume</strong>
Next, the logical volume will need to be resized with lvresize [lv path]
We can choose to resize to fill up the empty space, or we can select a specific size. In the example, I've resized the logical volume to use all available space.
Replace /dev/mapper/volume-vg-root with the path of the logical volume that needs resizing.
<pre><code data-trim class="yaml">
# lvresize -l +100%FREE /dev/mapper/volume-vg-root
</code></pre>

<strong>Resizing Filesystem</strong>

The last thing to do is to resize the filesystem. 
<pre><code data-trim class="yaml">
# resize2fs /dev/mapper/volume-vg-root
</code></pre>

Thanks

