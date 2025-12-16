# RebateFi Hook - Findings Report

# Table of contents
- ## [Contest Summary](#contest-summary)
- ## [Results Summary](#results-summary)
- ## High Risk Findings
    - ### [H-01. Incorrect ReFi Token Validation Allows Initialization of Invalid Pools](#H-01)
- ## Medium Risk Findings
    - ### [M-01. Use of IERC20.transfer without checking return value; consider SafeERC20](#M-01)
- ## Low Risk Findings
    - ### [L-01. Event argument order swapped in withdrawTokens (incorrect emit)](#L-01)


# <a id='contest-summary'></a>Contest Summary

### Sponsor: First Flight #53

### Dates: Nov 20th, 2025 - Nov 27th, 2025

[See more contest details here](https://codehawks.cyfrin.io/c/2025-11-rebatefi-hook)

# <a id='results-summary'></a>Results Summary

### Number of findings:
- High: 1
- Medium: 1
- Low: 1


# High Risk Findings

## <a id='H-01'></a>H-01. Incorrect ReFi Token Validation Allows Initialization of Invalid Pools            



# Root + Impact

## Description

* `_beforeInitialize` should enforce that **any pool initialized with this hook MUST include the ReFi token** as either `currency0` or `currency1`. This ensures the hook only runs on pools relevant to ReFi and prevents unintended behavior on unrelated pools.

* **Problem:**\
  The current implementation incorrectly checks `currency1` twice and never checks `currency0`, leading to a broken invariant that allows initialization of pools that **do not include the ReFi token at all**.

```Solidity
// Root cause in the codebase with @> marks to highlight the relevant section
if (
@>  Currency.unwrap(key.currency1) != ReFi &&
@>  Currency.unwrap(key.currency1) != ReFi
) {
    revert ReFiNotInPool();
}
```

## Risk

**Likelihood**:

* Pools are often initialized in automated scripts or via external integrators, causing this broken check to routinely allow pools without ReFi.

*  

  The hook applies dynamic fees and emits ReFi-specific events; when used on the wrong pool, execution paths depending on ReFi will misbehave or revert inconsistently during swaps.

**Impact**:

* Non-ReFi pools can be initialized with this hook, enabling **incorrect dynamic fee behavior** and wrong event emission.

*  

  Downstream logic assuming the presence of ReFi may malfunction, leading to inconsistent state, unexpected reverts, or incorrect accounting.

## Proof of Concept

A pool with tokens `(ReFi, TokenB)` (neither is ReFi) still initializes successfully:

```Solidity
PoolKey memory key = PoolKey({
    currency0: ReFi,
    currency1: Currency.wrap(address(TokenB)),
    // …
});

vm.prank(admin);
hook.beforeInitialize(address(0), key, 0);
// ❌ Expected revert ReFiNotInPool()
// ✅ Initializes successfully due to incorrect check

```

## Recommended Mitigation

Check both currencies explicitly:

 

```Solidity
// Root cause in the codebase with @> marks to highlight the relevant section
if (
@>  Currency.unwrap(key.currency1) != ReFi &&
@>  Currency.unwrap(key.currency1) != ReFi
) {
    revert ReFiNotInPool();
}
```

```diff
- if (
-     Currency.unwrap(key.currency1) != ReFi &&
-     Currency.unwrap(key.currency1) != ReFi
- ) {
-     revert ReFiNotInPool();
- }
+ if (
+     Currency.unwrap(key.currency0) != ReFi &&
+     Currency.unwrap(key.currency1) != ReFi
+ ) {
+     revert ReFiNotInPool();
+ }

```

    
# Medium Risk Findings

## <a id='M-01'></a>M-01. Use of IERC20.transfer without checking return value; consider SafeERC20            



# Root + Impact

## Description

* Normal behavior: ERC20 transfers should be made via `SafeERC20` (or at minimum check the boolean return) to support tokens that return `false` instead of reverting.

*  

  Problem: `withdrawTokens` calls `IERC20(token).transfer(to, amount)` and ignores the return value; non-standard tokens can return `false` and cause silent failures.

```Solidity
// Root cause in the codebase with @> marks to highlight the relevant section
function withdrawTokens(address token, address to, uint256 amount) external onlyOwner {
@>     IERC20(token).transfer(to, amount);
      emit TokensWithdrawn(token, to, amount);
}

```

## Risk

**Likelihood**:

* Occurs when withdrawing tokens that are non-standard (historical examples include some stablecoins and older tokens).

*  

  Many integrations may interact with a wide range of ERC20s, including non-standard ones.

**Impact**:

* Transfers may silently fail (return false) leaving tokens in contract while owner believes they were withdrawn.

*  

  Can cause lost time, mistaken accounting, or locked assets until remedied on-chain.

## Proof of Concept

When withdrawTokens is called with BadTokenMock address, transfer returns false but execution continues.

```Solidity
contract BadTokenMock {
    function transfer(address, uint256) external pure returns (bool) {
        return false; // non-standard behavior: returns false instead of reverting on failure
    }
}

```

## Recommended Mitigation

Add OpenZeppelin SafeERC20 and use `safeTransfer`:

```diff
+ import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
+ using SafeERC20 for IERC20;
 
 function withdrawTokens(address token, address to, uint256 amount) external onlyOwner {
-    IERC20(token).transfer(to, amount);
-    emit TokensWithdrawn(token, to, amount);
+    IERC20(token).safeTransfer(to, amount);
+    emit TokensWithdrawn(token, to, amount);
 }

```


# Low Risk Findings

## <a id='L-01'></a>L-01. Event argument order swapped in withdrawTokens (incorrect emit)            



# Root + Impact

## Description

* Normal behavior: Events should be emitted with arguments in the same order and semantic meaning as declared so off-chain indexers and tooling parse logs correctly.

*  

  Problem: The declared `TokensWithdrawn(address indexed token, address indexed to, uint256 amount)` event is emitted with arguments in the wrong order, swapping `token` and `to`.

```Solidity
// Root cause in the codebase with @> marks to highlight the relevant section
event TokensWithdrawn(address indexed token, address indexed to, uint256 amount);

function withdrawTokens(address token, address to, uint256 amount) external onlyOwner {
    IERC20(token).transfer(to, amount);
@> emit TokensWithdrawn(to, token , amount);
}

```

## Risk

**Likelihood**:

* Withdrawal function is callable by owner and will emit this incorrect event on every withdraw call.

*  

  Any off-chain consumer (indexer, analytics, explorers) that assumes the declared order will consistently misinterpret logs.

**Impact**:

* No on-chain funds or contract state are altered incorrectly by the emit itself.

*  

  Off-chain monitoring, accounting, and forensic tools will show swapped fields, potentially causing misreporting or investigations.

## Proof of Concept

The event arguments are emitted in the wrong order

```Solidity
// On withdrawTokens(tokenAddr, recipient, 1 ether)
emit TokensWithdrawn(to, token , amount);
// Indexer will record:
// token  => recipient   (incorrect)
// to     => token       (incorrect)

```

## Recommended Mitigation

Change the order of even arguments.

```Solidity
// Root cause in the codebase with @> marks to highlight the relevant section
event TokensWithdrawn(address indexed token, address indexed to, uint256 amount);

function withdrawTokens(address token, address to, uint256 amount) external onlyOwner {
    IERC20(token).transfer(to, amount);
@> emit TokensWithdrawn(to, token , amount);
}
```

```diff
 function withdrawTokens(address token, address to, uint256 amount) external onlyOwner {
     IERC20(token).transfer(to, amount);
-    emit TokensWithdrawn(to, token , amount);
+    emit TokensWithdrawn(token, to, amount);
 }

```



