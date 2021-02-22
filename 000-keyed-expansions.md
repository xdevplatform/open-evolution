# Return keyed dictionaries for expanded objects

-   Proposal: (link to Github PR related to this proposal)
-   Discussion: (link to Github issue related to this proposal)
-   Authors:
    -   Daniele ([Twitter](https://twitter.com/i_am_daniele), [Github](https://github.com/iamdaniele))

-   Review Manager: TBD (this will be filled by the review manager)
-   Status: Open

## Introduction

Change the arrays in the `includes` payload into keyed dictionaries for easier lookup.

## Motivation

The current design of the Twitter API contains a main `data` object and an `includes` object that gets returned when the user requests an expansion:

```bash
$ twurl '/2/tweets?ids=20,21&expansions=author_id'
```

```js
{
  "data": [
    {
      "author_id": "12",
      "id": "20",
      "text": "just setting up my twttr"
    },
    {
      "author_id": "13",
      "id": "21",
      "text": "just setting up my twttr"
    }
  ],
  "includes": {
    "users": [
      {
        "id": "12",
        "name": "jack",
        "username": "jack"
      },
      {
        "id": "13",
        "name": "Biz Stone",
        "username": "biz"
      }
    ]
  }
}
```

There are two problems with the current implementation:

1. There is no explicit reference or mapping to what entity needs to be looked up in `includes`. In this example `author_id` matches to `includes.users.id`, but that is not immediately explicit to a user unless the documentation is consulted.
1. `includes` returns zero or more arrays. The user needs to cycle through the content of each array to lookup the information requested, which is clearly introduces problem a suboptimal computational resource usage, especially over a high number of requests.

This is particularly relevant when requesting expansion and looking up multiple results at the same time; this is the case for Search or Follower graph, where the user is requesting enough data to display tweets on mobile devices with enough data to satisfy Twitter's display requirement policy.

```bash
$ twurl '/2/tweets/search/recent?query=cats has:images&expansions=author_id,attachments.media_keys&max_results=100'
```

```js
{
  "data": [
    // up to 100 tweets
  ],
  "includes": {
    "users": [
      {"id": "1"},
      {"id": "2"},
      {"id": "3"},
      {"id": "4"},
      // up to 100 elements
    ],
    "media": [
      {"media_key": "1_1234676"},
      {"media_key": "1_1234677"},
      {"media_key": "1_1234678"},
      {"media_key": "1_1234679"},
      // up to 400 elements on a 100-results page
    ]
  },
  "meta": {
    "newest_id": "…",
    "oldest_id": "…",
    "result_count": 100,
    "next_token": "…"
  }
}
```

In this example, users who wish to reconcile `data` with `includes` will need to implement three theoretically O(n) lookups. An incomplete implementation will look like this (using Swift as a reference):

```swift
extension TweetLookupResponse {  
  func user(of tweet: Tweet) -> User? {
    return includes?.users?.first { $0.id == tweet.authorId }
  }

  func media(tweet: Tweet) -> [Media]? {
    if let mediaKeys = tweet.attachments?.mediaKeys,
       let media = self.includes?.media {
      return media.filter { (item) -> Bool in
        return mediaKeys.contains(item.mediaKey)
      }
    }
    return nil
  }
```

And so on for the other `includes` (tweets and poll data).

While the implementation is reasonably simple, it introduces two main issues:

- It's not clear if the API guarantees that a user or media will be in `includes`. A lengthy lookup can turn to no results, with unforeseen circumstances.
- We're adding 2 * O(n) complexity, which is unneeded if we mapped or keyed the content of `includes`

This complexity can be avoided by making each `includes` object a dictionary, and keying these dictionaries by their `id`. This does not introduce a radically new usage pattern, and it does not not change the way `includes` are requested.

## Proposed solution

Make the objects in `include` dictionaries instead of arrays, and key their objects by their `id`:

```bash
$ twurl '/2/tweets?ids=20,21&expansions=author_id'
```

```js
{
  "data": [
    {
      "author_id": "12",
      "id": "20",
      "text": "just setting up my twttr"
    },
    {
      "author_id": "13",
      "id": "21",
      "text": "just setting up my twttr"
    }
  ],
  "includes": {
    "users": {
      "12": {
        "id": "12",
        "name": "jack",
        "username": "jack"
      },
      "13": {
        "id": "13",
        "name": "Biz Stone",
        "username": "biz"
      }
    }
  }
}
```

For a multi result lookup:
```bash
$ twurl '/2/tweets/search/recent?query=cats has:images&expansions=author_id,attachments.media_keys&max_results=100'
```

```js
{
  "data": [
    // up to 100 tweets per page
  ],
  "includes": {
    "users": {
      "1": {"id": "1"},
      "2": {"id": "2"},
      "3": {"id": "3"},
      "4": {"id": "4"},
      // up to 100 elements per page
    },
    "media": [
      "1_1234676": {"media_key": "1_1234676", "url": "…"},
      "1_1234677": {"media_key": "1_1234677", "url": "…"},
      "1_1234678": {"media_key": "1_1234678", "url": "…"},
      "1_1234679": {"media_key": "1_1234679", "url": "…"},
      // up to 400 elements on a 100-results page
    ]
  },
  "meta": {
    "newest_id": "…",
    "oldest_id": "…",
    "result_count": 100,
    "next_token": "…"
  }
}
```

There is no change in the request. The response only changes in the `includes` payload, only when requested.

Because the dictionary is keyed by the `id` of the requested element, this change improves the developer experience by removing the need for developers to implement unnecessary search (access to a dictionary is direct). Our previous Swift will not need to extend the data model, and can be indexed directly as:


```swift

let fullTweet = data.map {
  let user = includes?.users[$0.author_id]?
  let media = $0.attachments?.mediaKeys.map { (key) in 
    return includes?.media[key]?
  }
}
```

And so on for other `includes`.

This solution is also cleaner compared to the current implementation because it reduces the amount of code developers have to deal with, and makes it explicit what has been expanded by just counting keys instead of counting array elements.

In terms of both data transfer and data protection, this solution does not offer significant advantages and it does not introduces significant detriment.

## Detailed design

The payload structure will change from an JSON array to a JSON dictionary:

```js
"includes": { // dictionary (same as current)
  "media": { // dictionary (change from current)
    "id<string>": {} // media object (same as current)
  },
  "tweets": { // dictionary (change from current)
    "id<string>": {} // tweet object (same as current)
  },
  "users": { // dictionary (change from current)
    "id<string>": {} // user object (same as current)
  },
  "polls": { // dictionary (change from current)
    "id<string>": {} // poll object (same as current)
  },
  "places": { // dictionary (change from current)
    "id<string>": {} // place object (same as current)
  },
}
```

In OpenAPI, this could be implemented by coding an `addiionalProperties` payload, keyed to be a string:

```yaml
Expansions:
  type: object
  properties:
    users:
      type: object
      additionalProperties:
        $ref: '#/components/schemas/User'
      minItems: 1
    tweets:
      type: object
      additionalProperties:
        $ref: '#/components/schemas/Tweet'
      minItems: 1
    places:
      type: object
      additionalProperties:
        $ref: '#/components/schemas/Place'
      minItems: 1
    media:
      type: object
      additionalProperties:
        $ref: '#/components/schemas/Media'
      minItems: 1
    polls:
      type: object
      additionalProperties:
        $ref: '#/components/schemas/Poll'
      minItems: 1
```

## Compatibility, breaking changes and migrations

This proposal will introduce a breaking change in the `includes` payload.

Developers who are not interested in `includes`, such as those who do not request `expansions`, can safely ignore the change. The remaining developers can easily migrate in the following ways.

1. Ignore dictionary keys and iterate over each element of the dictionary as if it was an array. This works best with loosely typed languages. For example, in JavaScript, developers can maintain compatibility by using `Object.values(includes.users)` which will discard the keys in `includes.users` and convert the dictionary into an array with no further action required.
2. Implement direct object access and removing the now unnecessary lookup code.

Because this is a breaking change, if implemented today, developers may notice errors; this applies only if the developer is requesting `expansions`, and only if the `includes` payload is returned. Some SDKs may also require to be adapted in order to remove unnecessary lookup code (if implemented) and to ensure compatibility for the new mapping. An initial analysis of Twitter SDKs does not indicate the presence of lookup code to match an expansion to the main response body; this suggests the migration would be reasonably easy.

## Alternatives considered

There are some alternatives to this proposed approach.

### Do nothing

Doing nothing will keep the need to perform lookups on each request payload; developers may end up creating additional structures to optimize lookups in case of high usage, which adds complexity for those developers who use the API on a large scale. For every developer, regardless of their expertise, it's an unnecessary obstacle that can be removed, so they can focus on less foundational issues. In the longer term, this can be a hurdle for all developers who use he API in large volumes.

### Expand objects in-place

The basic principle of an expansion is to populate the ID of an entity with its full object. For this reason, it can be envisioned that the `includes` payload can be removed and not returned; instead, expansions could populate in place of its `id`:

```bash
$ twurl '/2/tweets?ids=20,21&expansions=author_id'
```

```js
{
  "data": [
    {
      "author_id": {
        "id": "12",
        "name": "jack",
        "username": "jack"
      },
      "id": "20",
      "text": "just setting up my twttr"
    },
    {
      "author_id": {
        "id": "13",
        "name": "Biz Stone",
        "username": "biz"
      },
      "id": "21",
      "text": "just setting up my twttr"
    }
  ]
}
```

This solution is similar to the [expansion design adopted by Stripe](https://stripe.com/docs/expand); it has the advantage of removing the need to lookup additional information in the `includes` object, but it has the following disadvantages:

1. It has type safety implication, as the data model would need to accept either a string or an object
1. If an API has results belonging to the same entity (e.g. more than one tweet belonging to a user), the response is forced to populate the same information twice, leading to redundant information and increased response size
1. In this specific example, `author_id` looks semantically wrong (the field is `author_id`, but the API returns an object).
