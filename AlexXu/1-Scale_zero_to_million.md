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

<img width="940" height="1035" alt="image" src="https://github.com/user-attachments/assets/95b94031-ee64-4b9c-befa-62b336bfb0e3" />

Let us take a look at the design:
- A user gets the IP address of the load balancer from DNS.
- A user connects the load balancer with this IP address.
- The HTTP request is routed to either Server 1 or Server 2.
- A web server reads user data from a slave database.
- A web server routes any data-modifying operations to the master database. This includes write, update, and delete operations.

### Cache
A cache is a temporary storage area that stores the result of expensive responses or frequently accessed data in memory so that subsequent requests are served more quickly. As illustrated in Figure, every time a new web page loads, one or more database calls are executed to
fetch data. The application performance is greatly affected by calling the database repeatedly. The cache can mitigate this problem.

<img width="943" height="157" alt="image" src="https://github.com/user-attachments/assets/8eb6d952-acf7-45a6-a69c-531b8fb31698" />

**Considerations for using cache**
Here are a few considerations for using a cache system

- Decide when to use cache. Consider using cache when data is read frequently but modified infrequently. Since cached data is stored in volatile memory, a cache server is not ideal for persisting data.
- Consistency: This involves keeping the data store and the cache in sync. Inconsistency can happen because data-modifying operations on the data store and cache are not in a single transaction.
- Mitigating failures: A single cache server represents a potential single point of failure (SPOF), defined in Wikipedia as follows: “A single point of failure (SPOF) is a part of a system that, if it fails, will stop the entire system from working” . As a result, multiple cache servers across different data centers are recommended to avoid SPOF.
- Eviction Policy: Once the cache is full, any requests to add items to the cache might cause existing items to be removed. This is called cache eviction. Least-recently-used (LRU) is the most popular cache eviction policy. Other eviction policies, such as the Least
Frequently Used (LFU) or First in First Out (FIFO), can be adopted to satisfy different use cases.

### Content Delivery Network (CDN): 
A CDN is a network of geographically dispersed servers used to deliver static content. CDN servers cache static content like images, videos, CSS, JavaScript files, etc.
It enables the caching of HTML pages that are based on request path, query strings, cookies, and request headers.
CDN server closest to the user will deliver static content. Intuitively, the further users are from CDN servers, the slower the website loads.

<img width="952" height="454" alt="image" src="https://github.com/user-attachments/assets/45fed2b8-7cf3-43ac-a37b-dec2097da05f" />

<img width="940" height="335" alt="image" src="https://github.com/user-attachments/assets/f1db94a6-d4bf-4c1f-890e-60f771fbd7af" />

**Considerations of using a CDN**
- Cost: CDNs are run by third-party providers, and you are charged for data transfers in and out of the CDN. Caching infrequently used assets provides no significant benefits so you should consider moving them out of the CDN.
- Setting an appropriate cache expiry: For time-sensitive content, setting a cache expiry time is important. The cache expiry time should neither be too long nor too short. If it is too long, the content might no longer be fresh. If it is too short, it can cause repeat
reloading of content from origin servers to the CDN.
- CDN fallback: You should consider how your website/application copes with CDN failure. If there is a temporary CDN outage, clients should be able to detect the problem and request resources from the origin.

<img width="940" height="1016" alt="image" src="https://github.com/user-attachments/assets/b1cc17e8-05eb-4b0e-a8e8-1953c0fadfaf" />

1. Static assets (JS, CSS, images, etc.,) are no longer served by web servers. They are fetched from the CDN for better performance.
2. The database load is lightened by caching data.

**Stateful architecture**
- A stateful server and stateless server has some key differences. A stateful server remembers
- client data (state) from one request to the next. A stateless server keeps no state information.
  
<img width="940" height="589" alt="image" src="https://github.com/user-attachments/assets/e51a4867-1f5f-4944-b303-a81e78a2a82b" />

**Stateless architecture**

In this stateless architecture, HTTP requests from users can be sent to any web servers, which fetch state data from a shared data store. State data is stored in a shared data store and kept out of web servers. A stateless system is simpler, more robust, and scalable.

