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

so the final command after injection will look like the following
{% highlight shell %}
grep -i $(grep /etc/natas_webpass/natas17) dictionary.txt
{% endhighlight %}

