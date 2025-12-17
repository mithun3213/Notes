
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
