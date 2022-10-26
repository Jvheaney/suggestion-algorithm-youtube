# Building Suggestion Algorithms
This repository is a collection of the Neo4J Cypher commands used in <a href="https://youtu.be/GIJuSwgZmDs">this video</a> about suggestion algorithms and how they're used in tech companies to serve friends, content, or topics to you based on your interests, friends, or previous event history on the website. You can follow along with the video and run the commands at home using the Neo4J Desktop application.

### Check the video out
https://youtu.be/GIJuSwgZmDs

## The Commands
We will start by creating a user named Bob. To do this, we will run the following command:

`create (:User {name: "Bob"})`

Execute that and the run `match (u:User) return u` to see Bob in our graph.

He looks a little lonely; let's add some more users for Bob.

<pre>
create (:User {name: "Alice"})
create (:User {name: "Justin"})
create (:User {name: "Janice"})
create (:User {name: "Ken"})
create (:User {name: "Sally"})
</pre>

Now if we run `match (u:User) return u` once again, we'll see Bob now has other users within the graph! Wonderful.

Let's give Bob some friends now, starting with Alice.
<pre>
match (u1:User) where u1.name="Bob"
match (u2:User) where u2.name="Alice"
create (u1)-[:friends_with]->(u2)
create (u2)-[:friends_with]->(u1)
</pre>

If we run the same `match (u:User) return u` query we can see that Bob and Alice are friends now. Woo, go Bob! Let's do this a few more times for everyone else.

<pre>
match (u1:User) where u1.name="Bob"
match (u2:User) where u2.name="Justin"
create (u1)-[:friends_with]->(u2)
create (u2)-[:friends_with]->(u1)

match (u1:User) where u1.name="Ken"
match (u2:User) where u2.name="Sally"
create (u1)-[:friends_with]->(u2)
create (u2)-[:friends_with]->(u1)

match (u1:User) where u1.name="Justin"
match (u2:User) where u2.name="Sally"
create (u1)-[:friends_with]->(u2)
create (u2)-[:friends_with]->(u1)

match (u1:User) where u1.name="Sally"
match (u2:User) where u2.name="Alice"
create (u1)-[:friends_with]->(u2)
create (u2)-[:friends_with]->(u1)

match (u1:User) where u1.name="Janice"
match (u2:User) where u2.name="Ken"
create (u1)-[:friends_with]->(u2)
create (u2)-[:friends_with]->(u1)

match (u1:User) where u1.name="Janice"
match (u2:User) where u2.name="Bob"
create (u1)-[:friends_with]->(u2)
create (u2)-[:friends_with]->(u1)
</pre>

If we run that `match (u:User) return u` query we can see we have a little community of friends. Let's leverage this to find friends of friends and suggest them to our users, starting first with Sally.

<pre>
match (u1:User) where u1.name = "Sally"
match (u1)-[:friends_with]->(:User)-[:friends_with]->(u2:User)
where u2<>u1
return u2
</pre>

We have suggestion for Sally! Now let's try Bob...

<pre>
match (u1:User) where u1.name = "Bob"
match (u1)-[:friends_with]->(:User)-[:friends_with]->(u2:User)
where u2<>u1
return u2
</pre>

Looks good! Now let's start adding some interests. Let's start with soccer.

`create (:Interest {type: "soccer"})`

And now if we query for our interests using `match (i:Interest) return i`, we see our soccer interest in the graph. Great! Let's create a few more while we're at it.

<pre>
create (:Interest {type: "programming"})
create (:Interest {type: "minecraft"})
create (:Interest {type: "swimming"})
</pre>

And let's run `match (i:Interest) return i` again to see all of our interests. Let's connect these interests to our users within our graph.

<pre>
match (u1:User) where u1.name="Alice"
match (i:Interest) where i.type="soccer"
create (u1)-[:interested_in]->(i)

match (u1:User) where u1.name="Bob"
match (i:Interest) where i.type="soccer"
create (u1)-[:interested_in]->(i)

match (u1:User) where u1.name="Bob"
match (i:Interest) where i.type="programming"
create (u1)-[:interested_in]->(i)

