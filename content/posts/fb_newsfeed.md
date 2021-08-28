---
title: "Design FB News Feed"
date: 2021-08-17T21:36:32-05:00
draft: false
---

## Overview
Problem: Design a news feed system. 
What's a news feed?
> "News feed is the constantly updating list of stories
in the middle of your homepage. News feed includes status
updates, photos, videos, links, app activity and likes from
people, pages, and groups that you follow on Facebook."

## Step 1: Understand the problem and scope
Make sure to ask clarifying questions to gauge the scope
of the problem which you have to design around. 
Some sample questions:
- Is it a mobile app? Web app? both?
- What are the important features?
- Is the news feed sorted? Reverse chronological or other?
- How many friends can a user have?
- What is the traffic volume?
- Does the feed contain media? Or just text?

## Step 2: Propose high-level design
Two parts: feed publishing and news feed building.
- *Feed publishing*: user publishes post, data is written to cache
and database. Posts are populated to friend's news feeds.
- *Newsfeed building*: We're assuming the news feed is built by
aggregating friends' posts in reverse chronological order.

### Newsfeed APIs
#### Feed publishing API
HTTP Post to publish with the content and an auth_token.
e.g. POST /v1/me/feed

#### Newsfeed retrieval API
The API to get the news feed. Supply auth token.
e.g. GET /v1/me/feed

### Feed publishing flow
The main pieces to the flow are the users, load balancer, web servers,
post service, fanout service, and notification service.
The users will view and make posts through the API. The load
balancer distributes traffic evenly to the web servers. The 
web servers redirect traffic to different internal services.
Post service is responsible for persisting posts in the db and cache.

The fanout service's job is to push new content to friends' news feeds.
Newsfeed data is stored in the cache for fast retrieval.
The notification service informs friends that new content is
available and it sends out push notifications.

### Step 3: Dig in to the design
#### Feed Publishing Dive
The role of the web server is to communicate with clients, and
also enforce authentication and rate-limiting. Users need
a valid auth token to make posts, and the number of posts a
user can make within a certain period will be limited to prevent
abuse.

##### Fanout
Fanout is the process of distributing a post to friends.
There are two different approaches to fanout:

- **Fanout on write**: News feed is pre-computed during write time. 
A new post is delivered to a friends' cache right after it's published.

    - Pros:
        - News feed is generated in real time and can be pushed immediately
        - Fetching news is fast because its pre-computed
    - Cons:
        - If a user has a lot of friends, fetching the friend list
        and generating news feeds for all of them are slow and time consuming.
        This is known as a hotkey problem.
        - For inactive users, pre-computing news feeds wastes compute.


- **Fanout on read**: The newsfeed is generated during read time, on demand.
When a user loads their home page, thats when recent posts are pulled.

    - Pros:
        - For inactive users this is a better approach because it won't waste
        compute.
        - No hotkey problem because data isn't pushed to friends
    - Cons:
        - Fetching the news feed is slow.

We can adopt a hybrid approach depending on the amount of friends a user has.
This is similar to how Twitter handles their regular users vs. celebrities.
We can use a push model for most users, but then use a pull model for
celebrities. 

**Consistent hashing** is a useful technique to get around the hotkey (or hotspot)
problem as it helps distribute requests evenly.

The fanout service flow is like this:
1. Fetch friend IDs from the graph db. Graph db's are suited for managing
friend relationship and friend recommendations.
2. Get friends info from the user cache. Then filter out friends
based on user settings, like if you mute someone you shouldn't see
their posts.
3. Send friends list and new post ID to the message queue.
4. Fanout workers fetch data from the queue and store news feed
data in the news feed cache.
5. Store post_id, and user_id in the cache.

