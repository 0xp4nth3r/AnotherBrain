

Subdomain enum

~~~
subfinder -d url -o subfinder.txt
~~~

~~~
assetfinder -sub-only url > assfinder.txt
~~~

find live domain

~~~
cat subfinder.txt | httprobe | tee -a alivesubs.txt
~~~

wayback urls


~~~
cat alivesubs.txt | waybackurls | tee -a urls.txt
~~~


~~~
cat urls.txt | httprobe | tee -a aliveurls.txt
~~~



Shodan Dork

~~~
ssl.cert.subject.CN:"taret" http.title:"gitlab"
~~~

~~~
ssl.cert.subject.CN:"target" http.title:"index of/"
~~~

~~~
ssl.cert.subject.CN:"target" "230 login successfull" port"21"
~~~

~~~
ssl.cert.subject.CN:"*TARGET"+200 http.title:"Admin"
~~~


Subfinder 

~~~
subfinder -d target.com -all -recursive -t 200 -silent -o subfinder-rescursive.txt
~~~


Findomain 

~~~
findomain --quiet -t target.com | tee findomain.txt
~~~

Amass 

~~~
amass enum -passive -d target.com -o amass.txt
~~~

Active subdomain enumeration

~~~
python3 subbrute.py target.com -w wordlist.txt -o brute_subs.txt
~~~

Subdomain Brute-Forcing

~~~
ffuf -u https://target.com -H "Host: FUZZ.target.com" -w /usr/share/wordlists/subdomains.txt -t 100 -fc 403 | tee ffuf_subs_output.txt
~~~

indentifing live web services

```
httpx -l live_subs.txt -title -sc -location -p 80,443,8000,8080,8443 -td -cl -probe -o httpx_output.txt
```

