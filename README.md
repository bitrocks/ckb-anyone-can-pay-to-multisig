# ckb-anyone-can-pay-to-multisig

CKB anyone-can-pay multisig lock, forked from the [official anyone-can-pay repo](https://github.com/nervosnetwork/ckb-anyone-can-pay).

warn: this is an experimental work.

[RFC Draft](https://talk.nervos.org/t/rfc-anyone-can-pay-lock/4438)

## Build

``` sh
make all-via-docker && cargo test
```

## Quick start

### Create

1, create a cell to receive UDT and CKB:

```
Cell {
    lock: {
        code_hash: <any-one-can-pay>
        args: <secp256k1 multisig args>
    }
    data: <UDT amount>
    type: <UDT>
}

secp256k1 multisig args: S | R | M | N | PubKeyHash1 | PubKeyHash2 | ...
```

2, create a cell to receive only CKB:

```
Cell {
    lock: {
        code_hash: <any-one-can-pay>
        args: <secp256k1 multisig args>
    }
    data: <empty>
    type: <none>
}
```

3, we can add minimum amount transfer condition:

```
Cell {
    lock: {
        code_hash: <any-one-can-pay>
        args: <secp256k1 multisig args> | <minimum CKB> | <minimum UDT>
    }
    data: <UDT amount>
    type: <UDT>
}
```

`minimum CKB` and `minimum UDT` are two optional args, each occupied a byte, and represent `10 ^ x` minimal amount. The default value is `0` which means anyone can transfer any amount to the cell. A transfer must satisfy the `minimum CKB` **or** `minimum UDT`.

If the owner only wants to receive `UDT`, the owner can set `minimum CKB` to `255`.

### Send UDT and CKB

To transfer coins to an anyone-can-pay lock cell, the sender must build an output cell that has the same `lock_hash` and `type_hash` to the input anyone-can-pay lock cell; if the input anyone-can-pay cell has no `data`, the output cell must also be empty.

```
# inputs
Cell {
    lock: {
        code_hash: <any-one-can-pay>
        args: <secp256k1 mutisig args> | <minimum CKB: 2>
    }
    data: <empty>
    type: <none>
    capacity: 100
}
...

# outputs
Cell {
    lock: {
        code_hash: <any-one-can-pay>
        args: <secp256k1 mutisig args> | <minimum CKB: 2>
    }
    data: <empty>
    type: <none>
    capacity: 200
}
...
```

### Signature

The owner can provide a M-of-N threshold secp256k1 signature to unlock the cell, the signature method is the same as the [Multisig](https://github.com/nervosnetwork/ckb-system-scripts/wiki/How-to-sign-transaction#multisig).

Unlock a cell with a signature has no restrictions, which helps owner to manage the cell as he wants.
