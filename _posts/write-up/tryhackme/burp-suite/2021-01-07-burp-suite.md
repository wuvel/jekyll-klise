---
title: TryHackMe Writeup - Burp Suite
date: 2021-01-07 17:11:47 +07:00
tags: [tryhackme]
description: Tryhackme write-up
comments: true
---

# [Burp Suite](https://tryhackme.com/room/rpburpsuite)
---
---
---
---
---
Enumerating and Exploiting More Common Network Services & Misconfigurations

## Overview of Features
---
---
---
#### Burp Features
Here's a quick overview of each section covered:
- **Proxy** - What allows us to funnel traffic through Burp Suite for further analysis
- **Target** - How we set the scope of our project. We can also use this to effectively create a site map of the application we are testing.
- **Intruder** - Incredibly powerful tool for everything from field fuzzing to credential stuffing and more
- **Repeater** - Allows us to 'repeat' requests that have previously been made with or without modification. Often used in a precursor step to fuzzing with the aforementioned Intruder
- **Sequencer** - Analyzes the 'randomness' present in parts of the web app which are intended to be unpredictable. This is commonly used for testing session cookies
- **Decoder** - As the name suggests, Decoder is a tool that allows us to perform various transforms on pieces of data. These transforms vary from decoding/encoding to various bases or URL encoding.
- **Comparer** - Comparer as you might have guessed is a tool we can use to compare different responses or other pieces of data such as site maps or proxy histories (awesome for access control issue testing). This is very similar to the Linux tool diff.
- **Extender** - Similar to adding mods to a game like Minecraft, Extender allows us to add components such as tool integrations, additional scan definitions, and more!
- **Scanner** - Automated web vulnerability scanner that can highlight areas of the application for further manual investigation or possible exploitation with another section of Burp. This feature, while not in the community edition of Burp Suite, is still a key facet of performing a web application test.

#### Answer
Which tool in Burp Suite can we use to perform a 'diff' on responses and other pieces of data?
- **Comparer**.

What tool could we use to analyze randomness in different pieces of data such as password reset tokens?
- **Sequencer**.

Which tool can we use to set the scope of our project?
- **Target**.

While only available in the premium versions of Burp Suite, which tool can we use to automatically identify different vulnerabilities in the application we are examining?
- **Scanner**.

Encoding or decoding data can be particularly useful when examining URL parameters or protections on a form, which tool allows us to do just that?
- **Decoder**.

Which tool allows us to redirect our web traffic into Burp for further examination?
- **Proxy**.

Simple in concept but powerful in execution, which tool allows us to reissue requests?
- **Repeater**.

With four modes, which tool in Burp can we use for a variety of purposes such as field fuzzing?
- **Intruder**.

Last but certainly not least, which tool allows us to modify Burp Suite via the addition of extensions?
- **Extender**.


## Proxy
---
---
---
#### Definition
Generally speaking, proxy servers by definition allow us to relay our traffic through an alternative route to the internet. This can be done for a variety of reasons ranging from educational filtering (common in schools where restricted content must be blocked) to accessing content that may be otherwise unavailable due to region locking or a ban. Using a proxy, however, for web application testing allows us to view and modify traffic inline at a granular level. Throughout this task, we'll explore the major components of the Burp proxy including interception, request history, and the various configuration options we have access to. 

We configured our web traffic to route through our instance of Burp Suite. By default, Burp will be set to 'intercept' our traffic. This means a few things:
1. Requests will by default require our authorization to be sent.
1. We can modify our requests in-line similar to what you might see in a man-in-the-middle attack and then send them on.
1. We can also drop requests we don't want to be sent. This can be useful to see the request attempt after clicking a button or performing another action on the website. 
1. And last but not least, we can send these requests to other tools such as Repeater and Intruder for modification and manipulation to induce vulnerabilities. 

#### Answer
\* note: _Dont forget to deploy the VM first!_

