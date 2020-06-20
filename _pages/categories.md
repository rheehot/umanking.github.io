---
layout: page
title: Categories
permalink: /categories/
---
<div id="archives">
  {% for category in site.categories %}
  <div class="archive-group">
    {% capture category_name %}{{ category | first }}{% endcapture %}
    <div id="#{{ category_name | slugize }}"></div>
    <p></p>
    <a name="{{ category_name | slugize }}"></a>
    <h2 class="category-head">{{ category_name }}</h2>
    {% for post in site.categories[category_name] %}
    <article class="archive-item">
    <ul>
      <li>
        <span style="font-size: 20px"><a href="{{ site.baseurl }}{{ post.url }}">{{post.title}}</a></span>
      </li>
    </ul>
    </article>
    {% endfor %}
  </div>
  {% endfor %}
</div>
