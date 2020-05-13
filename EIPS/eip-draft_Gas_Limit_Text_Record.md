---
eip: <to be assigned>
title: Suggested Gas Limit ENS Text Record for Wallets
author: Christopher Robison (@CBobRobison)
discussions-to: https://t.me/joinchat/BP2EeRnxgLMKQ1jCyxgJfA
status: Draft
type: Standards Track
category Interface
created: 2020-05-12
requires (*optional): <EIP number(s)>
replaces (*optional): <EIP number(s)>
---

<!--You can leave these HTML comments in your merged EIP and delete the visible duplicate text guides, they will not appear and may be helpful to refer to if you edit it again. This is the suggested template for new EIPs. Note that an EIP number will be assigned by an editor. When opening a pull request to submit your EIP, please use an abbreviated title in the filename, `eip-draft_title_abbrev.md`. The title should be 44 characters or less.-->

## Simple Summary
<!--"If you can't explain it simply, you don't understand it well enough." Provide a simplified and layman-accessible explanation of the EIP.-->

## Abstract
<!--A short (~200 word) description of the technical issue being addressed.-->

Wallets don't always know what the gas limit of a particular transaction should be set to when interacting with a dapp for the first time if it does not have access to the contract ABI. This may happen, for example, when sending ETH to a dapp address to trigger a fallback function.

This proposal suggests that [ENS](ens.domains) adds a new `suggested gas` text record for dapp developers to set a gas limit for wallets to reference. And that wallets adopt this new text record as a standard method for estimating gas limits.

## Motivation
<!--The motivation is critical for EIPs that want to change the Ethereum protocol. It should clearly explain why the existing protocol specification is inadequate to address the problem that the EIP solves. EIP submissions without sufficient motivation may be rejected outright.-->

When users send ETH directly to a dapp in order to trigger a [fallback function](https://solidity.readthedocs.io/en/v0.5.3/contracts.html#fallback-function) (without access to the contract ABI), wallets are unable to estimate the gas limit, which often causes the transaction to fail. [MetaMask](https://metamask.io/), for example, will simply default to a gas limit of 21000 if a user attempts to send ETH regardless of its destination. While a gas limit of 21000 is sufficient for sending ETH to another user's wallet, it is insuffient for sending ETH to dapps with fallback functions. 

Users can manually set their own gas limit above 21000 in these scenarios. However, doing so requires that a user knows how to manually configure their gas limit *and* that they be able to provide a sufficient limit. Being able to provide a sufficient limit requires the user to either (A) be prepared with the existing knowledge of the contract's ABI, or (B) participate in a process of guess and check until they discover the correct gas limit. We cannot expect all users to:
- know how to configure their gas limit manually
- know what the gas limit should be for any single contract (let alone a vast array of contracts)
- waste their ETH attempting multiple transactions with various gas limits

Some wallets have developed their own caching solutions to improve upon this UX problem. MetaMask, for example, will auto-populate an estimated gas limit *if* a user has interacted with a specific contract before. If a MetaMask user has previous completed the following transactions and subsequently attempts to, once again:
- send an ERC20, thier wallet defaults to a gas limit of ~45000
- send ETH to [dai.uniswap.eth](https://github.com/Uniswap/uniswap-frontend/issues/236#issuecomment-466464721) (to automatically exchange ETH for DAI using [Uniswap's](https://uniswap.org/) fallback function), their wallet defaults to a gas limit of ~100000

Unfortunately, this solution is not universal and still fails to provide the ideal UX in many common situations including:
- when a user sends ETH to a dapp for the first time
- when a user sends ETH to a dapp from another wallet provider (eg, [Argent](https://www.argent.xyz/), [Status](https://status.im/), [Trust Wallet](https://trustwallet.com/), etc)
- when a user sends ETH to a dapp from another wallet address (even if the same wallet provider)
- when a user sends ETH to a dapp from another wallet environment (iOS mobile, Android mobile, Chrome plugin, [Brave](https://brave.com/?ref=dfd940) plugin, etc)

## Specification
<!--The technical specification should describe the syntax and semantics of any new feature. The specification should be detailed enough to allow competing, interoperable implementations for any of the current Ethereum platforms (go-ethereum, parity, cpp-ethereum, ethereumj, ethereumjs, and [others](https://github.com/ethereum/wiki/wiki/Clients)).-->

The solution is implimented in two parts:
1. Using the existing [ENS.sol](https://github.com/ensdomains/ens/blob/36e10e71fcddcade88646821e0a57cc6c19e1ecf/contracts/ENS.sol) `setRecord()` function to set a `suggested gas` text record
2. Gaining wallet adoption to check the `suggested gas` text record and, if set, populate the `gas limit` field with the variable

```js
function setSubnodeRecord(bytes32 node, bytes32 label, address owner, address resolver, uint64 ttl) external
```



## Rationale
<!--The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->
The rationale fleshes out the specification by describing what motivated the design and why particular design decisions were made. It should describe alternate designs that were considered and related work, e.g. how the feature is supported in other languages. The rationale may also provide evidence of consensus within the community, and should discuss important objections or concerns raised during discussion.-->

## Backwards Compatibility
<!--All EIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The EIP must explain how the author proposes to deal with these incompatibilities. EIP submissions without a sufficient backwards compatibility treatise may be rejected outright.-->
All EIPs that introduce backwards incompatibilities must include a section describing these incompatibilities and their severity. The EIP must explain how the author proposes to deal with these incompatibilities. EIP submissions without a sufficient backwards compatibility treatise may be rejected outright.

## Test Cases
<!--Test cases for an implementation are mandatory for EIPs that are affecting consensus changes. Other EIPs can choose to include links to test cases if applicable.-->
Test cases for an implementation are mandatory for EIPs that are affecting consensus changes. Other EIPs can choose to include links to test cases if applicable.

## Implementation
<!--The implementations must be completed before any EIP is given status "Final", but it need not be completed before the EIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.-->
The implementations must be completed before any EIP is given status "Final", but it need not be completed before the EIP is accepted. While there is merit to the approach of reaching consensus on the specification and rationale before writing code, the principle of "rough consensus and running code" is still useful when it comes to resolving many discussions of API details.

## Security Considerations
<!--All EIPs must contain a section that discusses the security implications/considerations relevant to the proposed change. Include information that might be important for security discussions, surfaces risks and can be used throughout the life cycle of the proposal. E.g. include security-relevant design decisions, concerns, important discussions, implementation-specific guidance and pitfalls, an outline of threats and risks and how they are being addressed. EIP submissions missing the "Security Considerations" section will be rejected. An EIP cannot proceed to status "Final" without a Security Considerations discussion deemed sufficient by the reviewers.-->
All EIPs must contain a section that discusses the security implications/considerations relevant to the proposed change. Include information that might be important for security discussions, surfaces risks and can be used throughout the life cycle of the proposal. E.g. include security-relevant design decisions, concerns, important discussions, implementation-specific guidance and pitfalls, an outline of threats and risks and how they are being addressed. EIP submissions missing the "Security Considerations" section will be rejected. An EIP cannot proceed to status "Final" without a Security Considerations discussion deemed sufficient by the reviewers.

## Copyright
Copyright and related rights waived via [CC0](https://creativecommons.org/publicdomain/zero/1.0/).
