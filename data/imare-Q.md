# Lines of code

https://github.com/code-423n4/2024-03-coinbase/blob/e0573369b865d47fed778de00a7b6df65ab1744e/src/WebAuthnSol/WebAuthn.sol#L160


# Vulnerability details

`CoinbaseSmartWallet` contract currently uses `FCL.ecdsa_verify` method when verifying a `UserOperation` is indeed coming from one of the owners of a wallet.

`FreshCryptoLib` library has been already addressed (on the same line) as having a bug in the original audit but currently there is a newer [PR](https://github.com/rdubois-crypto/FreshCryptoLib/pull/65) which patches it from being susceptible to signature malleability attacks.

## Impact

Signature malleability poses a security risk in systems that use signatures as unique identifier.
In case of `CoinbaseSmarWallet` the owner is the only one that can provide this malleable signatures and "attack" its own funds so the impact is low if FCL.ecdsa_verify is only used in this context.

## Proof of Concept

The library is used on this [line](https://github.com/code-423n4/2024-03-coinbase/blob/e0573369b865d47fed778de00a7b6df65ab1744e/src/WebAuthnSol/WebAuthn.sol#L160) when signature is being verified as coming from WebAuthn. This signature is being passed to the wallet on this [line](https://github.com/code-423n4/2024-03-coinbase/blob/e0573369b865d47fed778de00a7b6df65ab1744e/src/SmartWallet/CoinbaseSmartWallet.sol#L321) when checking if the signature matches a owner on `validateUserOp` callback. 

The following [comment](https://github.com/rdubois-crypto/FreshCryptoLib/pull/65#issuecomment-2036417191) is taken from the PR :

"*This PR fixes unicity in the representation of a point. While it is not a vulnerability as such, but rather malleability, it is correct that unexpected uses of malleability can lead to disaster.*"

## Tools Used
Manual review

## Recommended Mitigation Steps

Apply the patch from the mentioned PR or use the latest version of the `FreshCryptoLib` library where the PR is already applied.



## Assessed type

Access Control