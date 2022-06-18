# Add a flag to determine if a particular content has been promoted

-   Proposal: (link to GitHub PR related to this proposal)
-   Discussion: (link to GitHub issue related to this proposal)
-   Authors:
    -   Kato Shinya ([Twitter](https://twitter.com/_kato_shinya), [GitHub](https://github.com/myConsciousness))

-   Review Manager: TBD (this will be filled by the review manager)
-   Status: Open

## Introduction

To better utilize `promoted_metrics` programmatically on Twitter API v2.0, a flag is needed to determine if a particular content has been promoted.

## Motivation

The current **Twitter API v2.0** specification seems to assume that the specific content being referenced is promoted in order to access `promoted_metrics`. For example, trying to reference `promoted_metrics` for a tweet that has not yet been promoted will result in an error.

However, the problem is that there is no way for the program to know that this particular content has been promoted whether it is a tweet or media.

If we could dynamically determine from the flags whether a particular piece of content retrieved has been promoted or not, we would have more flexible analysis.

Also, since `promoted_metrics` also allow access only from authenticated users, difficulties regarding access privileges may need to be considered for this new flag as well.

## Proposed solution

- Add `promoted` enum in `tweet.fields` and `media.fields` as a request parameter.
- Add `promoted` field to `Tweet Object` and `Media Object`.

## Detailed design

### Request

GET https://api.twitter.com/2/users/:id/tweets?tweet.fields=promoted

## Response

```json
{
  "data": [
    {
      "id": "1338971066773905408",
      "text": "💡 Using Twitter data for academic research? Join our next livestream this Friday @ 9am PT on https://t.co/GrtBOXh5Y1!n n@SuhemParack will show how to get started with recent search &amp; filtered stream endpoints on the #TwitterAPI v2, the new Tweet payload, annotations, &amp; more. https://t.co/IraD2Z7wEg",
      "promoted": true
    },
    {
      "id": "1338923691497959425",
      "text": "📈 Live now with @jessicagarson and @i_am_daniele! https://t.co/Y1AFzsTTxb",
      "promoted": false
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

An alternative other than adding flags would be an endpoint that returns promoted tweets tied to authenticated users, but I don't think this is a good idea.
