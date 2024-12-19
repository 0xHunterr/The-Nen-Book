Hello, this is SirReda (0xHunterr). I recently participated in CyCTF 2024 Quals, and today, we will walkthrough OSINT challenges.

![](https://miro.medium.com/v2/resize:fit:688/1*5rzN_EFCT2pUjPxgrwmy-A.png)

# First Challenge: Aerospace

![](https://miro.medium.com/v2/resize:fit:714/1*idLc97UMnS91DXdezhtMoQ.png)

**Description:** We are investigating the history of a nano-satellite project, Satellites usually are being observed by researchers/scientists via stations on earth. can you find out the last station has observed the satellite with a status “Good” , flag is the station name without spaces example flag: CyCTF{1337-Station}

Files provided with the challenge Data.txt File:

1 43728U 18096K   23081.21782463  .01925735  32332-2  20784-2 0  9995  
2 43728  97.3099 175.2941 0008625 277.9511  82.0794 16.10744455241223

So our duty here is to find the station, to do that we must know more about that mini-satellite

from the observation logs provided in the challenge you can search and know what info it gives us, ChatGPT can be useful here.

![](https://miro.medium.com/v2/resize:fit:875/1*mOeyJqQc6Fs8lD4KPk0d7Q.png)

ChatGPT response for “at what time this observation loged?”

![](https://miro.medium.com/v2/resize:fit:875/1*BzxjiiLR1EIqA527oOoPGA.png)

using the `NORD ID` in a query with our best friend “google” to search for the satellite: `43728 norad id satellite` , we got its name `3CAT1`

![](https://miro.medium.com/v2/resize:fit:875/1*-2lu6n9d-G0nREpzOxUFWg.png)

going to [https://network.satnogs.org/observations](https://network.satnogs.org/observations) websites to see what observations there and using the name we got

![](https://miro.medium.com/v2/resize:fit:875/1*dDCGJpVvy8O5bw99NGiAig.png)

no status good appear

from the challenge description, our goal is the “last” observation with the status “Good” playing filters we got:

![](https://miro.medium.com/v2/resize:fit:875/1*g4ga6jjD7qeF5ZE6VlINmA.png)

so our flag is: `CyCTF{766-Dunchurch}`

# Second Challenge: OhMyCell

![](https://miro.medium.com/v2/resize:fit:760/1*3ar_Nc0ljt3dd1OLuOaOTw.png)

**Description:** A Friend of mine is working at the Arab German Company in Cairo , he told me about his struggle to call his wife while he is at the office , i decided to make an investigation for the region to tell him where is the best spot he can go to have a good signal i mean i am a communication engineer after all , but looks like i need some help from a smart person like you. The flag is the cell id of the better cell he can be nearby to have a better signal and the radio Type, example format CyCTF{1337_CDMA}

Our mission is clear and the situation we are in is pretty simple first let’s find what company they meant and where it’s located.  
Searching Google with the same quote “the Arab German Company in Cairo” we got plenty

![](https://miro.medium.com/v2/resize:fit:875/1*gjmIfAIsgZ53FKqOqShasg.png)

but the one that fits with the description is the “`Arab German Company For License Plates S.A.E.`” Since it’s near the Airport that answers the Struggle in phone calls and now it makes sense unlike the others

![](https://miro.medium.com/v2/resize:fit:875/1*MAPHHuACXbagScpgQla_9g.png)

near the Airport

determining the longitude and latitude: (30.083411, 31.387606)  
now searching for a site that enables us to see what cell towers are available in a specific area  
I used [OpenCelliD](https://opencellid.org/#zoom=16&lat=37.77889&lon=-122.41942), you will notice some search filters like MCC, MNC…

Mobile Country Codes (MCC) are used in wireless telephone networks (GSM, CDMA, UMTS, etc.)  
checkout this [https://www.mcc-mnc.com/](https://www.mcc-mnc.com/)

Using this info in [OpenCelliD](https://opencellid.org/#zoom=16&lat=37.77889&lon=-122.41942) and locating the same place in its map and simulating the exact place (in front of Cairo AirPort)  
we reached:

![](https://miro.medium.com/v2/resize:fit:875/1*Qz2i8-2KW6I-KNlzGJycFQ.png)

after digging and trying the cells in this area we found our cell

![](https://miro.medium.com/v2/resize:fit:875/1*xJT4hX99B8Tr1wHu4ZRxMQ.png)

the flag: `CyCTF{16456_UMTS}`

![](https://miro.medium.com/v2/resize:fit:188/0*fhvxbt4vVFUEGyE_.gif)

bro use Etisalat to call his wife

we reached the end of this writeup See you in the next ones (ان شاء الله)
If you have any questions, You can reach me through my social accounts:
[Twitter(X)](https://twitter.com/HunterXReda) | [Linkedin](https://www.linkedin.com/in/0xhunter/) |[Github](https://github.com/0xHunterr) |[Facebook](https://www.facebook.com/0xHunterr)