---
layout: post
section-type: post
title: How To Delete A Git Remote Tag
category: sysadmin
tags: [ 'sysadmin', 'git', 'tag']
--- 

<strong>Get the list of remote tags</strong>

<pre><code data-trim class="yaml">
git fetch
</code></pre>

<strong>List the tags in the repository</strong>

<pre><code data-trim class="yaml">
git tag
</code></pre>

<strong>Remove a tag from a repository</strong>

<pre><code data-trim class="yaml">
git tag -d tag-name
git push origin :refs/tags/tag-name
</code></pre>

Thank you!