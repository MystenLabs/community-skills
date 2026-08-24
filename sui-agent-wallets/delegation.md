# Scoped, expiring authority

## The problem it solves

Asking a human to approve every action is correct for a large transfer and
unusable for anything else. A trading loop, a game, or an agent doing metered
work cannot stop for a confirmation each time.

A **scoped grant** is the answer: the person approves a shape once, and actions
matching that shape proceed without a prompt.

## The four axes

A grant must bound all four. One missing axis makes it broader than it looks.

| Axis | What it bounds |
|---|---|
| **Recipients** | Which addresses may be paid. An empty allowlist means anyone |
| **Chain** | Which chain the grant is valid on |
| **Cumulative ceiling** | A total spend across every action under the grant, not per action |
| **Expiry** | How long it lives |

The ceiling being **cumulative** is the part most often misread. It is a budget
for the whole grant, not a per-transaction limit, so an agent making many small
actions consumes it exactly as fast as one making a few large ones.

## Requesting one

From an application:

```ts
window.waap.requestPermissionToken({
  allowedAddresses: ['0x...'],
  chainId: 1,
  requestedAmountUsd: 50,
  requestedExpirySeconds: 3600,
})
```

From an agent, pass the grant to a signing command:

```bash
waap-cli send-tx --permission-token <token> ...
```

The origin is bound server-side to the domain that requested it, so a grant
issued to one application cannot be replayed by another.

## The lifetime ceiling

**A grant lasts at most two hours, enforced server-side.** It is a session, not
a standing mandate.

Inside that session it does what recurring billing wants: many charges against
one approval, under a ceiling the user set. Across a month it does not, because
the grant expires long before the next charge.

Works today: pay-per-use inside a visit, metered API calls, an agent running a
bounded task, in-game purchases during a session.

## Grants are not a way around policy

A grant carves a narrow, expiring exemption from the **approval step** inside
limits the policy already sets. It never widens the policy itself. An agent
holding a grant is still subject to the daily spend limit, and a request outside
the grant's scope falls back to the normal gate.

If a design needs the grant to exceed the policy, the policy is what should
change, deliberately, with a human making that call.

## Revocation and handoff

Because the underlying authority on Sui is an object rather than a server-side
flag, it can be transferred, and a scope can be revoked without asking the agent
to cooperate. Assume an agent will not cooperate: revocation must work against a
compromised agent, which is exactly when it is needed.

Do not let a delegated proof travel with an agent that changes hands. A scope
granted to an agent under one operator should not survive the agent moving to
another.
