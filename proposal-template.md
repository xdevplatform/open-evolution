# Add profile_banner_url to v2 User object 

-   Proposal: (link to GitHub PR related to this proposal)
-   Discussion: [(link to GitHub issue related to this proposal)](https://github.com/twitterdev/open-evolution/issues/13)
-   Authors:
    -   Author 1 (dickhardt, dickhardt)

-   Review Manager: TBD (this will be filled by the review manager)
-   Status: Open

## Introduction

profile_banner is available in v1.1 API, but not in v2.0

## Motivation

profile_banner is part of a user's identity, and is useful for presenting info about the user

## Proposed solution

add profile_banner_url to fields in user API so that the profile_banner_url can be obtained just like the profile_image_url

This would allow applications to use only the v2.0 API instead of having to request the user to go through a OAuth 1.0 authorization flow to grant access to user info as well as an OAuth 2.0 flow to grant access to v2.0 APIs

## Detailed design

add profile_banner_url to fields

## Compatibility, breaking changes and migrations

Can't see how anything will break. Brings v2.0 closer to v1.1 functionality

## Alternatives considered

Continue using v1.1 API and OAUth 1.0A <blech>
