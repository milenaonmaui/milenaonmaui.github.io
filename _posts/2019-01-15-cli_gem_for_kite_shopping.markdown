---
layout: post
title:      "CLI Gem for Kite Shopping"
date:       2019-01-15 20:20:27 +0000
permalink:  cli_gem_for_kite_shopping
---

Kitesurfing is my hobby, unfortunately the gear is expensive and I am always on the lookout for some good deals.

So I decided to build a gem for kite shopping. It wasn't easy to find a suitable web site. A lot of them seem to be using Java Script, perhaps pulling data from a database and other more advanced techniques.
Finally I located the Real Water Sports web site which has a good structure with elements such as divs with classes, so I can use Nokogiri to scrape the data.

My first challenge was that there were simply too many products if I was to look at all kites of all sizes. Normally, when shopping for a kite, just like shopping for a shoe, you already know what size you want. So I decided my first screen to ask the user what size they are looking for.  From there I noticed that I can costruct the URL by appending "size_XX" (e.g. 5m, 7m etc.) to the main URL.

The next big obstacle was that when using Browser IDE, or even Dowload IDE, which I was using for most of my labs, I was constantly getting disconnected, especially when scraping. I lost my work a couple of times, and had problems reconnecting to the Sandbox and this was really slowing me down. In a discussion with Antonio about the project, we did a speed test of my Internet connection and noticed that while the download speed was decent, about 10 Mbps, the upload was pretty low - about 0.5 Mbps. We agreed that probably the best route for me would be to setup a local environment. Unfortunately I have a Windows PC, so I had to install Oracle Virtual Box and the the Ubuntu virtual machine image provided by Flatiron. Surprisingly, the virtual environment installation went pretty smoothly. I had to do some upgrades to Ruby, install gems, Bundler, etc. GIT setup was somewhat challenging with setting up the private and public SSH keys, but eventually it worked.

Setting up the local environment was a huge improvement for me. No more disconnecting and losing my work. I could just focus on the development part.

I used bundle gem command to generate the skeleton of directories and files required for my gem. The next roadblock however was to get a basic console and CLI to work. It took me a while to realize that the gemspec file needed to be updated with all the dependencies required. Also, I had to create my environment file that loads all the required gems and classes I need.

Finally after getting the CLI, console and basic scraping with Nokogiri to work I was able to focust on the details of my particular project. Of course, that task had its own issues. For example, the website is inconsistent in how it presents different brands. Some have colors, some don't. Some have details listed as an unordered <ul> list, others are presented differently or missing.
I just had to work with whatever was there. If there are colors, I pull them and store them in an instance variable as an array. It may be empty in some cases. I have my print_colors method that only prints if the array is non-empty. Similarly, with the details, they may or may not be present. Sometimes I had 36 lines of details about a kite and I decided to limit them to 5 by using the take(5) method on my array - very handy!. Uslually the top ones are then most important ones anyway.  

Another part I found tricky was that in my Kite class, I couldn't simply save all the instances in a @@all class variable. The reason for that is that my CLI is size-based and I can't mix all sizes into one array. I had to make it into a hash where the key is the size and the value is an array of kites of that size.

Scraping the data takes a while and I don't like as a user to look at a screen and not know if the application is hung or is doing something. I was thinking of implementing some kind of progress bar while scraping the data. But that would probably take considerable time, perhaps introduce new bugs, so for now I am just displaying "Please wait while data is loading...". Only the first time though. Once the list for that size is generated, returning to view the same size again will not cause a delay as my data is already saved in my class.

So this was my first independent Ruby project and there were a lot of roadblocks on the way, but dealing with all these issues taught me a lot. The project is far from perfect as it is, and I can think of tons of improvements, but then I may never finish it. 

