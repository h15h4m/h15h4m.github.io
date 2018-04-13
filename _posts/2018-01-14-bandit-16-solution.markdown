---
title: "Overthewire Bandit 16 Walkthrough"
layout: post
date: 2018-01-24 22:44
image: /assets/images/markdown.jpg
headerImage: false
tag:
- ctf
- overthewire
- bandit
- wargames
star: true
category: blog
author: Hisham Alshaikh
description: This is just a walkthrough for overthewire's bandit level 16
---

## Challenge

![Markdown Image](/assets/images/bandit_16_solution/overthewire_challenge.png)

As you can read the challenge, it requires us to find open ports in the range of 31000 to 32000. After that, we have to find which port of the open ports we have discovered can communicate over SSL, then we have to send the current password over that port to obtain the password for the new level. 

## Solution

Lets start out with sshing the server.
![Markdown Image](/assets/images/bandit_16_solution/sshing_the_server.png)

Now we can scan the ports in the specfied range using any port scanner. In my case I am going to use Nmap like the following
![Markdown Image](/assets/images/bandit_16_solution/nmaping_the_server.png)

Cool! we were able to obtain several open ports in that range. Since we did not get many open ports we can check which one of the open ports accepts SSL manually, it unnecessary to write a script to automate this for us. So it just a matter of trial and error
![Markdown Image](/assets/images/bandit_16_solution/failed_ssl_connection.png)

As you can see we were able to find that port 31790 accepts over the SSL. Now all we need to do is to send the current password of this level.
![Markdown Image](/assets/images/bandit_16_solution/success_ssl_connection.png)


We got a text that says "Correct!" and as it seems an SSH private key for the next level.
![Markdown Image](/assets/images/bandit_16_solution/obtaining_ssh_private_key.png)

lets just put the private key we obtained in a file and change its permission to 600, so we can use it to access the new level.
![Markdown Image](/assets/images/bandit_16_solution/accessing_the_new_level.png)



