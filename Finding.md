# Spearbit Take Home Finding: 

## Delegatecall to Untrusted Callee

**Severity**: Critical

**Context**: [Implementation.sol#L18-L19](https://github.com/spearbit-audits/writing-exercise/blob/develop/contracts/Implementation.sol#L18-L19):

The proxy contract does not check for the contract's existence prior to returning. It may return success to a failed call and result in unintended behavior should application logic depend on the result of such call. 

A warning in the [solidity docs](https://docs.soliditylang.org/en/v0.4.24/control-structures.html#error-handling-assert-require-revert-and-exceptions) states:
> The low-level call, `delegatecall` and callcode will return success if the called account is non-existent, as part of the design of EVM. Existence must be checked prior to calling if desired.

Here are two examples of how this can be exploited: 

- The lack of an existence check may lead a third party to believing that a `delegatecall` was successful when it was not, which can result in loss of user funds. 
- Since the target address has control over the caller contract, it may modify storage in the caller contract or make malicious actions such as invoking `selfdestruct` on the caller. 

**Recommendation**: The target address is derived from user input. Consider checking the address against a list of whitelisted addresses. 
