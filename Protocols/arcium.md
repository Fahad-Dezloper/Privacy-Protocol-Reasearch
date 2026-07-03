Arcium is MPC

Phase 1 — What is MPC: the third way to hide a transaction — split the secret so no one holds the whole thing. (now) -- DONE

- Phase 2 — Computing on hidden pieces: the real magic — doing math on the shares without ever rebuilding the secret. -- DONE
- Phase 3 — Arcium's network: Arx nodes, clusters, and the MXE (where a private computation actually runs). -- DONE
- Phase 4 — The two engines: Cerberus vs. Manticore — different speed/trust trade-offs.
- Phase 5 — The full flow: a confidential computation end-to-end across Arcium + Solana (encrypt → queue → compute → callback).
- Phase 6 — Trust & risks: MPC vs. TEE vs. ZK, the collusion problem, liveness, and the Elusiv backstory.
- Phase 7 — What is the point of solana in all this?

plits the secret across many separate parties

MPC = Multi-Party Computation. You take a secret, split it into random-looking pieces called "shares," and give each piece to a different party — so no single party ever holds the whole secret. Yet, working together, they can still compute the correct answer — and only the answer comes out.

Arx nodes
one of the separate computers taking part

// Computing on the Hidden Pieces
In MPC secret gets sharded into diffrent nodes of arcium.

Adding + Multiplication secures everything

( Adding )
adding makes it impossible to break the actual shards value you just have output not the input
7 + 20 = 27

i give 27 to someone they can never guess the actual inputs

( Multiplication )
Multiplication can not be done purely locally - the parties have to talk to each other, exchanging some messages ( "communication round" ).

- (The technical name is Beaver triples — you don't need the details, just the idea: pre-made scratch material that lets them multiply hidden numbers.)

// Add + Multiply = you can compute anything privately
So once you can add (free) and multiply (with hidden cooperation) on shares, you can run any program over secret data. That's why Arcium calls itself a "confidential supercomputer" — it's general-purpose private computing, not a one-trick mixer.

// reveal
The inputs stay split and hidden the entire time. Only at the very end, when you actually want the result, do the nodes combine the shares of the result (not the inputs).

- In Arcium's terms: .reveal() makes the answer public, or they can re-encrypt it to one specific person so only that recipient can read it (called "sealing").

// Arx Nodes, Clusters, and the MXE
Arx node = one "party"
A real machine, run by an operator somewhere in the world, that holds a share of your secret and does its part of the MPC math.

Cluster = the group of parties working on your job
A cluster is a specific group of Arx nodes that team up to run a computation together. Your secret is split across the nodes in that cluster, and they cooperate to compute on it.

- every public cluster is forced to include one randomly-chosen node from the whole network — a stranger the others didn't pick — which makes "everyone secretly conspires" very hard to arrange.
- if a node dies or misbehaves, the cluster can migrate — swap that node out for a new one — so your computation isn't stuck

MXE = the private workspace where your app's computation runs
MXE (Multi-party eXecution Environment) is the dedicated, isolated private "computer" for one app's confidential work. It runs on top of a cluster, and it has its own encryption key.
MXEs are isolated from each other, so many can run at once — that's how Arcium scales to lots of apps in parallel.

Arcium uses DKG ( Distributed key generation )

// The Two Engines: Cerberus vs. Manticore
the result is either correct, or it stops — a cheater can never push a wrong answer through. (Built on a protocol called BDOZ)

- Cerberus = a team where every member's work is notarized with a tamper-proof seal. If anyone forges, it's caught instantly and the whole job is voided. Safe even in a room full of potential crooks — as long as one person is honest. Careful, thorough, a bit slower.
- Manticore = a fast team that assumes everyone's honest (just nosy), with one trusted person prepping materials beforehand. Much quicker — but only safe among colleagues you already trust.

// the full flow of confidential computation, end to end

How a Private Job Actually Runs, Start to Finish

examples of swish
ex1 - Coinflip
you pick heads/tails the system flips a coin nobody can rig, and only win/lose is revealed.
your choice and coin result stays secret.

pub struct UserChoice {
pub choice: bool, // true = heads, false = tails
}

pub fn flip(input_ctxt: Enc<Shared, UserChoice>) -> bool {
let input = input_ctxt.to_arcis(); // 1. decrypt YOUR choice into secret shares
let toss = ArcisRNG::bool(); // 2. nodes jointly roll a fair random coin
(input.choice == toss).reveal() // 3. compare, reveal ONLY win(true)/lose(false)
}

- Input is Enc<Shared, UserChoice> → your choice, encrypted to you. Only the cluster (working together) can pull it into shares.
- .to_arcis() → your choice becomes shares across the nodes. From here, no node knows if you picked heads.
- ArcisRNG::bool() → the trustless coin. Each node throws in secret randomness; combined, it's a fair coin no single node can predict or bias (safe as long as one node is honest — your Phase 1 rule!). This is why it can't be rigged: the house can't see or steer it.
- (input.choice == toss) → compares two hidden values → a hidden true/false.
- .reveal() → unlocks only that one bit: did you win? Your actual choice and the actual coin face are never revealed.

ex2 - waiter

// what is solana doin?
// what nodes a stop from lying why it can't be cracked
// what's these engines doin in arcium and where are they being used Cerberus vs. Manticore?
// who is running these nodes?
