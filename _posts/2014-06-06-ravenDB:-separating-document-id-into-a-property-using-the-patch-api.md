---
layout: post
published: true
category: Development
description: Use a simple patch request to extract data from a document id and save as a new property on the document with RavenDB
tags: 
  - RavenDB
---

It may feel a little dirty to put the same value in both the document id, for example:

_id: Users/E41270DC-4E0D-48C2-B3CE-4A51FFB649E3_
{% highlight javascript %}

  {
     "UserId" : "E41270DC-4E0D-48C2-B3CE-4A51FFB649E3",
     "Name" : "User",
     "Email" : "user@server.com",
   }

{% endhighlight %}

However, if UserId wasn't included in the document as a property, then when you go to create an index on Users that needs to load related documents with ids that also contain the user id value, you'll be in trouble.  The closest thing you have to use is the _string_ value of the document id (ie. _id: Users/E41270DC-4E0D-48C2-B3CE-4A51FFB649E3_).  It's very difficult, if not impossible, to pull the GUID out of the user id string from within the index. 
 
If you do have millions of User documents in your database, and you *did not* remember to include the UserId property in the document, here's an easy [patch request](http://ravendb.net/docs/2.5/client-api/partial-document-updates) that will add this UserId property to all of your docs:
 
{% highlight csharp %}

  store.DatabaseCommands.UpdateByIndex("Raven/DocumentsByEntityName",
    new IndexQuery { Query = "Tag:Users" },
    new ScriptedPatchRequest
    {
      Script = @"
          var currentDocId = __document_id;
          var id = currentDocId.substring(currentDocId.indexOf('/')+1);
          this.UserId = id;
          "
    }
  );
                
{% endhighlight %}