---
# You don't need to edit this file, it's empty on purpose.
# Edit theme's home layout instead if you wanna make some changes
# See: https://jekyllrb.com/docs/themes/#overriding-theme-defaults
---

# Tsuharesu

## Who am I?

I am developer passionate about mobile and web technologies. Currently I am an Android
developer at MetaLab, but I also try to keep in touch with backend technologies.

I have a passion for [movies, shows](https://trakt.tv/users/tsuharesu), animes, [books](https://www.goodreads.com/tsuharesu
) and [music](https://www.last.fm/user/tsuharesu)! I also like to catalog and
track things I do.

As someone that loves media so much I also write. My posts are utterly random: I write about my
life, I write about development and sometimes even poetry.

## Whats up with the name?
Oh, this is an old RPG character that I created and since no one else IN THE WORLD has this name,
I use it everywhere. Pretty handy, eh? My real name is Thales.

## Development posts
* [ViewBinder for Android in
  Kotlin - Medium](https://medium.com/making-internets/viewbinder-for-android-in-kotlin-abbeae67fab3#.je8w00qx3)
{% for post in site.categories.dev -%}
* [{{ post.title }}]({{ post.url }})
{% endfor %}

## Personal projects
{% for project in site.data.projects -%}
* [{{ project.name }}]({{ project.url }})
{% endfor %}

## About life posts (mostly in pt-br)
{% for post in site.categories.personal -%}
* [{{ post.title }}]({{ post.url }})
{% endfor %}

<hr />
<footer markdown="1">
{% for social in site.data.social -%}
[{{ social.name }}]({{ social.url }}){% if forloop.last != true %} / {% endif %}
{%- endfor %}
</footer>

