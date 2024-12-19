بسم الله الرحمن الرحيم والصلاه والسلام على سيدنا محمد  
Hey there, this is SirReda (AKA 0xHunterr), and this is a walkthrough for the OSINT challenges I solved in Intigriti 1337 Up 2024-CTF

![](https://miro.medium.com/v2/resize:fit:875/1*BEvaxTJ4bUpuVe-SMgG_6g.png)
## content:  
- Trackdown  
- Trackdown2  
- No Comment
# Trackdown:

![](https://miro.medium.com/v2/resize:fit:783/1*kD-qoTa2nJ1P0_lRUWSOqA.png)

![](https://miro.medium.com/v2/resize:fit:875/1*sRmf3mVpv8YrkE2DRk9-iw.jpeg)

We can see in the provided image a huge building with the title “Trang Tien Plaza” heading to Google Maps and hitting the street view to locate the exact angel

> note that the question here is from where the photograph was taken? not what is the photo is?  
> and also note that there is two or more entrance for the building but our target is the one with GUCCI

![](https://miro.medium.com/v2/resize:fit:875/1*KltYC_sn0XHfTpjS6LkjoA.png)

![](https://miro.medium.com/v2/resize:fit:875/1*z08eRu6FbYl7OINfglRIhA.png)

after digging around we got the place

![](https://miro.medium.com/v2/resize:fit:875/1*OeaF9j-QBGdyMX6LN8rZoQ.png)

simulating the angel of our image we could see a restaurant but I couldn’t find any tables perhaps cuz the image was a night and our view is morning so there are some changes

anyway trying that restaurant and it worked  
flag: `INTIGRITI{Si_Lounge_Hanoi}`

# Trackdown2:

![](https://miro.medium.com/v2/resize:fit:794/1*INELSqqBhCabE7dKhVhfkw.png)

![](https://miro.medium.com/v2/resize:fit:875/1*crQotKSwlr0PtDwhJvpGtA.jpeg)

Trying to reverse the image to get initial clues

![](https://miro.medium.com/v2/resize:fit:875/1*xrcufEjWA1m2A_vZ_m845w.png)

not the best results but fine

trying to be specific in our search we can see that high tower in the photo I can use it

![](https://miro.medium.com/v2/resize:fit:875/1*TdIZcx0MxsQMo05e9rcx9w.png)

got the name of the bulding and the City

searching for clues in the provided image, zooming in to the right side I could see a lot of titles and text that will be helpful

![](https://miro.medium.com/v2/resize:fit:875/1*oUjfSfVRR_fiHJXb1ndZkg.png)

![](https://miro.medium.com/v2/resize:fit:875/1*krqUPEfgS149MDkDm16O7w.png)

let’s collect the sources we have now, we search for the `little hanoi egg coffee` next to `the simple cafe` and `A25 Hotel` and a Bus station or something

Searching Google Maps for `little hanoi egg coffee`

![](https://miro.medium.com/v2/resize:fit:875/1*2cWSi0jDgXKNEkm3PCtRIQ.png)

after checking them by zooming + satellite mode, the left one is our target

![](https://miro.medium.com/v2/resize:fit:875/1*kPtnSZT_LpfzD1GhSp98EQ.png)

this place is the most reasonable one

flag: `INTIGRITI{Express_by_M_Village}`

# No Comment

![](https://miro.medium.com/v2/resize:fit:615/1*h1g8cqNE3KfX7eHZlRIiqQ.png)

![](https://miro.medium.com/v2/resize:fit:875/1*8lgR0f08IYT0SIH0BOv0JA.jpeg)

the name of the challenge gives us the vibe of metadata or stegno more than reversing the image, examining the EXIF data:

![](https://miro.medium.com/v2/resize:fit:875/1*5l19969HO6FqBPzQf2nNpg.png)

encoding didn’t work here so I started to think a different way

![](https://miro.medium.com/v2/resize:fit:875/1*v5IO4ba0q4zlfSR9s0sFkw.png)

![](https://miro.medium.com/v2/resize:fit:875/1*Xmrelht1IrrGcU71rGla8w.png)

craving help from GPT

validating its response I got this on [Reddit](https://www.reddit.com/r/redditdev/comments/35bb7i/imgur_link_format/), going to the link imgur.com/a/pq6TgwS

![](https://miro.medium.com/v2/resize:fit:875/1*9xKatkNBUj7WzmBRN7Lxqw.png)

decoding it with [Base64](https://gchq.github.io/CyberChef/#recipe=From_Base64('A-Za-z0-9%2B/%3D',true,false)&input=VjJoaGRDQmhJQ0pzYjI1blgzTjBjbUZ1WjJWZmRISnBjQ0lnYVhRbmN5QmlaV1Z1SVFvS2FIUjBjSE02THk5d1lYTjBaV0pwYmk1amIyMHZSbVJqVEZSeFdXYz0)

What a "long_strange_trip" it's been!  
  
https://pastebin.com/FdcLTqYg

going to the Pastebin link and found a protected paste with a password  
after revising my approach for any missing pieces, there is nothing missed

but the pass is in front of us you can notice something is odd with the decoded string it has double quotes which means that the phrase is the literal as it is

![](https://miro.medium.com/v2/resize:fit:875/1*TANLJwSw3FCexWfOB6Zu7w.png)

`25213a2e18213d2628150e0b2c00130e020d024004301e5b00040b0b4a1c430a302304052304094309`

trying hex and other decodings nothing works and I got stuck here,  
here we have a new aspect that recently joined which is the Pastebin profile itself (it’s the last possible hope also)  
I got this public paste

![](https://miro.medium.com/v2/resize:fit:875/1*6R3736d765sFHG6ymasR5Q.png)

I got stuck here, going with the provided link and sealed

but it was just a hint to XOR the secret paste we have to get the flag 😐

That’s it for today's challenges see you next ones  
If you have any questions, You can reach me through my social accounts:  
[Twitter(X)](https://twitter.com/HunterXReda) | [Linkedin](https://www.linkedin.com/in/0xhunter/) | [Github](https://github.com/0xHunterr) |[Facebook](https://www.facebook.com/0xHunterr)