# Private Communty App

 ### Just like a community itself is a Facebook for users

#### High Level requirements - 
1.	User onboarding and profile (sign up and sign in).
2.	User can create/update Community based on user type and permissions
3.	User can invite people to join a community
4.	User can request to join community
5.	Community admin user can accept/reject requests to add into a community.
6.	User can search other users in community
7.	Two users in same community can have a relationship e. g. friend, acquittances, relative etc. So, a user can request another user to become friends. Other users can accept / reject the request.
8.	User can post text, image, video within Community.
9.	Interaction with post e. g. Like, comment and share.
10.	Users can receive post as feeds, posted by another user on their timeline.
11.	Persist user’s activity logs (audit).
12.	Users can chat one to one or group chat within community.
13.	On Chat user can also share links, images, videos. 

#### Constraints -
1.	There would be more reads compare than writes within community. Ratio would be 1 write: 100 reads. So, it would be Read heavy application.
2.	Fetching/response and posting should be quick. 
3.	Posts on user’s Timeline as feed can be asynchronous. Can also take time to process with small lag (5-30 second) would be okay.
4.	Chat messenger should be real time.

#### System Design – 
Below is detailed flow diagram of Some of major use cases for this system.

We can download or view this from below link – 
https://whimsical.com/design-facebook-with-messenger-service-P8LMSrjMAjZMXFVZyN2y9J 

![image](https://user-images.githubusercontent.com/15645692/127738821-ed0ca663-accf-4578-9049-7dbe7c84751a.png)

We would need load balancers and webservers which will handle requests from actual users, decide where to send particular request to server.

There are multiple services e. g. user service, community service, graph service (for relationships), content-Post service, timeline service (to fetch timeline data and interactions with post). These services are like data source services which actually creates and process data in system, whenever user interact with application.

We will use Couchbase as master Data for community, user, posts, activity data etc. Couchbase is a highly available, performant and scalable service which enables user-defined business logic to be triggered in real-time on the server when application interactions create changes in data.
All data creation layer will publish sourced data to KAFKA, and KAFKA will be subscribed to read data by many asynchronous services. KAFKA will be able to handle data production of TOP Data publishing services to Down consuming services easily. 

Down consuming services are like calculating Trends, insights analytics, user’s timelines, user’s notification, search services etc. 

We need a system which is more of available, partitioned, no point of failure, we have to compromise with consistency. Which can be done by Eventual consistency. By KAFKA and its down streaming services, we will be able to achieve eventual consistency. 

For live user timelines, live trends and notifications are required streaming solution, so both services are can user Spark Streaming for processing, and for Analytics we can use spark Batch. For search we can use index based Elastic search.

For active users which are offline, required notifications when they are login again- we need to use cache – Radis for this. Similarly, people who are frequently active, we need use cache for them. There are many use cases to use cache, in this kind of system.

For Assets like images, videos, or documents we are maintaining different storage like S3. And handling all these operations, we have introduced Asset service.
---------------------------------
Now let’s talk about **Messenger system** – A user can send and receive message from set of users which are friends within same community. We can fetch this user relationship data via APIs from Radis cache or Couchbase database. 
Then again there would be load balancer and web servers. Those are connected with Chat service. This service publish data to another instance or cluster of KAFKA, which is subscribed by Notification service and Couchbase and Radis cache. 
Notification service is to create channel for messaging and to notify live users for real time messaging via web-socket and users which are not live we get all data via Rest API from cache or Database directly. 
Again, there we can maintain archive service and asset service for older messages and assets respectively.


#### Data collections types in Couchbase Database are - 
o	Communities
o	Users
o	Relationships
o	Posts
o	Messages – Chats
o	Assets - pictures, videos,
o	Activities - Likes, comment, share, 
o	Analytics insights
o	Trends
o	Search

Here is detailed structure of Data mentioned in below excel file -


YouTube Video link –   https://youtu.be/jXY7XI3R3ZY 
Google drive - https://drive.google.com/file/d/1hQ44K7k29x45hekRJiLeHjJHMs8x4tVI/view?usp=sharing 



