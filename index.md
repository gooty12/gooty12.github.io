---
layout: default
title: Home
---

<!--
<div class="posts">
  {% for post in paginator.posts %}
  <div class="post">
    <h1 class="post-title">
      <a href="{{ site.baseurl }}/{{ post.url }}">
        {{ post.title }}
      </a>
    </h1>

    <span class="post-date">{{ post.date | date_to_string }}</span>

    {{ post.content }}
  </div>
  {% endfor %}
</div>
-->
<div>
  <img src="{{ site.base_url }} {% link /assets/Profile.png%}"
       alt="me"
       style="width:300px; height=300px;">
</div>


Hi, I'm Vinod. Welcome to my blog I'm a second year masters student at the University of Utah,
[School of Computing department](http://www.cs.utah.edu/). Previously, I did my bachelors in Computer
Science at [National Institute of Technology, Calicut](http://www.nitc.ac.in/). In between, I worked for
two years as a software developer at Thermo Fisher Scientific, Bangalore. I come from a
[small town](https://en.wikipedia.org/wiki/Guntakal) in southern part of India.


### My Fitness Challenge:
I've an ongoing, self-assigned fitness challenge of working out for more than six days (6.5
out of 7 on average) for the next two years. It's been almost an year, and I haven't missed a single day. In short, I'm
more committed to fitness than to anything else in life right now. See [this page]("LINK") for more on this.

### Coding and Algorithm challenge:
After taking some inspiration from my fitness challenge and applying the same discipline, I've (unknowingly) ended up
with another challenge too - __solve one algorithm per day__. Although never strictly enforced, I did solve a lot of
competitive coding problems in the past six months. See [this page](#) for more details.

### Projects:
- **Extended Hyperwall**
- **Gradual Typing**
- **Mini Java Compiler**
- **XOS Operating System**

<div class="pagination">
  {% if paginator.next_page %}
    <a class="pagination-item older" href="{{ site.baseurl }}/page{{paginator.next_page}}">Older</a>
  {% else %}
    <span class="pagination-item older">Older</span>
  {% endif %}
  {% if paginator.previous_page %}
    {% if paginator.page == 2 %}
      <a class="pagination-item newer" href="{{ site.baseurl }}/">Newer</a>
    {% else %}
      <a class="pagination-item newer" href="{{ site.baseurl }}/page{{paginator.previous_page}}">Newer</a>
    {% endif %}
  {% else %}
    <span class="pagination-item newer">Newer</span>
  {% endif %}
</div>

