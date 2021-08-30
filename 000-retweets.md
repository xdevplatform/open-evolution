# Retweets

-   Proposal: https://github.com/twitterdev/open-evolution/pull/4
-   Discussion: (link to Github issue related to this proposal)
-   Authors:
    -   Igor Brigadir (@igorbrigadir, @igorbrigadir)

-   Review Manager: TBD (this will be filled by the review manager)
-   Status: Open

## Introduction

Add the retweet IDs to the retweets endpoint. 

## Motivation

The current API https://developer.twitter.com/en/docs/twitter-api/tweets/retweets/introduction only replaces the v1.1 https://developer.twitter.com/en/docs/twitter-api/v1/tweets/post-and-engage/api-reference/get-statuses-retweeters-ids endpoint, which only gives you the users that have retweeted a tweet, not the retweet itself.

The retweet itself is important to get the `created_at` and other meta data from the retweet.

## Proposed solution

Add the tweet ids of the retweets to the response. Or add a new endpoint that gives you the actual retweets themselves instead of the users.

The existing `id` field should be renamed to `user_id` or `author_id` - to be less confusing.

## Detailed design

Current API call https://developer.twitter.com/en/docs/twitter-api/tweets/retweets/api-reference/get-tweets-id-retweeted_by is like this:

```
{
  "data": [
    {
      "id": "1425663002867494915",
      "name": "Prafful Sharma",
      "username": "hashencoder"
    },
    {
      "id": "1424971375882637312",
      "name": "okuda_dev",
      "username": "okuda_dev"
    },
    {
      "id": "1422484652380545030",
      "name": "pocket_universe",
      "username": "pcktunvrs"
    },
    ...
```

The proposed one will be like this (using `1415348607813832708` as an example, these should be the actual retweet ids.):

```
{
  "data": [
    {
      "author_id": "1425663002867494915",
      "name": "Prafful Sharma",
      "username": "hashencoder",
      "retweet_id": "1415348607813832708"
    },
    {
      "author_id": "1424971375882637312",
      "name": "okuda_dev",
      "username": "okuda_dev",
      "retweet_id": "1415348607813832708"
    },
    {
      "author_id": "1422484652380545030",
      "name": "pocket_universe",
      "username": "pcktunvrs",
      "retweet_id": "1415348607813832708"
    },
    ...
```

## Compatibility, breaking changes and migrations

If it's a new endpoint, it will require more changes. Otherwise if it's a new field it may require fewer changes.

## Alternatives considered

Currently the only way to do this is using Search with `retweets_of:user` which is not adequate as it gives you all retweets of all tweets by theuser, not a specific tweet.
