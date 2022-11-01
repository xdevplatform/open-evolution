# Synchronize Twitter Topics with context annotations

-   Proposal: [(link to GitHub PR related to this proposal)](https://github.com/twitterdev/open-evolution/pull/28)
-   Discussion: [(link to GitHub issue related to this proposal)](https://github.com/twitterdev/open-evolution/issues/27)
-   Authors:
    -   Stephane Selim ([Twitter](https://twitter.com/selim_epfl), [GitHub](https://github.com/stefnans))

-   Review Manager: TBD (this will be filled by the review manager)
-   Status: Open

## Introduction

[Twitter Topics](https://blog.twitter.com/en_us/topics/product/2020/topics-behind-the-tweets) have been introduced since 2020. While most can be found as entities 
of the context domain 131 Unified Twitter Taxonomy, some are missing and cannot be queried over the API.

## Motivation

Twitter Topics's a nice feature of Twitter with its personalized aspect. This feature is powered by Twitter's API context annotations, more specifically
context domain 131 Unified Twitter Taxonomy after referring to the documentation of context annotations.
My initial thought was that the entities set of this domain must be a superset of those that are visible on Twitter Topics. 
This belief was further made stronger by the ability to use any entity id from the Unified Twitter Taxonomy domain and use it in the URL of the Topics page to
open the page about this topic (https://twitter.com/i/topics/<entity_id>). 

However, this is not the case as I bumped into a use case where a topic was not available among the Unified Twitter Taxonomy entities, namely "Viral Tweets" topics 
(https://twitter.com/i/topics/1284234742661963776).

The aim of this proposal is to have more of a one-to-one correspondence between the Twitter Topics and the entities of context domain 131 Unified Twitter Taxonomy,
or at least understand why it may not be so. I can think of 2 reasons of why such a one-to-one mapping may not be possible:
- First, new Topics are added all the time. But in that case, why are they not added to the API as well? Is the "Viral Tweets" a new one that has not been added yet?
- Second, some of these Topics are personalized (e.g "Based on your searches topic").

No new changes to the API design are needed to address this problem.

## Proposed solution

Nothing changes in terms of API design nor endpoints. The aim here is to have more synchronization between the entities of domain 131 of the Unified Twitter Taxonomy and the Twitter Topics (unless it's personalized). 

For example, if a Twitter Topic about "Adidas Baseball" (which is already an entity of context 48 "Product" -> context id: 48, entity id: 10040353848) were to appear, then we can simply add this entity to context 131 as well.
That way, if we search for tweets with context id 131 or 48 and entity id 10040353848 using the API, we would get tweets about Adidas Baseball. This same entity id will be used to identify the page of the Twitter Topic.

## Detailed design

I imagine the solution here is to enquire about how a new Topic is added to Twitter Topics and unless it's a personalized topic, have it added as an entity to the domain 131 of Unified Twitter Taxonomy.
This would prevent further confusion from people who expect that these Twitter Topics should figure in the domain of the Unified Twitter Taxonomy.

## Compatibility, breaking changes and migrations

This proposal should not change any previous design or break any apps that use the API. It will only add more data behind the endpoint of search query using context annotations.

## Alternatives considered

While it's a minor change to consider, it can still be confusing at first for people who are searching for a specific Topic they found on Twitter Topics. In my case,
it was "Viral Tweets" which is a very convenient topic to retrieve said viral tweets. 

Since it's not available as an entity on the API, the only straightforward way is to scrape the ["Viral Tweets" Topic page](https://twitter.com/i/topics/1284234742661963776), which only displays 
around 60 said tweets at a time. 

Another solution is to keep retrieving random tweets and filtering those that I consider to be viral. But this will not get as good nor as many results as the ones found on the "Viral Tweets" Topic page, and will quickly hit the API rate limit before retrieving a good amount of them.
