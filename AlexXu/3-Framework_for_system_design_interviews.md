## Framework for system design interviews

### 4-step process for effective system design interview**

**Step 1 - Understand the problem and establish design scope**
What kind of questions to ask? Ask questions to understand the exact requirements. Here is a list of questions to help you get started:
- What specific features are we going to build?
- How many users does the product have?
- How fast does the company anticipate to scale up? What are the anticipated scales in 3 months, 6 months, and a year
- What is the company’s technology stack? What existing services you might leverage to simplify the design?

Example : Design a News Feed app
what types of question you might ask, How conversation should proceed. 

C : is this a mobile app or web app or both? <br/>
I : Both <br/>
C : What is the most important feature of the app?<br/>
I : Ability to make post and see friends news feed. <br/>
C : How the feeds in the app should appear in which order, random, reverse chronological order? A closer friend feed should get more weightage than the group. <br/>
I : To keep things simple, lets take reverse chronological order <br/>
C : How many friends a user can have ?<br/>
I : 5000<br/>
C : What is the traffic volume?<br/>
I : 10 millions users per day. <br/>
C : Can feeds contains video, impages or just text? <br/>
I : It can contain media files, including videos and images. <br/>

Why to these questions? To clarify the requirement, and understand ambiguities, Do Not assume. 

**Step 2 - Propose a high level deisgn and bet they buy in**
*Collaborate with Interviewer*
- Come up with a high level design. Ask for feedback. Review. Treat Interviewer as your team mate.
- Drew box diagram. This might include web server, DNS, CDN, load balancer, data store, API, cache, message queue etc.
- Do back of the envelope calculations. Ask Interviewer specifically if that should be considered.

Go through possible use cases. 
Should you include API endpoints, depends on problem. Communicate with interviewer. 

Example : Design News feed system. 
At high level design is divided into two flow: News Publishing and news feed building.
- Feed Publishing: When a user publishes a post, corresponding data is written in cache, data store, and the post will be populated into friends-  news feed.
- News Feed Building: News feeds are built by aggregating friends post and populating in reverse chronological order.

<img width="638" height="1061" alt="image" src="https://github.com/user-attachments/assets/10f675a2-c498-4f5d-a6e2-0dfe5397af64" />

<img width="577" height="903" alt="image" src="https://github.com/user-attachments/assets/3c24bff7-a894-4801-8ffb-dfbc4710618b" />







