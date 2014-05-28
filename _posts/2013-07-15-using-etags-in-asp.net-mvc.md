---
layout: post
published: true
category: Development
tags: 
  - ASP.NET_MVC
description: "Making use of ETags in your web application will not only improve the site's perceived speed to the end user, but will also reduce server processing load.   Here's how to implement ETags properly in ASP.NET MVC."
---

There are two parts to taking advantage of ETags in ASP.NET MVC (or really any other web framework):
1. Set ETags when returning a response
2. Checking a provided ETag on a request

##Setting ETags on the Response
{% highlight csharp %}
var requestedETag = Request.Headers["If-None-Match"];
if (requestedETag == eTagOfContentToBeReturned)
        return new HttpStatusCodeResult(HttpStatusCode.NotModified);
{% endhighlight %}

Also, another tip is that you need to set the cacheability of the response, otherwise by default it's "private" and the ETag won't be set in the response:

`Response.Cache.SetCacheability(HttpCacheability.ServerAndPrivate);`

So a full example:

{% highlight csharp %}
public ActionResult Test304(string input)
{
    var requestedETag = Request.Headers["If-None-Match"];
    var responseETag = LookupEtagFromInput(input); // lookup or generate etag however you want
    if (requestedETag == responseETag)
        return new HttpStatusCodeResult(HttpStatusCode.NotModified);

    Response.Cache.SetCacheability(HttpCacheability.ServerAndPrivate);
    Response.Cache.SetETag(responseETag);
    return GetResponse(input); // do whatever work you need to obtain the result
}
{% endhighlight %}