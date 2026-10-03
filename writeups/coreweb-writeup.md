# coreweb writeup

# reconnaissance
## i started by examinining the web app, the first thought i had is to try some ssrf, or bugs related to pdf parsing. 
## to check the basics , i tried 169.254.169.254.nip.io hoping that i will get aws metadata, after getting nothing i got my personal link at webhook.site and got a ping back getting the chromium version

# inital access 
## after googling for a bit, i found out that this version is vulnerable to CVE-2025-0291 and started searching for the exploit. eventually, i found https://github.com/Petitoto/chromium-exploit-dev/tree/main , after reading documentation, i built the actual exploit by executing python tools/build.py main.js exploit.js . i found out that it is reproducible , and started setting it up to get the reverse shell. i launched netcat at port 4444 and a tcp tunnel using pinggy. also i added actual reverse shell code to calc.js (PoC file, that was used to launch a calculator as proof) and built it again. now i launched a server on port 8000, and lauched a tunnelled it though localhost.run, after i pasted the tunnel link into the web app, and it started fetching it

# getting the flag
## after getting the shell i found the flag using find / -name "flag*" 2>/dev/null and found the flag 