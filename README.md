**Event+: Java Web Service Development - Event Search and Recommendation**

- Implemented **Java servlets** to handle HTTP requests and responses with **RESTful APIs** allows users to view nearby events and purchase tickets.  Designed algorithms (e.g. content-based recommendation) to implement event recommendation;
- Fetching data from   **TicketMaster API ,following factory pattern to support other APIs like Yelp, to increase extensibility ; Login module is**** migrated** from md5 encrypt to Firebase login using auth tokens.
- Migrate from mysql to mongodb for better scalability. Deployed server to **Amazon EC2** for scalable deployment  and tested using Apache JMeter to handle 150 **QPS** (queries per second)

This is a web application that allows user to view nearby events and buy tickets. I used Java servlet with RESTful APIs to handle HTTP requests and responses. To store data like user information, event information, I first uses MySQL database and then migrated to MongoDB for better scalability. Recommendations are also provided, and it is based on user&#39;s history. I choose to recommend events with categories the user likes. The whole application is deployed to AWS EC2 together with ELK, and I used this to do user behavior analysis (identify peak periods, visualize geographic distribution of users)

**Basically I split the whole thing into small parts: 3-tier architecture**
![mxajq-6vjx2w5i3xo_g6kzfhf-t6i3iyez7lrjttarptjbluihkhwzo8ggyojkshb8pv34klbgpxve8dkobgq-m7byatz1lxx_pri075tijohz-2vsyg_zovqayrf4aaluobilen](https://user-images.githubusercontent.com/6482545/37437899-ebf07392-27c5-11e8-8d8d-fd6781356820.jpg)

** **

**Logic tier design:**

Search

- searchEvent
- Ticketmaster api
- parse/clean, saveEvent
- return

History

- Get, Set, Delete
- Query database
- return

Recommendation

- recommendEvent
- Get history/favorite
- Search similar event, sorting
- return
![x97z9xaqzsch36x8ld-ap_gt-damwptiyhnkjqkjh56vih3lnx5k0qx5wxhgozp6sd4hcq6lu64crzkrzhfjujgj5gevtrhixiztyuef9ietjtsz6semh2wxr7kh09xzkbti9a21](https://user-images.githubusercontent.com/6482545/37437909-fec213d6-27c5-11e8-9e37-7ce425b0d7fa.jpg)

Red line: search nearby events

Yellow line: recommend nearby events

Green line: get favorite events

Light blue line: set favorite events

Dark blue line: delete favorite events

![ngz3dnjan82i0eu4s282sus7jovwouku23kswztnhpl6ya_ves15mftzlu7fadv_lodhyyuo55y60h598udf1s_pnwglgjugdn8hwznmzyypboggklyknr-fmf1k24ngqcm-ctks](https://user-images.githubusercontent.com/6482545/37437931-11ec6358-27c6-11e8-803e-2675e4db8895.jpg)
![utvkdx-wx--3mkmy1kibdcl5i9st2bfmvodays2_akvl38gdzrhtgukbngdog_ikkvgxnlga_4szd0k9ls2hzd_3b2ejp_pp0twpso16j0iycpky-w-nx1sesakjaovkxt667f7y](https://user-images.githubusercontent.com/6482545/37437932-12001fec-27c6-11e8-8cc6-dc488cf674e2.jpg)


**Why I Use firebase login**

1 Google+ login and FB/TWITTER/GITHUB/GMAIL login. Register free.

2 Migrate from using md5 encrypt to using firebase auth token to improve the security.

**J2EE/Tomcat framework**

Apache tomcat. This is a java servlet container, and I build my server inside this container to handle http request.

I used Java (Tomcat framework) to build 3 servlets. The first one is a SearchItems api that provides the functionalities to search around, The second one is a POST servlet that allows a user to set or unset their favorite records. The last one is a GET servlet that recommends similar items to the user.

**Why do I use  (MySQL\MongoDB)? or what&#39;s the main difference between them?**

I used MySQL to build three tables. The first table is an item table for all the events fetched from an external API. The second table is a history table …

Also  I used MySQL first and then I would like to improve the scaling and then I used MongoDB.

**Why I used builder pattern?**

Builder pattern has a nested static builder class to build the object step-by-step and provide a method that will actually return the object. Builder pattern is to deal with constructors that require too many parameters. Also it can make sure the object is immutable. For example, in our project, our Item class has many different attributes and thus we use builder pattern.

**Why I followed factory pattern?**

Factory pattern is a creational pattern for creating objects without having to specify the exact class of the object that will be created. It allows us to easily support multiple implementations from the same interface. We used this pattern to build a DBconnection in our project such that it can build either MongoDB Connection or MySQL Connection on the fly.

**Why I use REST and what&#39;s the benefit of REST?**

REST is a stateless protocol for web URLs … The main benefit is to understand the web service easier, also make it more scalable because of its stateless calls.

Compared to SOAP, which is stateful, REST is more flexible. SOAP only support XML, while REST supports more data format like JSON, etc.

**Stateless of REST**

Each client request to the server requires that its state be fully represented. The server must be able to completely understand the client request without using any server context or server session state. It follows that all state must be kept on the client.

**How to migrate a Service at industry field?**

The migration is done step by step.

On the other hand, we could use a primary-secondary mechanism. For example, we can first employ MySQL primary - MongoDB secondary structure,

First we can gradually migrate, for example 1% to verify the correctness, and 5% to verify scaling, 50%, then 100%. On the other hand, we can use the primary-secondary mechanism, such as the initial MySQL primary - MongoDB secondary, and then do a diffing compare the results of the two DB is the same, and then turn into the MongoDB Primary - MySQL secondary, and finally completely changed Being MongoDB alone is complete.

**TicketMaster API has limited events, how do I improve this?**

Using other APIs like Eventbrite, Meetup may give more events, but they don&#39;t support geolocation queries. So even though TicketMaster API is not good enough, I still have to use it for this feature. Or given more time or human resources, maybe we could create a database and maintain an API of our own.

**TicketMaster has already supported event search, why I still design this project?**

TicketMaster or Meetup are just search engines. We need to give specific conditions/inputs to get the results/outputs. Compared to TicketMaster, my app is more of a recommendation engine. It can guess what events a user might like even without much information about the user.

**What&#39;s the difference between (Content-based, User-based, Demographic-based, Item-based) recommendation?**

Demographic based is to recommend items to users with similar demographic attributes like age, gender etc. Content based is to recommend similar items to the same user. User based is one type of Collaborative Filtering that calculates the similarity of users and then recommend items. Item based calculates the similarit of items and then do the recommendation.

**How do I use polymorphism in this project?**

 bility of an object to take on many forms. The most common use of polymorphism in OOP occurs when a parent class reference is used to refer to a child class object. We use polymorphism to provide better extensibility. For example, we needed to migrate from MySQL to MongoDB and we defined an interface for a DBConnection and two implementation of MySQL and MongoDB.
