---
# You don't need to edit this file, it's empty on purpose.
# Edit theme's home layout instead if you wanna make some changes
# See: https://jekyllrb.com/docs/themes/#overriding-theme-defaults
---

# Tsuharesu

## Who am I?

I am developer passionate about mobile and web technologies. Currently I am an Android Developer at Simprints in Cambridge.

I have a passion for [movies, shows](https://trakt.tv/users/tsuharesu), animes, [books](https://www.goodreads.com/tsuharesu
) and [music](https://www.last.fm/user/tsuharesu)! I also like to catalog and
track things I do.

As someone that loves media so much I also write. My posts are utterly random: I write about my
life, I write about development and sometimes even poetry.

## Whats up with the name?
Oh, this is an old RPG character that I created and since no one else IN THE WORLD has this name,
I use it everywhere. Pretty handy, eh? My real name is Thales.

## Apps I've worked on
{% for project in site.data.professional -%}
* [{{ project.name }}]({{ project.url }})
{% endfor %}

## Personal projects
{% for project in site.data.projects -%}
* [{{ project.name }}]({{ project.url }})
{% endfor %}

## Development posts
{% for post in site.categories.dev -%}
* [{{ post.title }}]({{ post.url }})
{% endfor %}
**Medium posts**
* [Mimic GCM messages for faster development](https://medium.com/@tsuharesu/mimic-gcm-messages-for-faster-development-faadef42eb79)
* [ViewBinder for Android in
  Kotlin](https://medium.com/making-internets/viewbinder-for-android-in-kotlin-abbeae67fab3#.je8w00qx3)

## About life posts
{% for post in site.categories.personal -%}
* [{{ post.title }}]({{ post.url }})
{% endfor %}

<hr />
<footer markdown="1">
{% for social in site.data.social -%}
[{{ social.name }}]({{ social.url }}){% if forloop.last != true %} / {% endif %}
{%- endfor %}
</footer>

