# Relation information between an authenticated user and the result of users/lookup

-   Proposal: (link to Github PR related to this proposal)
-   Discussion: [(link to Github issue related to this proposal)](https://github.com/twitterdev/open-evolution/issues/5)
-   Author:
    -   Redouane Bali ([@RedouaneBali](https://twitter.com/RedouaneBali), [Redouane59](https://github.com/redouane59))

-   Review Manager: Daniele ([Twitter](https://twitter.com/i_am_daniele), [Github](https://github.com/iamdaniele))
-   Status: Rejected

## Introduction

When using the [user lookup endpoint](https://developer.twitter.com/en/docs/twitter-api/v1/accounts-and-users/follow-search-get-users/api-reference/get-users-lookup), we can't know if the returned user is following us or not.

## Motivation

Using a [bot](https://twitter.com/RedTheBot_) , I would like to keep some features only for the accounts following it. But today, to know if someone interacting with it is following the bot account or not is really hard.
I have to keep a list of all my followers ids locally and update it regularly. The cost of these calls are really heavy.

## Proposed solution

To me, the payload of the user lookup endpoint should contain informations about the relation between the authenticated user and the other user.

## Detailed design

Adding two new fields in the API response of users/lookup endpoint, i.e :

```
{
"followed": false,
"following": true
}
```

## Compatibility, breaking changes and migrations

These two additional fields will have to be defined in libraries.

## Alternatives considered

Only alternative I see would be to increase the API calls limit for get followers ids & get followings isd endpoints.
Currently only 15 calls are permitted so to retrieve 30K followers, it can last up to 30min.