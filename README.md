An example of GraphQL akka-http server with SSE-based subscriptions. It features:

* Implementation based on CQRS (Command Query Responsibility Segregation) + event-sourcing 
* Server Sent Events subscription queries based on akka-streams and akka-sse
* Optimistic concurrency control for mutation queries

This example is pretty rough around the edges at the moment (and uses snapshot version of sangria). Subscriptions support in GraphQL and well as sangria is still in experimental phase, so expect big changes and improvements in near future. This also means that your feedback is important and very welcome ;)  

## How to start

The only prerequisites are [SBT](http://www.scala-sbt.org/download.html) and [Java 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). After you cloned the project, you need to run an application with SBT:
 
```bash
sbt run
```

If you would like to experiment and change code youseld, then better alternative would be an [sbt-revolver](https://github.com/spray/sbt-revolver) which is already available in the project. You just need yo run `sbt ~reStart` and it will compile abd restart the server on every change.
   
After you started the server, you can point your browser to following URLs:
 
* [http://localhost:8080](http://localhost:8080) - GraphiQL UI
* [http://localhost:8080/client](http://localhost:8080/client) - Simple Server Sent Events GraphQL client that will stream events based on subscription query. 

## High-level picture

High-level picture looks like this:
 
![Event-stream based subscriptions](http://olegilyenko.github.io/reactive-ecommerce-api-design/assets/img/graphq-subscription-3.svg)

I also described this approach in much more detail here:

[Event-stream based GraphQL subscriptions](https://gist.github.com/OlegIlyenko/a5a9ab1b000ba0b5b1ad)

Please not that this particular example does not have persistence, this means that `MemoryEventStore` and views keep all of the data in memory.   

## Optimistic concurrency control

I find optimistic concurrency control pretty important for this kind of architecture. So I decided to include it in this example application, even though it add a bit of complexity.

This pattern helps clients to detect conflicts when they are doing mutations. Imagine that two clients would like to change an articale at the same time with GraphQL query like this:
  
```js
mutation NewText {
  changeArticleText(id: "123", version: 5, text: "bar")
}
```

Each client will first read an article and then make some decision based on the returned article. Let's say that they both have decided to updated an article in different ways. In order to perform the mutation they both need to tell server which version of article that based their decision on. Thanks to version server is able to detect a conflict and only successfully perform one mutation, rejecting the other one:   

![Optimistic concurrency control](http://olegilyenko.github.io/reactive-ecommerce-api-design/assets/img/optimistic-sangria.svg)

## Feedback

Feedback is very welcome in any form :) Feel free to make PRs, post issues or join [the chat](https://gitter.im/sangria-graphql/sangria).

 

