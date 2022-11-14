# GET aHEAD - PicoCTF Challenge

## Notes
All we're told is the flag is somewhere on this server;
`http://mercury.picoctf.net:47967/`

And we're given the option to `choose red` or `choose blue` with some terrible CSS choices. 

Both buttons fetch `index.php`, which as far as I can tell is the same as the root page. The only difference being red sends a `GET` request and blue sends `POST`.

I'm kind of inclined to think I've gotta send a `GET` request for just the `HEAD` of the page to get the flag?

I also just realized that sending a `POST` request for this makes absolutely no sense- At all. There's not even a body to the request. Or any data. 

And that `ffs//cheat.sh` alias is the best thing I've ever done.

```bash
# Fetch only the HTTP headers from a response.
curl -I http://example.com
```

So that whole `GET` just the headers thing did not work with curl, at least not right off the bat.

So didn't work with `curl`, but editing the request in DevTools and resending it with the `HEAD` option spitout the flag in the response headers. 

## Solution
Using DevTools or Proxy, edit HTTP Request Method to `HEAD` and send the request to `http://mercury.picoctf.net:47967/`, the flag will be in the response headers. 

#picoCTF #webExploit 