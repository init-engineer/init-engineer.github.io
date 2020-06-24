---
layout: page
title: 作者群
description: 純靠北工程師 | 共同協作部落格 | 作者群
permalink: /about/
---

{% for author in site.authors %}
<div class="list-wrapper" ng-app="app" ng-controller="MainCtrl as ctrl">
  <ul class="list">
    <li class="list-item">
      <div>
        <img src="{{ author.photo }}" alt="{{ author.name }}" class="list-item-image">
      </div>
      <div class="list-item-content">
        <a href="/authors/{{ author.name }}">
          <h4>{{ author.display_name }} | {{ author.position }}</h4>
          <p>@{{ author.name }}</p>
        </a>
      </div>
    </li>
  </ul>
</div>
{% endfor %}