<img width="940" height="891" alt="image" src="https://github.com/user-attachments/assets/a6148e93-1be9-4bda-8793-087d9f33c8f3" />

<img width="940" height="909" alt="image" src="https://github.com/user-attachments/assets/787a50c5-b530-42bb-be06-202bb5cf8de0" />

### Data Centers
In normal operation, users are geoDNS-routed, also known as geo-routed, to the closest data center, with a split traffic of x% in US-East and (100 – x)% in US-West. geoDNS is a DNS service that allows domain names to be resolved to IP addresses based on the location of a user.

<img width="940" height="945" alt="image" src="https://github.com/user-attachments/assets/232d392b-f1c8-48f6-967c-222bbe2876f9" />

In the event of any significant data center outage, we direct all traffic to a healthy data center. In Figure , data center 2 (US-West) is offline, and 100% of the traffic is routed to data center 1 (US-East).

<img width="940" height="955" alt="image" src="https://github.com/user-attachments/assets/8554809f-70c9-42be-9ee4-f544b6472aa9" />

Several technical challenges must be resolved to achieve multi-data center setup:
1) Traffic redirection
2) Data synchronization
3) Test and deployment

To further scale our system, we need to decouple different components of the system so they can be scaled independently. Messaging queue is a key strategy employed by many realworld distributed systems to solve this problem

**Message Queue**
Input services, called producers/publishers, create messages, and publish them to a message queue. Other services or servers, called
consumers/subscribers, connect to the queue, and perform actions defined by the messages.

<img width="941" height="198" alt="image" src="https://github.com/user-attachments/assets/29ce4593-43df-4e72-9b08-4046c0db15a8" />

Decoupling makes the message queue a preferred architecture for building a scalable and reliable application
With the message queue, the producer can post a message to the queue when the consumer is unavailable to process it. The consumer can read messages from the queue even when the producer is unavailable.

The producer and the consumer can be scaled independently. When the size of the queue becomes large, more workers are added to reduce the processing time.
<img width="940" height="233" alt="image" src="https://github.com/user-attachments/assets/e0214541-ee81-41f9-a399-e9272328186b" />

### Logging, metrics, automation
When working with a small website that runs on a few servers, logging, metrics, and automation support are good practices but not a necessity. However, now that your site has grown to serve a large business, investing in those tools is essential.

<img width="852" height="1110" alt="image" src="https://github.com/user-attachments/assets/0c29fdca-7a4a-40ad-a41c-3f0fc66b5f36" />

**Vertical Scaling vs Horizontal Scaling**

<img width="940" height="685" alt="image" src="https://github.com/user-attachments/assets/4bec2e86-14e7-4b58-be66-57379b6e9fef" />

Sharding separates large databases into smaller, more easily managed parts called shards. Each shard shares the same schema, though the actual data on each shard is unique to the shard.
<img width="555" height="416" alt="image" src="https://github.com/user-attachments/assets/177d06cd-81b6-4f8e-9eef-29d582c30bdf" />

Sharding is a great technique to scale the database but it is far from a perfect solution. It introduces complexities and new challenges to the system:

*Resharding data*: Resharding data is needed when 1) a single shard could no longer hold more data due to rapid growth. 2) Certain shards might experience shard exhaustion faster than others due to uneven data distribution. When shard exhaustion happens, it requires
updating the sharding function and moving data around.

*Celebrity problem*: This is also called a hotspot key problem. Excessive access to a specific shard could cause server overload. Imagine data for Katy Perry, Justin Bieber, and Lady Gaga all end up on the same shard. For social applications, that shard will be overwhelmed
with read operations. To solve this problem, we may need to allocate a shard for each celebrity.

*Join and de-normalization*: Once a database has been sharded across multiple servers, it is
hard to perform join operations across database shards. A common workaround is to denormalize
the database so that queries can be performed in a single table.

<img width="940" height="1187" alt="image" src="https://github.com/user-attachments/assets/4bf32756-0267-47fd-b7bf-da0a8a3d8ad4" />

















