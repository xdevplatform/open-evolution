# Add `liked` and `retweeted` flags to `Tweet` object

-   Proposal: (link to GitHub PR related to this proposal)
-   Discussion: (link to GitHub issue related to this proposal)
    - [#16](https://github.com/twitterdev/open-evolution/issues/16)
-   Authors:
    -   Kato Shinya ([Twitter](https://twitter.com/_kato_shinya), [GitHub](https://github.com/myConsciousness))

-   Review Manager: TBD (this will be filled by the review manager)
-   Status: Open

## Introduction

The `liked` and `retweeted` flags are needed to make better use of tweet data retrieved from the **Twitter API v2.0**.

## Motivation

**Twitter API v2.0** does not provide a way to programmatically determine if a particular tweet retrieved from the API has been **liked** or **retweeted** by an authenticated user.

By having no way of knowing these states, users of the Twitter API v2.0 will not be able to make the following UI representations in third-party apps they create.

![example](https://user-images.githubusercontent.com/13072231/174460422-92c7dd5f-10db-40b0-bc45-28a950dc141d.png)

For example, to reproduce the above tweet status in a third-party application using only Twitter API v2.0, the third-party application must remember that a particular tweet has been liked or retweeted.

## Proposed solution

- Add `liked` and `retweeted` enums in `tweet.fields` as a request parameter.
- Add `liked` and `retweeted` fields to `Tweet Object`.

## Detailed design

### Request

GET https://api.twitter.com/2/users/:id/timelines/reverse_chronological?tweet.fields=liked,retweeted

## Response

```json
{
  "data": [
    {
      "id": "1338971066773905408",
      "text": "💡 Using Twitter data for academic research? Join our next livestream this Friday @ 9am PT on https://t.co/GrtBOXh5Y1!n n@SuhemParack will show how to get started with recent search &amp; filtered stream endpoints on the #TwitterAPI v2, the new Tweet payload, annotations, &amp; more. https://t.co/IraD2Z7wEg",
      "liked": true,
      "retweeted": false
    },
    {
      "id": "1338923691497959425",
      "text": "📈 Live now with @jessicagarson and @i_am_daniele! https://t.co/Y1AFzsTTxb",
      "liked": true,
      "retweeted": true
    }
  ],
  "meta": {
    "oldest_id": "1338923691497959425",
    "newest_id": "1338971066773905408",
    "result_count": 2,
  }
}
```

## Compatibility, breaking changes and migrations

There are no breaking changes or migration concerns with the addition of field.

## Alternatives considered

No alternative found. Adding a flag would be most effective.
