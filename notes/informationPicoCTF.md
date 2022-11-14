### Information (PicoCTF)
We have what appears to be a cat photo as `cat.jpg`, and told files can be changed in secret ways.
This is either steganography or metadata shenanigans.
output of `file cat.jpg`
`cat.jpg: JPEG image data, JFIF standard 1.02, aspect ratio, density 1x1, segment length 16, baseline, precision 8, 2560x1598, components 3`
The `2560x1598` resolution has got to be off, it's 2 pixels short of the standard 2560x1600. It *might* be possible those two lines of pixels have been hex-edited. *maybe.*
`exiv2` doesn't give any info on the exif-data.
`ImageMagick` also, does not have any info I need for this.
`exif` tool is giving me a `corrupt data` error. 
All of that is leading me more towards the hex-editing possibility
Installed `hexedit`, nothing completely obvious jumps out initially. 
Gave in, googled the challenge. Ran strings on the .jpg, it did not click that any of that giberish would be base64.
The value on this line `<cc:license rdf:resource='cGljb0NURnt0aGVfbTN0YWRhdGFfMXNfbW9kaWZpZWR9'/>`
is [base64]({% link notes/base64.md%}) encoded. To decode it, run:
`echo "cGljb0NURnt0aGVfbTN0YWRhdGFfMXNfbW9kaWZpZWR9" | base64 -d`
The flag will be printed to STDOUT.

#### Follow up
This one was probably a classic case of "you're making this much more complicated than it actually is." I was on the right track with something being edited, but how and what I was off about. 


#picoCTF #base64 #metadata