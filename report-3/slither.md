# Slither Audit Report

## uninitialized-state

**File / Line:** src/helpers/ERC20Internals.sol:8

**Impact:** High

## Description

ERC20Internals._totalSupply (src/helpers/ERC20Internals.sol#8) is never initialized. It is used in:
	- ERC20Internals.totalSupply_() (src/helpers/ERC20Internals.sol#13-20)
	- ERC20Internals._mint(address,uint256) (src/helpers/ERC20Internals.sol#134-156)
	- ERC20Internals._burn(address,uint256) (src/helpers/ERC20Internals.sol#158-180)


## Risk

- Likelihood: Medium
- Impact: Medium

## Proof of Concept

```solidity
// PoC not provided by Slither
```

## Recommended Mitigation

```diff
// Add proper fix here
```

---

## uninitialized-state

**File / Line:** src/helpers/ERC20Internals.sol:5

**Impact:** High

## Description

ERC20Internals._balances (src/helpers/ERC20Internals.sol#5) is never initialized. It is used in:
	- ERC20Internals._balanceOf(address) (src/helpers/ERC20Internals.sol#22-38)
	- ERC20Internals._transfer(address,address,uint256) (src/helpers/ERC20Internals.sol#92-132)
	- ERC20Internals._mint(address,uint256) (src/helpers/ERC20Internals.sol#134-156)
	- ERC20Internals._burn(address,uint256) (src/helpers/ERC20Internals.sol#158-180)


## Risk

- Likelihood: Medium
- Impact: Medium

## Proof of Concept

```solidity
// PoC not provided by Slither
```

## Recommended Mitigation

```diff
// Add proper fix here
```

---

## uninitialized-state

**File / Line:** src/helpers/ERC20Internals.sol:6

**Impact:** High

## Description

ERC20Internals._allowances (src/helpers/ERC20Internals.sol#6) is never initialized. It is used in:
	- ERC20Internals._approve(address,address,uint256) (src/helpers/ERC20Internals.sol#40-70)
	- ERC20Internals._allowance(address,address) (src/helpers/ERC20Internals.sol#72-90)
	- ERC20Internals._spendAllowance(address,address,uint256) (src/helpers/ERC20Internals.sol#182-203)


## Risk

- Likelihood: Medium
- Impact: Medium

## Proof of Concept

```solidity
// PoC not provided by Slither
```

## Recommended Mitigation

```diff
// Add proper fix here
```

---

## assembly

**File / Line:** src/helpers/ERC20Internals.sol:92

**Impact:** Medium

## Description

ERC20Internals._transfer(address,address,uint256) (src/helpers/ERC20Internals.sol#92-132) uses assembly
	- INLINE ASM (src/helpers/ERC20Internals.sol#93-131)


## Risk

- Likelihood: Medium
- Impact: Medium

## Proof of Concept

```solidity
// PoC not provided by Slither
```

## Recommended Mitigation

```diff
// Add proper fix here
```

---

## assembly

**File / Line:** src/helpers/ERC20Internals.sol:40

**Impact:** Medium

## Description

ERC20Internals._approve(address,address,uint256) (src/helpers/ERC20Internals.sol#40-70) uses assembly
	- INLINE ASM (src/helpers/ERC20Internals.sol#41-69)


## Risk

- Likelihood: Medium
- Impact: Medium

## Proof of Concept

```solidity
// PoC not provided by Slither
```

## Recommended Mitigation

```diff
// Add proper fix here
```

---

## assembly

**File / Line:** src/helpers/ERC20Internals.sol:13

**Impact:** Medium

## Description

ERC20Internals.totalSupply_() (src/helpers/ERC20Internals.sol#13-20) uses assembly
	- INLINE ASM (src/helpers/ERC20Internals.sol#14-19)


## Risk

- Likelihood: Medium
- Impact: Medium

## Proof of Concept

```solidity
// PoC not provided by Slither
```

## Recommended Mitigation

```diff
// Add proper fix here
```

---

## assembly

**File / Line:** src/helpers/ERC20Internals.sol:182

**Impact:** Medium

## Description

ERC20Internals._spendAllowance(address,address,uint256) (src/helpers/ERC20Internals.sol#182-203) uses assembly
	- INLINE ASM (src/helpers/ERC20Internals.sol#183-202)


## Risk

- Likelihood: Medium
- Impact: Medium

## Proof of Concept

```solidity
// PoC not provided by Slither
```

## Recommended Mitigation

```diff
// Add proper fix here
```

---

## assembly

**File / Line:** src/helpers/ERC20Internals.sol:158

**Impact:** Medium

## Description

ERC20Internals._burn(address,uint256) (src/helpers/ERC20Internals.sol#158-180) uses assembly
	- INLINE ASM (src/helpers/ERC20Internals.sol#159-179)


## Risk

- Likelihood: Medium
- Impact: Medium

## Proof of Concept

```solidity
// PoC not provided by Slither
```

## Recommended Mitigation

```diff
// Add proper fix here
```

---

## assembly

**File / Line:** src/helpers/ERC20Internals.sol:72

**Impact:** Medium

## Description

ERC20Internals._allowance(address,address) (src/helpers/ERC20Internals.sol#72-90) uses assembly
	- INLINE ASM (src/helpers/ERC20Internals.sol#73-89)


## Risk

- Likelihood: Medium
- Impact: Medium

## Proof of Concept

```solidity
// PoC not provided by Slither
```

## Recommended Mitigation

```diff
// Add proper fix here
```

---

## assembly

**File / Line:** src/helpers/ERC20Internals.sol:22

**Impact:** Medium

## Description

ERC20Internals._balanceOf(address) (src/helpers/ERC20Internals.sol#22-38) uses assembly
	- INLINE ASM (src/helpers/ERC20Internals.sol#23-37)


## Risk

- Likelihood: Medium
- Impact: Medium

## Proof of Concept

```solidity
// PoC not provided by Slither
```

## Recommended Mitigation

```diff
// Add proper fix here
```

---

## assembly

**File / Line:** src/helpers/ERC20Internals.sol:134

**Impact:** Medium

## Description

ERC20Internals._mint(address,uint256) (src/helpers/ERC20Internals.sol#134-156) uses assembly
	- INLINE ASM (src/helpers/ERC20Internals.sol#135-155)


## Risk

- Likelihood: Medium
- Impact: Medium

## Proof of Concept

```solidity
// PoC not provided by Slither
```

## Recommended Mitigation

```diff
// Add proper fix here
```

---

## pragma

**File / Line:** src/ERC20.sol:2

**Impact:** Medium

## Description

2 different versions of Solidity are used:
	- Version constraint ^0.8.24 is used by:
		-^0.8.24 (src/ERC20.sol#2)
		-^0.8.24 (src/helpers/ERC20Internals.sol#2)
		-^0.8.24 (src/helpers/IERC20Errors.sol#2)
	- Version constraint ^0.8.0 is used by:
		-^0.8.0 (src/IERC20.sol#3)


## Risk

- Likelihood: Medium
- Impact: Medium

## Proof of Concept

```solidity
// PoC not provided by Slither
```

## Recommended Mitigation

```diff
// Add proper fix here
```

---

## dead-code

**File / Line:** src/helpers/ERC20Internals.sol:158

**Impact:** Medium

## Description

ERC20Internals._burn(address,uint256) (src/helpers/ERC20Internals.sol#158-180) is never used and should be removed


## Risk

- Likelihood: Medium
- Impact: Medium

## Proof of Concept

```solidity
// PoC not provided by Slither
```

## Recommended Mitigation

```diff
// Add proper fix here
```

---

## dead-code

**File / Line:** src/helpers/ERC20Internals.sol:134

**Impact:** Medium

## Description

ERC20Internals._mint(address,uint256) (src/helpers/ERC20Internals.sol#134-156) is never used and should be removed


## Risk

- Likelihood: Medium
- Impact: Medium

## Proof of Concept

```solidity
// PoC not provided by Slither
```

## Recommended Mitigation

```diff
// Add proper fix here
```

---

## solc-version

**File / Line:** src/IERC20.sol:3

**Impact:** Medium

## Description

Version constraint ^0.8.0 contains known severe issues (https://solidity.readthedocs.io/en/latest/bugs.html)
	- FullInlinerNonExpressionSplitArgumentEvaluationOrder
	- MissingSideEffectsOnSelectorAccess
	- AbiReencodingHeadOverflowWithStaticArrayCleanup
	- DirtyBytesArrayToStorage
	- DataLocationChangeInInternalOverride
	- NestedCalldataArrayAbiReencodingSizeValidation
	- SignedImmutables
	- ABIDecodeTwoDimensionalArrayMemory
	- KeccakCaching.
It is used by:
	- ^0.8.0 (src/IERC20.sol#3)


## Risk

- Likelihood: Medium
- Impact: Medium

## Proof of Concept

```solidity
// PoC not provided by Slither
```

## Recommended Mitigation

```diff
// Add proper fix here
```

---

## missing-inheritance

**File / Line:** src/ERC20.sol:7

**Impact:** Medium

## Description

ERC20 (src/ERC20.sol#7-51) should inherit from IERC20 (src/IERC20.sol#8-24)


## Risk

- Likelihood: Medium
- Impact: Medium

## Proof of Concept

```solidity
// PoC not provided by Slither
```

## Recommended Mitigation

```diff
// Add proper fix here
```

---

## constable-states

**File / Line:** src/helpers/ERC20Internals.sol:8

**Impact:** Medium

## Description

ERC20Internals._totalSupply (src/helpers/ERC20Internals.sol#8) should be constant 


## Risk

- Likelihood: Medium
- Impact: Medium

## Proof of Concept

```solidity
// PoC not provided by Slither
```

## Recommended Mitigation

```diff
// Add proper fix here
```

---

