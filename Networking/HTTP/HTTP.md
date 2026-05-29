# HyperText Transfer Protocol (HTTP)

# Reference
Thanks these awesome folks for reference material!!
- [yet another insignificant programming notes: HTTP Basics](https://www3.ntu.edu.sg/home/ehchua/programming/webprogramming/HTTP_Basics.html)
- [yet another insignificant programming notes: State & Session Management](https://www3.ntu.edu.sg/home/ehchua/programming/webprogramming/HTTP_StateManagement.html)
- [yet another insignificant programming notes: HTTP Authentication](https://www3.ntu.edu.sg/home/ehchua/programming/webprogramming/HTTP_Authentication.html)
- [yet another insignificant programming notes: HTTP Security](https://www3.ntu.edu.sg/home/ehchua/programming/webprogramming/HTTP_SSL.html)

## Basics of HTTP
Internet is a massive information exchange system e.g. web-browsing/surfing, email, file transfer, video streaming etc. Applications on internet run on protocols e.g. HTTP, FTP, SMTP, etc. Among all the protocols, HTTP is the most widely used protocol for web-browsing/surfing on the Internet.

HTTP is an asymmetric request-response client-server protocol.

![HTTP Request-Response Diagram I stole from the material](./assets/HTTP.png)

In layman's terms, A client sends a request to a server, and the server responds with a response. In other words, HTTP is a pull protocol, client pulls data from the server instead of the server pushing it to the client.

HTTP is **stateless**, which means the server does not need to know what has been transmitted previously.
