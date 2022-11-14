# PCAP Badge
## PCAP 01
[Challenge URL](https://pentesterlab.com/exercises/pcap_01/course)

This challenge, we'll be analyzing `.pcap` file. A `.pcap` (packet-capture) file is a 'point-in-time' record of every packet/request that's been made on a specific (or multiple) network interface, recording every packet that goes through [OSIModel]({% link notes/OSIModel.md%}) levels 2 through 7. There's a few different tools available for this, but for now we'll be using wireshark.

We're told the `.pcap` file in this case only contains a single TCP connection, a client connecting to the server with the string `the key is ...`. To start, we'll download the `.pcap` file given in the challenge, then open it in `wireshark`. Once the file is loaded, we're told to use the `Follow -> "TCP-Stream"` functionality. `TCP-Stream` essentially reconstructs the original connection, allowing us to see the original request, instead of the main view of individual packets/frames. Once the new `TCP-Stream` window opens, it should reveal the key to this challenge.

## PCAP 02
[Challenge URL](https://pentesterlab.com/exercises/pcap_02/course)

This challenge contains only a `TCP` connection of a user connecting to a server via `telnet`. The `.pcap` will show multiple color-coded connections of requests sent by the server and client. Because `telnet` is a plaintext protocol, we'll be able to clearly see the username and password the client sent to the server, as well as any commands the user ran in the session. 

To start, we'll again right-click and select `Follow -> TCP Stream`. In the new window, we can see the server response in blue text starting with the Linux kernel info and a login prompt. In red, we can see the `victim` user responses. The key for this challenge is the password the user submitted to the server. 

## PCAP 03
[Challenge URL](https://pentesterlab.com/exercises/pcap_03/course)

Here we're looking at an unsecured `FTP` session. Because the connection was used *without* `TLS` encryption, we can see the full plain-text responses of the server and client, allowing us to see the username/password combo the user used, as well as any files they downloaded in the session.

Using `Follow TCP Stream`, we see the initial response from the server, followed by the user submitting a username and password. We also see the user ran `PWD` and `HELP SITE` before running `QUIT` to close the connection. The key for this challenge is the password the `victim` user submitted. 

## PCAP 04
[Challenge URL](https://pentesterlab.com/exercises/pcap_04/course)

This challenge shows a user using `FTP` in `passive` mode.
During an `active` `FTP` connection, the client connects to the `FTP COMMAND` channel, typically on port 21, the client then must use the `PORT` command to specify the port for the `DATA` channel. The *server* then initiates the connection of the `DATA` channel from server-port 20 to the port the *client* specified.

For a `passive FTP` connection, the *client* again initiates the connection to the server's `COMMAND` channel on port 21, then sens the `PASV` command, the *server* responds to `PASV` with a random port and opens it for the `DATA` channel. The *client* then initiates another connection to the *server* on the port that the *server* responded with from the `PASV` command. 

```sh
# Active
# Command Channel
Client(random Port) --> Server(port 21)
$ PORT
# Data Channel
Server(port20) --> Client(random port)

# Passive
# Command Channel
Client(random Port) --> Server(port 21)
$ PASV
# Data Channel
Client(random port) --> Server(random port)
```

We'll again use `Follow TCP Stream` to inspect the packets. However, we have multiple connections/streams to inspect with this challenge. If we only inspect the `COMMAND` channel stream, we'll see the user login, commands ran, and the name of the file the user retrieved, but our goal for this challenge is to retrieve the *contents* of the file the user downloaded. To do so, we'll need to look at the correct connection. In the lower-right corner of the `Follow TCP` window, we can select the stream number we'd like to view, in this case either `0` or `1`. Stream `0` is the `COMMAND` channel, shows the user interaction, and contains many more requests and responses. Stream `1` is our `DATA` channel, only showing 10 requests/responses in our main window, and in the `Follow` window, shows the contents of the file the user requested. The content of this file is the key for this challenge. 

## PCAP 05
[Challenge URL](https://pentesterlab.com/exercises/pcap_05/course)

We're again looking at a `passive FTP` connection and looking for the contents of the file the user transferred. However, we're told some 'noise'/additional requests have been added. Instead of just the `FTP` requests, we also have some `DNS` and `ICMP` traffic cluttering the file. If we right-click on any of the `DNS` or `ICMP` packets, we're able to follow the `UDP Stream`, however because `FTP` is a `TCP` based protocol, we won't see any of the `FTP` traffic in the `UDP` streams.

To get to the correct data stream, scan the available packets for one with `FTP` in the protocol. From there, we can change our stream to `1`, which in this case is the `DATA` channel. Here we can see the contents of the file requested, and the key for this challenge. 

## PCAP 06
[Challenge URL](https://pentesterlab.com/exercises/pcap_06/course)

This challenge we're looking at a `rsh` connection, the pre-cursor to `ssh`. `rsh` creates a file on the server that contains the `ip` of the *client*, and uses this as the main basis of authentication, which can allow a malicious user to spoof the `ip` and connect. Also, all operations are performed in plain-text, which will allow us to see the login information and commands the user issued. However, instead of this info appearing in the `TCP Stream` like in previous challenges, with `rsh` the information is sent in a single `frame`, in this case `frame 09`. In the main window's bottom section, there should be a 'Remote Shell' drop-down, expand this and we can see the client connected as the `root` user, sending `Client username: root`, `Server username: root`, and `Command to execute: cat /home/victim/key.txt`. The contents of that file is the key to this exercise. If we sort the frame list by protocol and go over the `rsh` frames, we can see the initial connection, followed by the auth and command frame. The server then executes the `cat` command and streams the encoded data back to the client.

To view the contents of the transferred file, we'll go back to `Follow TCP Stream`. Inspecting `stream 0` shows both *client* and *server* usernames, both `root` in this case, the `cat` command that was ran, followed by the output of that command, and thus the key for this challenge. 

## PCAP 07
[Challenge URL](https://pentesterlab.com/exercises/pcap_07/course)

This challenge we're examining an `rlogin` connection. Because `rlogin` sends credentials over plaintext, we can again `Follwo TCP Stream` to find the password the user sent, and thus the key for this challenge. There's only one `TCP Stream` in this case, and we can see the password/key at the top of the `TCP Stream` window after the username and network that was specified. We can also find the password in `frame 14` in the 'Data:' field under the 'Rlogin Protocol' section within the main window. Note that in this view, the password is followed by two carriage-return character codes (`\r\r`).

## PCAP 08
[Challenge URL](https://pentesterlab.com/exercises/pcap_08/course)

Here we're looking at a `SMTP` (Simple Main Transfer Protocol) connection to send an email. We can see the user provides a username/pass combo to login, but both values are `base64` encoded as part of the `SMTP` protocol.

Starting with the 'Info' section of the main window, we can see a series of `SMTP` frames 9 through 14;
```bash
AUTH LOGIN
334 VXNlcm5hbWU6
dmljdGlt
334 UGFzc3dvcmQ6
Password: MTVhNjkwYjItYWE0Ni00NzlmLWJjNWUtMzQ0ODE1OGYwNmNl
Response: 235 2.7.0 Authentication successful
```
This is also visible in the `Follow TCP Stream` view. If we select the 'Analyze' menu in the main window with `Frame 13` highlighted, we can select the 'Show Packet Bytes..' option, which will open a new window with the encoded password. From there, we can select the 'Decode as' option 'base64', leaving 'Show as' on 'ASCII' and get the plaintext version of the password/key for this challenge. 

## PCAP 09
[Challenge URL](https://pentesterlab.com/exercises/pcap_09/course)

Here we're again looking at the `SMTP` protocol, but our goal is to find the recipient of an email sent to `@pentesterlab.com` in the `TCP Stream`. The username/recipient of the email will be the key for this challenge. 

Looking at `stream 0`, we see the client connect and authenticate to the server, followed by a response from the server with the recipient and content of the email.

## PCAP 10
[Challenge URL](https://pentesterlab.com/exercises/pcap_10/course)

This challenge, our goal is to retrieve the contents of an attachment sent with an email. Opening the `Follow TCP Stream`, we can see the usual auth sequence, followed by the email headers, the plain text of the email, and at the end a bunch of what looks like garbage data after the `begin 644 the_key.zip`. This is the compressed data of the attachment. We have two options to obtain the attached `zip` file. Either save the full email as a `.eml` file and open it with a local email client, or decode the attachment using `uudecode`. I'll opt for the second option, using the `uudecode` command from the kali package `sharutils`. Copy the 'contents' of the `zip` file, from the `begin` to `end` entries into a new file. Use `uudecode -o decodedOutput encodedInputFile`, and it should output a `zip` archive, possibly two `zip` archives, one with our specified output file name, and one with the filename specified in the encoded input file. Run `unzip` on either archive, and the key will be in the outputted `./tmp/attachment.txt` file.

## PCAP 11
[Challenge URL](https://pentesterlab.com/exercises/pcap_11/course)

This challenge, we're looking at a network dump of the `POP3` email protocol. Our goal is to the find the password the `victim` user used to authenticate to the server. We can see the password/key for this challenge in two places. First, in the main request list in frame 11, we can see the frame is using the `POP` protocol with `PASS UUID` in the info column. Second, if we open the `Follow TCP Stream` window, we can see the entire connection exchange in `stream 0`, including the pass/key for the challenge. 

## PCAP 12
[Challenge URL](https://pentesterlab.com/exercises/pcap_12/course)

Here, we're looking at a connection using the `IMAP` email protocol. The entire exchange once again occurs in a single stream, `stream 0`, and in our `Follow TCP Stream` window we can see the entire exchange of the user asking the server to list it's capabilities, both pre- and post-auth. We can also see the username/pass the victim sent in plaintext in the stream, as well as in frame 09's info column with the command `LOGIN "victim" "UUID-pass-key-Here"`

## PCAP 13
[Challenge URL](https://pentesterlab.com/exercises/pcap_13/course)

This challenge we're looking at a single `HTTP GET` request. The key is in `GET` parameter of the request. We can see the key in the info column of frame 4, as well as if we right-click `Follow TCP Stream`, or `Follow HTTP Stream`. Both windows will reconstruct the request and show the key in the first line of the the HTTP header. 

## PCAP 14
[Challenge URL](https://pentesterlab.com/exercises/pcap_14/course)

Here we're looking at a single `HTTP POST` request. If we inspect frame 4, we can see the key in the "HTML Form URL Protocol" drop-down with the `key:value` values in the form. We can also see the `key=UUID` parameter if we use `Follow TCP Stream`.

Note: The `Content-Length` in the request header corresponds to the the size of the request body, which is the value the server uses to determine how much data is being read from the opened `TCP Socket`.

## PCAP 15
[Challenge URL](https://pentesterlab.com/exercises/pcap_15/course)

This challenge we're looking at an `HTTP` request, and the key for the challenge will be found in the `cookie` of the request. If we go to our `Follow TCP Stream` window, we can see the `Cookie: key=UUID` value in the request header. 

## PCAP 16
[Challenge URL](https://pentesterlab.com/exercises/pcap_16/course)

Here we're looking at another `HTTP` request. This challenge the key is in the body of the request. We can either `Follow TCP Stream` to the entire exchange, or we can inspect frame 8 and find the key in the 'Line-based text data' drop down. 

## PCAP 17
[Challenge URL](https://pentesterlab.com/exercises/pcap_17/course)

This challenge we'll find the key in the `Set-Cookie` header of the servers response to a `HTTP` request. `Follow TCP Stream` to reconstruct the request, and we can see a `key=` value in the `Set-Cookie` line, second-to-last line of the header. 

## PCAP 18
[Challenge URL](https://pentesterlab.com/exercises/pcap_18/course)

Here we're examining an `HTTP` request that's using `Authorization Basic`. The username/password are sent to the server in the `Authorization:` line of the request header, but is sent as `base64` instead of plain-text. Taking the `base64`'d value that comes after `Basic` in the header and decoding it should yield a username:password (separated by a colon). Tossing the value into python's `base64.b64decode(' ')` gives us an admin username, and associated password, the password is the key for this challenge. 

## PCAP 19
[Challenge URL](https://pentesterlab.com/exercises/pcap_19/course)

This challenge we're extracting the key our of a `Bearer` token in the `Authorization` header of an `HTTP` request. The token is using the `JSON Web Token` format;
- The Header
- a period (`.`)
- The payload
- another period `.`
- The signature. 

The entire token is encoded in `base64`, but we can see the two periods/dots separating the values in the `Authorization` header. Once we've decoded the base64, we get the initial `JSON` field denoting the algorithm and key type, followed by the 'my_key' field that contains the key for this challenge. 

## PCAP 20
[Challenge URL](https://pentesterlab.com/exercises/pcap_20/course)

This challenge, they key is in the `HTML` body of an `HTTP` request. However, to reduce the amount of data being transmitted, the server `gzip`s the response before sending it. We'll have to jump through a couple hoops to get the uncompressed response, but it's still very possible. To start, we'll use the typical `Follow TCP Stream` option. If we check the 'Accept-Encoding' section of the header, we'll see it indicates `gzip` was used. We'll then set 'show data as..' to 'Raw', then we'll use 'Save as' in the same window to save the reconstructed stream as a new local file with a `.gz` extension. Once we have the local file, open it in `vi`, using `micro` caused issues with the text-encoding, for some fucking reason. Use `dd` in `command` mode to delete the Header lines, leaving only the encoded body. If you left the correct portions of data, you should be able to run `file` on the edited file, and it should return it as `gzip` format. 

Once we have the `gzip`'d response body, we can run `gzip filename.gz` to get the uncompressed, original `HTML` body text that contains the key for this challenge. 

## PCAP 21
[Challenge URL](https://pentesterlab.com/exercises/pcap_21/course)

Here we'll again find the key to the challenge in the body of an `HTML` response. This time, however, the 'Accept-Encoding' header states `deflate`, which uses the `zlib` compression algorithm.

We'll follow the same steps as the previous challenge to start, setting 'Show Data as..' to `Raw` and saving a local copy. However, in order to get `gunzip` to recognize the data, we'll need to add some `magic bytes` to the beginning of the file before decompressing it. First, use `vi` to remove the header of the file (including the first 'empty' line above the body), leaving only the encoded body of the response. Then, we'll run `printf "\x1f\x8b\x08\x00\x00\x00\x00\x00"  | cat - pcap_21_extracted_body | gunzip`. This will add the required `magic bytes` to the the beginning of the file stream from `cat`, then pipe the entire thing into `gunzip` to decompress it, printing on the contents to STDOUT. If we wanted to keep a copy of the output, we can append `> newfilename` to the end of the command. 

## PCAP 22
[Challenge URL](https://pentesterlab.com/exercises/pcap_22/course)

Here, the key to the challenge is again in the body of the single `HTTP` request. If we `Follow TCP Stream`, we can see the response is using `chunk` encoding because of the the `hex` numbers before every line in the body, indicating the size of the proceeding chunk, and the lack of a `Content-Legnth` header. `Chunk` encoding allows the server to send 'chunks' of the response without waiting for the entire response to be ready to send. However, there isn't any compression or special encoding with it, so we can see the key in plain-text. 

## PCAP 23
[Challenge URL](https://pentesterlab.com/exercises/pcap_23/course)

This challenge, we're looking at two `HTTP` requests/responses. But because the client/server are using the `Connection: keep-alive` parameter, both requests use a single `TCP Stream`. If we `Follow TCP Stream`, we can see both `GET` requests and the resulting server response. The key for the challenge is in a url-parameter of the 2nd `GET` request.

## PCAP 24
[Challenge URL](https://pentesterlab.com/exercises/pcap_24/course)

Here we're looking at a `UDP` stream. The `.pcap` contains a single `DNS` query, which results as a single-frame request from the client, and a single-frame response from the server. If we 'right-click' one of the frames, we can `Follow -> UDP Stream` to see the exchange. If we open the 'Domain Name System (query)' drop down of the 1st frame in the main window, we can see the query for a 'Class A' record to get the IP of the given hostname. In the second frame, we can see the servers response in the 'Answer' section, relaying that the `ip` of the host is `127.0.0.1`. In this case, the hostname that was queried is the key for the challenge. 

## PCAP 25
[Challenge URL](https://pentesterlab.com/exercises/pcap_25/course)

This challenge, we're looking at a single `DNS` query over `TCP`. We can see the associated `SYN -> ACK` frame requests in the main window, along with frames 04 and 06 containing the `DNS` query and response, respectively. The query is again for an 'A Record', the `ip` of the given hostname. We can see the hostname in both the `Follow TCP Stream` window, and in the 'Query' and 'Answer' sections of the 'Domain Name System' drop-down of the `DNS` protocol frames in the main window. The hostname is the key for the challenge. 

## PCAP 26
[Challenge URL](https://pentesterlab.com/exercises/pcap_26/course)

Here we're looking at another `UDP` `DNS` request/response. The client requests a `TXT` record from the server for `key.pentesterlab.com`. The server responds with the `TXT` content. We can see the response in the 'Domain Name System' drop-down of frame 02 in the main window, as well as in the `Follow UDP Stream` window. `Follow UDP Stream` shows the request for `key.pentesterlab.com`, followed by the servers response of the name of the requested file/data, then the contents of the file, which in this case is the key for the challenge. 

## PCAP 27
[Challenge URL](https://pentesterlab.com/exercises/pcap_27/course)

This challenge we're looking at a `.pcap` containing multiple `DNS` requests to illustrate a common problem with IOT devices. Some IOT devices will include a `Transaction ID` with every sent request. The idea is that the response should have a matching `Transaction ID` to prevent an attacker from sending a malicious response. However, often times this can be poorly implemented, such as using a fixed ID for every transaction, very predictable or incremental IDs, or failing to implement a check on the client that the response's ID actually matches. 

This challenge is looking at the first example of using a fixed `Transaction ID` set to `0`. Starting with the first frame, we'll open up the 'Domain Name System' drop-down, the first item is the `Transaction ID` for the request/response as a `hex` address. The ID of our first query is `0xbad9`. Because we know we're looking for `Transaction ID 0`, (which would be `0x0000` in `hex`), we can use the filter bar in the top of our main window, and input `dns.id==0` (two equal signs) to filter out every `UDP` stream that *doesn't* have ID `0`. From here, we can find the hostname that was queried in the 'info' column, in the 'Domain Name System' drop-down, as well as in the `Follow UDP Stream` window. The hostname queried with `Transaction ID 0` is the key for this challenge. 

## PCAP 28
[Challenge URL](https://pentesterlab.com/exercises/pcap_28/course)

Here we're looking at an attacker trying to inject a `DNS` response to the client in an attempt to spoof the `TXT` record that gets returned. Looking at frame 01, we see in the 'info' column that it's the initial query, with a `Transaction ID` of `0xf755`. The correct key is in the `TXT` response of the corresponding query response with the same ID of `0xf755`. If we look at a response the attacker sent, (any DNS frame except frames 01 and 02), we can see at the bottom of the 'Domain Name System' drop down section `[Unsolicited: True]`, indicating that the client did not explicitly request this response. 

To filter out all of the attacker's queries, we can use `dns.id==0xf755` in the filter field of the main window to only show the requests/responses with the matching ID. If we look at our matching frame 02, and look at the bottom of the 'Domain Name System' drop-down, the `[Unsolicited: True]` notation is absent, indicating this is the response from the server the client actually requested. The key for this challenge is the `TXT` record in the response from frame 02.

## PCAP 29
[Challenge URL](https://pentesterlab.com/exercises/pcap_29/course)

This challenge is demonstrating the use of an `ICMP` (ping) request/reply as a covert data channel. If we open up the the 'Internet Control Message Protocol -> Data' drop-downs in the main window, we see 36 bytes of data embedded into the `ICMP` packets. An `ICMP` packet is made up of four sections, described in the table below.

| Section     | Byte Size |
| ----------- | --------- |
| MAC Header  | 14 bytes  |
| IP Header   | 20 bytes  |
| ICMP Header | 8 bytes   |
| ICMP Data   | Variable  |

`ICMP Data` is the section that can be used to transmit arbitrary data. In this case the embedded data is the `hex` encoded key for the challenge. Copy the value of the 'Data:' field from either request/reply packet, and decode the `hex` into `ASCII` to get the key for this challenge. 

## PCAP 30
[Challenge URL](https://pentesterlab.com/exercises/pcap_30/course)

Here we're looking at a client initiating a `TLS` connection to a server. The connection begins with the `TCP SYN -> ACK` sequence, followed by the client sending a `TLS Client Hello` packet to begin the `TLS Handshake`. The server responds with a `TLS Server Hello` packet, then initiates the key exchange for the connection. Because `TLS` encrypts the traffic, if we `Follow TCP Stream` the headers/body of the request are encrypted and we get essentially mostly garbage data. However, in the `Server Hello` packet, the server reveals the certificate, cipher suite, and the server's `Common Name` for the connection. The `Common Name` (CN) can be found in the 'Transport Layer Security -> TLS Record Layer: Handshake Protocol: Certificate -> Certificates' drop-down which contains an (id-at-commonName=) parameter. It can also be seen as some of the only plain-text information available in the `TCP Stream`. The CN for the server is the key for this challenge. If copying the key from the `Follow TCP Stream` window, be sure not to copy any leading/trailing characters that are part of the `TLS` encoding. 

## PCAP 31
[Challenge URL](https://pentesterlab.com/exercises/pcap_31/course)

This challenge we're looking at a client connecting to a server that potentially hosts multiple `TLS` servers on the same port. Because there's no way for the server to know which `TLS` server the client is attempting to connect to at the `TCP` level, in order for the server to return the correct certificate, the client sends the `Client Hello` packet with a `Server Name Indication (SNI)` extension to indicate which server the connection is too. The `SNI` can be found in the 'Transport Layer Security -> Record Layer -> Handshake Protocol -> Extension: server_name -> Server Name Indication Extension' drop down. The server name is the key for this challenge. 

## PCAP 32
[Challenge URL](https://pentesterlab.com/exercises/pcap_32/course)

In this challenge, we've obtained/been provided the private key (the `.pem` file) the server uses to encrypt traffic. Using the private key, we can decrypt the packets being sent/recieved to plain-text.

To do so, we'll right-click on one of the `TLS` packets, and select 'Protocol Preferences -> Transport Layer Security -> RSA Keys List'. In the pop-up window, enter the *server* IP, the port used (port 443), then select and load the private-key file. Once we hit 'Ok' on this window, a new `HTTP` packet should be revealed. We can now either `Follow TLS Stream` or `Follow HTTP Stream` and see the plain-text request and response. the key for this challenge is the `GET` request the client sent. 

## PCAP 33
[Challenge URL](https://pentesterlab.com/exercises/pcap_33/course)

Here we're looking at another TLS-encrypted request. However this time the server has implemented the `Forward Secrecy` feature, which prevents the server's private key from being used to decrypt traffic. But, it's possible to modify the server or client to retrieve the *pre-shared* key that was generated for the connection. Fortunately, we're given a file containing the *pre-shared* key for this challenge, as well as the server's *private* key, we'll need to use both to decrypt the request. 

If we attempt to load the servers *private key* `.pem` file, the traffic will remain encrypted. So, we'll right click on a `TLS` packet, and select 'Protocol Preferences -> Transport Layer Security -> (Pre)-Master-Secret log filename..' and load in the given `pcap_33_premaster.txt` file. Once the file has been loaded, a new `HTTP` packet should appear in the main window. Note: the `.pem` *private* key *does not* need to be loaded for this challenge. We can now either look in the 'Hypertext Transfer Protocol' drop-down in the main window, or `Follow HTTP Stream` to see the decrypted `GET` request with the key for this challenge.

## PCAP 34
[Challenge URL](https://pentesterlab.com/exercises/pcap_34/course)

This challenge we're looking at a `TLS` connection where the client sends a client-certificate to authenticate against the server. We won't be able to decrypt the request this round, however we can gather the `Common Name` used by the client, which will serve as the key for this challenge. If we select the 'Certificate, Client Key Exchange...' packet (#08), we can find the `Common Name` under the 'Transport Layer Security -> Record Layer: Handshake Protocol: Certificate -> Handshake Protocol: Certificate -> Certificates -> Certificate' drop-down entry, or `Follow TCP Stream`, scroll down to the end of the first-block of encrypted server response/blue-highlighted block of data and find the key/`Common Name` as one of the only plain-text portions within the encrypted data. 

## PCAP 35
[Challenge URL](https://pentesterlab.com/exercises/pcap_35/course)

Here we're looking at a connection to a `MySQL` database. In the 'info' column of the second `MySQL` packet, we can see the client login to the server as the `MySQL` `root` user to the `pentesterlab` database. In the fourth `MySQL` packet (frame 09), under the 'MySQL Protocol -> Request Command Query' drop-down, we can see the client runs `select @@version_comment limit 1`, followed by the server's response in the next packet, where we can see in the second-to-last drop-down the server's response of '(Debian)'. If we look at the next query, we see the client runs `SELECT * FROM users`. If we scroll-down in the drop-down window, we can see the server's response, towards the bottom we'll find 'text: ' fields that contain a username and password, in this case 'admin' and the key for this challenge as the password.
