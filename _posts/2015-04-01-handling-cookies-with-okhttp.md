---
title: 'Handling Cookies with OkHttp/Retrofit'
category: dev
---
When you are dealing with APIs you always have to save your user tokens somewhere. What most
developers do (I tend to think) is to use Android SharedPreferences as it is easy to save and
retrieve things. (You could use a Database too, it's your choice)

But how to get these Cookies when they are given to us on a response? And how to send them back on
the next requests? OkHttp don't handle Cookies transparently, like say, Ion. But it's "easy" to do
it yourself.

What I do is to intercept my Responses via
[Interceptors](https://github.com/square/okhttp/wiki/Interceptors) (OkHttp 2.2+) and use the same
OkClient on all next requests. Every time a new Cookie is received, it is saved on my
SharedPreferences and on every Request done after that, the Cookie is added "magically".

This is a simple solution, maybe there are betters on the wild. But I couldn't find anything as easy
as this for a long time. Hope you enjoy.

<script src="https://gist.github.com/tsuharesu/cbfd8f02d46498b01f1b.js"></script>
