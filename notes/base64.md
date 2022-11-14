### Base64
To encode:
`echo "text to encode" | base64`
to decode:
`echo "text to decode" | base64 -d`

NOTE: I often associate base64 with strings that end in `==`, however as evidenced by [informationPicoCTF]({% link notes/informationPicoCTF.md%}) challenge, that is not always the case. 