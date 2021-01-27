---
layout: default
title: Why looking through source of a web page matters
description: You always hear people say you must look through a source of a web page when hunting for bugs. But don't know what to look for, you have landed on the right place!
date: 2020-07-26
date_only: July 26, 2020
permalink: /why-looking-through-source-matters/
reading_time: 3 minutes
image: /assets/images/sourcewolf.jpg
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
                So, let's start from the basics, so that everyone is in the same track! 
                <br>
                <br>

                <b>What does the source page contain?</b>
                <br>
                <br>

                It contains the HTML, CSS and javascript code which is powering the websites, apart from the backend services if any.
                The HTML and CSS code are mostly doing static stuff and so this won't be of much interest except when you are hunting for bugs like XSS.
                <br>
                <br>

                <b>What remains? Javascript!</b>

                <br><br>

                This is the stuff that we are most interested in.😋
                <br>
                Javascript is responsible for manipulating the DOM of the website, or in other words making static content dynamic. You can't be displaying an user specific page just with HTML and CSS. The website must use a backend setup, and use javascript to query endpoints, fetch a particular data and change the contents depending on who is viewing the page.

                <br><br>

                <b>What could possibly go wrong here?</b>

                <br><br>

                Not all endpoints are hit! There may be endpoints which the developers might have forgotten to remove. They might be endpoints, which are current not used by the company, but still in the source. Something like <code>/api/admin/analytics?user=michaelben</code>. Let's say the company previously used this endpoint for analytics, and it returns all the user's usage data or leaks any other sensitive data on changing the <code>user</code> parameter.

                <br><br>

                <b>Yeah, it seems like looking through javascript code actually is useful. So, do I have to manually look through every page I visit everytime?</b>

                <br><br>

                This is where my tool <a href="https://github.com/micha3lb3n/SourceWolf/">SourceWolf</a> comes in. It does all the hard work for you. You just need to provide the list of URLs, and it crawls the responses for you to find all the endpoints.
                <br><br>
                It currently only searches for endpoints in the files, fuzz endpoints and grab all the social media links in the page to find out potential broken links , but you can expect in the future that he will be adding more tricks up his sleeve like creating a custom wordlist for a specific target based on the words obtained from the source, and flagging secret api keys leaked in the source.

                <br><br>

                It can also serve as a endpoint bruteforcer and something that can get you the status codes of a list of URLs really fast. For more detailed information, check out the docs!

                <br><br>

                An amazing feature which SourceWolf has is, it automatically checks for updates right from the terminal everytime it is run, and it notifies the user of a newer version along with <b>any bug fixes and new features</b> added to the new version. You can also update SourceWolf directly from the terminal without the need for you to go the repo for you to clone it back again every time you want to update.

                <br><br>

            </p>
            <center><span class=""><img src="{{ page.image }}" alt="" height="30%" width="80%" /></span></center>
            <br><br>

            <b>Link to the repository: <a href="https://github.com/micha3lb3n/SourceWolf/">https://github.com/micha3lb3n/SourceWolf/</a></b>

            <br><br>
            <br><br>
        </div>
        Share: <a class="icon fa-twitter" target="_blank"
      href="https://twitter.com/intent/tweet?text=Check out '{{ page.title }}' by @micha3lb3n at https://micha3lb3n.github.io{{ page.permalink }}" data-size="large" url="https://micha3lb3n.github.io/why-looking-through-source-matters/">
    Tweet</a>
    </article>

  </div>
</section>
