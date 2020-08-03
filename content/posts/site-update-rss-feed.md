+++
title = "📣 Update - RSS and JSON feeds"
date = "2020-07-05"
author = "Kareem" 
authorTwitter = "kareemdagg" #do not include @
cover = "" #cover image to show for this post
tags = ["vhaos.dev",] #what tags (in the tags page) to list this post under
keywords = ["blog", "coding", "tech"] #keywords to set for SEO
description = "Another new site update 🎉 (albeit a small one). In this update I've added support for both RSS and JSON Feeds.  Now anyone can subscribe to get the latest updates using their feed reader of choice 📲." #text that shows in the post list view (if showFullContent) is false. Also set as description for SEO
showFullContent = false #whether to show all post content in list view (overrides description field if true)
weight = 0 #priority to set post in list and search views. 0 is default priority, 1 pins post, lower weight = higher priority. 
+++

> **Edit:** the recently updated [JSON Feed 1.1](https://jsonfeed.org/version/1.1) spec brings the more specific `application/feed+json` MIME type; I've updated the site's JSON Feed has to use this MIME type for JSON Feeds instead.

Another new site update 🎉 (albeit a small one). In this update I've added support for both {{<fancylink href="validator.w3.org/feed/docs/rss2.html" label="RSS">}} and {{<fancylink href="jsonfeed.org/" label="JSON Feeds">}}. Now anyone can subscribe to get the latest updates using their feed reader of choice 📲.

Admittedly, implementing this took slightly longer than I had anticipated, specifically when it came to generating my own templates. While Hugo has a decent amount of {{<fancylink href="gohugo.io/templates/rss/" label="documentation">}} on this, figuring out how it all fit together eluded me. Looking back, it does make _a lot_ more sense now, but to quote the words of another programmer:

>_"it makes a ton of sense after you already know what’s going on — but not before."_
>- <cite>Benjamin Pollack</cite>

I might consider writing a post on how to add RSS and JSON feeds to a Hugo site in the future. Until then, please feel welcome to check back in again for more updates. 

🦥
