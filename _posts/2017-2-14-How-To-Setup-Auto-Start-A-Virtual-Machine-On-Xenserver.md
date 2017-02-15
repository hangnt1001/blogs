---
layout: post
section-type: post
title: How to setup auto start a virtual machine on Xenserver 
category: sysadmin 
tags: [ 'sysadmin', 'xenserver', 'vm' ]
--- 

### How to setup auto start a virtual machine on Xenserver

To setup auto start a virtual machine, we have to enable auto-start on Xenserver then enable auto-start the VM.

1. Enable auto-start on Xenserver
- get the list of the pools on the xenserver

<pre><code data-trim class="yaml">
# xe pool-list
uuid ( RO)                : XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX

</code></pre>

- copy the UUID and use:

<pre><code data-trim class="yaml">
# xe pool-param-set uuid=XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX other-config:auto_poweron=true

</code></pre>
2. Enable auto-start the VM
- get the UUIDs of the VM that we want to auto-start
<pre><code data-trim class="yaml">
# xe vm-list 
uuid ( RO)           : XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
     name-label ( RW): Control domain on host: XenSever_host_name
    power-state ( RO): running
 
 
uuid ( RO)           : AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA
     name-label ( RW): VM_host_name_1
    power-state ( RO): running
 
 
uuid ( RO)           : BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB
     name-label ( RW): VM_host_name_2
    power-state ( RO): running

</code></pre>
- Copy the UUIDs and type the following command:
<pre><code data-trim class="yaml">
# xe vm-param-set uuid=AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA other-config:auto_poweron=true
</code></pre>

Done!