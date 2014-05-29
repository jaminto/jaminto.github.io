---
layout: post
published: false
category: Deployment
description: "By default, IIS will not serve gzip compressed pages through CloudFront.   This article describes how to configure IIS so it does."
tags: 
  - IIS
---

If you [create a CloundFront distribution with a custom origin](http://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/distribution-web-creating.html), and your custom origin is a windows server running IIS7, you may notice that while requests directly to the origin.
enable http compression through cloud front
edit existing `<serverRuntime/>` node in applicationHost.config to:
`<serverRuntime enabled="true" frequentHitThreshold="1" frequentHitTimePeriod="00:00:20" />`

added to httpcompression node:
`noCompressionForHttp10="false" noCompressionForProxies="false"`