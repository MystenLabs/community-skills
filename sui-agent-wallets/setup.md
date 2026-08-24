# Provisioning an agent wallet on Sui

## What the account is

One login provisions an account whose signing keys are generated inside an
attested Trusted Execution Environment. Nothing on the agent's machine holds key
material.

On Sui, the authority to sign is a **capability object**. Signing is gated on
that object, so the permission is on-chain state rather than a server-side flag.
Two consequences worth designing around:

- **It is transferable.** The user can move it to another address, so they are
  not locked to one operator.
- **A Move package can wrap it.** Because it is an object, Move logic can hold
  it and impose conditions before a signature is ever requested. That is the
  path to a rule that binds across chains, since the approval settles on Sui.

## Two curves

| Curve | Chains | Live today |
|---|---|---|
| `secp256k1` | EVM networks | **EVM: yes** |
| `ed25519` | Sui, Solana, and other ed25519 chains | **Sui and Solana: yes** |

One login provisions both, so the same account signs on Sui and on EVM without a
second wallet.

⚠️ Other chains on these curves are reachable **without new cryptography**, but
each still needs transaction formatting and network plumbing. Do not tell a
developer the account transacts on any chain on either curve today.

## Two signing modes

| | Standard | Squid Mode |
|---|---|---|
| Who completes the signature | An attested enclave | The enclave together with an independent validator network. Both required |
| Who can sign alone | The enclave operator | Nobody |
| What the account must hold | The destination chain's gas | **SUI** for Sui gas and **IKA** for the network fee |

Squid Mode is the mode where key material is never assembled in one place. In
Standard the key is held whole inside the enclave, and the protection is the
attestation plus the policy gate. Both are self-custody for the user; they are
not the same trust model, so do not describe them interchangeably.

## Provisioning with the CLI

```bash
npm install -g @human.tech/waap-cli

waap-cli signup
waap-cli policy set --daily-spend-limit 100
waap-cli squid init
waap-cli squid addresses
```

`squid init` provisions both curves. `squid addresses` prints the Sui, Solana
and EVM addresses for the one account.

**Fund the Sui address before the first signature.** A Squid Mode signature
settles on Sui and is completed by the validator network, so the account needs
**SUI** and **IKA**. An unfunded account fails in a way that reads like a bug in
your own code.

## Provisioning from the SDK

```bash
npm install @human.tech/waap-sdk
```

```ts
import { initWaaPSquid, WAAP_EVENTS } from '@human.tech/waap-sdk'

const waap = initWaaPSquid({
  environment: 'production',
  chains: ['evm', 'sui']
})

await waap.session.login()
await waap.squid.onboard()

waap.session.on(WAAP_EVENTS.squidReady, () => {
  console.log(waap.squid.getStatus())
})
```

Accounts in this mode are created explicitly. Call `onboard()` after login and
before any signing, or the first signature will fail on an account that does not
exist yet.

## Versions

⚠️ **Check the registry rather than trusting a version written in a document.**

```bash
npm view @human.tech/waap-cli dist-tags
npm view @human.tech/waap-sdk dist-tags
```

Squid Mode ships in `@human.tech/waap-cli` **2.1** and `@human.tech/waap-sdk`
**2.2**. Earlier versions have no `squid` command group and no `initWaaPSquid`,
so an agent built against an older install will fail at provisioning rather than
at signing, which is a confusing place to debug.

## Running in a container

The CLI is the surface for agents. It is headless, it holds no key, and it
authenticates a session rather than loading a secret, so a container image built
around it has nothing sensitive baked in.

Do not mount a key into the container. There is no key to mount, and a design
that wants one has reintroduced the problem this setup exists to remove.
