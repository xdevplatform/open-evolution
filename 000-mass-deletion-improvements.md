# Increase deletion rate limit

-   Proposal: (link to GitHub PR related to this proposal)
-   Discussion: (link to GitHub issue related to this proposal)
-   Authors:
    -   Ada Cable(@adacable, adacable)

-   Review Manager: TBD (this will be filled by the review manager)
-   Status: Open

## Introduction

The current rate limit on deleting your own tweets is 50 

## Motivation

"Tweet delete" Applications are popular-  They allow people to retain their account, followers, and other elements, while removing old content which may no longer represent them, or which can be taken out of context 

They're used by people when they apply for a new job, may come into the public eye, have a life circumstance change, or other event where they may wish to remove content they don't remeber writing(which may be more than a decade old). A variety of popular options exist both hosted and as 'scripts', either deleting tweets as people go(setting a "one week expiry" or deleting en masse based on a twitter archive file). 

These applications may need to delete thousands of tweets quickly, for legitimate use cases- for example, if someone is applying for a new job tomorow, and has 15 years of use equating to possibly tens of thousands of tweets on their account. These applications are also favoured by power users, and high profile users, who have a greater risk of public humilation by having their tweets taken out of context. 

The former API had effectivley no rate limit on the delete call, but as far as I can tell effectivley rate limited it by being slower- leading people to write multithreaded delete scripts. 

I think it's also good practice compliance wise- while you have a month to process GDPR/etc data deletion requests, or people can instantly delete their entire account, allowing people to control their data without forcing them to do that is both good practice and can increase public engagement. 

## Proposed solution

Two options are proposed

- The simple way is to remove the rate limit on deleting tweets(as was implemented previously), or increase it significantly(500/15m block will give deleting 100k tweets a 50 hour deletion- with 100k requests. This is 10x faster than the current system.). 
- The more developer intensive but lower overhead way is an endpoint that allows deletion of multiple tweets in one request on the DELETE/2/tweets(as one can with GET /2/tweets, but for destroying rather than getting) reducing the number of requests and overhead. Allowing 50 requests/15m and 100 tweets/request would give 20000 tweets/hour, or 5 hours to delete 100k tweets(with 1000 requests- much more efficient than the first option). This could have a higher rate limit, if the backend is easily stressed, maybe 5/15m reqests to delete 100 tweets each- 100x faster than the current system. 

Both of these improve the developer experience, and while the first is simpler to implement for both twitter and third party developer, the second is preferable for the third party because it's faster, and for twitter because of a lower overhead. 

The first solution is on the face of it cleaner(it requires no changes to the actual api, just the rate limiting) but the second allows

Allowing people to delete their own publicly facing data as fast as practical is obviously preferable from a data protection standpoint, and the place to combat the threat of an attacker gains authenticated api access is not effectivley prevented by rate limiting. 

The first is not more efficient than the current system(indeed, it would result in more densley packed requests), however the second significantly reduces the number of requests to the API by people deleting their tweets. 

## Detailed design

A rate limit change does not significantly change the current API.

Implementing DELETE /2/tweets as well as DELETE /2/tweets/:id is a good model- It mirrors GET /2/tweets but with DELETE, and is IMO already expected behavior. This is the better option, but requires more implementation time for twitter staff. 

Having differing rate limits for DELETE /2/tweets/:id(deleting a single tweet) and DELETE /2/tweets/ (deleting multiple tweets) would be complex to implment, explain and monitor- but could be done if there are worries about backend stress. 

## Compatibility, breaking changes and migrations

No breaking changes, though significant advantages for developers who use the new rate limits or the DELETE /2/tweets. The time to upgrade code to batch tweets in a request should be minimal, however simply increasing the rate limit is obviously simpler. 

With a rate limit increase, well implemented clients should adjust automatically, even in deployment, without any changes.

Updating client lidbaries for the second one will be more work, however it's similarity to GET /2/tweets should speed things along significantly. 

## Alternatives considered

This proposal discusses two alternatives. The second is the better from an external perspective, but twitter inc will have different priorities to me. 
