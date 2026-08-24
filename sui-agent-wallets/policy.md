# Limits the agent cannot raise

## Where the check happens

The policy gate sits **in front of the key**, server-side, and runs before a
signature exists. On a signing request:

1. **The exact bytes are verified.** The transaction that was reviewed is the
   transaction that gets signed. A client that renders one thing and submits
   another does not get a signature.
2. **The policy engine returns one verdict.** Spend limits, risk thresholds and
   approval rules apply regardless of what the caller asked for.
3. **The approval is single-use.** It is bound to one user, one transaction
   digest and one operation, then burned before signing.
4. **The signer re-verifies independently** before producing a signature.

The agent participates in none of these steps. That is the property that makes
the limit a control rather than a note.

## Setting a limit

```bash
waap-cli policy set --daily-spend-limit 100
waap-cli policy get
```

## Why an in-agent check is not a boundary

A limit enforced inside the agent's own loop is evaluated **after** the agent has
decided, by code the agent can read and, in most runtimes, edit. Two failures
follow:

- **Prompt injection reaches the check.** An attacker who can influence the
  agent's context can influence anything the agent computes, including whether it
  believes it is under a cap.
- **A capable agent can rewrite its own config.** If the cap lives in a file the
  agent can open, the cap is a suggestion.

The published agent-loss incidents share this shape. The control existed in the
same trust domain as the thing being controlled.

Write the limit where the agent cannot reach it, and let the request fail
server-side. An agent that hits a wall it cannot move is behaving correctly.

## Human approval

For anything above the line, the gate can require a second factor from the
person rather than the agent: a human-readable description of what is about to
happen, and an approval sent out of band. The agent proposes; the human
approves; the protocol enforces.

Design the agent to **expect refusal**. A signing request that returns "denied by
policy" is a normal outcome, not an exception to retry in a loop. An agent that
retries a refused transaction is generating noise at best and, if the refusal is
rate-based, working against its own operator.

## Reviewing an agent that touches money

Check these in order. The first three are disqualifying.

- [ ] No private key or seed phrase in the environment, the prompt, the tool
      definitions, or anything a tool can read back.
- [ ] No spend limit whose only enforcement is agent-side code.
- [ ] No delegated grant without a recipient allowlist, a ceiling and an expiry.
- [ ] Dry run is the default, and enabling spend required a deliberate number.
- [ ] The account is funded for the chain it settles on, so failures are real
      failures rather than empty balances.
- [ ] Refusal is handled as a normal branch, not retried blindly.
- [ ] Nothing arriving from outside the agent (a token, an NFT, a tool result,
      a webhook body) can widen what the agent may do. Authority comes from the
      gate, never from inbound data.

That last one is the lesson of the incidents where an inbound asset carried
permissions with it. Nothing that arrives in a wallet should be able to grant
anything.
