بسم الله الرحمن الرحيم والصلاه والسلام على سيدنا محمد  
Hey there, this is SirReda (AKA 0xHunterr), and this is a walkthrough for the Web challenges I solved in Intigriti 1337 Up 2024-CTF
![](../../Media/Writeups/Pasted%20image%2020241219130654.png)

# Pizza Paradise

![](https://miro.medium.com/v2/resize:fit:618/1*n7dUq4rlcotpzGBEw03llw.png)

static site with almost no functions and the page source code has nothing interesting, moving forward to check for `robots.txt`

User-agent: *  
Disallow: /secret_172346606e1d24062e891d537e917a90.html  
Disallow: /assets/

![](https://miro.medium.com/v2/resize:fit:875/1*31VyTQ25-c7TOAhsn8OUtA.png)

html page

checking the source code, noticed interesting scripts

![](https://miro.medium.com/v2/resize:fit:875/1*ClnByFn27OMmoKA2t0yAfg.png)

```
//auth.js file  
const validUsername = "agent_1337";  
const validPasswordHash = "91a915b6bdcfb47045859288a9e2bd651af246f07a083f11958550056bed8eac";  
  
function getCredentials() {  
    return {  
        username: validUsername,  
        passwordHash: validPasswordHash,  
    };  
}

 <script>  
            function hashPassword(password) {  
                return CryptoJS.SHA256(password).toString();  
            }  
  
            function validate() {  
                const username = document.getElementById("username").value;  
                const password = document.getElementById("password").value;  
  
                const credentials = getCredentials();  
                const passwordHash = hashPassword(password);  
  
                if (  
                    username === credentials.username &&  
                    passwordHash === credentials.passwordHash  
                ) {  
                    return true;  
                } else {  
                    alert("Invalid credentials!");  
                    return false;  
                }  
            }  
        </script>
```

cracking the hash with [crackstation](https://crackstation.net/),

![](https://miro.medium.com/v2/resize:fit:875/1*w9Kvjd7j1-pAqikfpl0XRA.png)

log in with the credentials I have `agent_1337:intel420`

![](https://miro.medium.com/v2/resize:fit:875/1*2rYknNfFPjhQvj6Ywj47AQ.png)

the download process work with a GET req to `[https://pizzaparadise.ctf.intigriti.io/topsecret_a9aedc6c39f654e55275ad8e65e316b3.php?download=/assets/images/topsecret1.png](https://pizzaparadise.ctf.intigriti.io/topsecret_a9aedc6c39f654e55275ad8e65e316b3.php?download=%2Fassets%2Fimages%2Ftopsecret1.png)`

testing path traversal in that query param  
`https://pizzaparadise.ctf.intigriti.io/topsecret_a9aedc6c39f654e55275ad8e65e316b3.php?download=/assets/images/../../../../../etc/passwd` and it worked

![](https://miro.medium.com/v2/resize:fit:875/1*CLtnBEmW5V82UWgxJP_BLw.png)

trying the `/flag.txt` but the file not found, so instead of guessing the file let’s download the source code php file `[https://pizzaparadise.ctf.intigriti.io/topsecret_a9aedc6c39f654e55275ad8e65e316b3.php?download=/assets/images/../../topsecret_a9aedc6c39f654e55275ad8e65e316b3.php](https://pizzaparadise.ctf.intigriti.io/topsecret_a9aedc6c39f654e55275ad8e65e316b3.php?download=%2Fassets%2Fimages%2F..%2F..%2Ftopsecret_a9aedc6c39f654e55275ad8e65e316b3.php)`

![](https://miro.medium.com/v2/resize:fit:800/1*-tpjY1iVbd0A-7SGAtekWQ.png)

in the very beginning

flag: `INTIGRITI{70p_53cr37_m15510n_c0mpl373}`

# BioCorp

![](https://miro.medium.com/v2/resize:fit:623/1*blB0gjVTb_esNrXPFmKuiw.png)

again with a static basic site, let’s head into provided code:

![](https://miro.medium.com/v2/resize:fit:875/1*4IX_ujgXb3VasKf995AXfw.png)

nothing interesting except `panel.php` file found a hidden panel with strict access by some kind of a header and IP to access it, we can use curl commands or match and replace in Burp:

![](https://miro.medium.com/v2/resize:fit:875/1*8taI0oW-YycjwDQTUaGfaA.png)

![](https://miro.medium.com/v2/resize:fit:875/1*0mS2bBj3IXVpCAHPnwUevw.png)

It displays the XML data from the nuclear equipment. However, it also accepts data via a POST request.

```
if ($_SERVER['REQUEST_METHOD'] === 'POST' && strpos($_SERVER['CONTENT_TYPE'], 'application/xml') !== false) {
    $xml_data = file_get_contents('php://input');
    $doc = new DOMDocument();
    if (!$doc->loadXML($xml_data, LIBXML_NOENT)) {
        echo "<h1>Invalid XML</h1>";
        exit;
    }
} else {
    $xml_data = file_get_contents('data/reactor_data.xml');
    $doc = new DOMDocument();
    $doc->loadXML($xml_data, LIBXML_NOENT);
}

$temperature = $doc->getElementsByTagName('temperature')->item(0)->nodeValue ?? 'Unknown';
$pressure = $doc->getElementsByTagName('pressure')->item(0)->nodeValue ?? 'Unknown';
$control_rods = $doc->getElementsByTagName('control_rods')->item(0)->nodeValue ?? 'Unknown';
```

XML is everywhere, the next step is clear which is to test for XXE  
the situation here is basic we can use the basic XXE to get the flag using external entities like:

![](https://miro.medium.com/v2/resize:fit:875/1*xGYSfu4zJqGOPIQ3L4sAOQ.png)

and here we go flag: `INTIGRITI{c4r3ful_w17h_7h053_c0n7r0l5_0r_7h3r3_w1ll_b3_4_m3l7d0wn}`

That’s we reached the end hope you enjoyed see you  
If you have any questions, You can reach me through my social accounts:  
[Twitter(X)](https://twitter.com/HunterXReda) | [Linkedin](https://www.linkedin.com/in/0xhunter/) | [Github](https://github.com/0xHunterr) |[Facebook](https://www.facebook.com/0xHunterr)