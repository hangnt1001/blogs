---
layout: post
section-type: post
title: How To Backup And Restore Gitlab In A New Server
category: devops
tags: [ 'git', 'gitlab', 'backup', 'devops','restore' ]
---
An application data backup creates an archive file that contains the database, all repositories and all attachments. This archive will be saved in <strong>backup_path</strong>, which is specified in the <strong>config/gitlab.yml</strong> file. The filename will be <strong>[TIMESTAMP]_gitlab_backup.tar</strong>, where TIMESTAMP identifies the time at which each backup was created.

You can only restore a backup to exactly the same version of GitLab on which it was created. The best way to migrate your repositories from one server to another is through backup restore.

To restore a backup, you will also need to restore <strong>/etc/gitlab/gitlab-secrets.json</strong> (for omnibus packages) and <strong>/etc/gitlab/gitlab-secret.js</strong> (for installations from source). This file contains the database encryption key and CI secret variables used for two-factor authentication. If you fail to restore this encryption key file along with the application data backup, users with two-factor authentication enabled will lose access to your GitLab server.
###Create a backup of the gitlab system
Use this command if you've installed GitLab with the Omnibus package:
<pre><code data-trim class="yaml">
sudo gitlab-rake gitlab:backup:create
</code></pre>
Use this if you've installed GitLab from source:
<pre><code data-trim class="yaml">
sudo -u git -H bundle exec rake gitlab:backup:create RAILS_ENV=production
</code></pre>
If you are running GitLab within a Docker container, you can run the backup from the host:
<pre><code data-trim class="yaml">
docker exec -t <container name> gitlab-rake gitlab:backup:create
</code></pre>
You can specify that portions of the application data be skipped using the environment variable SKIP. You can skip:
<pre><code data-trim class="yaml">
db (database)
uploads (attachments)
repositories (Git repositories data)
builds (CI build output logs)
artifacts (CI build artifacts)
lfs (LFS objects)
registry (Container Registry images)
</code></pre>
Separate multiple data types to skip using a comma. For example:
<pre><code data-trim class="yaml">
sudo gitlab-rake gitlab:backup:create SKIP=db,uploads
</code></pre>

###Restore a previously created backup
- Restore <strong>/etc/gitlab/gitlab.rb</strong> and <strong>/etc/gitlab/gitlab-secrets.json</strong>
- Note: that you need to run <strong>gitlab-ctl reconfigure</strong> after changing gitlab-secrets.json
- Restore a previously backup
<pre><code data-trim class="yaml">
sudo service gitlab stop

bundle exec rake gitlab:backup:restore RAILS_ENV=production
</code></pre>
Options:
<pre><code data-trim class="yaml">
BACKUP=timestamp_of_backup (required if more than one backup exists)
force=yes (do not ask if the authorized_keys file should get regenerated)
</code></pre>