In a network system, a rate limiter is used to control the rate of traffic sent by a client or a service. In the HTTP world, a rate limiter limits the number of client requests allowed to be
sent over a specified period. If the API request count exceeds the threshold defined by the rate limiter, all the excess calls are blocked

- A user can write no more than 2 posts per second.
- You can create a maximum of 10 accounts per day from the same IP address.
- You can claim rewards no more than 5 times per week from the same device.

**Benefits** of using an API rate limiter:
- Prevent resource starvation caused by Denial of Service (DoS) attack. Almost all APIs published by large tech companies enforce some form of rate limiting. For example, Twitter limits the number of tweets to 300 per 3 hours. Google docs APIs have the following default limit: 300 per user per 60 seconds for read requests. A rate limiter prevents DoS attacks, either intentional or unintentional, by blocking the excess calls.
- Reduce cost. Limiting excess requests means fewer servers and allocating more resources to high priority APIs. Rate limiting is extremely important for companies that use paid third party APIs. For example, you are charged on a per-call basis for the following external APIs: check credit, make a payment, retrieve health records, etc. Limiting the number of calls is essential to reduce costs.
- Prevent servers from being overloaded. To reduce server load, a rate limiter is used to filter out excess requests caused by bots or users’ misbehavior.

**Step 1** - Understand the problem and establish design scope

Candidate: What kind of rate limiter are we going to design? Is it a client-side rate limiter or server-side API rate limiter?
Interviewer: Great question. We focus on the server-side API rate limiter.

Candidate: Does the rate limiter throttle API requests based on IP, the user ID, or other properties?
Interviewer: The rate limiter should be flexible enough to support different sets of throttle rules.

Candidate: What is the scale of the system? Is it built for a startup or a big company with a large user base?
Interviewer: The system must be able to handle a large number of requests.

Candidate: Will the system work in a distributed environment?
Interviewer: Yes.

Candidate: Is the rate limiter a separate service or should it be implemented in application code?
Interviewer: It is a design decision up to you.

Candidate: Do we need to inform users who are throttled?
Interviewer: Yes.

Requirements
Here is a summary of the requirements for the system:
- Accurately limit excessive requests.
- Low latency. The rate limiter should not slow down HTTP response time.
- Use as little memory as possible.
- Distributed rate limiting. The rate limiter can be shared across multiple servers or
processes.
- Exception handling. Show clear exceptions to users when their requests are throttled.
- High fault tolerance. If there are any problems with the rate limiter (for example, a cache
server goes offline), it does not affect the entire system.

**Step 2** - Propose high-level design and get buy-in
Let us keep things simple and use a basic client and server model for communication

*where to put a rate limiter*
- Client-side implementation. Generally speaking, client is an unreliable place to enforce rate limiting because client requests can easily be forged by malicious actors. Moreover, we might not have control over the client implementation.
- Server-side implementation. Figure 4-1 shows a rate limiter that is placed on the serverside.
  <img width="877" height="218" alt="image" src="https://github.com/user-attachments/assets/959e899c-ee5c-409e-b880-f071fe68aeed" />

Besides the client and server-side implementations, there is an alternative way. Instead of putting a rate limiter at the API servers, we create a rate limiter middleware, which throttles our APIs as shown in Figure
<img width="859" height="356" alt="image" src="https://github.com/user-attachments/assets/acca70f7-cd17-4426-b744-4709b1031237" />

How it works - Assume our API allows 2 requests per second, and a client sends 3 requests to the server within a second. The first two requests are routed to API servers. However, the rate limiter middleware throttles the third request and returns a HTTP status code 429. The HTTP 429 response status code indicates a user has sent too many requests.
<img width="852" height="338" alt="image" src="https://github.com/user-attachments/assets/9fbe6fae-cbf1-4204-a650-8b867342489c" />

where should the rater limiter be implemented, on the server-side or in a gateway? There is no absolute answer.
It depends on your company’s current technology stack, engineering resources, priorities, goals, etc. Here are a few general guidelines:
- Evaluate your current technology stack, such as programming language, cache service, etc. Make sure your current programming language is efficient to implement rate limiting on the server-side.
- Identify the rate limiting algorithm that fits your business needs. When you implement everything on the server-side, you have full control of the algorithm. However, your choice might be limited if you use a third-party gateway.
- If you have already used microservice architecture and included an API gateway in the design to perform authentication, IP whitelisting, etc., you may add a rate limiter to the API gateway.
- Building your own rate limiting service takes time. If you do not have enough engineering resources to implement a rate limiter, a commercial API gateway is a better option.

**Algorithms for rate limiting**
Rate limiting can be implemented using different algorithms, and each of them has distinct
pros and cons. Even though this chapter does not focus on algorithms, understanding them at
high-level helps to choose the right algorithm or combination of algorithms to fit our use
cases. Here is a list of popular algorithms:
- Token bucket
- Leaking bucket
- Fixed window counter
- Sliding window log
- Sliding window counter

### High Level Architecture 
The basic idea of rate limiting algorithms is simple. At the high-level, we need a counter to keep track of how many requests are sent from the same user, IP address, etc. If the counter is larger than the limit, the request is disallowed

Where shall we store counters? Using the database is not a good idea due to slowness of disk access. In-memory cache is chosen because it is fast and supports time-based expiration strategy. For instance, Redis is a popular option to implement rate limiting. It is an inmemory
store that offers two commands: INCR and EXPIRE.
- **INCR:** It increases the stored counter by 1.
- **EXPIRE:** It sets a timeout for the counter. If the timeout expires, the counter is automatically deleted.

The client sends a request to rate limiting middleware.
- Rate limiting middleware fetches the counter from the corresponding bucket in Redis and checks if the limit is reached or not.
- If the limit is reached, the request is rejected.
- If the limit is not reached, the request is sent to API servers. Meanwhile, the system increments the counter and saves it back to Redis.

**Step 3 - Design deep dive**
The high-level design in Figure 4-12 does not answer the following questions:
- How are rate limiting rules created? Where are the rules stored?
- How to handle requests that are rate limited?

**Rate limiting rules**
Lyft open-sourced their rate-limiting component. We will peek inside of the component and look at some examples of rate limiting rules:

domain: messaging
descriptors:
  key: message_type
    Value: marketing
  rate_limit:
    unit: day
    requests_per_unit: 5

In the above example, the system is configured to allow a maximum of 5 marketing messages
per day. Here is another example:
domain: auth
descriptors:
  key: auth_type
    Value: login
  rate_limit:
    unit: minute
    requests_per_unit: 5

This rule shows that clients are not allowed to login more than 5 times in 1 minute. Rules are generally written in configuration files and saved on disk.

The rate limiter returns the following HTTP headers to clients:
- X-Ratelimit-Remaining: The remaining number of allowed requests within the window.
- X-Ratelimit-Limit: It indicates how many calls the client can make per time window.
- X-Ratelimit-Retry-After: The number of seconds to wait until you can make a request again without being throttled.

When a user has sent too many requests, a 429 too many requests error and X-RatelimitRetry-After header are returned to the client.

