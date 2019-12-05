**Cross-Site Scripting** (also known as XSS) is one of the most common application-layer web attacks.
This is a type of attack that injects malicious code into web systems forcing it to produce changed data, replacing links (visible / hidden) or displaying its own advertising on the affected resource.
XSS can affect user in next ways:
It can steal user's session or sensitive data, rewrite web pages, redirect user to phishing or malware site
But most severe it can do, is to install XSS proxy, which allows attacker to observe and direct all user's behavior on vulnerable site and force user to other sites.
Now let me explain how XSS works. 
In general, there are two stages to a typical XSS attack:
The first one is Injection of malicious code into a web page that the victim visits.
On the second stage, after the victim visits the web page, malicious code is executed.
For example, look at the snippet of server-side pseudocode that is used to display the most recent comment on a web page:
```javascript
<html>
<h1>Most recent comment</h1>
database.latestComment
</html>
```
It simply takes the latest comment from a database and includes it in an HTML page. 
It assumes that the comment printed out consists of only text and contains no HTML tags or other code. It is vulnerable to XSS, because an attacker could submit a comment that contains a malicious payload, for example:
```javascript
<script>doSomethingEvil();</script>
```
This comment will be saved in database. 
The web server provides the following HTML code to users that visit this web page:
```javascript
<html>
<h1>Most recent comment</h1>
<script>doSomethingEvil();</script>
</html>
```
When the page loads in the victim’s browser, the attacker’s malicious script executes. Most often, the victim does not realize it and is unable to prevent such an attack.
The figure on slide illustrates a step-by-step walkthrough of a simple XSS attack.
1.	The attacker injects a payload into the website’s database by submitting a vulnerable form with malicious JavaScript content.
2.	The victim requests the web page from the web server.
3.	The web server serves the victim’s browser the page with attacker’s payload as part of the HTML body.
4.	The victim’s browser executes the malicious script contained in the HTML body. In this case, it sends the victim’s cookie to the attacker’s server.
5.	The attacker now simply needs to extract the victim’s cookie when the HTTP request arrives at the server.
6.	The attacker can now use the victim’s stolen cookie for impersonation.
**There are next types of XSS:**
* Stored XSS Attacks
* Reflected XSS Attacks
*	DOM Based XSS Attacks
**Stored (or Persistent) XSS ATTACKS.**
Stored attacks XSS represent one of three major types of Cross-site Scripting.
A Persistent XSS attack is possible when a website or web application stores user input and later provides it to other users. An application is vulnerable if it does not validate user input before storing content.
Attackers use vulnerable web pages to inject malicious code and have it stored on the web server for later use.
You saw this situation on the previous example.
One of the most frequent targets are websites that allow users to share content, such as blogs, social networks message boards and anther.  
**Reflected XSS attack.**
Next type of attack is Reflected XSS attack.
It also known as non-persistent attack.
1.	An attacker pre-creates a URL link that will contain malicious code and sends it to his victim.
2.	Victim follow this link 
3.	Victim browser load legitimate site and also execute malicious script.
4.	As a result, the browser will receive all necessary cookies from this user.
The main feature of these attacks is that the attacker must make the victim to follow the link.
**DOM-based XSS**
DOM-based XSS is an advanced XSS attack. It is possible if the web application’s client-side scripts write data provided by the user to the Document Object Model (DOM). 
Then the data is read from the DOM by the web application and outputted to the browse and make damage.
The main feature of this kind of attacks is that a DOM-based XSS attack is often a client-side attack and the malicious payload is never sent to the server. This makes it even more difficult to detect for Web Application Firewalls (WAFs) and security engineers who analyze server logs because they will never even see the attack. 
**Preventing cross-site scripting.**
1. Filter input on arrival.The simplest and arguably the easiest form of cross-site scripting vulnerability elimination would be to pass all external data through a filter. Such a filter would remove dangerous keywords, for example, the infamous <script> tag, JavaScript commands, CSS styles, and other dangerous HTML markups (such as those that contain event handlers.) In all cases, the web developer should be aware that the data is coming from an external source and therefore must not be trusted since it may introduce a security vulnerability. You can implement own XSS filter mechanisms or use some sort of library that has been tried and tested by the community at large. Many libraries exist to choose from and your choice will primarily depend on the back-end technology that your web server uses.
The side effect of filtering techniques is that legitimate text is often removed because it matches forbidden keywords.
2. Escaping is the primary means to avoid cross-site scripting attacks. When escaping, you are telling the web browser that the data you are sending should be treated as data and should not be interpreted in any other way. If an attacker manages to put a malicious script on your page, the victim will not be affected because the browser will not execute the script if it is properly escaped.
The two most popular escaping libraries available are the ESAPI provided by OWASP and AntiXSS provided for Microsoft. ESAPI can plug into various technologies such as Java, .NET, PHP, Classic ASP, Cold Fusion, Python, and Haskell.
There are several places on your web page which you need to ensure are properly escaped. You can use your own escaping functions (not recommended) or existing libraries.
HTML
Use HTML escaping when untrusted data is inserted between HTML opening and closing tags. These are standards tags such as <body>, <div>, <table>, etc. For example:
```javascript
<div>If this data is untrusted, it must be HTML-escaped.</div>
```
JavaScript 
Use JavaScript escaping when untrusted data is inserted inside one of your scripts, or in a place where malicious JavaScript may be included. This includes certain HTML attributes such as style and all event handlers such as onmouseover and onload. For example:
```javascript
<script>alert('If this data is untrusted, it must be JavaScript-escaped.')</script>
<body onload="If this data is untrusted, it must be JavaScript-escaped.">
```
CSS
Use CSS escaping when untrusted data is inserted inside your CSS styles. Many CSS styles can be used to smuggle a script into your page. For example:
```javascript
<div style="background-image: If this data is untrusted, it must be CSS-escaped.">
```
3. Encode data on output. At the point where user-controllable data is output in HTTP responses, encode the output to prevent it from being interpreted as active content. Depending on the output context, this might require applying combinations of HTML, URL, JavaScript, and CSS encoding.
Use appropriate response headers. To prevent XSS in HTTP responses that should not contain any HTML or JavaScript, you can use the Content-Type and X-Content-Type-Options headers to ensure that browsers interpret the responses in the required way.
4. Content Security Policy. As a last line of defense, you can use Content Security Policy (CSP) to reduce the severity of any XSS vulnerabilities that still occur. It's a browser side mechanism which allows you to create source whitelists for client side resources of your web application, e.g. JavaScript, CSS, images, etc. CSP via special HTTP header instructs the browser to only execute or render resources from those sources.
For example this CSP:
```javascript
Content-Security-Policy: default-src: 'self'; script-src: 'self' static.domain.tld
```
Will instruct web browser to load all resources only from the page's origin and JavaScript source code files additionally from static.domain.tld.

Links:
https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html
https://www.owasp.org/index.php/XSS_Filter_Evasion_Cheat_Sheet
https://content-security-policy.com/
https://www.imperva.com/learn/application-security/cross-site-scripting-xss-attacks/
https://www.acunetix.com/websitesecurity/cross-site-scripting/
https://www.acunetix.com/blog/articles/persistent-xss/
https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project
https://portswigger.net/web-security/cross-site-scripting
