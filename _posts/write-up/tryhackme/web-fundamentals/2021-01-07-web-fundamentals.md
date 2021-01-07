---
title: TryHackMe Writeup - Web Fundamentals
date: 2021-01-07 16:11:47 +07:00
tags: [tryhackme]
description: Tryhackme write-up
comments: true
---

# [Web Fundamentals](https://tryhackme.com/room/webfundamentals)
---
---
---
---
---
Learn how the web works!

## How do we load websites?
---
---
---
#### Finding the server
Initially, a DNS request is made. DNS is like a giant phone book that takes a URL (Like https://tryhackme.com/) and turns it into an IP address. This means that people don’t have to remember IP addresses for their favourite websites.

The IP address uniquely identifies each internet connected device, like a web server or your computer. These are formed of 4 groups of numbers, each 0-255 (x.x.x.x) and called an octet. An example shown below is 100.70.172.11.

#### Loading the content
Once the browser knows the server's IP address, it can ask the server for the web page. This is done with a HTTP GET request. GET is an example of a HTTP verb, which are the different types of request (More on these later). The server will respond to the GET request with the web page content. If the web page is loading extra resources, like JavaScript, images, or CSS files, those will be retrieved in separate GET requests.

For most websites now, these requests will use HTTPS. HTTPS is a secure (encrypted) version of HTTP, it works in more or less the same way. This uses TLS 1.3 (normally) encryption in order to communicate without:
- Other parties being able to read the data
- Other parties being able to modify the data

Imagine if someone could modify a request to your bank to send money to your friend. That'd be disastrous!

A web server is software that receives and responds to HTTP(S) requests. Popular examples are Apache, Nginx and Microsoft's IIS. By default, HTTP runs on port 80 and HTTPS runs on port 443. Many CTFs are based around websites, so it's useful to know that if port 80 is open, there's likely a web server listening that you can attack and exploit.

The actual content of the web page is normally a combination of HTML, CSS and JavaScript. HTML defines the structure of the page, and the content. CSS allows you to change how the page looks and make it look fancy. JavaScript is a programming language that runs in the browser and allows you to make pages interactive or load extra content.

#### Answer
What request verb is used to retrieve page content?
- **get**.

What port do web servers normally listen on?
- **80**.

What's responsible for making websites look fancy?
- **css**.



## More HTTP - Verbs and request formats
---
---
---
#### For Requests
First, is GET request that are used to retrieve content. Then there is POST POST requests that are used to send data to a web server, like adding a comment or performing a login. There are several more verbs, but these aren't as commonly used for most web servers.

This is the broke down of a HTTP requests:
1. Verb and path for the server
1. Headers
1. Body

Here's an example that performing GET request to retrieve JS file:
```bash
GET /main.js HTTP/1.1        # verb and path
Host: 192.168.170.129:8081
Connection: keep-alive
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.122 Safari/537.36
Accept: */*
Referer: http://192.168.170.129:8081/
Accept-Encoding: gzip, deflate
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8

# ^the rest are headers
# the body should be shown in POST request as:
# id=x, blabla
```

#### For Responses
The server should reply with a response. The response follows a similar structure to the request, but the first line describes the status rather than a verb and a path. Basic breakdown of the status codes is:
- **100-199**: Information
- **200-299**: Successes (200 OK is the "normal" response for a GET)
- **300-399**: Redirects (the information you want is elsewhere)
- **400-49**9: Client errors (You did something wrong, like asking for something that doesn't exist)
- **500-599**: Server errors (The server tried, but something went wrong on their side
- More at [https://developer.mozilla.org/en-US/docs/Web/HTTP/Status](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)

Example response to the GET request:
```bash
HTTP/1.1 200 OK
Accept-Ranges: bytes
Content-Length: 28
Content-Type: application/javascript; charset=utf-8
Last-Modified: Wed, 12 Feb 2020 12:51:44 GMT
Date: Thu, 27 Feb 2020 21:47:30 GMT

console.log("Hello, World!")
```

#### Answer
What verb would be used for a login?
- **post**.

What verb would be used to see your bank balance once you're logged in?
- **get**.

Does the body of a GET request matter? Yea/Nay
- **Nay**.

What's the status code for "I'm a teapot"?
- **418**. From [https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/418](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/418).

What status code will you get if you need to authenticate to access some content, and you're unauthenticated?
- **401**.



## Cookies, tasty!
---
---
---
#### Cookies?!
Cookies are small **bits of data** that are stored in your browser. Each browser will store them separately, so cookies in Chrome won't be available in Firefox. They have a huge number of uses, but the most common are either **session management** or **advertising** (tracking cookies). Cookies are normally sent with every HTTP request made to a server.

Cookies are used because HTTP is **stateless** (Each request is independent and no state is tracked internally), cookies are used to keep track of this. They allow sites to keep track of data like what items you have in your shopping cart, who you are, what you've done on the website and more.

Cookies can be broken down into several parts. Cookies have a **name**, a **value**, an **expiry date** and a **path**. The name identifies the cookie, the value is where data is stored, the expiry date is when the browser will get rid of the cookie automatically and the path determines what requests the cookie will be sent with. Cookies are normally only sent with requests to the site that set them (Weird things happen with advertising/tracking).

The server is normally what sets cookies, and these come in the response headers ("Set-Cookie"). Alternatively, these can be set from JavaScript inside your browser. When you log in to a web application, normally you are given a **Session Token**. This allows the web server to **identify your requests** from someone else's. _Stealing someone else's session token can often allow you to impersonate them_.

#### Manipulating Cookies?
Using your browser's developer tools (F12), you can view and modify cookies.. In the Storage tab, you can see cookies that the website has set. There's also a "+" button to allow you to create your own cookies which will come in handy in a minute. You can modify all cookies that you can see in this panel, as well as adding more.

#### Alternatives to cookies
Slowly, for some uses, LocalStorage and SessionStorage are used instead. This has a similar functionality but isn't sent with HTTP requests by default. These are HTML5 features.



## Mini CTF
---
---
---
##### Making HTTP requests - cURL
By default, cURL will perform GET requests on whatever URL you supply it, such as:
```bash
$ curl https://tryhackme.com
```

This would retrieve the main page for tryhackme with a GET request. Using command line flags for cURL, we can do a lot more than just GET content. The -X flag allows us to specify the request type, eg -X POST. You can specify the data to POST with --data, which will default to plain text data. It's worth mentioning cURL does not store cookies, and you have to manually specify any cookies and values that you would like to send with your request. If you want to send cookies from cURL, you can look up how to do this. _Remember, cookies are not shared between different browsers (I'm counting cURL as a browser here)_.

#### What to-do
There's a web server running on http://10.10.155.86:8081. Connect to it and get the flags!
1. GET request. Make a GET request to the web server with path /ctf/get
1. POST request. Make a POST request with the body "flag_please" to /ctf/post
1. Get a cookie. Make a GET request to /ctf/getcookie and check the cookie the server gives you
1. Set a cookie. Set a cookie with name "flagpls" and value "flagpls" in your devtools (or with curl!) and make a GET request to /ctf/sendcookie

#### Answer
What's the GET flag?
```bash
$ curl http://10.10.155.86:8081/ctf/get                                           
thm{162520bec925bd7979e9ae65a725f99f}
```
The flag is **thm{162520bec925bd7979e9ae65a725f99f}**.

What's the POST flag?
```bash
$ curl -X POST -d flag_please http://10.10.155.86:8081/ctf/post
thm{3517c902e22def9c6e09b99a9040ba09}
```
The flag is **thm{3517c902e22def9c6e09b99a9040ba09}**.

What's the "Get a cookie" flag?
```bash
$ curl -v http://10.10.155.86:8081/ctf/getcookie
*   Trying 10.10.155.86:8081...
* Connected to 10.10.155.86 (10.10.155.86) port 8081 (#0)
> GET /ctf/getcookie HTTP/1.1
> Host: 10.10.155.86:8081
> User-Agent: curl/7.72.0
> Accept: */*
> 
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
< Set-Cookie: flag=thm{91b1ac2606f36b935f465558213d7ebd}; Path=/
< Date: Thu, 07 Jan 2021 09:37:41 GMT
< Content-Length: 19
< Content-Type: text/plain; charset=utf-8
< 
* Connection #0 to host 10.10.155.86 left intact
Check your cookies!
```
The flag is **flag=thm{91b1ac2606f36b935f465558213d7ebd}**.

What's the "Set a cookie" flag?
```bash
$ curl -b flagpls=flagpls http://10.10.155.86:8081/ctf/sendcookie
thm{c10b5cb7546f359d19c747db2d0f47b3}
```
The flag is **thm{c10b5cb7546f359d19c747db2d0f47b3}**.