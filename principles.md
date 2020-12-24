# General principles of the Twitter API platform

Before submitting a proposal, it’s important to understand some fundamental principles of our API platform.

## Privacy and security

Honoring and protecting the privacy of users on Twitter is a fundamental aspect of the API platform. New proposals should respect this principle and, if possible, set an even higher standard for developers.

## Consistency

Endpoints that are expected to return the same objects (e.g. Tweets) are also expected to behave in the same way. For example, developers should expect a Tweet lookup endpoint and a Tweet search endpoint to accept the same fields and expansions, and to return the same payload. The search endpoint may provide additional fields (e.g. to support pagination), but the request and response structure should be very similar, if not identical.

## Versioning

A new API version will be issued if the API design requires a breaking change. A breaking change is a change that will require developers to significantly change their current implementations.

Once a new version is released, the previous version is supported for 12 months, while the now-new current version is supported until the next breaking change. Developers should expect new API versions no more frequently than once every 12 months.

## Authentication

Most endpoints will require authentication, and the authentication method will be OAuth 1.0a or OAuth 2.0. We encourage submitting proposals about scopes and improvements to data granularity in the context of these two authentication methods. Because we are very strict on protecting the trust our developers and users put in our service, it's unlikely that we will consider different authentication methods. We take data protection and user privacy very seriously. When submitting a proposal for his aspect of the API, we strongly recommend describing the implications on data security, and expect these proposals to encounter increased scrutiny.

## Rate limiting and activity quotas

There are many ways to implement metered access to data; rate limiting and activity are just two examples. When discussing limiting and quotas, focus on the general architecture that can be applied in a coherent way across endpoints. It's possible that certain endpoints may require an additional level of access (e.g. a search endpoint may require both a rate limit and an activity quota), so if a proposal is covering these aspects, ensure to cover the circumstances where more than one access factor should be considered.

Discussions about increasing or decreasing rate limits are generally considered outside the scope of the API design.
