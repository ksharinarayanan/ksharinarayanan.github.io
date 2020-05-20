---
layout: default
title: My first bug !
description: Get to know how I found my first bug, and took over a whole page completely.
date:   2020-05-17 16:11:05 +0530
date_only: May 18, 2020
categories: jekyll update
permalink: /my-first-bug/
reading_time: 4 minutes
image: /assets/images/stored_xss.png
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
            <!-- <span class=""><img src="/assets/images/pic01.jpg" alt="" height="200px" width="300px" /></span> -->
        </center>
        <div class="content" style="padding:10px">
            <p style="font-size: 20px;">
                Yeah, finally after 2 weeks of persistent hunting in various domains, I was able to find out a bug on a site.✌ (I won’t be disclosing the site name for obvious reasons).The bug was stored cross site scripting (XSS) on the profile page of the site.
                <br ><br >

                For those of the people who have no idea what I am talking about, cross site scripting is a type of vulnerability which lets me execute any javascript code when someone visits a link with the vulnerability. For example, consider a web page http://www.greetuser.com/name=michaelben/ which displays a message “Hi michaelben”, which technically means anything which you supply in the name parameter will be displayed in the page. What if I put in < script>alert(1)</script> in the name parameter? The page will be something like “Hi < script>alert(1)</script>”. The browser executes the javascript code and an alert box pops us. The main idea behind this is not making an alert box pop up but the fact that I can execute any javascript code if the user input is not properly sanitized. Since the user input is being reflected on to the page, this is called reflected cross site scripting (XSS). There are a lot of ways to exploit this and I am not going into that.
                <br ><br >

                Next comes stored XSS which was found in this site. A really good example for this would be the “Samy (a person) worm” which occurred in the social networking site MySpace in the year 2005. He put in a javascript code in his profile page which makes any user visiting his profile send Samy a friend request and change the user’s bio to “but most of all, samy is my hero”, without their knowledge. And not only that, this javascript code also got copied onto any user’s profile visiting Samy’s page. This started propagating rapidly and affected one million accounts in 20 hours. By this, one could see the severity of this.
                <br ><br >

                Okay, now let’s get back to our original report, I was testing this site and the first thing I always test is to check for stored XSS on the profile page on every website. While signing up, it didn’t allow me to insert “<” onto any of the fields, and so I signed up normally. Then there was this edit profile section, where I could insert “<” here. I didn’t really understand what kind of logic was that🙄. They don’t allow special characters while signing up but they allow the same while editing the profile (Not a issue). So I changed my first name to an XSS payload (< script>alert(1)</script>). No luck! It did some type of encoding and displayed it. The encoding was completely different. I had never seen such a thing on any site ever before. So I fired up burp and then noticed that as soon as I submit the name is sent as encoded text and after that, input displays as such⚠⚠️⚠️️!! So I changed the input inside the burp to the XSS payload. Still no luck! 😫
                <br ><br >

                But I knew this thing could be exploited. This time I encoded the payload and then changed the request in burp. Actually, the website design is such that after you click “Save changes” it does not redirect you to the profile page but stays there and so if the payload is executed, there would be no changes. This time the payload was actually executed but I thought it didn’t because it would be executed only on the pages where my first name is being displayed.
                <br ><br >

                I thought of coming back to the website after some time and signed out of all my test accounts. After 4 hours around 2:30 AM, I logged onto my test account, BOOM!! I got an alert box greeting me🤑. But the problem was I had no idea from where this was coming, having tested so many features and trying XSS in all of those. I was really confused. I reported the bug right away, but even without knowing what caused it 😂. And I got a reply straight away in 5 minutes telling the team were able to reproduce it and they told me that they will get back to me if they required further details. It was 3:00 AM, I decided to sleep and closed my laptop and tried sleeping. But I was too excited to sleep. I somehow wanted to find the reason for that bug. So I switched on my laptop again, and started backtracking all my actions. After 15 minutes, I knew the cause :)
                <br ><br >

                As the company was a startup, they did not realize the impact of stored XSS, so took down my whole profile page using javsscript, and altered it so that it showed whatever content I wanted. There was another critical possibility of stealing cookies leading to account takeover, but that wasn't going to reflect it's impact immediately to a non-security person. I wanted to show something that immediately catches their attention.
            </p>
            <center><span class=""><img src="{{ page.image }}" alt="" height="30%" width="80%" /></span></center>
            <br><br>
            <p style="font-size: 20px;">
                If you are into this kind of stuff, you probably know the excitement, when an alert box pops up and that too for the first time. I don't know whether this is strange or whatever, but I like it more with a firefox alert box, than a chromium one ;)
            </p>
        </div>
        Share: <a class="icon fa-twitter" target="_blank"
      href="https://twitter.com/intent/tweet?text=Check out {{ page.title }} by micha3lb3n at https://micha3lb3n.github.io{{ page.permalink }}" data-size="large" url="https://micha3lb3n.github.io/">
    Tweet</a>
    </article>
  </div>
</section>

