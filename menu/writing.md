---
layout: page
title: Writing
---
<ul class="posts">
  <!-- based on order by post name -->
  <!-- for example : 2018-10-10 must after 2016-10-10 -->
  <!-- sort -->
  <!-- {% assign pages = site.posts | sort:"title" %} -->
  {% for post in site.posts %}
    <!-- control show year -->
    {% capture bookclass %}{{ post.bookclass }}{% endcapture %}
    {% if bookclass != 'leetcode' %}
      {% unless post.next %}
        <h3>{{ post.date | date: '%Y' }}</h3>
      {% else %}
        {% capture year %}{{ post.date | date: '%Y' }}{% endcapture %}
        {% capture nyear %}{{ post.next.date | date: '%Y' }}{% endcapture %}
        {% if year != nyear %}
          <h3>{{ post.date | date: '%Y' }}</h3>
          {% else %}
        {% endif %}
      {% endunless %}
      <!-- control -->
      <li itemscope>
        <a href="{{ site.github.url }}{{ post.url }}">{{ post.title }}</a>
        <p class="post-date"><span><i class="fa fa-calendar" aria-hidden="true"></i> {{ post.date | date: "%B %-d" }} - <i class="fa fa-clock-o" aria-hidden="true"></i> {% include read-time.html %}</span></p>
      </li>
    {% endif %}
  {% endfor %}
</ul>
