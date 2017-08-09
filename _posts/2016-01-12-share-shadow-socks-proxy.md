---
layout: post
title: share  shadow socks proxy
date: 2016-01-12 13:23
author: afeilulu
comments: true
categories: [gfw, mac]
---
Shadow socks has supported many platform very well except IOS, Because background process can only run for 15mins. But you can share shadow socks proxy which is install on your mac or windows pc with your android or ios phone, just be sure they are in the same wifi.

On windows pc, It is very easy. After you installed shadow socks windows client, check on this option on menu: share proxy on intranet. While on mac the client didn't integrate privoxy itself, which can translate socks proxy to http proxy, we have to install it manually.
<code>
$brew install privoxy
$brew install nginx
</code>

Config privoxy
<code>
$vim /usr/local/etc/privoxy/config</code>
<code>
listen-address 0.0.0.0:8118
forward-socks5t / 127.0.0.1:1080 #1080 is shadow socks's default port
</code>

Start privoxy
<code>
$privoxy /usr/local/etc/privoxy/config
</code>

On your mobile, setting proxy with "192.168.1.100:8118".Done! 192.168.1.100 is your mac intranet address.

How about auto-proxy with pac file? No problem. Cause applications on OSX are running in sandbox, which prohibit application access local file without user operation, We have to find a way to bypass it, that is nginx used for.

Download pac file in shadow socks. Open url of the file in your browser.

<img class="alignnone size-full wp-image-358" src="https://afeilulu.files.wordpress.com/2016/01/3288717956.png" alt="3288717956" width="1370" height="1080" />

Modify pac file
<code>
#var proxy = "SOCKS5 127.0.0.1:1080; SOCKS 127.0.0.1:1080; DIRECT;";
var proxy = "PROXY 192.168.2.177:8118; DIRECT;";
</code>

Put proxy.pac file in your nginx. Normally, it is "/usr/local/Cellar/nginx/1.8.0/html/proxy.pac"

Start nginx
<code>
$nginx start
</code>
Stop nginx
<code>
$nginx -s stop
</code>

On mobile, set auto-proxy with url "http://192.168.1.100:8080/proxy.pac ". Done!
