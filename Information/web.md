
cd C:\Users\mithu\Documents\blog
git init
git remote add origin https://github.com/mithun3213/blog.git
git branch -M main
git remote -v

origin  https://github.com/mithun3213/blog.git (fetch)
origin  https://github.com/mithun3213/blog.git (push)

git add .
git commit -m "Initial upload from local blog folder"
git push origin main or  --force


code :

@echo off
cd "C:\Users\mithu\Music\ctf"

echo ==========================
echo Auto Push Started at %date% %time%
echo ==========================

git add .
git commit -m "Auto update: %date% %time%"
git push origin main

echo Done!
pause



to download  only a particular folder from a GitHub repo use the link
**https://downgit.github.io/**


**To find the directory using the fuzz the command is :**

ffuf -u https://chall.polygl0ts.ch:8085/FUZZ -w /home/mithun/SecLists/Discovery/Web-Content/common.txt

 https://chall.polygl0ts.ch:8085 --> replace with the given the url


## `etc/passwd` on Linux **almost always starts like this**:

root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin



### What Redis is

- An **in-memory data structure store**
    
- Works as:
    
    - Cache
        
    - Database
        
    - Message broker
        
- Listens by default on **port 6379**


### What Memcached is

- A **simple in-memory cache**
    
- Even simpler than Redis
    
- Key → value only
    
- No persistence
    
- Default port: **11211**



--------

## AWS EC2 instance ??

n AWS EC2 instance is a resizable virtual server (VM) in the cloud, providing secure, scalable compute capacity for nearly any workload, allowing users to pay only for what they use, with options for various operating systems, processors, storage, and networking to match specific needs like web hosting, databases, or machine learning. Users select an instance type (e.g., General Purpose, Compute Optimized, Memory Optimized) and configure CPU, RAM, and storage, launching them on-demand and easily scaling up or down


### what is latest/meta-data/iam/security-credentials/admin ?

`http://169.254.169.254/latest/meta-data/iam/security-credentials/`

is a specific URL for the AWS EC2 Instance Metadata Service (IMDS) that retrieves temporary security credentials (Access Key ID, Secret Access Key, Session Token) for an IAM roleAmazon Web Services Documentation assigned to the running EC2 instance, allowing applications on the instance to access AWS resources with that role's permissions without hardcoding keys. Accessing this endpoint directly (like `.../security-credentials/<role_name>`) provides programmatic credentials, while `/latest/` fetches the most current version of metadata




