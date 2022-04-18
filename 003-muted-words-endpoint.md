# Muted Words API endpoint

-   Proposal: (link to Github PR related to this proposal)
-   Discussion: (link to Github issue related to this proposal)
-   Authors:
    -   Colin Winhall ([Twitter](https://twitter.com/colinwinhall))


-   Review Manager: TBD (this will be filled by the review manager)
-   Status: Open

## Introduction

Having the ability to create, update and delete muted words through the Twitter API would allow for much more flexible in being able to both maintain a muted word list as well as compile and curate lists for community use.

## Motivation

Given the nature of today's world of fast changing popular phrases, words and hashtags, it would make sense to allow for a programatic approach in dealing with these rapid and constant changes. The pace at which negativity, spam or fake news can be spread is not able to be countered through the manually adding single words at a time through the UI. To take control of this, a programatic approach is required that should elnd itself eventually to community curated word lists that can be muted at the click of a button.

The current design of the API allows for such flexibility when dealing with users via the [mutes/users endpoint](https://developer.twitter.com/en/docs/twitter-api/v1/accounts-and-users/mute-block-report-users/api-reference/post-mutes-users-create) and this functionality should be extended to muted words.

An example from the mutes/users endpoint for creating a new muted user using the `screen_name` of a user can be seen below;

**Request**
`POST https://api.twitter.com/1.1/mutes/users/create.json?screen_name=evilpiper`

**Response**

```{
  "id": 54931584,
  "id_str": "54931584",
  "name": "Evil Piper",
  "screen_name": "evilpiper",
  "location": "",
  "description": "mwah-ha-haaaaa",
  "url": null,
  "entities": {
    "description": {
      "urls": [

      ]
    }
  },
  "protected": false,
  "followers_count": 16,
  "friends_count": 10,
  "listed_count": 0,
  "created_at": "Wed Jul 08 15:40:42 +0000 2009",
  "favourites_count": 1,
  "utc_offset": 3600,
  "time_zone": "London",
  "geo_enabled": false,
  "verified": false,
  "statuses_count": 71,
  "lang": "en",
  "status": {
    "created_at": "Tue Apr 15 00:10:23 +0000 2014",
    "id": 455860653731753984,
    "id_str": "455860653731753984",
    "text": "Super cool app to install http://t.co/ZiH6VOqLB3",
    "source": "Twitter for  iPhone",
    "truncated": false,
    "in_reply_to_status_id": null,
    "in_reply_to_status_id_str": null,
    "in_reply_to_user_id": null,
    "in_reply_to_user_id_str": null,
    "in_reply_to_screen_name": null,
    "geo": null,
    "coordinates": null,
    "place": null,
    "contributors": null,
    "retweet_count": 0,
    "favorite_count": 0,
    "entities": {
      "hashtags": [

      ],
      "symbols": [

      ],
      "urls": [
        {
          "url": "http://t.co/ZiH6VOqLB3",
          "expanded_url": "http://cards-demo.cfapps.io/owntracks.html",
          "display_url": "cards-demo.cfapps.io/owntracks.html",
          "indices": [
            26,
            48
          ]
        }
      ],
      "user_mentions": [

      ]
    },
    "favorited": false,
    "retweeted": false,
    "possibly_sensitive": false,
    "lang": "en"
  },
  "contributors_enabled": false,
  "is_translator": false,
  "is_translation_enabled": false,
  "profile_background_color": "1A1B1F",
  "profile_background_image_url": "http://abs.twimg.com/images/themes/theme1/bg.png",
  "profile_background_image_url_https": "https://abs.twimg.com/images/themes/theme1/bg.png",
  "profile_background_tile": false,
  "profile_image_url": "http://pbs.twimg.com/profile_images/307611594/evil_normal.png",
  "profile_image_url_https": "https://pbs.twimg.com/profile_images/307611594/evil_normal.png",
  "profile_link_color": "A6001E",
  "profile_sidebar_border_color": "181A1E",
  "profile_sidebar_fill_color": "949399",
  "profile_text_color": "120312",
  "profile_use_background_image": false,
  "default_profile": false,
  "default_profile_image": false,
  "following": true,
  "follow_request_sent": false,
  "notifications": false,
  "muting": true
}```

A similar approach could be used for muted words as outlined further in the proposed solution section below.

## Proposed solution

Create a new endpoint `mutes/words` that allows for the following;

### POST mutes/words/create

#### Resource URL

https://api.twitter.com/1.1/mutes/words/create.json

#### Resource Information

Response formats	JSON
Requires authentication?	Yes (user context only)
Rate limited?	Yes

#### Parameters

`word`	Required

#### Example Request

`POST https://api.twitter.com/1.1/mutes/words/create.json?screen_name=nft`

#### Example Response
```
{
  "id": 54931584,
  "id_str": "54931584",
  "word": "nft",
  "created_at": "Mon April 18 13:33:37 +0000 2022"
}
```

Below an example of a GET request to get the list of muted words.

### GET mutes/words/list

#### Resource URL

https://api.twitter.com/1.1/mutes/words/list.json

#### Resource Information

Response formats	JSON
Requires authentication?	Yes (user context only)
Rate limited?	Yes
Requests / 15-min window (user auth)	15

#### Parameters

None? Perhaps this needs further research

#### Example Request

`GET https://api.twitter.com/1.1/mutes/words/list.json`

#### Example Response
```
{
  "words": [
  {
    "id": 54931584,
    "id_str": "54931584",
    "word": "nft",
    "created_at": "Mon April 18 13:33:37 +0000 2022"
  },
   {
    "id": 54931585,
    "id_str": "54931585",
    "word": "bayc",
    "created_at": "Mon April 18 04:20:69 +0000 2022"
  }
  ]
}
```

Below is an example of how a muted word can be deleted

### POST mutes/words/destroy

#### Resource URL

https://api.twitter.com/1.1/mutes/words/destroy.json

#### Resource Information

Response formats	JSON
Requires authentication?	Yes (user context only)
Rate limited?	Yes

#### Parameters

`word` Required
`word_id` Optional

#### Example Request

`POST https://api.twitter.com/1.1/mutes/words/destroy.json?word=nft`

#### Example Response
```
{
  "id": 54931584,
  "id_str": "54931584",
  "word": "nft",
  "created_at": "Mon April 18 13:33:37 +0000 2022"
}
```

## Detailed design

Covered in proposed solution.

## Compatibility, breaking changes and migrations

This feature introduces no breaking changes as it will follow all the rules and principles of the Twitter API.

Only new documentation will be required to be added to the existing API docs that provides the outline of this new endpoint.

## Alternatives considered

The only alternative to having an API endpoint for muted words in order to add new words in bulk would be through a browser extension that makes use of the users session in order to make a request within the browser to add a muted word. This functionality is less controlled and poses security risks and this is another reason having this functionality accessible via the twitter API should be considered for implementation.
