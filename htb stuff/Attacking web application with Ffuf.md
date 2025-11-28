Introduction :

In cybersecurity, **Ffuf** (short for **"Fuzz Faster U Fool"**) is a **fast web fuzzer** used primarily for **web content discovery and enumeration**. It helps ethical hackers and penetration testers find hidden files, directories, subdomains, and parameters on web servers

### What is Fuzzing?

Fuzzing is the process of sending a large number of inputs (like URLs or parameters) to a target application to observe its behavior and identify hidden paths or vulnerabilities.


## Directory Fuzzing

As we can see from the example above, the main two options are `-w` for wordlists and `-u` for the URL. We can assign a wordlist to a keyword to refer to it where we want to fuzz. For example, we can pick our wordlist and assign the keyword `FUZZ` to it by adding `:FUZZ` after it:

Directory Fuzzing

```shell-session
0xM4llc10u5@htb[/htb]$ ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ
```
the above code is for -w stands for the location for the directory-list-2.3-small.txt located  and the -u , we use further is for the url  -u http://SERVER_IP:PORT/FUZZ```

and the combined version of the fuff will be 
 ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://SERVER_IP:PORT/FUZZ

IN the directory fuzzing challenge the command is 

 ffuf -w Downloads/directory-list-2.3-small.txt:FUZZ -u http://94.237.58.121:50977/FUZZ 

## Extension Fuzzing

In the previous section, we found that we had access to `/blog`, but the directory returned an empty page, and we cannot manually locate any links or pages. So, we will once again utilize web fuzzing to see if the directory contains any hidden pages. However, before we start, we must find out what types of pages the website uses, like `.html`, `.aspx`, `.php`, or something else.

One common way to identify that is by finding the server type through the HTTP response headers and guessing the extension. For example, if the server is `apache`, then it may be `.php`, or if it was `IIS`, then it could be `.asp` or `.aspx`, and so on. This method is not very practical, though. So, we will again utilize `ffuf` to fuzz the extension, similar to how we fuzzed for directories. Instead of placing the `FUZZ` keyword where the directory name would be, we would place it where the extension would be `.FUZZ`, and use a wordlist for common extensions. We can utilize the following wordlist in `SecLists` for extensions:

```shell-session
 ffuf -w /opt/useful/seclists/Discovery/Web-Content/web-extensions.txt:FUZZ -u http://SERVER_IP:PORT/blog/indexFUZZ
```

PAGE FUZZING :

it same as the extension fuzzing , but here we are going to search the any page that is inside the port , but in the extension fuzzing we done searching the what server it is running 

ffuf -w Downloads/directory-list-2.3-small.txt:FUZZ -u http://server:port/blog/FUZZ.php here  we found the server is running through php and there is a blog directory 

# Recursive Fuzzing

So far, we have been fuzzing for directories, then going under these directories, and then fuzzing for files. However, if we had dozens of directories, each with their own subdirectories and files, this would take a very long time to complete. To be able to automate this, we will utilize what is known as `recursive fuzzing`.

## Recursive Flags

When we scan recursively, it automatically starts another scan under any newly identified directories that may have on their pages until it has fuzzed the main website and all of its subdirectories.

Some websites may have a big tree of sub-directories, like `/login/user/content/uploads/...etc`, and this will expand the scanning tree and may take a very long time to scan them all. This is why it is always advised to specify a `depth` to our recursive scan, such that it will not scan directories that are deeper than that depth. Once we fuzz the first directories, we can then pick the most interesting directories and run another scan to direct our scan better.

In `ffuf`, we can enable recursive scanning with the `-recursion` flag, and we can specify the depth with the `-recursion-depth` flag. If we specify `-recursion-depth 1`, it will only fuzz the main directories and their direct sub-directories. If any sub-sub-directories are identified (like `/login/user`, it will not fuzz them for pages). When using recursion in `ffuf`, we can specify our extension with `-e .php`

ffuf -w Downloads/directory-list-2.3-small.txt:FUZZ -u http://ip:port/FUZZ -recursion -recursion-depth 1 -e .php -v

## Sub-domains

A sub-domain is any website underlying another domain. For example, `https://photos.google.com` is the `photos` sub-domain of `google.com`.

