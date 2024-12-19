Hello, this is SirReda (0xHunterr). I recently participated in CyCTF 2024 Quals, and today, we are going to walkthrough the “**Old Friend**” OSINT challenge.

![](https://miro.medium.com/v2/resize:fit:688/1*5rzN_EFCT2pUjPxgrwmy-A.png)

# the challenge:

![](https://miro.medium.com/v2/resize:fit:875/1*31AftLqtzCEB4M-3U41y8A.png)

Two years ago, while I was working on my thesis in the U.S, I spent some great times with my friend Jacqueline. Unfortunately, I recently lost all my contacts and can’t reach her now. Here’s what I remember that might help: I once dropped her off at her home because her car was at a car wash ,we also once passed by her place while driving to get cash from an ATM. Her home was near a nice well known garden, and if I recall correctly, there was a river nearby. Can you help me find her email address to get in touch? The Flag format should be CyCTF{[mail-address@cy.com](mailto:mail-address@cy.com)}.

The PNG provided:

![](https://miro.medium.com/v2/resize:fit:875/1*cIa6Zkp6AG-LMqqjxjFuHg.png)

so we have a photo of a building and some story with hints,  
beginning our investigation trying to allocate this place in the photo

using reverse image techniques, we find that most of the building is almost the same structure and they mentioned being in New York

![](https://miro.medium.com/v2/resize:fit:875/1*Ai81cx4vpKZ72MfV-37izQ.png)

Going further with these links I found some new mentions of “Park Avenu”  
since our story mentioned the home is near a well-known park it’s a good choice to investigate further,

going with google maps

![](https://miro.medium.com/v2/resize:fit:875/1*zbR6h-0Y0yOYTmB_YrR7AA.png)

and it nears a River “Harlem River” as mentioned in the story, digging deeper and searching for the rest of the hints (ATM, Car wash) didn’t find anything  
so I decided to expand my search for the NewYork City with these key elements and noticed we have a lotttt of rivers there and the buildings are alike so maybe it’s not our place

searching for the ATMs in NY

![](https://miro.medium.com/v2/resize:fit:875/1*h6inVu_x_9yKe9RJJitlfw.png)

As we see a lot of ATMs and parks after investigating I didn’t find any Car wash, and we getting nowhere with this

decided to take advantage of another hint like the “gardens”

![](https://miro.medium.com/v2/resize:fit:875/1*wmI7CIZm3YerRbNS1jqKGQ.png)

juicy results

juicy results and a lot of work we have, starting with the first park  
found a lot of car washes and applying some filters, found also ATMs and last but not least it’s nearby the “Bronx River”

switching to the Satelite view to get some details, we find many buildings  
and from here the hard work begins we should find our target through these buildings (at least the ones in the garden and on the right side)

![](https://miro.medium.com/v2/resize:fit:875/1*r2UdHA9Bqm9jBIPx8Ij41A.png)

**Funny Note and a wrong way: at that point for some reason decided that I  
I was on the wrong path the whole building, ATMs, and Car wash thing is a rabbit hole.  
so I made up my mind to stop and think of a different approach which was searching for the universities in the place(in the challenge story a “thesis” was mentioned) and from there I will search for people who graduated the past 2 years and so on  
though it’s more reasonable BUT no our past path was the right one**

![](https://miro.medium.com/v2/resize:fit:875/1*iIDa0ZDA3qbDJoflw9yF_w.png)

**Building, ATMs, and Car wash thing is a rabbit hole right?…right?**

back to the right path which is to keep digging for her home  
after a lot of investigation using street view on Google Maps into the first row of buildings on the right side of the garden, we got our target

![](https://miro.medium.com/v2/resize:fit:875/1*mArLu3ck6gpuzmcfoL43Dw.png)

the 2300 Olinville Ave  
and we have the name and address, using [**True People search**](https://www.truepeoplesearch.com/) (make sure to use VPN ) or using this Dork :  
`2300 olinville ave + jacqueline site: [www.truepeoplesearch.com](http://www.truepeoplesearch.com/)`

![](https://miro.medium.com/v2/resize:fit:875/1*jlBSqX26a0_rWBtKMQL92A.png)

![](https://miro.medium.com/v2/resize:fit:664/1*yHO998pPsuzwSUVRJLyQiw.png)

the flag: `CyCTF{nena12x3@aol.com}`  
  
that’s it, hope you enjoyed  
If you have any questions, You can reach me through my social accounts:

[Twitter(X)](https://twitter.com/HunterXReda) | [Linkedin](https://www.linkedin.com/in/0xhunter/) | [Facebook](https://www.facebook.com/0xHunterr)