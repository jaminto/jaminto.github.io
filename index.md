---
layout: page
title: Latest Posts
tagline: "Building distributed systems running windows on Amazon Web Services."

---
{% include JB/setup %}


{% for post in site.posts limit:25 %}
{% capture modulo %}{{ forloop.index | modulo: 3 }}{% endcapture %}
{% if modulo == '0' or forloop.first %}
<div class="row">
{% endif %} 
  <div class="span4">
    <h2 class="title">{{ post.title }}</h2>
    <info datetime="{{ page.date | date: "%Y-%m-%d" }}">
    {{ post.date | date: "%b %Y" }}
    </info> - 
    {% if post.description %}
      <span class="body">{{ post.description | strip_html }}</span>
    {% else %}
      <span class="body">{{ post.excerpt | strip_html }}</span>
    {% endif %}
    <div><a href="{{post.url}}">Read More...</a></div>
  </div>
{% if modulo == '0' or forloop.last %}
</div>
{% endif %} 
{% endfor %}
