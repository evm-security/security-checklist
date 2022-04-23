# Security Pre-Audit Checklist

## Checklist
### Core Checks
These are basic checks to undertake with any contract. Checklist now includes features included in `0.8.13`. Consider hiring a professional organization for a detailed audit. Forked from [Cryptofin's Audit list](https://github.com/cryptofinlabs/audit-checklist) with modifications. Credits to [SWC](https://swcregistry.io/). Written by [abhinavmir](https://github.com/abhinavmir).

- [ ] Function Visibility
  - [ ] Ensure that all relevant functions are marked with the correct visibility
- [ ] Fix compiler warnings
- [ ] Fix linter warnings (Example set of rules can be found [here](https://github.com/protofire/solhint/blob/master/docs/rules.md#:~:text=Style%20Guide%20Rules,-Rule%20Id&text=Values%20must%20be%20'single'%20or%20'double'.&text=Constant%20name%20must%20be%20in%20capitalized%20SNAKE_CASE.&text=Contract%20name%20must%20be%20in%20CamelCase.&text=Event%20name%20must%20be%20in%20CamelCase.))
- [ ] Avoid using problematic features - If you must, be aware of their many nuances
  - [ ] send ([nuances](https://ethereum.stackexchange.com/a/38642/3118))
  - [ ] low level functions (`call`, `delegatecall`, `callcode`, inline assembly)
  - [ ] var
  - [ ] avoid arbitrary jumps ([ref.](https://swcregistry.io/docs/SWC-127))
- [ ] Explicitly state visibilities of state variables
- [ ] Remove unencrypted private on-chain data
- [ ] External Calls - Every external contract call is a risk
  - [ ] Avoid using data parameters on sub-calls with no assert checks ([ref.](https://swcregistry.io/docs/SWC-126))
     - `delegatecall` can, for example, overwrite parent smart contract via malicious calls. Implement strong assert checks, remember even then, `delegatecall` can override `msg.value` and `msg.sender` checks.
  - [ ] Check for [Re-entrancy](https://dasp.co/#item-1) - and ensure state committed before external call 
      - [Callstack depth](https://solidity.readthedocs.io/en/v0.4.24/security-considerations.html?highlight=callstack#callstack-depth) can cause this as well
- [ ] Avoid `SELFDESTRUCT`
  - [ ] If it must be used, apply strong protection via assert checks
- [ ] Dependencies
  - [ ] Use audited and trustworthy dependencies
  - [ ] Ensure code minimization by using trusted libraries
- [ ] Time Manipulation - Timestamps can theoretically be manipulated by malicious miners by up to a few minutes
  - [ ] Ensure important mechanisms aren't overly sensitive to timestamps
- [ ] Rounding Errors
  - [ ] Check that truncation doesn't produce unexpected behavior (eg. incorrect results, locked funds)
- [ ] Randomness
  - [ ] Don't rely on pseudo-randomness for important mechanisms (eg. keccak with a deterministic seed like blockhash, blocknumber, etc.)
  - [ ] On-chain randomness can be predicted and misused - consider using a service such as [Chainlink VRF](https://docs.chain.link/docs/chainlink-vrf/).
- [ ] Validate inputs of external/public functions
  - [ ] Ensure requires to bound and check presence of arguments
  - [ ] Any functions to interact with token must implement a form of assert check
- [ ] Prevent unbounded loops
- [ ] Implement circuit breakers in case of emergency
- [ ] Avoid failed call DoS
  - [ ] Favour pull over push payments (Such as claim airdrop over iterating airdrop to stored wallets) - more [here](https://swcregistry.io/docs/SWC-128) and [here](https://fravoll.github.io/solidity-patterns/pull_over_push.html).
- [ ] Don't use `tx.origin` as an authentication mechanism, always use `msg.sender` instead ([ref](https://swcregistry.io/docs/SWC-117).
- [ ] Verify changes in the most recent Solidity version - ([ref](https://docs.soliditylang.org/en/v0.8.8/080-breaking-changes.html)).
- [ ] Prevent overflow and underflow 
  - [ ] Use [SafeMath](https://github.com/OpenZeppelin/openzeppelin-solidity/blob/master/contracts/math/SafeMath.sol). Note: Situation dependent - [nuance](https://ethereum.stackexchange.com/questions/91367/is-the-safemath-library-obsolete-in-solidity-0-8-0)
- [ ] Change message call with hardcoded gas amount - [ref](https://swcregistry.io/docs/SWC-134)
- [ ] Do not assume specific Ether balances - [ref](https://swcregistry.io/docs/SWC-132)
- [ ] Implement inheritence from general to specific ([ref](https://swcregistry.io/docs/SWC-125))
- [ ] Remove transaction order dependence - will result in eventual front-running ref. Murphy's law
- [ ] Implement external checks to message calls - As such: `require(user.call())`
- [ ] Use fixed pragma statements (❌ Eg. Avoid `pragma solidity >=0.4.0 <0.6.0;`) 
- [ ] Implement strong signature verification - ideally without alterations to accepted methods
  - [ ] Verify cryptographic signatures to avoid proxy signatures
  - [ ] Avoid signature malleability 
- [ ] Reduce time dependence, if needed: Use oracles over `block` keyword - Malicious actors can alter block details

### Testing and Software Engineering
- [ ] Test Coverage
  - [ ] Have 100% branch test coverage
- [ ] Unit Tests
  - [ ] Cover all critical edge cases with unit tests
  - [ ] Integrate fuzzing (tool examples: Echidna, Foundry etc)
- [ ] Integration Tests
  - [ ] Have extensive integration tests
- [ ] Code Freeze
  - [ ] Try not to deploy code written under manegerial pressure
- [ ] Remove irrelevant and redundant code

### Auditing
Auditing helps catch many bugs, but shouldn’t also be seen as a magic bullet. Your system still needs to handle failure gracefully.

- [ ] Audits
  - [ ] Have code audited by (preferably) multiple external parties (in series)
- [ ] Time Management
  - [ ] Allocate comfortable time after the audit to address issues
- [ ] Consider open source auditing such as [Code4rena](https://code4rena.com/) and [ImmuneFi](https://immunefi.com/)

<hr>
