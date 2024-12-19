# بِسْمِ اللهِ الرَّحْمٰنِ الرَّحِيْمِ

Free Palestine, Free Gaza

Hello and welcome again, I’m Ahmed Reda (0xHunterr) and this is a walkthrough for some of the Web Exploitation & General Skills Challenges of PicoCTF 2024 Live

![](https://miro.medium.com/v2/resize:fit:500/0*cZkyCYPOzEIs7K-Q.jpg)

# Super SSH

Challenge [Link](https://play.picoctf.org/events/73/challenges/challenge/424?category=5&page=1&team_solved=0&user_solved=0)

![](https://miro.medium.com/v2/resize:fit:875/1*7y9L7QFVyLBuydKWg2bYjA.png)

It’s just about connecting through SSH, we can solve it using the following command: `ssh -p 50832 [ctf-player@titan.picoctf.net](mailto:ctf-player@titan.picoctf.net)`

![](https://miro.medium.com/v2/resize:fit:875/1*RB1yUfeJTL0wA9aqSoeRYQ.png)

the flag : `picoCTF{s3cur3_c0nn3ct10n_07a987ac}`

# Commitment Issues

![](https://miro.medium.com/v2/resize:fit:875/1*RXs32-hMyzcfRXa23JBoFg.png)

provided with a Zipped file to download, after unzipping it I got the following `unzip challenge.zip`

![](https://miro.medium.com/v2/resize:fit:831/1*b2gtHi9MEoepaQBHGlAwGg.png)

the challenge folder contains a message.txt file

![](https://miro.medium.com/v2/resize:fit:810/1*YNsPOGEa507I2m6tTleohQ.png)

the hints of the challenge are talking about git commits so checked the `git log`

![](https://miro.medium.com/v2/resize:fit:875/1*fjqqOkabF3zv0EROZlpgyg.png)

we can see that the first commit has the message that says “remove sensitive info“ so that’s exactly what we are looking for, let’s revert that commit

`git checkout ef0b7cc6b98367fa168573c931e0f7098ef59182`

![](https://miro.medium.com/v2/resize:fit:839/1*L2zK1thjViqXTw4t62ui1w.png)

now let’s read the file before deleting the sensitive info

![](https://miro.medium.com/v2/resize:fit:833/1*fpE7DIhGHvmxUF9pzYmv6Q.png)

the flag: `picoCTF{s@n1t1z3_cf09a485}`

# Time Machine

![](https://miro.medium.com/v2/resize:fit:875/1*JCpcjkU7oxi4E4tlD5XKLQ.png)

so after downloading the file and unzipping it we got :

![](https://miro.medium.com/v2/resize:fit:875/1*S2upWgF-WRGYdfJCc6Nozg.png)

wow, this was pretty fast☹ ..anyway  
the flag: `picoCTF{t1m3m@ch1n3_8defe16a}`

that’s it for this CTF see you in the future Inshaa Allah, Pray for me 🙏

If you have any questions, You can reach me through my social accounts:

Twitter(X): [https://twitter.com/HunterXReda](https://twitter.com/HunterXReda)  
Linkedin: [https://www.linkedin.com/](https://www.linkedin.com/in/0xhunter/)  
Facebook: [https://www.facebook.com/profile.php?id=100012814653588](https://www.facebook.com/profile.php?id=100012814653588)

![](https://miro.medium.com/v2/resize:fit:875/0*QpgOjgmy0u12llnF.jpg)

“Hide the Pain”