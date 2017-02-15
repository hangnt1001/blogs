---
layout: post
section-type: post
title: How to backup running virtual machine in Xenserver and move to Amazon S3 
category: sysadmin 
tags: [ 'sysadmin', 'xenserver', 'backup', 'aws', 's3' ]
---
### How to backup running virtual machine in Xenserver and move to Amazon S3

To backup running virtual machine in xenserver, we can following below steps:

Find VM UUIDs

<pre><code data-trim class="yaml">
# xe vm-list is-control-domain=false is-a-snapshot=false
uuid ( RO)           : 8ac95696-94f3-83c1-bc89-8bb2603f832b
     name-label ( RW): test-vm
    power-state ( RO): running
</code></pre>

Create VM snapshot

<pre><code data-trim class="yaml">
# xe vm-snapshot uuid=8ac95696-94f3-83c1-bc89-8bb2603f832b new-name-label=testvmsnapshot
</code></pre>

Above command will return a UUID of snapshot, use the UUID to convert snapshot to a VM, so we can export it to file using below command
<pre><code data-trim class="yaml">
# xe template-param-set is-a-template=false ha-always-run=false uuid=b15c0531-88a5-98a4-e484-01bc89131561
</code></pre>

Export snapshot to file

<pre><code data-trim class="yaml">
# xe vm-export vm=b15c0531-88a5-98a4-e484-01bc89131561 filename=vm-backup.xva
</code></pre>

Copy snapshot to S3

<pre><code data-trim class="yaml">
aws s3 cp vm-backup.xva s3://your-bucket
</code></pre>

Destroy snapshot

<pre><code data-trim class="yaml">
# xe vm-uninstall uuid=b15c0531-88a5-98a4-e484-01bc89131561 force=true
</code></pre>


Below is the bash shellscript, it will help you schedule the job to backup. The script mounted NFS file system to store the snapshot, will umount once done.

<pre><code data-trim class="yaml">
#!/bin/bash
#
#

DATE=`date +%d%b%Y`
MOUNTPOINT=/xenmnt
NFS_SERVER_IP="x.x.x.x"
VMUUID='XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX'
### Create mount point

mkdir -p ${MOUNTPOINT}

### Mounting remote nfs share backup drive

mount -F ${NFS_SERVER_IP}:/backup/vm ${MOUNTPOINT}

### Get VM name

VMNAME=`xe vm-list uuid=$VMUUID | grep name-label | cut -d":" -f2 | sed 's/^ *//g'`

### Create VM snapshot
SNAPUUID=`xe vm-snapshot uuid=${VMUUID} new-name-label="SNAPSHOT-${VMUUID}-$DATE"`

xe template-param-set is-a-template=false ha-always-run=false uuid=${SNAPUUID}

### Export snapshot
    
xe vm-export vm=${SNAPUUID} filename="${MOUNTPOINT}/${VMNAME}-$DATE.xva"

### Copy to S3
aws s3 cp ${MOUNTPOINT}/${VMNAME}-$DATE.xva s3://your-bucket
### Destroy snapshot
xe vm-uninstall uuid=${SNAPUUID} force=true

### Unmount drive
umount ${MOUNTPOINT}

</code></pre>