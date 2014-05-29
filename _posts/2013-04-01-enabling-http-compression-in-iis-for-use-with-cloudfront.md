---
layout: post
published: true
category: Deployment
description: "By default, IIS will not serve gzip compressed pages through CloudFront.   This article describes how to configure IIS so it does."
tags: 
  - IIS
  - CloudFront
---

##The Problem
If you [create a CloundFront distribution with a custom origin](http://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/distribution-web-creating.html), and your custom origin is a windows server running IIS7 or greater, you may notice that while requests directly to the origin are served with gzip compression, the same url requested through CloudFront will not be compressed.
This is because the default configuration for IIS7 does not allow compression when requests are made via proxies.

##The Solution
To fix this, you need to make a change to the `applicationHost.config` file on your windows server.   This is typically located at `C:\Windows\System32\Inetsrv\Config\applicationHost.config`.   

There are few changes that need to be made.  

First, locate the existing [`<serverRuntime/>`](http://www.iis.net/configreference/system.webserver/serverruntime) node in applicationHost.config and change it to read:

`<serverRuntime enabled="true" frequentHitThreshold="1" frequentHitTimePeriod="00:00:20" />`

Next, locate the [`<httpCompression>`](http://www.iis.net/configreference/system.webserver/httpcompression) node and add the following attributes:

`noCompressionForHttp10="false" noCompressionForProxies="false"`

After an [IISReset](http://msdn.microsoft.com/en-us/library/ms957500(v=cs.70).aspx), all requests through CloudFront should now return Gzipped content.

##References
A few links that helped me to this conclusion:

- http://www.jonkragh.com/index.php/amazon-cloudfront-with-iis7/
- https://forums.aws.amazon.com/thread.jspa?threadID=60812
-  http://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/ServingCompressedFiles.html#CompressedCustomOrigin
- http://stackoverflow.com/questions/2203798/in-iis7-gzipped-files-do-not-stay-that-way
