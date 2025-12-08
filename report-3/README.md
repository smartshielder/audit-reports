# Audit 3 — [Token 0x](https://github.com/CodeHawks-Contests/2025-12-token-0x)

# 1. Unspecific Solidity Pragma
**Severity:** Low

**Audit:** `Static tools`

## Description
Normal behavior: Solidity contracts should pin a compiler version to avoid accidental behavior changes in future minor releases.

Issue: The contract uses `pragma solidity ^0.8.24;` allowing any future 0.8.x version, which may modify behaviors relevant to inline assembly.

### Relevant Code
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.24;
```

## Risk
**Likelihood:**
- Inline assembly behavior may differ between minor compiler versions.
- Future Solidity versions may introduce breaking optimizer changes.

**Impact:**
- Silent behavior changes in assembly-based functions.
- Hard-to-debug compatibility issues across environments.

## Proof of Concept (PoC)
Compile with `0.8.24` then with a later 0.8.x version; compare bytecode and runtime behavior of inline assembly functions.

## Recommended Mitigation
```diff
- pragma solidity ^0.8.24;
+ pragma solidity 0.8.24;
```

---

# 2. Non-Standard Internal Function Naming (totalSupply_)
**Severity:** Informational / Low

**Audit:** `Static tools`

## Description
Normal ERC20 implementations use `_totalSupply` as variable and `totalSupply()` as the public getter. This project introduces a non-standard function:

```solidity
function totalSupply_() internal view returns (uint256) {
    assembly {
        let slot := _totalSupply.slot
        let supply := sload(slot)
        mstore(0x00, supply)
        return(0x00, 0x20)
    }
}
```

This may confuse maintainers and automated tools.

## Risk
**Likelihood:**
- Developers may call the wrong function when extending the contract.
- Static analysis tools may fail to recognize this pattern.

**Impact:**
- Confusion or incorrect usage of internal function.
- Increased maintenance cost.

## Proof of Concept (PoC)
Call `totalSupply_()` directly and compare with `totalSupply()`. Naming may mislead external reviewers or automation.

## Recommended Mitigation
Rename for clarity and memory safety:

```diff
- function totalSupply_() internal view returns (uint256) {
+ function _totalSupply_() internal view returns (uint256) {
     assembly {
-        mstore(0x00, supply)
-        return(0x00, 0x20)
+        let ptr := mload(0x40)
+        mstore(ptr, supply)
+        return(ptr, 0x20)
     }
 }
```

---

# 3. ERC20Internals Should Be Abstract
**Severity:** Low

**Audit:** `Static tools`

## Description
Contracts intended only for inheritance should be marked `abstract`. `ERC20Internals` is not marked abstract, allowing accidental deployment.

### Relevant Code
```solidity
contract ERC20Internals {
    mapping(address account => uint256) internal _balances;
    ...
}
```

## Risk
**Likelihood:**
- Accidental deployment may occur during testing or script execution.
- Developers may include base contracts in deployment artifacts.

**Impact:**
- Wasted gas on unused contract.
- Confusion in audits or deployment pipelines.

## Proof of Concept
Deploy `ERC20Internals` directly on-chain; contract has no usable interface but is deployed.

## Recommended Mitigation
```diff
- contract ERC20Internals {
+ abstract contract ERC20Internals {
```

---

# 4. ERC20 Contract Should Be Abstract or Provide Minting
**Severity:** Medium

**Audit:** `Static tools`

## Description
Deployable ERC20 contracts must be able to mint tokens. Current `ERC20` has `_mint` internal but no public/exposed minting. Without constructor minting or abstract designation, deployment yields a zero supply token.

### Relevant Code
```solidity
contract ERC20 is IERC20Errors, ERC20Internals {
    constructor(string memory name_, string memory symbol_) {
        _name = name_;
        _symbol = symbol_;
    }
    ...
}
```

## Risk
**Likelihood:**
- Developers may deploy thinking tokens exist.
- Template reuse in other projects without adding minting.

**Impact:**
- Contract has zero supply, unusable token.
- Application-level failures in dependent systems.

## Proof of Concept (PoC)
Deploy `ERC20` as-is; call `totalSupply()`. Returns 0; no public method to mint tokens exists.

## Recommended Mitigation
Option A: mark abstract

```diff
- contract ERC20 is IERC20Errors, ERC20Internals {
+ abstract contract ERC20 is IERC20Errors, ERC20Internals {
```
Option B: constructor mint example

```diff
+        _mint(msg.sender, 1_000_000 * 10**decimals());
```

---

# 5. Arithmetic Overflow/Underflow Risk in Inline Assembly
**Severity:** High

**Audit:** `Manual review`

## Description
Solidity ^0.8.x checks arithmetic overflows automatically. Inline assembly operations (`add`, `sub`) bypass this check. Functions like `_mint`, `_burn`, `_transfer` may overflow/underflow silently.

### Relevant Functions
```solidity
function _mint(address account, uint256 value) internal {
    assembly ("memory-safe") {
        if iszero(account) { revert(0, 0x24) }
        let balanceSlot := _balances.slot
        let supplySlot := _totalSupply.slot
        let supply := sload(supplySlot)
        sstore(supplySlot, add(supply, value))
        let ptr := mload(0x40)
        mstore(ptr, account)
        mstore(add(ptr, 0x20), balanceSlot)
        let accountBalanceSlot := keccak256(ptr, 0x40)
        let accountBalance := sload(accountBalanceSlot)
        sstore(accountBalanceSlot, add(accountBalance, value))
    }
}
```

## Risk
**Likelihood:**
- Large mint or transfer values may trigger overflow/underflow.
- External inputs not validated properly can trigger silent failure.

**Impact:**
- Corrupted token balances.
- Potential for unexpected behavior or loss of funds.

## Proof of Concept
Mint `type(uint256).max` tokens to any account using `_mint()` internally; total supply overflows silently in assembly arithmetic.

## Recommended Mitigation
Use Solidity-safe arithmetic or pre-check overflow:

```diff
+ require(supply + value >= supply, "Overflow");
+ require(accountBalance + value >= accountBalance, "Overflow");
```

```diff
- sstore(supplySlot, add(supply, value))
- sstore(accountBalanceSlot, add(accountBalance, value))
+ sstore(supplySlot, supply + value)
+ sstore(accountBalanceSlot, accountBalance + value)
```
# 6. ERC20 Does Not Inherit IERC20
**Severity:** Medium

**Audit:** `Manual review`

## Description
Normal ERC20 contracts implement the IERC20 interface to guarantee all standard functions and events are present. The current ERC20 inherits IERC20Errors but not IERC20.

### Relevant Code
```solidity
contract ERC20 is IERC20Errors, ERC20Internals { ... }
```

## Risk
**Likelihood:**
- Interfacing contracts or wallets expecting IERC20 interface may fail.
- Misinterpretation by external tooling.

**Impact:**
- Integration failures with DeFi protocols or wallets.
- Misreporting of balances or allowances.

## Proof of Concept
Try to cast ERC20 to IERC20 and call `transferFrom`; interface mismatch occurs.

## Recommended Mitigation
```diff
- contract ERC20 is IERC20Errors, ERC20Internals {
+ contract ERC20 is IERC20, IERC20Errors, ERC20Internals {
```

---

# 7. ERC20 _mint / _burn Missing Transfer Event
**Severity:** High

**Audit:** `Manual review`

## Description
ERC20 standard requires emitting Transfer events on `_mint` and `_burn`. Currently `_mint` and `_burn` do not emit Transfer events.

### Relevant Functions
```solidity
function _mint(address account, uint256 value) internal { ... }
function _burn(address account, uint256 value) internal { ... }
```

## Risk
**Likelihood:**
- Wallets and explorers rely on Transfer events for token tracking.

**Impact:**
- Incorrect balance reporting.
- DeFi integrations may fail.

## Proof of Concept
Mint or burn tokens internally; no Transfer event is emitted.

## Recommended Mitigation
```diff
+ log3(ptr, 0x20, 0xddf252ad..., address(0), account) // for _mint
+ log3(ptr, 0x20, 0xddf252ad..., account, address(0)) // for _burn
```

---

# 8. ERC20 log3 Memory Safety Issue
**Severity:** Medium

**Audit:** `Manual review`

## Description
Functions `_approve` and `_transfer` use `log3` with hardcoded memory pointer `0x00`, which may overwrite memory used elsewhere.

### Relevant Functions
```solidity
function _approve(...) internal returns (bool) {
    assembly {
        mstore(0x00, value)
        log3(0x00, 0x20, ApprovalSig, owner, spender)
    }
}

function _transfer(...) internal returns (bool) {
    assembly {
        mstore(ptr, value)
        log3(ptr, 0x20, TransferSig, from, to)
    }
}
```

## Risk
**Likelihood:**
- Memory used elsewhere in contract may be overwritten.
- Conflicts arise in contracts using multiple inline assembly operations.

**Impact:**
- Event logs may be corrupted.
- Potential data corruption in memory.

## Proof of Concept
Call `_approve` in a derived contract using assembly memory; observe memory overwrite.

## Recommended Mitigation
```diff
- mstore(0x00, value)
+ let eventPtr := mload(0x40)
+ mstore(eventPtr, value)
+ log3(eventPtr, 0x20, ApprovalSig, owner, spender)
```

---

# 9. _spendAllowance Ignores Max Allowance Optimization
**Severity:** Medium

**Audit:** `Manual review`

## Description
ERC20 normally allows `type(uint256).max` to bypass decrementing allowance. `_spendAllowance` always subtracts value, ignoring this optimization.

### Relevant Function
```solidity
function _spendAllowance(address owner, address spender, uint256 value) internal {
    assembly {
        let allowanceSlot := keccak256(...)
        let currentAllowance := sload(allowanceSlot)
        sstore(allowanceSlot, sub(currentAllowance, value))
    }
}
```

## Risk
**Likelihood:**
- High volume users always perform storage writes, even with max allowance.

**Impact:**
- Increased gas costs.
- Inefficient contract operation.

## Proof of Concept
Set allowance to `type(uint256).max` and call `transferFrom`; allowance still decremented.

## Recommended Mitigation
```diff
+ if currentAllowance != type(uint256).max { sstore(allowanceSlot, sub(currentAllowance, value)) }
```

---

# 10. _approve Emits Approval Event Even When Allowance Unchanged
**Severity:** Medium

**Audit:** `Manual review`

## Description
`_approve` emits Approval event even if the new allowance equals current allowance. This is unnecessary and may mislead off-chain listeners.

### Relevant Function
```solidity
function _approve(address owner, address spender, uint256 value) internal returns (bool) {
    assembly {
        sstore(allowanceSlot, value)
        log3(0x00, 0x20, ApprovalSig, owner, spender)
    }
}
```

## Risk
**Likelihood:**
- Frequent unnecessary Approval events if allowances are set repeatedly to the same value.

**Impact:**
- Noise in event logs.
- Misleading token analytics.

## Proof of Concept
Call `approve(spender, 100)` repeatedly; Approval events emitted even if allowance unchanged.

## Recommended Mitigation
```diff
+ if value != currentAllowance { sstore(allowanceSlot, value)
+   log3(ptr, 0x20, ApprovalSig, owner, spender) }
```
