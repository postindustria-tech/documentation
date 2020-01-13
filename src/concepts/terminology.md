@page Concepts_Terminology Terminology


# Aspect @anchor Concepts_Terminology_Aspect

An aspect of a web request is a specific entity that is involved in that request. 
Some examples of aspects are:

* The device hardware being used to make the request.
* The operating system running on that device.
* The physical location of the device.
* The ISP that is being used to service the request.
* The cell tower that services the request (If the device is a mobile using its network 
data access)
* etc

Each aspect can have one or more associated properties. For example, the device hardware
aspect has properties such as:

* Type of device (smart phone, computer, smart TV, games console, etc)
* Screen size in pixels
* Screen size in inches
* etc

# Cloud Service @anchor Concepts_Terminology_CloudService

A service accessed via a [web request](@term{WebRequest}). It is used to send data,
receive data, carry out processing, or all of the above.

A cloud service can be used in much the same way as a local service running on the same machine,
with the extra step of a [web request](@term{WebRequest}) being required.

# Data Tier @anchor Concepts_Terminology_DataTier
<!--TODO-->

# Native Code @anchor Concepts_Terminology_NativeCode

Native code refers to code written in C/C++. This is done to maximize performance, as many more
optimizations can be made in lower level languages than compared to high level languages such as C# or Java.

# Web Request @anchor Concepts_Terminology_WebRequest

A web request is a message sent from a client device to a server where a web site is hosted.
This message will usually be a simple request for a particular web page or other resource. 
However, it also contains a lot of additional information ([evidence](@ref Concepts_Data_Evidence)) 
that can be used to infer details about different contexts within which the request takes 
place (aspects). 

# Web Server @anchor Concepts_Terminology_WebServer

A network connected machine designed to serve content to external devices, or carry out
tasks. Most commonly, a web server is the host of a website.