To complete this task you need to connect to the TryHackMe network through OpenVPN. If you're using the in-browser machine this isn't needed (but make sure you're accessing the machine and using Burp inside the in-browser machine).
```bash
$ sudo openvpn path-to/wuvel.ovpn
```

By default, the Burp Suite proxy listens on only one interface. What is it? Use the format of IP:PORT
- **127.0.0.1:8080**.

In Burp Suite, navigate to the Intercept sub-tab of the Proxy section. Enable Intercept
<figure>
<center><a href="intercept.png"><img src="intercept.png" /></a></center>
<figcaption>Turn on the Intercept.</figcaption>
</figure>

Return to your web browser and navigate to the web application hosted on the VM we deployed just a bit ago. Note that the page appears to be continuously loading. Change back to Burp Suite, we now have a request that's waiting in our intercept tab. Take a look at the actions, which shortcut allows us to forward the request to Repeater?
<figure>
<center><a href="rep.png"><img src="rep.png" /></a></center>
<figcaption>Repeater shortcut.</figcaption>
</figure>
- **ctrl-R**. As above picture.

How about if we wanted to forward our request to Intruder?
<figure>
<center><a href="intru.png"><img src="intru.png" /></a></center>
<figcaption>Intruder shortcut.</figcaption>
</figure>
- **ctrl-I**. As above picture.

Burp Suite saves the history of requests sent through the proxy along with their varying details. This can be especially useful when we need to have proof of our actions throughout a penetration test or we want to modify and resend a request we sent a while back. What is the name of the first section wherein general web requests (GET/POST) are saved?
<figure>
<center><a href="http.png"><img src="http.png" /></a></center>
<figcaption>HTTP History.</figcaption>
</figure>
- **HTTP History**. As above picture.

Defined in RFC 6455 as a low-latency communication protocol that doesn't require HTTP encapsulation, what is the name of the second section of our saved history in Burp Suite? These are commonly used in collaborate application which require real-time updates (Google Docs is an excellent example here).
<figure>
<center><a href="web.png"><img src="web.png" /></a></center>
<figcaption>WebSockets History.</figcaption>
</figure>
- **WebSockets History**. As above picture.

Before we move onto exploring our target definition, let's take a look at some of the advanced customization we can utilize in the Burp proxy. Move over to the Options section of the Proxy tab and scroll down to Intercept Client Requests. Here we can apply further fine-grained rules to define which requests we would like to intercept. Perhaps the most useful out of the default rules is our only AND rule. What is it's match type?
<figure>
<center><a href="url.png"><img src="url.png" /></a></center>
<figcaption>URL.</figcaption>
</figure>
- **URL**. As above picture.

How about it's 'Relationship'? In this situation, enabling this match rule can be incredibly useful following target definition as we can effectively leave intercept on permanently (unless we need to navigate without intercept) as it won't disturb sites which are outside of our scope - something which is particularly nice if we need to Google something in the same browser.
<figure>
<center><a href="trgt.png"><img src="trgt.png" /></a></center>
<figcaption>Target Scope.</figcaption>
</figure>
- **is in target scope**. As above picture.



## Target Definition
---
---
---
#### Overview
The Target tab in Burp allows us to perform arguably some of the most important parts of a web application penetration test: defining our scope, viewing a site map, and specifying our issue definitions (although this is more useful within report generation and scanning). 

We can start to build our scope within Burp, something which is incredibly important in the case we are planning on performing any automated testing. Typically this is done in a tiered approach wherein we work our way up from the lowest privileged account (this includes unauthenticated access), browsing the site as a normal user would. Browsing like this to discover the full extent of the site is commonly referenced as the 'happy path'. Following the creation of a site map via browsing the happy path, we can go through and start removing various items from the scope. These items typically fit one of these criteria:
- The item (page, form, etc) has been designated as out of scope in the provided documentation from the client
- Automated exploitation of the item (especially in a credentialed manner) would cause a huge mess (like sending hundreds of password reset emails - If you've done a web app professionally you've probably done this at one point)
- Automated exploitation of the item (especially in a credentialed manner) would lead to damaging and potentially crashing the web app

Once we've removed any restricted or otherwise potentially dangerous items from our scope, we can move onto other areas of testing with the various tools within Burp Suite.

#### Answer
Before leaving the Proxy tab, switch Intercept to disabled. We'll still see the pages we navigate to in our history and the target tab, just having Intercept constantly stopping our requests for this next bit will get old fast.
<figure>
<center><a href="dsblprxy.png"><img src="dsblprxy.png" /></a></center>
<figcaption>Disable Intercept.</figcaption>
</figure>

Navigate to the Target tab in Burp. In our last task, Proxy, we browsed to the website on our target machine (in this case OWASP Juice Shop). Find our target site in this list and right-click on it. Select 'Add to scope'. Clicking 'Add to scope' will trigger a pop-up. This will stop Burp from sending out-of-scope items to our site map. Last, select 'Yes' to close the popup.
<figure>
<center><a href="add.png"><img src="add.png" /></a></center>
<figcaption>Add to scope.</figcaption>
</figure>

Browse around the rest of the application to build out our page structure in the target tab. Once you've visited most of the pages of the site return to Burp Suite and expand the various levels of the application directory. What do we call this representation of the collective web application?
- **site map**.

What is the term for browsing the application as a normal user prior to examining it further?
- **happy path**.

One last thing before moving on. Within the target tab, you may have noticed a sub-tab for issue definitions. Click into that now.
<figure>
<center><a href="issue.png"><img src="issue.png" /></a></center>
<figcaption>Issue definitions.</figcaption>
</figure>

The issue definitions found here are how Burp Suite defines issues within reporting. While getting started, these issue definitions can be particularly helpful for understanding and categorizing various findings we might have. Which poisoning issue arises when an application behind a cache process input that is not included in the cache key?
- **web cache poisoning**.

## Puttin' it on Repeat[er]
---
---
---
#### Overview
As the name suggests, Repeater allows us to repeat requests we've already made. These requests can either be reissued as-is or with modifications. In contrast to Intruder, Repeater is typically used for the purposes of experimentation or more fine-tuned exploitation wherein automation may not be desired. We'll be checking out Repeater with the goal of finding a proof of concept demonstrating that Juice Shop is vulnerable to SQL injection.

#### Answer 
To start, click 'Account' (this might be 'Login' depending on the version of Juice Shop) in the top right corner of Juice Shop in order to navigate to the login page.
<figure>
<center><a href="login.png"><img src="login.png" /></a></center>
<figcaption>Login page.</figcaption>
</figure>

Try logging in with invalid credentials. What error is generated when login fails?
<figure>
<center><a href="invalid.png"><img src="invalid.png" /></a></center>
<figcaption>Wrong credentials.</figcaption>
</figure>
- It's **Invalid email or password.**.

But wait, didn't we want to send that request to Repeater? Even though we didn't send it to Repeater initially via intercept, we can still find the request in our history. Switch over to the HTTP sub-tab of Proxy. Look through these requests until you find our failed login attempt. **Right-click on this request and send it to Repeater and then send it to Intruder, too!**
<figure>
<center><a href="rep1.png"><img src="rep1.png" /></a></center>
<figcaption>Send to Intruder and Repeater.</figcaption>
</figure>

Now that we've sent the request to Repeater, let's try adjusting the request such that we are sending a single quote (') as both the email and password. What error is generated from this request?
<figure>
<center><a href="err.png"><img src="err.png" /></a></center>
<figcaption>SQLITE_ERROR.</figcaption>
</figure>
- The error is **SQLITE_ERROR**.

Now that we've leveraged Repeater to gain proof of concept that Juice Shop's login is vulnerable to SQLi, let's try something a little more mischievous and attempt to leave a devastating zero-star review. First, click on the drawer button in the top-left of the application. If this isn't present for you, just skip to the next question. Next, click on 'Customer Feedback' (depending on the version of Juice Shop this also might be along the top of the page next to 'Login' under 'Contact Us').
<figure>
<center><a href="cust.png"><img src="cust.png" /></a></center>
<figcaption>Customer feedback.</figcaption>
</figure>

With the Burp proxy on submit feedback. Once this is done, find the POST request in your HTTP History in Burp and send it to Repeater.
<figure>
<center><a href="rep2.png"><img src="rep2.png" /></a></center>
<figcaption>Send to Repeater.</figcaption>
</figure>

What field do we have to modify in order to submit a zero-star review?
- **rating**.

Submit a zero-star review and complete this challenge in Juice Shop!
<figure>
<center><a href="rat.png"><img src="rat.png" /></a></center>
<figcaption>Submitted 0 rating.</figcaption>
</figure>

## Help! There's an Intruder!
---
---
---
#### Overview
Arguably the most powerful tool in Burp Suite, Intruder can be used for many things ranging from fuzzing to brute-forcing. At its core, Intruder serves one purpose: automation. Some common use are:
- Enumerating identifiers such as usernames, cycling through predictable session/password recovery tokens, and attempting simple password guessing
- Harvesting useful data from user profiles or other pages of interest via grepping our responses
- Fuzzing for vulnerabilities such as SQL injection, cross-site scripting (XSS), and file path traversal

To accomplish these various use cases, Intruder has four different attack types:

1. _Sniper_ - The most popular attack type, this cycles through our selected positions, putting the next available payload (item from our wordlist) in each position in turn. This uses only one set of payloads (one wordlist).
1. _Battering Ram_ - Similar to Sniper, Battering Ram uses only one set of payloads. Unlike Sniper, Battering Ram puts every payload into every selected position. Think about how a battering ram makes contact across a large surface with a single surface, hence the name battering ram for this attack type.
1. _Pitchfork_ - The Pitchfork attack type allows us to use multiple payload sets (one per position selected) and iterate through both payload sets simultaneously. For example, if we selected two positions (say a username field and a password field), we can provide a username and password payload list. Intruder will then cycle through the combinations of usernames and passwords, resulting in a total number of combinations equalling the smallest payload set provided. 
1. _Cluster Bomb_ - The Cluster Bomb attack type allows us to use multiple payload sets (one per position selected) and iterate through all combinations of the payload lists we provide. For example, if we selected two positions (say a username field and a password field), we can provide a username and password payload list. Intruder will then cycle through the combinations of usernames and passwords, resulting in a total number of combinations equalling usernames x passwords. Do note, this can get pretty lengthy if you are using the community edition of Burp. 

#### Answer
Which attack type allows us to select multiple payload sets (one per position) and iterate through them simultaneously?
- **pitchfork**.

How about the attack type which allows us to use one payload set in every single position we've selected simultaneously?
- **battering ram**.

Which attack type allows us to select multiple payload sets (one per position) and iterate through all possible combinations?
- **cluster bomb**.

Perhaps the most commonly used, which attack type allows us to cycle through our payload set, putting the next available payload in each position in turn?
- **sniper**.

Download the wordlist attached to this room, this is a shortened version of the fuzzdb SQLi platform detection list.
<figure>
<center><a href="dwn.png"><img src="dwn.png" /></a></center>
<figcaption>Download the file.</figcaption>
</figure>

Return to the Intruder in Burp. In our previous task, we passed our failed login attempt to both Repeater and Intruder for further examination. Open up the Positions sub-tab in the Intruder tab with this request now and verify that 'Sniper' is selected as our attack type.
<figure>
<center><a href="snp.png"><img src="snp.png" /></a></center>
<figcaption>Choos sniper mode.</figcaption>
</figure>

Burp attempts to automatically highlight possible fields of interest for Intruder, however, it doesn't have it quite right for what we'll be looking at in this instance. Hit 'Clear' on the right-hand side to clear all selected fields.
<figure>
<center><a href="clr.png"><img src="clr.png" /></a></center>
<figcaption>Clear the fields.</figcaption>
</figure>

Next, let's highlight the email field between the double quotes ("). This will be whatever you entered in the email field for our previous failed login attempt. And click 'Add' to select our email field as a position for our payloads.
<figure>
<center><a href="ad.png"><img src="ad.png" /></a></center>
<figcaption>Add as a position for our payloads.</figcaption>
</figure>

Next, let's switch to the payloads sub-tab of Intruder. Once there, hit 'Load' and select the wordlist you previously downloaded in question five that is attached to this task.
<figure>
<center><a href="load.png"><img src="load.png" /></a></center>
<figcaption>Load our payloads.</figcaption>
</figure>

Almost there! Scroll down and uncheck 'URL-encode these characters'. We don't want to have the characters sent in our payloads to be encoded as they otherwise won't be recognized by SQL.
<figure>
<center><a href="un.png"><img src="un.png" /></a></center>
<figcaption>Uncheck it.</figcaption>
</figure>

Finally, click 'Start attack'. What is the first payload that returns a 200 status code, showing that we have successfully bypassed authentication?
<figure>
<center><a href="hasil.png"><img src="hasil.png" /></a></center>
<figcaption>Payload that works!</figcaption>
</figure>
- It's **a' or 1=1--**.

## As it turns out the machines are better at math than us
---
---
---
#### Overview
Sequencer represents a core tool in a proper web application pentest. Burp's Sequencer, is a tool for analyzing the quality of randomness in an application's sessions tokens and other important data items that are otherwise intended to be unpredictable. Some commonly analyzed items include:
- Session tokens
- Anti-CSRF (Cross-Site Request Forgery) tokens
- Password reset tokens (sent with password resets that in theory uniquely tie users with their password reset requests)

#### Answer
Switch over to the HTTP history sub-tab of Proxy. 
<figure>
<center><a href="htp.png"><img src="htp.png" /></a></center>
<figcaption>HTTP History tab.</figcaption>
</figure>

We're going to dig for a response which issues a cookie. Parse through the various responses we've received from Juice Shop until you find one that includes a 'Set-Cookie' header. 
<figure>
<center><a href="set.png"><img src="set.png" /></a></center>
<figcaption>Set-cookie at response tab.</figcaption>
</figure>

Once you've found a request response that issues a cookie, right-click on the request and select 'Send to Sequencer'.
<figure>
<center><a href="seq.png"><img src="seq.png" /></a></center>
<figcaption>Send to sequencer.</figcaption>
</figure>

Change over Sequencer and select 'Start live capture'
<figure>
<center><a href="str.png"><img src="str.png" /></a></center>
<figcaption>Start live capture.</figcaption>
</figure>

Let Sequencer run and collect ~10,000 requests. Once it hits roughly that amount hit 'Pause' and then 'Analyze now'
<figure>
<center><a href="rs.png"><img src="rs.png" /></a></center>
<figcaption>Analyze now.</figcaption>
</figure>

Parse through the results. What is the effective estimated entropy measured in?
<figure>
<center><a href="bit.png"><img src="bit.png" /></a></center>
<figcaption>Bits.</figcaption>
</figure>
- **Bits**.

In order to find the usable bits of entropy we often have to make some adjustments to have a normalized dataset. What item is converted in this process?
<figure>
<center><a href="tok.png"><img src="tok.png" /></a></center>
<figcaption>Token.</figcaption>
</figure>
- **token**.

Now, read through the remaining results of the token analysis.

## Decoder and Comparer
---
---
---
#### Overview
Decoder is a tool that allows us to perform various transforms on pieces of data. These transforms vary from decoding/encoding to various bases or URL encoding. We chain these transforms together and Decoder will automatically spawn an additional tier each time we select a decoder, encoder, or hash. This tool ultimately functions very similarly to [CyberChef](https://gchq.github.io/CyberChef/), albeit slightly less powerful.

Similarly, Comparer, as you might have guessed is a tool we can use to compare different responses or other pieces of data such as site maps or proxy histories (awesome for access control issue testing). This is very similar to the Linux tool diff.
some common uses for Comparer are as follows:
- When looking for username enumeration conditions, you can compare responses to failed logins using valid and invalid usernames, looking for subtle differences in responses. This is also sometimes useful for when enumerating password recovery forms or another similar recovery/account access mechanism. 
- When an Intruder attack has resulted in some very large responses with different lengths than the base response, you can compare these to quickly see where the differences lie.
- When comparing the site maps or Proxy history entries generated by different types of users, you can compare pairs of similar requests to see where the differences lie that give rise to different application behavior. This may reveal possible access control issues in the application wherein lower privileged users can access pages they really shouldn't be able to.
- When testing for blind SQL injection bugs using Boolean condition injection and other similar tests, you can compare two responses to see whether injecting different conditions has resulted in a relevant difference in responses.

#### Answer
Let's first take a look at decoder by revisiting an old friend. Previously we discovered the scoreboard within the site JavaScript. Return to our target tab and find the API endpoint `/`, followed by `name=Score%20Board`.
<figure>
<center><a href="scr.png"><img src="scr.png" /></a></center>
<figcaption>Found the API endpoint!</figcaption>
</figure>

Copy the first line of that request and paste it into Decoder. Next, select 'Decode as ...' URL
<figure>
<center><a href="dcd.png"><img src="dcd.png" /></a></center>
<figcaption>Decode as URL.</figcaption>
</figure>

What character does the %20 in the request we copied into Decoder decode as?
- **space**.

Similar to CyberChef, Decoder also has a 'Magic' mode where it will automatically attempt to decode the input it is provided. What is this mode called? 
- **smart decode**.

What can we load into Comparer to see differences in what various user roles can access? This is very useful to check for access control issues.
- **site maps**.

Comparer can perform a diff against two different metrics, which one allows us to examine the data loaded in as-is rather than breaking it down into bytes?
- **words**.

## Installing some Mods [Extender]
---
---
---
#### Overview
Similar to adding mods to a game like Minecraft, Extender allows us to add components such as tool integrations, additional scan definitions, and more! Here are some of the most popular extensions I suggest checking out (not all of these are free but I suggest looking into them all the same):

- **Logger++** - Adds enhanced logging to all requests and responses from all Burp Suite tools, enable this one before you need it ;)
- **Request Smuggler** - A relatively new extension, this allows you to attempt to smuggle requests to backend servers. See this talk by James Kettle for more details: Link
- **Autorize** - Useful for authentication testing in web app tests. These tests typically revolve around navigating to restricted pages or issuing restricted GET requests with the session cookies of low-privileged users
- **Burp Teams Server** - Allows for collaboration on a Burp project amongst team members. Project details are shared in a chatroom-like format
- **Retire.js** - Adds scanner checks for outdated JavaScript libraries that contain vulnerabilities, this is a premium extension
- **J2EEScan** - Adds scanner test coverage for J2EE (java platform for web development) applications, this is a premium extension
- **Request Timer** - Captures response times for requests made by all Burp tools, useful for discovering timing attack vectors.
- Etc.

#### Answer
To start, let's go ahead and switch over to the Options sub-tab of the Extender tab. 
<figure>
<center><a href="ext.png"><img src="ext.png" /></a></center>
<figcaption>Extender's options.</figcaption>
</figure>

Scroll down until you reach the 'Python Environment' section. Note, Burp requires the standalone edition of Jython.
<figure>
<center><a href="py.png"><img src="py.png" /></a></center>
<figcaption>Python Environment.</figcaption>
</figure>

Download the standalone version of Jython from here: [Link](https://www.jython.org/download.html).
<figure>
<center><a href="jy.png"><img src="jy.png" /></a></center>
<figcaption>Download the Jython.</figcaption>
</figure>

Return back to Burp and hit 'Select file' under the Python Environment subsection for Jython standalone. Navigate to where you just downloaded this file and select it. 
<figure>
<center><a href="sel.png"><img src="sel.png" /></a></center>
<figcaption>Select the Jython.</figcaption>
</figure>

Burp is now set to go for installing extensions. Switch to the BApp Store sub-tab of Extender and look through the various extensions offered.
<figure>
<center><a href="ba.png"><img src="ba.png" /></a></center>
<figcaption>bApp Store.</figcaption>
</figure>

Which extension allows us too bookmark various requests?
<figure>
<center><a href="bo.png"><img src="bo.png" /></a></center>
<figcaption>Bookmarks extension.</figcaption>
</figure>
- It is **Bookmarks** extension.

## But wait, there's more!
---
---
---
#### Overview
Arguably the most powerful feature in Burp Suite, the Burp Suite Scanner allows us to passively and actively scan and spider the website we are testing for vulnerabilities. In Burp 2.0's task-based model, we can launch these scans (Scanner and Spider) from the dashboard and let them run in the background while we continue to examine the web app. In this case, I've run an unauthenticated scan against Juice Shop and have attached it to this task. These reports can provide a starting place for further enumeration and exploitation via the other tools in Burp Suite.
<figure>
<center><a href="owa.png"><img src="owa.png" /></a></center>
<figcaption>A Preview of the Report Attached to this Task Created with Burp Professional!</figcaption>
</figure>

Commonly used in manual tests, Burp Collaborator Client allows us to gain insight into issues that may otherwise seem to produce no output. Often during testing, we may come across items which, either due to timing/slowness of the web app or a lack of any reaction, are likely vulnerable but don't produce any sure-fire indicators. With Burp Collaborator, however, we can produce out-of-band alerts via generating payloads that reach back to Burp Suite's servers for us.
<figure>
<center><a href="co.png"><img src="co.png" /></a></center>
<figcaption>Burp Collaborator Client.</figcaption>
</figure>

#### Answer
Download the report attached to this task. What is the only critical issue?
<figure>
<center><a href="cors.png"><img src="cors.png" /></a></center>
<figcaption>CORS.</figcaption>
</figure>
- It's a **Cross-origin resource sharing: arbitrary origin trusted** issue.

How many 'Certain' low issues did Burp find?
<figure>
<center><a href="crt.png"><img src="crt.png" /></a></center>
<figcaption>12 Certain low.</figcaption>
</figure>
- **12** issues.

