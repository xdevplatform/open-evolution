# Proposal title

-   Proposal: (link to Github PR related to this proposal)
-   Discussion: (link to Github issue related to this proposal)
-   Authors:
    -   Author 1 (Twitter, Github)
    -   Author 2 (Twitter, Github)

-   Review Manager: TBD (this will be filled by the review manager)
-   Status: Open

## Introduction

Briefly describe what the proposal is about. This introduction is a short, one-paragraph vision statement for the proposal.

## Motivation

What problem or problems is this proposal looking to address?

For changes or modifications to the current API design, provide one or more examples of how a similar solution can be achieved within the current design. Practical examples are an effective way to visualize and reproduce (if possible and needed) the problem you’re trying to address with this proposal.

If you are introducing new functionality, explain in detail what are the advantages and benefits compared to the current design. This section should answer the following questions:

-   Does this proposed solution introduce new usage patterns? What learning curve should developers expect and why?
-   Are there similar API designs (e.g. RFCs, peer platforms) implementing a similar solution?

## Proposed solution

What is the intended solution or result? If the proposal is introducing new functionality, provide a detailed description and add examples as needed. For example, if the proposal aims to change the payload structure, provide an example request and response payload. If the proposal seeks to change the route structure of request fields or headers, provide a comprehensive list in order to describe the widest possible range of circumstances the proposal will apply to.

This section should answer these questions:

-   How would this solution look like from the perspective of the developer?
-   How is this proposed solution improving the developer experience?
-   How is this proposed solution cleaner than the current solution?
-   How is this proposed solution safer than the current solution, in terms of data protection?
-   How is this proposed solution more efficient in terms of data requested and retrieved?

## Detailed design

How would your proposal change the API design? This section should be used as the technical representation of your proposal. In this section, you detail how the grammar and ergonomics of the API should be changed. For example, if you are introducing a change in the route structure, you should explain how the route structure should be applied to each entity requested. If you are planning to add a field, expansion or payload section, explain how the behavior will be coherent across similar requests.

This section should contain all the necessary information with a level of clarity that can allow the Twitter team to implement this feature.

## Compatibility, breaking changes and migrations

Will this proposal require developers to change their current implementation? Your proposal should articulate how developers may implement the new feature. Some changes will require developers to change their implementation, and that is considered a breaking change. If your proposal changes the behavior of any currently implemented feature of the API design, you should clearly state that.

In any case, this section should always answer these questions:

-   How will this proposal work with existing developer implementations?
-   If we implemented this proposal today, what apps will require a change, and what apps will break without this change?
-   How can developers discover and quickly implement this proposed design?
-   How will SDK or client libraries maintainer need to implement or support this feature?

## Alternatives considered

Describe what other alternatives exist, or what other solutions you considered. For each alternative, your proposal should describe how this solution improves the Twitter API platform. There may be cases where certain solutions may offer a wider range of benefits than your proposal, and that’s okay. This can be the case of a solution that’s technically superior, but that may take more time and effort than what you’re proposing. In that case, explain why how your proposed solution can be a reasonable tradeoff.

This section should answer these questions:

-   What happens if we do nothing?
-   What alternatives did you consider? What are the benefits and tradeoffs of your proposed solution compared to this alternative?