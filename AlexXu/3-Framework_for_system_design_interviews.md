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

**step3 - Design Deep Dive**
At this step, you and your interviewer should have already achieved the following objectives:
- Agreed on the overall goals and feature scope
- Sketched out a high-level blueprint for the overall design
- Obtained feedback from your interviewer on the high-level design
- Had some initial ideas about areas to focus on in deep dive based on her feedback

You shall work with the interviewer to identify and prioritize components in the architecture. It is worth stressing that every interview is different. Sometimes, the interviewer may give off hints that she likes focusing on high-level design. Sometimes, for a senior candidate
interview, the discussion could be on the system performance characteristics, likely focusing on the bottlenecks and resource estimations. In most cases, the interviewer may want you to dig into details of some system components. For URL shortener, it is interesting to dive into the hash function design that converts a long URL to a short one. For a chat system, how to
reduce latency and how to support online/offline status are two interesting topic. 

Example : 
1) Feed Publishing
2) News Feed Retrieval

<img width="940" height="1109" alt="image" src="https://github.com/user-attachments/assets/6c8ea04a-f721-4876-85b2-08bf9fdb9bb9" />

<img width="820" height="868" alt="image" src="https://github.com/user-attachments/assets/15d9d15c-b044-420d-9359-f4fc52f3a434" />

**Step 4 - Wrap Up**
- The interviewer might want you to identify the system bottlenecks and discuss potential improvements. Never say your design is perfect and nothing can be improved. There is always something to improve upon. This is a great opportunity to show your critical
thinking and leave a good final impression.
- It could be useful to give the interviewer a recap of your design. This is particularly important if you suggested a few solutions. Refreshing your interviewer’s memory can be helpful after a long session.
- Error cases (server failure, network loss, etc.) are interesting to talk about.
- Operation issues are worth mentioning. How do you monitor metrics and error logs? How to roll out the system?
- How to handle the next scale curve is also an interesting topic. For example, if your current design supports 1 million users, what changes do you need to make to support 10 million users?
- Propose other refinements you need if you had more time

**Dos**
- Always ask for clarification. Do not assume your assumption is correct.
- Understand the requirements of the problem.
- There is neither the right answer nor the best answer. A solution designed to solve the problems of a young startup is different from that of an established company with millions of users. Make sure you understand the requirements.
- Let the interviewer know what you are thinking. Communicate with your interview.
- Suggest multiple approaches if possible.
- Once you agree with your interviewer on the blueprint, go into details on each component. Design the most critical components first.
- Bounce ideas off the interviewer. A good interviewer works with you as a teammate.
- Never give up.

**Don’ts**
- Don't be unprepared for typical interview questions.
- Don’t jump into a solution without clarifying the requirements and assumptions.
- Don’t go into too much detail on a single component in the beginning. Give the highlevel design first then drills down.
- If you get stuck, don't hesitate to ask for hints.
- Again, communicate. Don't think in silence.
- Don’t think your interview is done once you give the design. You are not done until your interviewer says you are done. Ask for feedback early and often.











