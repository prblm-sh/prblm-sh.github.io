The OSI model is a representation of how computers can be connected/networked, and how data passes over these networks. The model consists of Seven layers.

| Layer |     Name     |
|:-----:|:------------:|
|   7   | Application  |
|   6   | Presentation |
|   5   |   Session    |
|   4   |  Transport   |
|   3   |   Network    |
|   2   |  Data Link   |
|   1   |   Physical   |

# L-7 Application
L-7 handles application specific protocols and is the one a user typically interacts with. It includes HTTP(s) for browsers, FTP clients, and other protocols that a user interacts with directly.

# L-6 Presentation
L-6 can be thought of as a translation layer, protocols here ensure that data has the correct syntax or structure when it's being sent/received. It includes things like ASCII, MIDI, JPEG, other image formates, etc.

# L-5 Session
L-5 is responsible for establishing, maintaining, and ending connections between different applications. It's where a web-server determines how long to wait for a response, determining if a request has been successfully fulfilled or cancelled and closing the connection, etc. Protocols here are NFS, RPC, and SQL, for example.

# L-4 Transport
L-4 is responsible for transferring data between different hosts, handling size of requests, what data gets transferred, how many packets get sent, etc. Protocols include TCP, UDP, SPX, etc.

# L-3 Network
L-4 is responsible for ensuring individual packets in a given network travel to the correct destination. This is where firewall and routing rules exist. Protocols include TCP/IP, IPX, etc.


# L-2 Data Link
L-2 is where network switches live in the model, and can be divided into two sublayers, Media Access Control (MAC) and Logical Link Control (LLC). MAC determines how a connected system accesses data and what permissions that link has. LLC is responsible for maintaining proper order or sent/received packets or ensuring frames are kept in sync.

# L-1 Physical
L-1 is the physical components of the network, ethernet cables, wifi-repeaters, the actual ethernet ports themselves. It's how devices are physically (or wirelessly) connected. 