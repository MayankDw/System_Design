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

### Database
With the growth of the user base, one server is not enough, and we need multiple servers: one
for web/mobile traffic, the other for the database (Figure 1-3). Separating web/mobile traffic
(web tier) and database (data tier) servers allows them to be scaled independently.

<img width="940" height="578" alt="image" src="https://github.com/user-attachments/assets/5bb3723f-baab-4a20-a2ee-f988290dc0a1" />

### which database to use
we can choose between *relational database* and *nonretational database*. Let's understand their difference:

**Relational database** also knowns as RDMBS (Relational database management system). They have been their for over 40 years and historically generally a preferred option. They store value in row and columns format. They allow join operation between the tables. 
These are structured data. Most popular ones are Oracle DB, MySQL, Postgre etc.

**Non Relational database** of four different categories
1. key value store
2. Graph store
3. Columns store
4. Document store.
Join operation is generally not supported.
Might be of choice if
- Required low latency
- Need to store massive amount of data
- Data is unstructured, and don't have relational database
- Serialize and Deserialize JSON, XML, YAML etc

### Horizontal Scaling and Vertical Scaling
**Vertical Scaling ("scale up")** : Adding more CPU, memory - RAM etc. Limitation - you cannot keep on adding CPU, RAM as user base grows. Doesn't allow failover or redundancy. if one server fales, complete application goes down.<br/> <btr/>

**Horizontal Scaling "scale out")** : Adding more server. Preferred because . Adding more servers allows you failover. Users will unable to
access the website if the web server is offline. In another scenario, if many users access the web server simultaneously and it reaches the web server’s load limit, users generally experience slower response or fail to connect to the server. A load balancer is the best
technique to address these problems.