match (u1:User) where u1.name="Sally"
match (i:Interest) where i.type="programming"
create (u1)-[:interested_in]->(i)

match (u1:User) where u1.name="Ken"
match (i:Interest) where i.type="minecraft"
create (u1)-[:interested_in]->(i)

match (u1:User) where u1.name="Janice"
match (i:Interest) where i.type="minecraft"
create (u1)-[:interested_in]->(i)

match (u1:User) where u1.name="Janice"
match (i:Interest) where i.type="swimming"
create (u1)-[:interested_in]->(i)

match (u1:User) where u1.name="Justin"
match (i:Interest) where i.type="swimming"
create (u1)-[:interested_in]->(i)

match (u1:User) where u1.name="Justin"
match (i:Interest) where i.type="soccer"
create (u1)-[:interested_in]->(i)
</pre>

Now if we return the entire graph using `match (n) return n` we can see all of our interests and users together. Great! Now it's time to leverage these interests to see who we can suggest for our users, starting with Ken.

<pre>
match (u1:User) where u1.name="Ken"
match (u1)-[:interested_in]->(:Interest)<-[:interested_in]-(u2:User)
where u1<>u2
return u2
</pre>

Makes sense! Let's try Janice.

<pre>
match (u1:User) where u1.name="Janice"
match (u1)-[:interested_in]->(:Interest)<-[:interested_in]-(u2:User)
where u1<>u2
return u2
</pre>

Looks good. Now let's add some locations to our graph. We'll start with the esteemed ABC University.

`create (:Location {place: "ABC University"})`

And if we run our query to see all of our locations, `match (l:Location) return l`, we see ABC University. Let's add some more.

<pre>
create (:Location {place: "Gardens Mall"})
create (:Location {place: "Apple Grocery"})
create (:Location {place: "Google Campus #18"})
create (:Location {place: "Coding Bootcamp"})
</pre>

We'll check out our work so far using the same query, `match (l:Location) return l`. There they are, floating all alone. Let's connect them.

<pre>
match (u1:User) where u1.name="Bob"
match (l:Location) where l.place="ABC University"
create (u1)-[:goes_to]->(l)

match (u1:User) where u1.name="Alice"
match (l:Location) where l.place="ABC University"
create (u1)-[:goes_to]->(l)

match (u1:User) where u1.name="Alice"
match (l:Location) where l.place="Apple Grocery"
create (u1)-[:goes_to]->(l)

match (u1:User) where u1.name="Ken"
match (l:Location) where l.place="Apple Grocery"
create (u1)-[:goes_to]->(l)

match (u1:User) where u1.name="Ken"
match (l:Location) where l.place="Gardens Mall"
create (u1)-[:goes_to]->(l)

match (u1:User) where u1.name="Ken"
match (l:Location) where l.place="Google Campus #18"
create (u1)-[:goes_to]->(l)

match (u1:User) where u1.name="Sally"
match (l:Location) where l.place="Google Campus #18"
create (u1)-[:goes_to]->(l)

match (u1:User) where u1.name="Sally"
match (l:Location) where l.place="Coding Bootcamp"
create (u1)-[:goes_to]->(l)

match (u1:User) where u1.name="Justin"
match (l:Location) where l.place="Coding Bootcamp"
create (u1)-[:goes_to]->(l)
</pre>

Now let's return our entire graph to check out our connections.

`match (n) return n`

We're all connected, great! Let's use this to make a suggestion for Ken.

<pre>
match (u1:User) where u1.name="Ken"
match (u1)-[:goes_to]->(:Location)<-[:goes_to]-(u2:User)
where u1<>u2
return u2
</pre>

And there we have it! We have built a very simple suggestion algorithm for friends of friends, interests, and places our users go. You can combine any number of these and make your suggestion algorithm way stronger, even assigning weights of importance to categories of data you have. The sky is the limit.

### If you liked this...
consider subscribing to <a href="https://www.youtube.com/channel/UCY90q-_2zGIqSHvmLniFf4g">my channel</a>, I'd really appreciate it!

