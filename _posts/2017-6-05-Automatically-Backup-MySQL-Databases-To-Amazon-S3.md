---
layout: post
section-type: post
title: Automatically Backup MySQL Databases to Amazon S3
category: devops
tags: [ 'devops', 's3', 'Amazon', 'database', 'backup', 'lampp' ]
--- 

This is a simple way to backup your MySQL database to Amazon S3. In this post, I am using Ubuntu server 16.04 and running lampp
1. Install Mysql client and s3cmd
<pre><code data-trim class="yaml">
	apt-get install mysql-client
	apt-get install s3cmd
</code></pre>

2. Configure Amazon S3
You'll need your AWS access key and secret key, everything is optional and can be ignored
<pre><code data-trim class="yaml">
	s3cmd --configure
</code></pre>

3. Add your script

make a script like backupmysql.sh as below detail:
<pre><code data-trim class="yaml">
#!/bin/bash

# Basic variables
mysqlpass="YourRootPassword"
bucket="s3://yourbucketname"

# Timestamp (sortable AND readable)
stamp=`date +"%s - %A %d %B %Y @ %H%M"`

# List all the databases
databases=`/opt/lampp/bin/mysql -u root -p$mysqlpass -e "SHOW DATABASES;" | tr -d "| " | grep -v "\(Database\|information_schema\|performance_schema\|mysql\|phpmyadmin\)"
`

# Feedback
echo -e "Dumping to \e[1;32m$bucket/$stamp/\e[00m"

# Loop the databases
for db in $databases; do

  # Define our filenames
  filename="$stamp - $db.sql.gz"
  tmpfile="/tmp/$filename"
  object="$bucket/$stamp/$filename"

  # Feedback
  echo -e "\e[1;34m$db\e[00m"

  # Dump and zip
  echo -e "  creating \e[0;35m$tmpfile\e[00m"
  /opt/lampp/bin/mysqldump -u root -p$mysqlpass --force --opt --databases "$db" | gzip -c > "$tmpfile"

  # Upload
  echo -e "  uploading..."
  s3cmd put "$tmpfile" "$object"

  # Delete
  rm -f "$tmpfile"

done;

# Jobs a goodun
echo -e "\e[1;32mJobs a goodun\e[00m"
echo "Nightly Backup Successful: $(date)" >> /tmp/mybackup.log

</code></pre>

Add the executable permission
<pre><code data-trim class="yaml">
	chmod +x backupmysql.sh
</code></pre>

4. Schedule the job with Crontab
For example: I will run this job every 22:00pm every friday

<pre><code data-trim class="yaml">
	0 22 * * 5 bash /pathtoscript/backupmysql.sh
</code></pre>

Done.