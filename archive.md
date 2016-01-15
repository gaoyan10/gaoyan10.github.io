---
layout: page
title: 归档
---
{% for post in site.posts %}
{% unless post.next %}
<h1 class="page-data-year">{{ post.date | date: '%Y' }}</h1>
{% else %}
	{% capture year %}{{ post.date | date: '%Y' }}{% endcapture %}
	{% capture nyear %}{{ post.next.date | date: '%Y' }}{% endcapture %}
	{% if year != nyear %}
<h1 class="page-data-year">{{ post.date | date: '%Y' }}</h1>
	{% endif %}
{% endunless %}
<li class="page-data-md">{{ post.date | date:"%m" }}月{{ post.date | date:"%d" }}日 <a class="title" href="{{ post.url }}"><i class="fa fa-hand-o-right"></i> {{ post.title }}</a></li>
{% endfor %}