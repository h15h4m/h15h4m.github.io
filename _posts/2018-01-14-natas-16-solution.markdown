---
title: "Overthewire Natas 16 Walkthrough"
layout: post
date: 2018-01-24 22:44
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
description: This is just a walkthrough for overthewire's natas level 16
---

## Challenge

![Markdown Image](/assets/images/natas_16_solution/challenge_page_.png)

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

## Solution





