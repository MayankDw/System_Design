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
I : Both
C : What is the most important feature of the app?
I : Ability to make post and see friends news feed. 
C : How the feeds in the app should appear in which order, random, reverse chronological order? A closer friend feed should get more weightage than the group. 
I : To keep things simple, lets take reverse chronological order
C : How many friends a user can have ?
I : 5000
C : What is the traffic volume?
I : 10 millions users per day. 
C : Can feeds contains video, impages or just text? 
I : It can contain media files, including videos and images. 

Why to these questions? To clarify the requirement, and understand ambiguities, Do Not assume. 



