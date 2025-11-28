
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

