---
layout: blank
title: Source Code Adventures
---

<h4>A blog about source code and what happens when you dive into the guts of programming frameworks and libraries.</h4>

{% for post in site.posts %}

<hr/>

<div class="blog-header">
	 <h4 class="above-title">{{post.date | date: "%B %-d, %Y"}}</h4>
	<h2><a href="{{ site.baseurl }}{{ post.url }}">{{ post.title }}</a></h2>
	<p>{{ post.excerpt | remove: '</p>' }} <a href="{{ site.baseurl }}{{ post.url }}"><!--Read the full post! &raquo;-->></a> 

{% endfor %}
