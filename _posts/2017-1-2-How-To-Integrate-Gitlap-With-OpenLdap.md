---
layout: post
section-type: post
title: How to integrate Gitlap with OpenLDAP
category: sysadmin
tags: [ 'sysadmin', 'ldap', 'ubuntu', 'openldap', 'gitlap' ]
---
### How to integrate Gitlap with OpenLdap

This tutorial will show you how to integrate Gitlap with OpenLdap which is setup on Ubuntu.

After installed Gitlab and setup <a href="https://gluesolution.xyz/sysadmin/2016/12/30/How-To-Setup-LDAP-On-Ubuntu-16.04.html">OpenLdap</a> we configure the gitlab configs. Below is the gitlab.rb configs which is working fine.

<pre><code data-trim class="yaml">
gitlab_rails['ldap_enabled'] = true
 gitlab_rails['ldap_servers'] = YAML.load <<-'EOS' # remember to close this block with 'EOS' below
   main: # 'main' is the GitLab 'provider ID' of this LDAP server
     label: 'LDAP'
     host: 'localhost' #domain name or IP address of Ldap
     port: 389
     uid: 'uid' # should choose uid if you are using Openldap
     method: 'plain' # "tls" or "ssl" or "plain"
     bind_dn: 'cn=ldap,dc=domain,dc=local'
     password: 'password'
     active_directory: true
     allow_username_or_email_login: true
    #block_auto_created_users: false
     base: 'ou=Users,dc=domain,dc=local'
     user_filter: ''
  attributes:
#   username: ['uid', 'userid', 'sAMAccountName']
    email: email #   ['mail', 'email', 'userPrincipalName']
#   name:       'cn'
#       first_name: 'givenName'
#       last_name:  'sn'
#     ## EE only
#    group_base: 'ou=W-Integrate,dc=ldap,dc=com'
     #admin_group: 'cn=admin,dc=ldap,dc=com'
#     sync_ssh_keys: false
#

 EOS

</code></pre>

