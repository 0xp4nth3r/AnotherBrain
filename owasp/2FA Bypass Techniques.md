

1   Response Manipulation
2	 Status Code Manipulation
3	 2FA Code Leakage in Response
4	 JS File Analysis
5	  2FA Code Reusability
6	  Lack of Brute-Force Protection
7	  Missing 2FA Code Integrity Validation
8	  CSRF on 2FA Disabling
9	  Password Reset Disable 2FA
10  Backup Code Abuse
11	  Clickjacking on 2FA Disabling Page
12   Enabling 2FA doesn't expire Previously active Sessions
13   Bypass 2FA with null or 000000


# Response Manipulation


![[Pasted image 20231224224848.png]]

{“code”:”invalid_credentials”} 

![[Pasted image 20231224224930.png]]


Changing the HTTP code from​ ​ HTTP/1.1 401 Unauthorized​ ​ To HTTP/1.1 200 OK and the response value from {“code”:”invalid_credentials”}​ ​ to {“verify”:”true”}

https://avanishpathak.medium.com/an-account-takeover-vulnerability-due-to-response-manipulation-e23fe629bd1

https://medium.com/@tanyago/account-takeover-through-response-manipulation-db66c2f8830


# Status Code Manipulation

~~~
If Status Code is 400,404 etc
Try to change it to 200 OK and see if it bypass restrictions
~~~

When enabling 2FA, it asked first for a password confirmation(you already know that :D). Then after entering the right password the response looked like this:

~~~
HTTP/1.1 200 OK
Date: Tue, 28 Jul 2020 11:30:14 GMT
Content-Type: application/json
Connection: close
Set-Cookie: __cfduid=somecookie; expires=Thu, 27-Aug-20 11:30:14 GMT; path=/; domain=*.redacted.com; HttpOnly; SameSite=Lax; Secure
Cache-Control: no-cache, private
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 99
Z-Redacted-Password-Auth-Valid-Until: 1595935589
Strict-Transport-Security: max-age=31536000; includeSubDomains
CF-Cache-Status: DYNAMIC
Expect-CT: max-age=604800, report-uri="https://report-uri.cloudflare.com/cdn-cgi/beacon/expect-ct"
Server: cloudflare
Content-Length: 480

{"data":{"type":"createtwofa","attributes":{"secret":"code","qr":"someurl","qr_url":"anotherurl"},"id":"a code(not the password it asked)"},"last_user_action":{"date_iso8601":"2020-07-28T13:30:14+02:00","unix":"1595935814"}}
~~~


After this, it returned the value and the QR code. I scanned it using google-authenticator and clicked next, it asked for the authenticator code, and then confirmed 2fa was enabled. Everything was OK to try bypassing the password restriction using response manipulation.

So I disabled 2fa and tried to enable it when it asked for the password confirmation, it entered a wrong one and got this as a response:

~~~
HTTP/1.1 422 Unprocessable Entity
Date: Tue, 28 Jul 2020 11:41:27 GMT
Content-Type: application/json
Content-Length: 177
Connection: close
Set-Cookie: __cfduid=somecookie; expires=Thu, 27-Aug-20 11:41:26 GMT; path=/; domain=redacted.com; HttpOnly; SameSite=Lax; Secure
Cache-Control: no-cache, private
Access-Control-Allow-Headers: origin, content-type, access-token, x-tracking-id
Access-Control-Allow-Origin: *
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 99
X-BRedacted-Password-Auth-Valid-Until: 1595935589
Strict-Transport-Security: max-age=31536000; includeSubDomains
CF-Cache-Status: DYNAMIC
Expect-CT: max-age=604800, report-uri="https://report-uri.cloudflare.com/cdn-cgi/beacon/expect-ct"
Server: cloudflare

{"errors":[{"status":422,"code":"user.validation.exception","title":"Validation exception"}],"last_user_action":{"date_iso8601":"2020-07-28T13:41:27+02:00","unix":"1595936487"}}
~~~

Then i changed 

~~~
HTTP/1.1 200 OK
Date: Tue, 28 Jul 2020 11:30:14 GMT
Content-Type: application/json
Connection: close
Set-Cookie: __cfduid=somecookie; expires=Thu, 27-Aug-20 11:30:14 GMT; path=/; domain=*.redacted.com; HttpOnly; SameSite=Lax; Secure
Cache-Control: no-cache, private
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 99
Z-Redacted-Password-Auth-Valid-Until: 1595935589
Strict-Transport-Security: max-age=31536000; includeSubDomains
CF-Cache-Status: DYNAMIC
Expect-CT: max-age=604800, report-uri="https://report-uri.cloudflare.com/cdn-cgi/beacon/expect-ct"
Server: cloudflare
Content-Length: 480

{"data":{"type":"createtwofa","attributes":{"secret":"code","qr":"someurl","qr_url":"anotherurl"},"id":"a code(not the password it asked)"},"last_user_action":{"date_iso8601":"2020-07-28T13:30:14+02:00","unix":"1595935814"}}
~~~


https://muhdaffa.medium.com/bypass-2fa-using-status-code-manipulation-834a4fd8a9d4



# 2FA Code Leakage in Response

The user sends a request to enter the 2FA code during the authentication process.  
- The server verifies the 2FA code and authenticates the user.  
- After the authentication process is complete, the server returns a response.  
- However, the server discloses a message or data containing the 2FA code in the response.  
- In this case, any attacker or malicious user who sees the response can obtain the 2FA code and access the account bypassing security measures.

# JS File Analysis



https://blog.appsecco.com/static-analysis-of-client-side-javascript-for-pen-testers-and-bug-bounty-hunters-f1cb1a5d5288


# CSRF on 2FA Disabling


1. Request a 2FA code and use it
2. Now, Re-use the 2FA code and if it is used successfully that's an issue.
3. Also, try requesting multiple 2FA codes and see if previously requested Codes  
expire or not when a new code is requested
4. Also, try to re-use the previously used code after long time duration say 1 day or  
more. That will be an potential issue as 1 day is enough duration to crack and guess  
a 6-digit 2FA code

# Click jacking on 2FA Disabling Page
 
 Try to Iframe the page where the application allows a user to disable 2FA

  If Iframe is successful, try to perform a social engineering attack to manipulate victim to fall in your trap.

# Bypass 2FA with null or 000000

~~~
Enter the code 000000 or null to bypass 2FA protection.
~~~





https://github.com/Az0x7/vulnerability-Checklist/blob/main/2FA%20Bypass/2FA%20bypass.md
