---
layout: page
title: Blog
tooltip: C++ tidbits
permalink: /blog/
---

<ul class="post-list">
{% for post in site.posts %}
  <li>
    <!-- <span class="post-meta">{{ post.date | date: "%b %-d, %Y" }}</span> -->

    <h2>
      <a class="post-link" href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a> 
    </h2>
<p class="post-meta post-tags">{{ post.date | date: "%b %-d, %Y" }}{% if post.author %} • {{ post.author }}{% endif %}{% if post.tags %} • &nbsp;{% for tag in post.tags %}<span class = "post-tag">{{ tag }}</span>&nbsp;{% endfor %}&nbsp;{% endif %}{% if post.meta %} • {{ post.meta }}{% endif %}</p>
{{ post.excerpt }}
	
	<!-- <div class = "post-tags">
  	  {% for tag in post.tags %}
        <span class = "post-tag">{{ tag }}</span>&nbsp;
      {% endfor %}
	</div> -->
  </li>
{% endfor %}
</ul>
