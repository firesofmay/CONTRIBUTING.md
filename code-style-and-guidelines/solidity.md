---
description: Our standards for smart contract code.
---

# Solidity

## Style

We follow the [Official Solidity Style Guide](https://solidity.readthedocs.io/en/develop/style-guide.html) and use [Kathari](../kathari.md) for linting.

## Comments & Documentation

### When to Comment

Comments should be used for:

* Documenting all functions using the [Ethereum Natural Specification Format](https://github.com/ethereum/wiki/wiki/Ethereum-Natural-Specification-Format) \(ENSF\).
* Explaining all non-obvious parts of code. A smart contract **must work**, but must also **convince external reviewers that it works**.
* Explaining security choices.

#### Computational Complexity

If a function is not `O(1)` , the first line of the `@dev` part of the ENSF comment should be followed by what the big O complexity is and a brief explanation. This is so that callers know what they are getting into in terms of gas consumption. It is common for some gas intensive functions to only be called from clients and not from other contracts.

#### External Trust

For functions that make an external call with enough gas for re-entry, not `send` or `transfer`, specify "UN/TRUSTED." at the end of the `@dev` part of the ENSF comment. This tells readers wether the external call receiver is trusted or not, i.e. the code it executes is controlled.

{% hint style="danger" %}
Note that specifying a contract type for a variable does not guarantee that it will reference a contract of that type as any address can be coerced into any contract, and vice-versa.
{% endhint %}

#### Work in Progress

Mark unsecured functions or functions that are not finished yet with "TODO." at the end of the `@dev` part of the ENSF comment.

## Good Practices

### Code

A lot of these apply to software in general.

* Do not use magic numbers or values, define a constant or enum.
* If you write the same code more than twice, you need to factorize it.
* If you need special testing functions that expose functionality not intended for production, inherit the contract from a test contract, rather than adding the testing functions to the original contract.

### Mindset

Traditional back ends do most of the heavy lifting for the front end, because computation is cheap and faster than on the client. But, smart contract backends, leave most of the heavy lifting for the front end, because computation is expensive and slower than on the client. This is an inherent property of consensus based systems that involve lots of parties. Improvements will always be made, but it will also always be cheaper to execute code on the front end.

{% hint style="info" %}
Abstractions can be made to make front end developers' lives easier. Our [archon](https://github.com/kleros/archon) does just that for ERC 792 contracts.
{% endhint %}

This and the security risks of smart contracts that interact with other smart contracts with complex and stateful interdependencies requires that we take a unique approach and mindset when developing smart contracts. Here are some general guidelines:

* If possible, i.e. does not lead to a significant increase in computation, externally called functions should follow this **3-step pattern**: 

  * Verify that the call is authorized. Modifiers can be used for this.
  * Execute state changes.
  * Make external calls and interactions.

  This protects the function from being re-entered through the external calls and having some effect running more times than was intended.

* Be aware of gas consumption. Smart contracts differ to traditional software where you can "Get it correct the first time, fast the second.", being slow in a smart contract is being incorrect. Out of gas transactions fail and high TX fees can place a huge burden on users.
* Be OK with **"doing 0"**. A function that sends 0 tokens, gives a penalty of 0%, and/or can't be called again unless 0 seconds pass is fine. Smart contracts should have the minimum amount of code possible and this ties into the next point.
* Don't protect the user from himself. Client execution is almost free, but smart contract execution isn't, so **limit smart contracts to blocking malicious behavior** and **let clients prevent the stupid ones**.



