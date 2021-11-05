# Retweets

-   Proposal: https://github.com/twitterdev/open-evolution/pull/4
-   Discussion: https://github.com/twitterdev/open-evolution/pull/4
-   Authors:
    -   Igor Brigadir (@igorbrigadir, @igorbrigadir)

-   Review Manager: TBD (this will be filled by the review manager)
-   Status: Open

## Introduction

Add a new retweets endpoint. 

## Motivation

The current v2 API https://developer.twitter.com/en/docs/twitter-api/tweets/retweets/introduction only replaces the v1.1 https://developer.twitter.com/en/docs/twitter-api/v1/tweets/post-and-engage/api-reference/get-statuses-retweeters-ids endpoint, which only gives you the *users* that have retweeted a tweet, not the *retweet* itself.

The retweet itself is important to get the full tweet metadata, especially `created_at` and ID of the retweet. The timestamps are important for modeling retweet spread and the other metadata that goes along with a retweet (user, public metrics from the `referenced_tweet`) are also important to maintain counts and a sense of how things change over time with retweets.

## Proposed solution

Add a new endpoint `/2/tweets/:id/retweets` that gives you the actual retweets themselves instead of the users. This will be a replacement for https://developer.twitter.com/en/docs/twitter-api/v1/tweets/post-and-engage/api-reference/get-statuses-retweets-id

## Detailed design

The proposed new endpoint for retweets will closely mirror https://developer.twitter.com/en/docs/twitter-api/tweets/timelines/api-reference/get-users-id-mentions having the same query parameters like `since_id`, `until_id`, fields and expansions etc, except the `id` path parameter is of a tweet not a user. 

For example:

`GET /2/tweets/1441054496931541004/retweets`

Using https://twitter.com/TwitterDev/status/1451609611455242241 as an example, calling `GET /2/tweets/1441054496931541004/retweets` (usual fields and expansions parameters ommitted) should give you:


```
{
  "data": [
    {
      "id": "1452022021651517441",
      "referenced_tweets": [
        {
          "type": "retweeted",
          "id": "1451609611455242241"
        }
      ],
      "text": "RT @TwitterDev: Join us on Friday October 29th at 2 pm ET for our next live stream on academic research using the #TwitterAPI. We are excit…",
      "author_id": "1375017222813310981",
      "conversation_id": "1452022021651517441"
    },
    {
      "id": "1451852904764149761",
      "referenced_tweets": [
        {
          "type": "retweeted",
          "id": "1451609611455242241"
        }
      ],
      "text": "RT @TwitterDev: Join us on Friday October 29th at 2 pm ET for our next live stream on academic research using the #TwitterAPI. We are excit…",
      "author_id": "2906851412",
      "conversation_id": "1451852904764149761"
    },
    {
      "id": "1451644319098720257",
      "referenced_tweets": [
        {
          "type": "retweeted",
          "id": "1451609611455242241"
        }
      ],
      "text": "RT @TwitterDev: Join us on Friday October 29th at 2 pm ET for our next live stream on academic research using the #TwitterAPI. We are excit…",
      "author_id": "744265436",
      "conversation_id": "1451644319098720257"
    },
    {
      "id": "1451625579640344576",
      "referenced_tweets": [
        {
          "type": "retweeted",
          "id": "1451609611455242241"
        }
      ],
      "text": "RT @TwitterDev: Join us on Friday October 29th at 2 pm ET for our next live stream on academic research using the #TwitterAPI. We are excit…",
      "author_id": "1153115735192932353",
      "conversation_id": "1451625579640344576"
    }
  ],
  "includes": {
    "tweets": [
      {
        "id": "1451609611455242241",
        "attachments": {
          "media_keys": [
            "3_1451606862583926784"
          ]
        },
        "text": "Join us on Friday October 29th at 2 pm ET for our next live stream on academic research using the #TwitterAPI. We are excited to host @dfreelon and Andrew Crist from @unc_citap to share how they use the Twitter API for research studies. We will be live on https://t.co/GrtBOXh5Y1 https://t.co/URtpGcU3ZK",
        "conversation_id": "1451609611455242241",
        "author_id": "2244994945"
      }
    ],
    "users": [
      {
        "name": "LaiAvery",
        "username": "taishen8808",
        "id": "1375017222813310981"
      },
      {
        "name": "Twitter Dev",
        "pinned_tweet_id": "1441054496931541004",
        "username": "TwitterDev",
        "id": "2244994945"
      },
      {
        "name": "nov it's jack birthday⚡",
        "pinned_tweet_id": "1174968413254406144",
        "username": "novitsjackbdy",
        "id": "2906851412"
      },
      {
        "name": "Newspaper & Online News Division of AEJMC",
        "username": "aejmc_nond",
        "id": "744265436"
      },
      {
        "name": "Center for Information, Technology, & Public Life",
        "username": "unc_citap",
        "id": "1153115735192932353"
      }
    ]
  },
  "meta": {
    "newest_id": "1452022021651517441",
    "oldest_id": "1451625579640344576",
    "result_count": 4,
    "next_token": "b26v89c19zqg8o3fpdv67sb97sn3b0wzcwvnn8it32a2l"
  }
}
```

## Compatibility, breaking changes and migrations

As this is a new endpoint, there are no breaking changes.

## Alternatives considered

In v1.1 API the eaquivalent is https://developer.twitter.com/en/docs/twitter-api/v1/tweets/post-and-engage/api-reference/get-statuses-retweets-id or the Account Activity API which requires a persistent webhook to receive retweets as the happen, and cannot fetch them retrospectively. Another alternative is the `retweets_of_status_id:` operator in PowerTrack. (This operator is not available in v2 filtered stream). Just like Account Activity API, it only returns real time results. This is also available via Historical PowerTrack, but it is only available to enterprise users and managed customers.

Currently the only way to do this is using v2 API Search is with `retweets_of:user` which is not adequate as it gives you all retweets of all tweets by the user, not a specific tweet.
