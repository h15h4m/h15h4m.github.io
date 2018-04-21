---
title: "Overthewire Natas 17 Walkthrough"
layout: post
date: 2018-04-10 21:44
image: /assets/images/markdown.jpg
headerImage: false
tag:
- ctf
- overthewire
- natas
- wargames
star: true
category: blog
author: Hisham Alshaikh
description: This is just a walkthrough for overthewire's natas level 17
---

## Challenge

![Markdown Image](/assets/images/natas_17_solution/challenge_page.png)

{% highlight php %}
<? 
/* 
CREATE TABLE `users` ( 
  `username` varchar(64) DEFAULT NULL, 
  `password` varchar(64) DEFAULT NULL 
); 
*/ 

if(array_key_exists("username", $_REQUEST)) { 
    $link = mysql_connect('localhost', 'natas17', '<censored>'); 
    mysql_select_db('natas17', $link); 
     
    $query = "SELECT * from users where username=\"".$_REQUEST["username"]."\""; 
    if(array_key_exists("debug", $_GET)) { 
        echo "Executing query: $query<br>"; 
    } 

    $res = mysql_query($query, $link); 
    if($res) { 
    if(mysql_num_rows($res) > 0) { 
        //echo "This user exists.<br>"; 
    } else { 
        //echo "This user doesn't exist.<br>"; 
    } 
    } else { 
        //echo "Error in query.<br>"; 
    } 

    mysql_close($link); 
} else { 
?> 
{% endhighlight %}

The goal of this challenge is to exploit a blind SQL injection vulnerability in order the to read the 'password' column for the username natas18, so we obtain the password for the next level. 


## Solution


