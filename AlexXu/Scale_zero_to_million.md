## SCALE FROM ZERO TO MILLION OF USERS

Below figure shows the illustration of a single server setup where everything is running on one server: webapp, database, cache, etc

<img width="940" height="592" alt="image" src="https://github.com/user-attachments/assets/cf93a904-bb96-4b29-8d34-4cc2831774cc" />

Request Flow and Traffic
<img width="940" height="584" alt="image" src="https://github.com/user-attachments/assets/64ea722e-8d39-4d5f-b8ba-5fee5c8282c3" />

1. Users access websites through domain names, such as api.mysite.com. Usually, the
Domain Name System (DNS) is a paid service provided by 3rd parties and not hosted by
our servers.
2. Internet Protocol (IP) address is returned to the browser or mobile app. In the example,
IP address 15.125.23.214 is returned.
3. Once the IP address is obtained, Hypertext Transfer Protocol (HTTP) [1] requests are
sent directly to your web server.
4. The web server returns HTML pages or JSON response for rendering.
