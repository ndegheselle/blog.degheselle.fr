---
layout: post
tags: web
---

Trying to summarize website weight, in terme of what is effectively downloaded by the client. Performances caused by the Javascript or even the CSS is another matter that I wont talk about.

* TOC
{:toc}

## Why is it important

Well, the lighter the better but why ?

**Better perofrmances** : The lighter the website the faster it will be sended / loaded by the client, this will be true whatever the performances of the client or server are. For the server it will means serving more clients, for the client it will mean faster [time to interactive](https://developer.mozilla.org/en-US/docs/Glossary/Time_to_interactive).

**Less caching** : Almost everything you send to a client will be cached and will take space and memory on the client side (which can be quite frustrating). The client will probably never know that your website in particular didn't take a whooping 100MB of sapce and memory, but well it is still a nice step to the next point.

**Ecology !** : Sending the data cost computing power on the server, client, DNS, routers, ... Every one of these machine is consuming power to treat and pass the data along, it may also cache the website which make the impact of the website way more than just what is happening on only the server and client.

## What is important

What exactly make a website heavy ? There is a lot of guide online to improve the load time of a website. There is a good survey of what exactly use space on modern website [there](https://almanac.httparchive.org/en/2021/page-weight).

In a nutshell and in order of importance :

- Ressources : Images, videos, icons, ... most of the time it will be the easier to improve. Videos or simply audio files can be ridicoulously big and it is better to avoid it if you can. For the rest using compression and correct format is the way to go (avoid images if you can use SVG, use compresed JPG with correct compression, use PNG if you really need transparancy).
- Javascript : Most website are build on top of javascript library depending on javascript dependencies depending on ... it can become big really fast. Most of the time developper prefer ease of developpement over overall optimization (which is may be understable).
- CSS : Same as Javascript, importing a huge CSS library to end up using only half the components and utilities end up taking a lot of space.

It is also recommended to have as few separate ressources as possible since each HTTP request add aditional overhead.

## What really matter ?

Here is a **theorical** benchmark of how much time it would take to load a site in function of it's total size, network speed is also estimated but not real mesured speed since it vary on a lot of different parameters. The idea is to give an insight on what could be the impact of the size, real load time will obviously vary a lot with the navigator, server, type of javascript, gzip, ...

We will use the following size : 25 KB, 50 KB, 500 KB, 1 MB, 5 MB. For comparison, the equivalent after gzip would be the following (with ~70% compression) : 7.5 KB, 15 KB, 150 KB, 300 KB, 1.5 MB.

We have the estimated internet speeds :

<table>
	<tr>
		<th>Protocol</th>
		<th>Theoretical Speed</th>
		<th>Estimated Average Speed	</th>
	</tr>
	<tr>
		<td>EDGE</td>
		<td>236.8 kbps</td>
		<td>50-150 kbps</td>
	</tr>
	<tr>
		<td>3G</td>
		<td>42 Mbps</td>
		<td>1-14 Mbps</td>
	</tr>
	<tr>
		<td>3G+ (HSPA)</td>
		<td>84 Mbps</td>
		<td>5-42 Mbps</td>
	</tr>
	<tr>
		<td>4G (LTE)</td>
		<td>1 Gbps</td>
		<td>5-100 Mbps</td>
	</tr>
</table>

We will have the following estimated loading speed :

<table>
	<tr>
		<th>Protocol</th>
		<th>50 KB</th>
		<th>500 KB</th>
		<th>1 MB</th>
		<th>5 MB</th>
	</tr>
	<tr>
		<td>EDGE</td>
		<td>1-3 sec</td>
		<td>10-30 sec</td>
		<td>20-60 sec</td>
		<td>100-300 sec</td>
	</tr>
	<tr>
		<td>3G</td>
		<td>0.01-0.03 sec</td>
		<td>0.1-0.3 sec</td>
		<td>0.2-0.6 sec</td>
		<td>1-3 sec</td>
	</tr>
	<tr>
		<td>3G+ (HSPA)</td>
		<td>0.006-0.018 sec</td>
		<td>0.06-0.18 sec</td>
		<td>0.12-0.36 sec</td>
		<td>0.6-1.8 sec</td>
	</tr>
	<tr>
		<td>4G (LTE)</td>
		<td>0.002-0.006 sec</td>
		<td>0.02-0.06 sec</td>
		<td>0.04-0.12 sec</td>
		<td>0.2-0.6 sec</td>
	</tr>
</table>

In a nutshell : you don't have to be crazy about size, most users wont see the differances between a 25 KB website or a 1 MB one (which heavily depend where you live and a ton of other factors).

What is important is what you are trying to achieve, in some case where speed doesn't matter at all or if you are trying things (like creating proof of concept websites) optimizing the weight isn't important. On the other hand, if one of the main feature is accessibilty for every user possibles everywhere possible you should definitly look into weight, as you can see that low internet speed really make each KB count.

That aside optimization is something that should be considred from the start, even if it is not you priority you should let the door open before having to redo the whole thing.

Anyway, I personnally love the mindset of doing as much as possible with as litle as possible and think that if you have the time to do it small you should definitly take it.

> “I have only made this letter longer because I have not had the time to make it shorter.”
> -Blaise Pascal