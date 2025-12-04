# Slither Audit Report

## arbitrary-send-eth

**File / Line:** src/PuppyRaffle.sol:157

**Impact:** High

## Description

PuppyRaffle.withdrawFees() (src/PuppyRaffle.sol#157-163) sends eth to arbitrary user
	Dangerous calls:
	- (success,None) = feeAddress.call{value: feesToWithdraw}() (src/PuppyRaffle.sol#161)


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

## weak-prng

**File / Line:** src/PuppyRaffle.sol:125

**Impact:** High

## Description

PuppyRaffle.selectWinner() (src/PuppyRaffle.sol#125-154) uses a weak PRNG: "winnerIndex = uint256(keccak256(bytes)(abi.encodePacked(msg.sender,block.timestamp,block.difficulty))) % players.length (src/PuppyRaffle.sol#128-129)" 


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

## incorrect-equality

**File / Line:** src/PuppyRaffle.sol:157

**Impact:** Medium

## Description

PuppyRaffle.withdrawFees() (src/PuppyRaffle.sol#157-163) uses a dangerous strict equality:
	- require(bool,string)(address(this).balance == uint256(totalFees),PuppyRaffle: There are currently players active!) (src/PuppyRaffle.sol#158)


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

## reentrancy-no-eth

**File / Line:** src/PuppyRaffle.sol:96

**Impact:** Medium

## Description

Reentrancy in PuppyRaffle.refund(uint256) (src/PuppyRaffle.sol#96-105):
	External calls:
	- address(msg.sender).sendValue(entranceFee) (src/PuppyRaffle.sol#101)
	State variables written after the call(s):
	- players[playerIndex] = address(0) (src/PuppyRaffle.sol#103)
	PuppyRaffle.players (src/PuppyRaffle.sol#23) can be used in cross function reentrancies:
	- PuppyRaffle.enterRaffle(address[]) (src/PuppyRaffle.sol#79-92)
	- PuppyRaffle.getActivePlayerIndex(address) (src/PuppyRaffle.sol#110-117)
	- PuppyRaffle.players (src/PuppyRaffle.sol#23)
	- PuppyRaffle.refund(uint256) (src/PuppyRaffle.sol#96-105)
	- PuppyRaffle.selectWinner() (src/PuppyRaffle.sol#125-154)


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

## missing-zero-check

**File / Line:** src/PuppyRaffle.sol:167

**Impact:** Low

## Description

PuppyRaffle.changeFeeAddress(address).newFeeAddress (src/PuppyRaffle.sol#167) lacks a zero-check on :
		- feeAddress = newFeeAddress (src/PuppyRaffle.sol#168)


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

## missing-zero-check

**File / Line:** src/PuppyRaffle.sol:60

**Impact:** Low

## Description

PuppyRaffle.constructor(uint256,address,uint256)._feeAddress (src/PuppyRaffle.sol#60) lacks a zero-check on :
		- feeAddress = _feeAddress (src/PuppyRaffle.sol#62)


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

## reentrancy-events

**File / Line:** src/PuppyRaffle.sol:125

**Impact:** Low

## Description

Reentrancy in PuppyRaffle.selectWinner() (src/PuppyRaffle.sol#125-154):
	External calls:
	- (success,None) = winner.call{value: prizePool}() (src/PuppyRaffle.sol#151)
	- _safeMint(winner,tokenId) (src/PuppyRaffle.sol#153)
		- returndata = to.functionCall(abi.encodeWithSelector(IERC721Receiver(to).onERC721Received.selector,_msgSender(),from,tokenId,_data),ERC721: transfer to non ERC721Receiver implementer) (lib/openzeppelin-contracts/contracts/token/ERC721/ERC721.sol#441-447)
		- (success,returndata) = target.call{value: value}(data) (lib/openzeppelin-contracts/contracts/utils/Address.sol#119)
	External calls sending eth:
	- (success,None) = winner.call{value: prizePool}() (src/PuppyRaffle.sol#151)
	- _safeMint(winner,tokenId) (src/PuppyRaffle.sol#153)
		- (success,returndata) = target.call{value: value}(data) (lib/openzeppelin-contracts/contracts/utils/Address.sol#119)
	Event emitted after the call(s):
	- Transfer(address(0),to,tokenId) (lib/openzeppelin-contracts/contracts/token/ERC721/ERC721.sol#343)
		- _safeMint(winner,tokenId) (src/PuppyRaffle.sol#153)


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

## reentrancy-events

**File / Line:** src/PuppyRaffle.sol:96

**Impact:** Low

## Description

Reentrancy in PuppyRaffle.refund(uint256) (src/PuppyRaffle.sol#96-105):
	External calls:
	- address(msg.sender).sendValue(entranceFee) (src/PuppyRaffle.sol#101)
	Event emitted after the call(s):
	- RaffleRefunded(playerAddress) (src/PuppyRaffle.sol#104)


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

## timestamp

**File / Line:** src/PuppyRaffle.sol:125

**Impact:** Low

## Description

PuppyRaffle.selectWinner() (src/PuppyRaffle.sol#125-154) uses timestamp for comparisons
	Dangerous comparisons:
	- require(bool,string)(block.timestamp >= raffleStartTime + raffleDuration,PuppyRaffle: Raffle not over) (src/PuppyRaffle.sol#126)


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

**File / Line:** lib/base64/base64.sol:3

**Impact:** Medium

## Description

4 different versions of Solidity are used:
	- Version constraint >=0.6.0 is used by:
		->=0.6.0 (lib/base64/base64.sol#3)
	- Version constraint >=0.6.0<0.8.0 is used by:
		->=0.6.0<0.8.0 (lib/openzeppelin-contracts/contracts/access/Ownable.sol#3)
		->=0.6.0<0.8.0 (lib/openzeppelin-contracts/contracts/introspection/ERC165.sol#3)
		->=0.6.0<0.8.0 (lib/openzeppelin-contracts/contracts/introspection/IERC165.sol#3)
		->=0.6.0<0.8.0 (lib/openzeppelin-contracts/contracts/math/SafeMath.sol#3)
		->=0.6.0<0.8.0 (lib/openzeppelin-contracts/contracts/token/ERC721/ERC721.sol#3)
		->=0.6.0<0.8.0 (lib/openzeppelin-contracts/contracts/token/ERC721/IERC721Receiver.sol#3)
		->=0.6.0<0.8.0 (lib/openzeppelin-contracts/contracts/utils/Context.sol#3)
		->=0.6.0<0.8.0 (lib/openzeppelin-contracts/contracts/utils/EnumerableMap.sol#3)
		->=0.6.0<0.8.0 (lib/openzeppelin-contracts/contracts/utils/EnumerableSet.sol#3)
		->=0.6.0<0.8.0 (lib/openzeppelin-contracts/contracts/utils/Strings.sol#3)
	- Version constraint >=0.6.2<0.8.0 is used by:
		->=0.6.2<0.8.0 (lib/openzeppelin-contracts/contracts/token/ERC721/IERC721.sol#3)
		->=0.6.2<0.8.0 (lib/openzeppelin-contracts/contracts/token/ERC721/IERC721Enumerable.sol#3)
		->=0.6.2<0.8.0 (lib/openzeppelin-contracts/contracts/token/ERC721/IERC721Metadata.sol#3)
		->=0.6.2<0.8.0 (lib/openzeppelin-contracts/contracts/utils/Address.sol#3)
	- Version constraint ^0.7.6 is used by:
		-^0.7.6 (src/PuppyRaffle.sol#2)


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

**File / Line:** src/PuppyRaffle.sol:173

**Impact:** Medium

## Description

PuppyRaffle._isActivePlayer() (src/PuppyRaffle.sol#173-180) is never used and should be removed


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

**File / Line:** Unknown

**Impact:** Medium

## Description

solc-0.7.6 is an outdated solc version. Use a more recent version (at least 0.8.0), if possible.


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

**File / Line:** src/PuppyRaffle.sol:2

**Impact:** Medium

## Description

Version constraint ^0.7.6 contains known severe issues (https://solidity.readthedocs.io/en/latest/bugs.html)
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
	- ^0.7.6 (src/PuppyRaffle.sol#2)


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

## low-level-calls

**File / Line:** src/PuppyRaffle.sol:125

**Impact:** Medium

## Description

Low level call in PuppyRaffle.selectWinner() (src/PuppyRaffle.sol#125-154):
	- (success,None) = winner.call{value: prizePool}() (src/PuppyRaffle.sol#151)


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

## low-level-calls

**File / Line:** src/PuppyRaffle.sol:157

**Impact:** Medium

## Description

Low level call in PuppyRaffle.withdrawFees() (src/PuppyRaffle.sol#157-163):
	- (success,None) = feeAddress.call{value: feesToWithdraw}() (src/PuppyRaffle.sol#161)


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

## cache-array-length

**File / Line:** src/PuppyRaffle.sol:111

**Impact:** Medium

## Description

Loop condition i < players.length (src/PuppyRaffle.sol#111) should use cached array length instead of referencing `length` member of the storage array.
 

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

## cache-array-length

**File / Line:** src/PuppyRaffle.sol:174

**Impact:** Medium

## Description

Loop condition i < players.length (src/PuppyRaffle.sol#174) should use cached array length instead of referencing `length` member of the storage array.
 

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

## cache-array-length

**File / Line:** src/PuppyRaffle.sol:87

**Impact:** Medium

## Description

Loop condition j < players.length (src/PuppyRaffle.sol#87) should use cached array length instead of referencing `length` member of the storage array.
 

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

**File / Line:** src/PuppyRaffle.sol:38

**Impact:** Medium

## Description

PuppyRaffle.commonImageUri (src/PuppyRaffle.sol#38) should be constant 


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

**File / Line:** src/PuppyRaffle.sol:48

**Impact:** Medium

## Description

PuppyRaffle.legendaryImageUri (src/PuppyRaffle.sol#48) should be constant 


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

**File / Line:** src/PuppyRaffle.sol:43

**Impact:** Medium

## Description

PuppyRaffle.rareImageUri (src/PuppyRaffle.sol#43) should be constant 


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

## immutable-states

**File / Line:** src/PuppyRaffle.sol:24

**Impact:** Medium

## Description

PuppyRaffle.raffleDuration (src/PuppyRaffle.sol#24) should be immutable 


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

