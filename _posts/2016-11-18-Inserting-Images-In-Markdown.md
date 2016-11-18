---
layout: post
section-type: post
title: Inserting Images In Markdown Jekyll Posts
category: tech
tags: [ 'jekyll', 'images', 'syntax', 'makrdown','yaml' ]
---
### Inserting images in markdown Jekyll posts

It’s often useful to add images in the body of a markdown document. While the featured image of a post or page can be specified in the YAML frontmatter, you can also call images directly within the markdown document.

This article outlines how to achieve this within Jekyll.

### KRAMDOWN IMAGE SYNTAX

Insert the following into your .md document:
<pre><code data-trim class="bash">
![image-title-here](/path/to/image.jpg){:class="img-responsive"}
</code></pre>
The methodology for adding a class to the image took a bit of hunting.

Extra attributes (like the image class attribute) are added via span and block inline-attribute-list (IAL). As well as adding a class, you could specify image width and height.

Because the image is an inline (span level) element, the IAL needs to be on the same line. If you add it on the following line, it will wrap the image in a <p> tag and apply the class to that element.

Reference: <a href="http://dev-notes.eu/2016/01/images-in-kramdown-jekyll/" alt="images in markdonw">Link</a>