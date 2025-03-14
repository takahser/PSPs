# PSP-22 Fungible Token Standard in WASM

- **PSP Number:** 22
- **Authors:** Green Baneling <green.baneling@supercolony.net>, Markian <markian@supercolony.net>, Pierre <pierre.ossun@supercolony.net>, Sven <sven.seven@supercolony.net>, Varg <varg.vikernes@supercolony.net>
- **Status:** Call For Feedback
- **Created:** 2021-06-19
- **Reference Implementation:** [OpenBrush](https://github.com/Supercolony-net/openbrush-contracts/blob/main/contracts/token/psp20/traits.rs)


## Summary

A standard fungible token interface for WASM contracts.

This proposal aims to define the standard fungible token in WASM smart contracts, just like [EIP-20](https://github.com/ethereum/EIPs/edit/master/EIPS/eip-20.md) for Ethereum ecosystem.

## Importance
Currently, while there is no standard, every contract will have different signature. Thus, no interoperability is possible. This proposal aims to resolve that by having one **interface** that shares the
same **ABI** between all implementations.

## Implementation
Example of ink! implementation:

- [OpenBrush](https://github.com/Supercolony-net/openbrush-contracts/blob/main/contracts/token/psp20/traits.rs)

## Motivation
A standard interface allows any tokens on Polkadot/Kusama to be re-used by other applications: from wallets to decentralized exchanges.


## Motivation for having a standard separate from ERC20
Due to different nature of WASM smart contracts and the difference between EVM and `pallet-contract` in substrate, the standard should have specific rules and methods,
therefore PSP-22 differs from ERC-20 in its implementation.

Also, this standard proposal defines an extensive method list in the interface. Unlike ERC20, it includes `increase_allowance` & `decrease_allowance`, and defines metadata fields as part of a separate interface.
Another difference is that it has `PSP22Receiver` interface, and `before_received` method is called at the end of transfer if the recipient is a contract.

# This standard is at ABI level

As `pallet-contract` in Substrate can execute any WASM contracts, we should not restrain this standard to only Rust & ink! Framework, so that it can be used by any language/framework that compile to WASM.

## Specification
1. [Interface](#Interface)
2. [Events](#Events)
3. [Types](#Types)
4. [Errors](#Errors)

### Interface

#### PSP22 is an interface of Fungible Token Standard

##### **total_supply**() -> Balance
Selector: `0x162df8c2` - first 4 bytes of `blake2b_256("PSP22::total_supply")`
```json
{
  "args": [],
  "docs": [
    " Returns the total token supply."
  ],
  "mutates": false,
  "name": [
    "PSP22",
    "total_supply"
  ],
  "returnType": {
    "displayName": [
      "Balance"
    ],
    "type": "Balance"
  },
  "selector": "0x162df8c2"
}
```

##### **balance_of**(owner: AccountId) -> Balance
Selector: `0x6568382f` - first 4 bytes of `blake2b_256("PSP22::balance_of")`
```json
{
  "args": [
    {
      "name": "owner",
      "type": {
        "displayName": [
          "AccountId"
        ],
        "type": "AccountId"
      }
    }
  ],
  "docs": [
    " Returns the account Balance for the specified `owner`.",
    "",
    " Returns `0` if the account is non-existent."
  ],
  "mutates": false,
  "name": [
    "PSP22",
    "balance_of"
  ],
  "returnType": {
    "displayName": [
      "Balance"
    ],
    "type": "Balance"
  },
  "selector": "0x6568382f"
}
```

##### **allowance**(owner: AccountId, spender: AccountId) -> Balance
Selector: `0x4d47d921` - first 4 bytes of `blake2b_256("PSP22::allowance")`
```json
{
  "args": [
    {
      "name": "owner",
      "type": {
        "displayName": [
          "AccountId"
        ],
        "type": "AccountId"
      }
    },
    {
      "name": "spender",
      "type": {
        "displayName": [
          "AccountId"
        ],
        "type": "AccountId"
      }
    }
  ],
  "docs": [
    " Returns the amount which `spender` is still allowed to withdraw from `owner`.",
    "",
    " Returns `0` if no allowance has been set `0`."
  ],
  "mutates": false,
  "name": [
    "PSP22",
    "allowance"
  ],
  "returnType": {
    "displayName": [
      "Balance"
    ],
    "type": "Balance"
  },
  "selector": "0x4d47d921"
}
```

##### **transfer**(to: AccountId, value: Balance, data: [u8]) -> Result<(), PSP22Error>
Selector: `0xdb20f9f5` - first 4 bytes of `blake2b_256("PSP22::transfer")`
```json
{
  "args": [
    {
      "name": "to",
      "type": {
        "displayName": [
          "AccountId"
        ],
        "type": "AccountId"
      }
    },
    {
      "name": "value",
      "type": {
        "displayName": [
          "Balance"
        ],
        "type": "Balance"
      }
    },
    {
      "name": "data",
      "type": {
        "displayName": [
          "[u8]"
        ],
        "type": "[u8]"
      }
    }
  ],
  "docs": [
    " Transfers `value` amount of tokens from the caller's account to account `to`",
    " with additional `data` in unspecified format.",
    "",
    " On success a `Transfer` event is emitted.",
    "",
    " # Errors",
    "",
    " Reverts with error `InsufficientBalance` if there are not enough tokens on",
    " the caller's account Balance.",
    "",
    " Reverts with error `ZeroSenderAddress` if sender's address is zero.",
    "",
    " Reverts with error `ZeroRecipientAddress` if recipient's address is zero."
  ],
  "mutates": true,
  "name": [
    "PSP22",
    "transfer"
  ],
  "returnType": {
    "displayName": [
      "Result"
    ],
    "type": 1
  },
  "selector": "0xdb20f9f5"
}
```

##### **transfer_from**(from: AccountId, to: AccountId, value: Balance, data: [u8]) -> Result<(), PSP22Error>
Selector: `0x54b3c76e` - first 4 bytes of `blake2b_256("PSP22::transfer_from")`
```json
{
  "args": [
    {
      "name": "from",
      "type": {
        "displayName": [
          "AccountId"
        ],
        "type": "AccountId"
      }
    },
    {
      "name": "to",
      "type": {
        "displayName": [
          "AccountId"
        ],
        "type": "AccountId"
      }
    },
    {
      "name": "value",
      "type": {
        "displayName": [
          "Balance"
        ],
        "type": "Balance"
      }
    },
    {
      "name": "data",
      "type": {
        "displayName": [
          "[u8]"
        ],
        "type": "[u8]"
      }
    }
  ],
  "docs": [
    " Transfers `value` tokens on the behalf of `from` to the account `to`",
    " with additional `data` in unspecified format.",
    "",
    " This can be used to allow a contract to transfer tokens on ones behalf and/or",
    " to charge fees in sub-currencies, for example.",
    "",
    " On success a `Transfer` and `Approval` events are emitted.",
    "",
    " # Errors",
    "",
    " Reverts with error `InsufficientAllowance` if there are not enough tokens allowed",
    " for the caller to withdraw from `from`.",
    "",
    " Reverts with error `InsufficientBalance` if there are not enough tokens on",
    " the the account Balance of `from`.",
    "",
    " Reverts with error `ZeroSenderAddress` if sender's address is zero.",
    "",
    " Reverts with error `ZeroRecipientAddress` if recipient's address is zero."
  ],
  "mutates": true,
  "name": [
    "PSP22",
    "transfer_from"
  ],
  "returnType": {
    "displayName": [
      "Result"
    ],
    "type": 1
  },
  "selector": "0x54b3c76e"
}
```

##### **approve**(spender: AccountId, value: Balance) -> Result<(), PSP22Error>
Selector: `0xb20f1bbd` - first 4 bytes of `blake2b_256("PSP22::approve")`
```json
{
  "args": [
    {
      "name": "spender",
      "type": {
        "displayName": [
          "AccountId"
        ],
        "type": "AccountId"
      }
    },
    {
      "name": "value",
      "type": {
        "displayName": [
          "Balance"
        ],
        "type": "Balance"
      }
    }
  ],
  "docs": [
    " Allows `spender` to withdraw from the caller's account multiple times, up to",
    " the `value` amount.",
    "",
    " If this function is called again it overwrites the current allowance with `value`.",
    "",
    " An `Approval` event is emitted.",
    "",
    " # Errors",
    "",
    " Reverts with error `ZeroSenderAddress` if sender's address is zero.",
    "",
    " Reverts with error `ZeroRecipientAddress` if recipient's address is zero."
  ],
  "mutates": true,
  "name": [
    "PSP22",
    "approve"
  ],
  "returnType": {
    "displayName": [
      "Result"
    ],
    "type": 1
  },
  "selector": "0xb20f1bbd"
}

```

##### **increase_allowance**(spender: AccountId, delta_value: Balance) -> Result<(), PSP22Error>
Selector: `0x96d6b57a` - first 4 bytes of `blake2b_256("PSP22::increase_allowance")`
```json
{
  "args": [
    {
      "name": "spender",
      "type": {
        "displayName": [
          "AccountId"
        ],
        "type": "AccountId"
      }
    },
    {
      "name": "delta_value",
      "type": {
        "displayName": [
          "Balance"
        ],
        "type": "Balance"
      }
    }
  ],
  "docs": [
    " Atomically increases the allowance granted to `spender` by the caller.",
    "",
    " An `Approval` event is emitted.",
    "",
    " # Errors",
    "",
    " Reverts with error `ZeroSenderAddress` if sender's address is zero.",
    "",
    " Reverts with error `ZeroRecipientAddress` if recipient's address is zero."
  ],
  "mutates": true,
  "name": [
    "PSP22",
    "increase_allowance"
  ],
  "returnType": {
    "displayName": [
      "Result"
    ],
    "type": 1
  },
  "selector": "0x96d6b57a"
}
```

##### **decrease_allowance**(spender: AccountId, delta_value: Balance) -> Result<(), PSP22Error>
Selector: `0xfecb57d5` - first 4 bytes of `blake2b_256("PSP22::decrease_allowance")`
```json
{
  "args": [
    {
      "name": "spender",
      "type": {
        "displayName": [
          "AccountId"
        ],
        "type": "AccountId"
      }
    },
    {
      "name": "delta_value",
      "type": {
        "displayName": [
          "Balance"
        ],
        "type": "Balance"
      }
    }
  ],
  "docs": [
    " Atomically decreases the allowance granted to `spender` by the caller.",
    "",
    " An `Approval` event is emitted.",
    "",
    " # Errors",
    "",
    " Reverts with error `InsufficientAllowance` if there are not enough tokens allowed",
    " by owner for `spender`.",
    "",
    " Reverts with error `ZeroSenderAddress` if sender's address is zero.",
    "",
    " Reverts with error `ZeroRecipientAddress` if recipient's address is zero."
  ],
  "mutates": true,
  "name": [
    "PSP22",
    "decrease_allowance"
  ],
  "returnType": {
    "displayName": [
      "Result"
    ],
    "type": 1
  },
  "selector": "0xfecb57d5"
}
```

#### PSP22Metadata 
PSP22Metadata is an optional interface of metadata for Fungible Token Standard

##### **token_name**() -> Option<String>
Selector: `0x3d261bd4` - first 4 bytes of `blake2b_256("PSP22Metadata::token_name")`
```json
{
  "args": [],
  "docs": [
    " Returns the token name."
  ],
  "mutates": false,
  "name": [
    "PSP22Metadata",
    "token_name"
  ],
  "returnType": {
    "displayName": [
      "Option"
    ],
    "type": "Option<string>"
  },
  "selector": "0x3d261bd4"
}
```

##### **token_symbol**() -> Option<String>
Selector: `0x34205be5` - first 4 bytes of `blake2b_256("PSP22Metadata::token_symbol")`
```json
{
  "args": [],
  "docs": [
    " Returns the token symbol."
  ],
  "mutates": false,
  "name": [
    "PSP22Metadata",
    "token_symbol"
  ],
  "returnType": {
    "displayName": [
      "Option"
    ],
    "type": "Option<string>"
  },
  "selector": "0x34205be5"
}
```

##### **token_decimals**() -> u8
Selector: `0x7271b782` - first 4 bytes of `blake2b_256("PSP22Metadata::token_decimals")`
```json
{
  "args": [],
  "docs": [
    " Returns the token decimals."
  ],
  "mutates": false,
  "name": [
    "PSP22Metadata",
    "token_decimals"
  ],
  "returnType": {
    "displayName": [
      "u8"
    ],
    "type": "u8"
  },
  "selector": "0x7271b782"
}
```

#### PSP22Receiver
PSP22Receiver is an interface for any contract that wants to support safe transfers from PSP22 token smart contracts to avoid unexpected tokens on balance of contract.
This method is called before transfer to ensure the recipient of the tokens acknowledges receipt.

##### **before_received**(&mut self, operator: AccountId, from: AccountId, value: Balance, data: [u8]) -> Result<(), PSP22ReceiverError>
Selector: `0xfda6f1a9` - first 4 bytes of `blake2b_256("PSP22Receiver::before_received")`
```json
{
  "args": [
    {
      "name": "operator",
      "type": {
        "displayName": [
          "AccountId"
        ],
        "type": "AccountId"
      }
    },
    {
      "name": "from",
      "type": {
        "displayName": [
          "AccountId"
        ],
        "type": "AccountId"
      }
    },
    {
      "name": "value",
      "type": {
        "displayName": [
          "Balance"
        ],
        "type": "Balance"
      }
    },
    {
      "name": "data",
      "type": {
        "displayName": [
          "[u8]"
        ],
        "type": "[u8]"
      }
    }
  ],
  "docs": [
    " Ensures that the smart contract allows reception of PSP22 token(s).",
    " Returns `Ok(())` if the contract allows the reception of the token(s) and Error `TransferRejected(String))` otherwise.",
    "",
    " This method will get called on every transfer to check whether the recipient in `transfer` is a contract, and if it is,",
    " does it accept tokens. This is done to prevent contracts from locking tokens forever.",
    "",
    " This method does not throw. Returns `PSP22ReceiverError` if the contract does not accept the tokens."
  ],
  "mutates": true,
  "name": [
    "PSP22Receiver",
    "before_received"
  ],
  "returnType": {
    "displayName": [
      "Result"
    ],
    "type": 2
  },
  "selector": "0xfda6f1a9"
}
```

### Events

##### Transfer 
When a contract creates (mints) new tokens, `from` will be `None`
When a contract deletes (burns) tokens, `to` will be `None`
```json
{
  "args": [
    {
      "docs": [],
      "indexed": true,
      "name": "from",
      "type": {
        "displayName": [
          "Option"
        ],
        "type": "Option<AccountId>"
      }
    },
    {
      "docs": [],
      "indexed": true,
      "name": "to",
      "type": {
        "displayName": [
          "Option"
        ],
        "type": "Option<AccountId>"
      }
    },
    {
      "docs": [],
      "indexed": false,
      "name": "value",
      "type": {
        "displayName": [
          "Balance"
        ],
        "type": "Balance"
      }
    }
  ],
  "docs": [
    " Event emitted when a token transfer occurs."
  ],
  "name": "Transfer"
}
```

#### Approval
```json
{
  "args": [
    {
      "docs": [],
      "indexed": true,
      "name": "owner",
      "type": {
        "displayName": [
          "AccountId"
        ],
        "type": "AccountId"
      }
    },
    {
      "docs": [],
      "indexed": true,
      "name": "spender",
      "type": {
        "displayName": [
          "AccountId"
        ],
        "type": "AccountId"
      }
    },
    {
      "docs": [],
      "indexed": false,
      "name": "value",
      "type": {
        "displayName": [
          "Balance"
        ],
        "type": "Balance"
      }
    }
  ],
  "docs": [
    " Event emitted when an approval occurs that `spender` is allowed to withdraw",
    " up to the amount of `value` tokens from `owner`."
  ],
  "name": "Approval"
}
```

### Types
```rust
// AccountId is 32 bytes array, like in substrate-based blockchains.
type AccountId = [u8; 32];
// u128 must be enough to cover most of the use cases of standard token.
type Balance = u128;
```
#### Return types
```json
{
  "types": {
    "1": {
      "def": {
        "variant": {
          "variants": [
            {
              "fields": [
                {
                  "type": {
                    "def": {
                      "tuple": []
                    }
                  }
                }
              ],
              "name": "Ok"
            },
            {
              "fields": [
                {
                  "type": {
                    "def": {
                      "variant": {
                        "variants": [
                          {
                            "fields": [
                              {
                                "type": "string"
                              }
                            ],
                            "name": "Custom"
                          },
                          {
                            "name": "InsufficientBalance"
                          },
                          {
                            "name": "InsufficientAllowance"
                          },
                          {
                            "name": "ZeroRecipientAddress"
                          },
                          {
                            "name": "ZeroSenderAddress"
                          },
                          {
                            "fields": [
                              {
                                "type": "string"
                              }
                            ],
                            "name": "SafeTransferCheckFailed"
                          }
                        ]
                      }
                    },
                    "path": [
                      "PSP22Error"
                    ]
                  }
                }
              ],
              "name": "Err"
            }
          ]
        }
      }
    },
    "2": {
      "def": {
        "variant": {
          "variants": [
            {
              "fields": [
                {
                  "type": {
                    "def": {
                      "tuple": []
                    }
                  }
                }
              ],
              "name": "Ok"
            },
            {
              "fields": [
                {
                  "type": {
                    "def": {
                      "variant": {
                        "variants": [
                          {
                            "fields": [
                              {
                                "type": "string"
                              }
                            ],
                            "name": "TransferRejected"
                          }
                        ]
                      }
                    },
                    "path": [
                      "PSP22ReceiverError"
                    ]
                  }
                }
              ],
              "name": "Err"
            }
          ]
        }
      }
    }
  }
}
```

### Errors
Suggested methods revert the transaction and return Result with Error from this list:

```rust
 /// PSP22Error 
 
 /// Custom error type for cases if writer of traits added own restrictions
 Custom(String),
 /// Returned if not enough balance to fulfill a request is available.
 InsufficientBalance,
 /// Returned if not enough allowance to fulfill a request is available.
 InsufficientAllowance,
 /// Returned if recipient's address is zero.
 ZeroRecipientAddress,
 /// Returned if sender's address is zero.
 ZeroSenderAddress,
 /// Returned if safe transfer check fails (see _do_safe_transfer_check() in PSP22 trait)
 SafeTransferCheckFailed(String),


 /// PSP22ReceiverError 
 
 /// Returned if a transfer is rejected.
 TransferRejected(String),
```

## Copyright

Each PSP must be labeled as placed in the
[public domain](https://creativecommons.org/publicdomain/zero/1.0/).
