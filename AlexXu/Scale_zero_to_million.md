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

### Load Balancer
A load balancer evenly distributes the traffic based on servers in the load balancer set. 

<img width="940" height="826" alt="image" src="https://github.com/user-attachments/assets/7800695f-7af8-46dc-94b6-3d42c915a138" />

As shown in Figure 1-4, users connect to the public IP of the load balancer directly. With this setup, web servers are unreachable directly by clients anymore. For better security, private IPs are used for communication between servers. A private IP is an IP address reachable only between servers in the same network; however, it is unreachable over the internet. The load balancer communicates with web servers through private IPs.
In Figure 1-4, after a load balancer and a second web server are added, we successfully solved no failover issue and improved the availability of the web tier. Details are explained below:
 - If server 1 goes offline, all the traffic will be routed to server 2. This prevents the website from going offline. We will also add a new healthy web server to the server pool to balance the load.
- If the website traffic grows rapidly, and two servers are not enough to handle the traffic, the load balancer can handle this problem gracefully. You only need to add more servers to the web server pool, and the load balancer automatically starts to send requests to them.
Now the web tier looks good, what about the data tier? The current design has one database, so it does not support failover and redundancy.

**Database replication** is a common technique to address those problems. Let us take a look.

A master database generally only supports write operations. A slave database gets copies of the data from the master database and only supports read operations. All the data-modifying commands like insert, delete, or update must be sent to the master database. Most
applications require a much higher ratio of reads to writes; thus, the number of slave databases in a system is usually larger than the number of master databases. Figure 1-5 shows a master database with multiple slave databases.

<img width="760" height="833" alt="image" src="https://github.com/user-attachments/assets/2081d525-d046-43e9-9f2f-2f52cae609c0" />

**Advantages of database replication:**
 - Better performance: In the master-slave model, all writes and updates happen in master nodes; whereas, read operations are distributed across slave nodes. This model improves performance because it allows more queries to be processed in parallel.
- Reliability: If one of your database servers is destroyed by a natural disaster, such as a typhoon or an earthquake, data is still preserved. You do not need to worry about data loss because data is replicated across multiple locations.
- High availability: By replicating data across different locations, your website remains in operation even if a database is offline as you can access data stored in another database server.

1. If only one slave database is available and it goes offline, read operations will be directed to the master database temporarily. As soon as the issue is found, a new slave database will replace the old one. In case multiple slave databases are available, read operations are redirected to other healthy slave databases. A new database server will replace the old one.
2. If the master database goes offline, a slave database will be promoted to be the new master. All the database operations will be temporarily executed on the new master database. A new slave database will replace the old one for data replication immediately.
In production systems, promoting a new master is more complicated as the data in a slave database might not be up to date. The missing data needs to be updated by running data recovery scripts. Although some other replication methods like multi-masters and circular
replication could help, those setups are more complicated;