In this case, we are simply checking different websites to see if they exist by checking if they have a public DNS record that would redirect us to a working server IP. So, let's run a scan and see if we get any hits. Before we can start our scan, we need two things:

- A `wordlist`
- A `target`

Luckily for us, in the `SecLists` repo, there is a specific section for sub-domain wordlists, consisting of common words usually used for sub-domains. We can find it in `/opt/useful/seclists/Discovery/DNS/`. In our case, we would be using a shorter wordlist, which is `subdomains-top1million-5000.txt`. If we want to extend our scan, we can pick a larger list.

As for our target, we will use `inlanefreight.com` as our target and run our scan on it. Let us use `ffuf` and place the `FUZZ` keyword in the place of sub-domains, and see if we get any hits:

ffuf -w Downloads/subdomains-top1million-5000.txt:FUZZ -u http://FUZZ.inlanefreight.com/ 

# Vhost Fuzzing

As we saw in the previous section, we were able to fuzz public sub-domains using public DNS records. However, when it came to fuzzing sub-domains that do not have a public DNS record or sub-domains under websites that are not public, we could not use the same method. In this section, we will learn how to do that with `Vhost Fuzzing`.

## Vhosts vs. Sub-domains

The key difference between VHosts and sub-domains is that a VHost is basically a 'sub-domain' served on the same server and has the same IP, such that a single IP could be serving two or more different websites.

`VHosts may or may not have public DNS records.`

In many cases, many websites would actually have sub-domains that are not public and will not publish them in public DNS records, and hence if we visit them in a browser, we would fail to connect, as the public DNS would not know their IP. Once again, if we use the `sub-domain fuzzing`, we would only be able to identify public sub-domains but will not identify any sub-domains that are not public.

This is where we utilize `VHosts Fuzzing` on an IP we already have. We will run a scan and test for scans on the same IP, and then we will be able to identify both public and non-public sub-domains and VHosts.

ffuf -w Downloads/subdomains-top1million-5000.txt:FUZZ -u http://htb.academy  -H  'Host: FUZZ.academy.htb'

# Filtering Results

So far, we have not been using any filtering to our `ffuf`, and the results are automatically filtered by default by their HTTP code, which filters out code `404 NOT FOUND`, and keeps the rest. However, as we saw in our previous run of `ffuf`, we can get many responses with code `200`. So, in this case, we will have to filter the results based on another factor, which we will learn in this section.

## Filtering

`Ffuf` provides the option to match or filter out a specific HTTP code, response size, or amount of words. We can see that with `ffuf -h`:

fuff  -w Downlods/subdomains-top1million-5000.txt:FUZZ -u http://ip address:port/ -H 'H: FUZZ.academy.htb'
after viewing the response code add with the filter like 

ffuf  -w Downlods/subdomains-top1million-5000.txt:FUZZ -u http://ip address:port/ -H 'H: FUZZ.academy.htb' -fs 900

# Parameter Fuzzing - GET

If we run a recursive `ffuf` scan on `admin.academy.htb`, we should find `http://admin.academy.htb:PORT/admin/admin.php`. If we try accessing this page, we see the following:

That indicates that there must be something that identifies users to verify whether they have access to read the `flag`. We did not login, nor do we have any cookie that can be verified at the backend. So, perhaps there is a key that we can pass to the page to read the `flag`. Such keys would usually be passed as a `parameter`, using either a `GET` or a `POST` HTTP request. This section will discuss how to fuzz for such parameters until we identify a parameter that can be accepted by the page.

fuff -w Downloads/burp-parameter-names.txt:FUZZ -u http://94.237.123.146:54261/admin/admin.php?FUZZ=key 

# Parameter Fuzzing - POST


The main difference between `POST` requests and `GET` requests is that `POST` requests are not passed with the URL and cannot simply be appended after a `?` symbol. `POST` requests are passed in the `data` field within the HTTP request. Check out the [Web Requests](https://academy.hackthebox.com/module/details/35) module to learn more about HTTP requests.

To fuzz the `data` field with `ffuf`, we can use the `-d` flag, as we saw previously in the output of `ffuf -h`. We also have to add `-X POST` to send `POST` requests.

ffuf -w Downloads/burp-parameter-names.txt:FUZZ -u http://ip_address:port/admin/admin.php -X post -d 'FUZZ=key' -H 'Content-type: application/x-www-form-urlencoded' -fs xxx