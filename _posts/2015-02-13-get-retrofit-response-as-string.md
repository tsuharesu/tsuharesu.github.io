---
title: 'Get Retrofit response as String'
category: dev
---

Since I first created this post Retrofit and OkHttp have changed a lot. Now in version 2 is much easier to get a String response from your server. All this methods can be seen on Retrofit `CallTest` [class](https://github.com/square/retrofit/blob/07d1f3de5e0eb11fb537464bd14fdbacfc9e55a7/retrofit/src/test/java/retrofit/CallTest.java#L59-L71), but I'll write them down anyway.


### Method 1
Get the String straight from the response body. Clean and easy:

{% highlight java linenos %}
interface Service {
    @GET("/") Call<ResponseBody> getBody();
}

// Nothing to add in your builder
Retrofit retrofit = new Retrofit.Builder()  
        .baseUrl(server.url("/"))
        .build();
Service example = retrofit.create(Service.class);

// get your response, sync or async
Response<ResponseBody> response = example.getBody().execute();
// get the damn string!
response.body().string()
{% endhighlight %}

### Method 2 (preferred)

Retrofit launched a [`ScalarConverter`](https://github.com/square/retrofit/tree/master/retrofit-converters/scalars) that makes it really easy. You just need to add it to your Retrofit builder and you are good to go.

{% highlight java linenos %}
interface Service {
    @GET("/") Call<String> getString();
}

// Use it in your Retrofit  builder
Retrofit retrofit = new Retrofit.Builder()
        .baseUrl(server.url("/"))
        .addConverterFactory(GsonConverterFactory.create()) // This is to use GSON for JSON
        .addConverterFactory(ScalarsConverterFactory.create()) // This gets your String
        .build();
Service example = retrofit.create(Service.class);

// get your response, sync or async, already as a String
Response<String> response = example.getString().execute();
// get the body
response.body()
{% endhighlight %}


Original post (Retrofit 1.9):
> Not all APIs always respond with JSON. Sometimes you just want a simple response with a text. And when using Retrofit this can be tough. Retrofit always uses GSON (or another converter you decide) to parse the Response. So, if you try to get a Response as String, your code will throw some exceptions regarding tokens and blablabla. One way to not parse the response is to get the Response object.
>
> The problem is that the Response#getBody doesn't return a String, but a **TypedInput**. And if you try to use toString, the method will not really return the response. So, how you can get only the response, as a String?
>
> Looking at the [Retrofit code](https://github.com/square/retrofit/tree/f6878a5ccbe7505fcf44fa7063c72e6434493f75/retrofit/src/main/java/retrofit/mime) you can see that **TypedInput** is an Interface and you can cast it to **TypedByteArray** so you can see the **byte[]** inside the response. With this, you can create a new String from the bytes and have your String response, yey!
>
> `new String(((TypedByteArray) response.getBody()).getBytes());`
