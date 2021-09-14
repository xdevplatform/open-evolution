# Proposal title

-   Proposal: (link to Github PR related to this proposal)
-   Discussion: (link to Github issue related to this proposal)
-   Authors:
    -   rMaxiQp (@NitDef, @rMaxiQp)

-   Review Manager: TBD (this will be filled by the review manager)
-   Status: Open

## Introduction

Making sure filter stream's matching ID has a consistent type.


## Motivation

There seems to be a [type inconsistency issue](https://twittercommunity.com/t/filter-stream-returns-invalid-matching-rules/146840/4) while using [Twitter API v2 Filter Stream](https://developer.twitter.com/en/docs/twitter-api/tweets/filtered-stream/introduction). Even though Python seems to handle it correctly, it leads to a possible rounding error in JavaScript.

## Proposed solution

1. Making sure we provide a consistent type for matching rule's ID
2. Exposing `id_str` (which is provided in v1.1 endpoints) as the ground truth

## Detailed design

Current designs:


```.json
// Get Matching Rule Endpoint

{
  data: [
    {
      value: 'cats has:videos',
      tag: 'cats videos',
      id: '1437524638125346816'
    }
  ],
  meta: {
    sent: '2021-09-13T21:12:36.349Z',
    summary: { created: 1, not_created: 0, valid: 1, invalid: 0 }
  }
}
```


```.json
// Tweet data that is returned from filter stream

{
    "data": {
        "id": "1437525241786425353",
        "text": "Footage of the semi-final match of the inaugural women's Clear Currency All-Ireland T20 Cup, broadcast by Cricket Ireland, showed the dog running onto the pitch to intercept the ball during play 🐶\n\nToday's top stories: https://t.co/4ozTbgvoxl https://t.co/C2r238OUlZ"
    },
    "matching_rules": [
        {
            "id": 1436114489988968481,
            "tag": "dog videos"
        }
    ]
}
```

The proposed update:

1. Update the tweet data that is returned from filter stream

```
{
    "data": {
        "id": "1437525241786425353",
        "text": "Footage of the semi-final match of the inaugural women's Clear Currency All-Ireland T20 Cup, broadcast by Cricket Ireland, showed the dog running onto the pitch to intercept the ball during play 🐶\n\nToday's top stories: https://t.co/4ozTbgvoxl https://t.co/C2r238OUlZ"
    },
    "matching_rules": [
        {
            "id": "1436114489988968481",
            "tag": "dog videos"
        }
    ]
}
```

2. Add `id_str` to the tweet data that is returned from filter stream

```
{
    "data": {
        "id": "1437525241786425353",
        "text": "Footage of the semi-final match of the inaugural women's Clear Currency All-Ireland T20 Cup, broadcast by Cricket Ireland, showed the dog running onto the pitch to intercept the ball during play 🐶\n\nToday's top stories: https://t.co/4ozTbgvoxl https://t.co/C2r238OUlZ"
    },
    "matching_rules": [
        {
            "id": 1436114489988968481,
            "id_str": "1436114489988968481"
            "tag": "dog videos"
        }
    ]
}
```

## Compatibility, breaking changes and migrations

1. Need to do a type conversion
2. An additional field is required

## Alternatives considered

Currently the only way would be comparing `tag` of each matching rule instead of relying on its `id`.