# Separate endpoint for deleting filtered stream rules

-   Proposal: [#7](https://github.com/twitterdev/open-evolution/pull/7)
-   Discussion: (link to Github issue related to this proposal)
-   Authors:
    -   Shubham Parihar ([Twitter](https://twitter.com/iShiibi), [Github](https://github.com/iShibi))

-   Review Manager: TBD (this will be filled by the review manager)
-   Status: Open

## Introduction

Add a separate `DELETE` endpoint for deleting filtered stream rules.

## Motivation

Currently, there is a single endpoint for adding and deleting filtered stream rules ([POST /2/tweets/search/stream/rules](https://developer.twitter.com/en/docs/twitter-api/tweets/filtered-stream/api-reference/post-tweets-search-stream-rules))

There are two major problems with this endpoint:

1) The endpoint is a `POST` request. It represents adding a rule correctly but the endpoint can also be used to delete a rule, which isn't a correct implementation of the spec. Deleting a resource should be done using a `DELETE` request. 

2) Keeping the spec aside, the endpoint throws an error if `add` and `delete` fields are used together in the JSON body of a request to this endpoint. This raises the question that why even merge these two actions into a single endpoint when they cannot be used together at a time.


## Proposed solution

Make `(POST /2/tweets/search/stream/rules)` endpoint to only be used for adding new rules and introduce a new `(DELETE /2/tweets/search/stream/rules)` endpoint for deleting the currently active rules.

This will change the `body` of the requests to not have `add` and `delete` fields as the action will get inferred from the HTTP verb.

This change will improve the developer experience by removing the caveats related to the current endpoint. One of them is the `add` and `delete` fields in the JSON body being mutually exclusive. 

## Detailed design

The requested new `DELETE` endpoint should look something like this:

```bash
curl -X DELETE 'https://api.twitter.com/2/tweets/search/stream/rules'
    -H "Content-type: application/json"
    -H "Authorization: Bearer $BEARER_TOKEN" -d
    '{
        "ids": [
            "1165037377523306498",
            "1165037377523306499"
        ]

        // or "values" array if one wants to delete using values of rules
    }'
```

Making the current `POST` endpoint become:

```bash
curl -X POST 'https://api.twitter.com/2/tweets/search/stream/rules' 
    -H "Content-type: application/json" 
    -H "Authorization: Bearer $BEARER_TOKEN" -d 
    '{
        "rules": [
            {"value": "cat has:media", "tag": "cats with media"},
            {"value": "cat has:media -grumpy", "tag": "happy cats with media"}
        ]
    }'
```

## Compatibility, breaking changes and migrations

This is a breaking change as the `body` of the request is getting changed. The old `add` and `delete` fields will be no more. `ids`, `values`, and the new `rules` fields will become top-level fields of the JSON body.

## Alternatives considered

None