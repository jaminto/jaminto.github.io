---
layout: post
title: "Creating a Login Service for an iPhone Application using WCF"
description: ""
category: Development
tags: [WCF]
---

In a [previous post]({% post_url 2009-10-19-creating-an-iphone-friendly-web-service-in-wcf %}), we described how to create a WCF service from scratch that would easily output data from a Windows Communication Foundation (WCF) service.   In this article, we'll show you how to send data the other way - from the iPhone to a WCF service.

We'll start with the sample code that was built using the instructions in the [previous post]({% post_url 2009-10-19-creating-an-iphone-friendly-web-service-in-wcf %}).  {::comment}Again it is available <a href="http://avaimobile.com/Portals/32035/blogcontent/wcfservicelibrary1.zip">here</a>.{:/comment}

For this exercise, let's assume the hypothetical application design requires that the iPhone be able to send login credentials to the WCF service which will be validated and a response will be sent back to the iPhone indicating login success or failure. 

Remembering that WCF automatically parses incoming data for us (when implemented correctly) and creates strongly typed, custom classes that we can use in our business logic, we'll first create our LoginData class.  Like most logins, we'll need to take a username and a password.  So in the WcfServiceLibrary1 project (which again is our WCF Service Library that contains all of the business logic for the service), create a new class with the following structure:

{% highlight csharp %}
[DataContract]
public class LoginData
{
    [DataMember]
    public string Username{ get; set;}
    [DataMember]
    public string Password { get; set; }
}
{% endhighlight %}

The [DataContract](http://msdn.microsoft.com/en-us/library/system.runtime.serialization.datacontractattribute.aspx) attribute indicates to WCF serializer that this object should be inspected and serialized to a specific format (we'll specify the format later in the web method definition).   The [DataMember](http://msdn.microsoft.com/en-us/library/system.runtime.serialization.datamemberattribute.aspx) attribute on each property notes that the property name and value should be included in serialization.

Since this is the data that the iPhone developer will need to be sending, it would be useful to be able to provide an example of the exact JSON string they should be sending.  To make this easy and accurate, we'll create a test method that returns a sample login object.    Add the following method to the IService1 interface:

{% highlight csharp %}
[OperationContract]
[WebGet(
    BodyStyle = WebMessageBodyStyle.Bare,
    RequestFormat = WebMessageFormat.Json,
    ResponseFormat = WebMessageFormat.Json,
    UriTemplate = "login/sample/"
    )]
LoginData LoginSample();
{% endhighlight %}


Implement the method in the Service1 class:

{% highlight csharp %}
public LoginData LoginSample()
{
    return new LoginData {Password = "thisIsThePassword", Username = "thisIsTheUsername"};
}
{% endhighlight %}

Run the web application and append Service1.svc/login/sample/ to the web dev test server location (e.g. http://localhost:52265/service1.svc/login/sample/) to see the sample data.  Again this is the exact string format that the iPhone developer should send.
It should look something like this:
{% highlight csharp %}
{
     Password: "thisIsThePassword",
     Username: "thisIsTheUsername"
}
{% endhighlight %}


*Again, review the [previous post]({% post_url 2009-10-19-creating-an-iphone-friendly-web-service-in-wcf %}), on making data available for the iPhone for a better explanation of the past few steps.*


Similarly, we also want to define a class that will store our login result data (whether or not the login was successful).  Again in the WcfServiceLibrary1, we'll create a new class with the following signature:
{% highlight csharp %}
[DataContract]
public class LoginResult
{
    [DataMember]
    public bool Success { get; set; }
    [DataMember]
    public string ErrorMessage { get; set; }
}
{% endhighlight %}

Success will indicate whether or not the login succeeded, and the ErrorMessage will hold a message describing why the login failed, or will be empty if login was successful.

Now that we have the data transfer objects defined, we'll create the method signature on the WCF Service Interface (IService1):

{% highlight csharp %}
LoginResult Login(LoginData data);
{% endhighlight %}

This says that we'll be calling a method that takes the LoginData and returns a LoginResult.  

Now we need to add the WCF magic that takes the JSON string that the iPhone sends and feeds it into our method call.  While the [WebGetAttribute](http://msdn.microsoft.com/en-us/library/system.servicemodel.web.webgetattribute.aspx) is used to handle web GET requests, the [WebInvokeAttribute](http://msdn.microsoft.com/en-us/library/system.servicemodel.web.webinvokeattribute.aspx) is used for handling POST operations.  So we'll add the WebInvoke attribute to our method signature so it looks like this:

{% highlight csharp %}
[OperationContract]
[WebInvoke(
    Method = "POST",
    BodyStyle = WebMessageBodyStyle.Bare,
    RequestFormat = WebMessageFormat.Json,
    ResponseFormat = WebMessageFormat.Json,
    UriTemplate = "login"
    )]
LoginResult Login(LoginData data);
{% endhighlight %}

The Method property here is set to "POST" meaning the client (the iPhone) will be POSTing data to this web method.  The BodyStyle, RequestFormat, and ResponseFormat all specify that we want to send and receive JSON with no extra junk.   The UriTemplate says that we will be posting data to a url similar to http://*server*/Service1.svc/login
Since we defined this method in the interface, we need to now implement this signature in the Service1 business logic class.  For now we'll let everyone in adding the following code Servic1.cs in our service library:

{% highlight csharp %}
public LoginResult Login(LoginData data)
{
    return new LoginResult {Success = true};
}
{% endhighlight %}

In a production implementation, this is where the data store would be queried and the credentials validated.  

The coding is now all done, but testing is now the hard part.   This method cannot be tested from a browser, only by a web client that can execute a POST operation.   Please leave a note in the comments if you've found a good app to perform this testing.  
We've created our own test application for this purpose.  {::comment}You can download use it from [here](http://avaimobile.com/Portals/32035/blogcontent/webclienttester.zip)  It is a Windows Form application that requires the .NET Framework 3.5 to run.{:/comment}