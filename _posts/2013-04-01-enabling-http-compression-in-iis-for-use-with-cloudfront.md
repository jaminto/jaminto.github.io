---
layout: post
published: false
category: Deployment
description: "By default, IIS will not serve gzip compressed pages through CloudFront.   This article describes how to configure IIS so it does."
tags: 
  - IIS
---

##The Problem
If you [create a CloundFront distribution with a custom origin](http://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/distribution-web-creating.html), and your custom origin is a windows server running IIS7, you may notice that while requests directly to the origin are served with gzip compression, the same url requested through CloudFront will not be compressed.
This is because the default configuration for IIS7 does not allow compression when requests are made via proxies.

##The Solution
To fix this, you need to make a change to the `applicationHost.config` file on your windows server.   This is typically located at `C:\Windows\System32\Inetsrv\Config\applicationHost.config`.   
edit existing `<serverRuntime/>` node in applicationHost.config to:
`<serverRuntime enabled="true" frequentHitThreshold="1" frequentHitTimePeriod="00:00:20" />`

added to httpcompression node:
`noCompressionForHttp10="false" noCompressionForProxies="false"`