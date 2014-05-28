---
layout: post
published: false
category: Development
---

@Elijah Glover's answer is part of the answer, but not really complete. This will set the ETag, but you're not getting the benefits of ETags without checking it on the server side. You do that with:

var requestedETag = Request.Headers["If-None-Match"];
if (requestedETag == eTagOfContentToBeReturned)
        return new HttpStatusCodeResult(HttpStatusCode.NotModified);
Also, another tip is that you need to set the cacheability of the response, otherwise by default it's "private" and the ETag won't be set in the response:

Response.Cache.SetCacheability(HttpCacheability.ServerAndPrivate);
So a full example:

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