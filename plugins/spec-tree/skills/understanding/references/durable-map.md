<overview>
The Spec Tree is a **durable map** — a permanent record of what the product is and does. Specs are not work items. They are not tickets. They do not move through a pipeline.
</overview>

<mental_model>

| Backlog thinking  | Durable map thinking               |
| ----------------- | ---------------------------------- |
| Create ticket     | Create potential                   |
| Close ticket      | Realize potential (tests pass)     |
| Archive done work | Nothing moves — specs stay forever |
| Assign status     | Derive state from lock files       |
| Sprint velocity   | Realization rate                   |
| Groom backlog     | Prune tree                         |

</mental_model>

<potential_energy>

When you write a spec, you create **potential energy** in the system. The spec defines a state of the world that doesn't yet exist but should.

When tests pass and the lock file is written, potential becomes **realized**. The spec now describes something true about the product.

When you edit a realized spec, you create new potential. The lock file becomes stale because the evidence no longer matches the claim. Reality needs to catch up to the new vision.

When you remove a spec, you **prune** — deciding this branch no longer serves the tree's growth. The product becomes simpler.

</potential_energy>

<atemporal_voice>

Specs state product truth. They never narrate history, never reference time, never describe a journey of discovery.

**Temporal markers to eliminate:**

| Temporal (wrong)                  | Atemporal (correct)           |
| --------------------------------- | ----------------------------- |
| "We discovered that X"            | "X ensures Y"                 |
| "X has accumulated without Y"     | "Y prevents Z"                |
| "We need to address"              | "[Product] provides"          |
| "Currently, the system"           | "[Product] [does thing]"      |
| "After investigating, we decided" | "[Decision] governs [scope]"  |
| "This was introduced because"     | "[Feature] enables [outcome]" |
| "Over time, X became"             | "X is [state]"                |

**Test:** Read any sentence aloud. If it would sound wrong after the work is done, it's temporal.

**Why it matters:** Temporal language creates documents that rot. "We currently need X" becomes false the moment X is delivered, but the document still says "we need" it. Atemporal language makes documents permanent statements — they're either true about the product or they should be removed.

</atemporal_voice>

<prohibited_operations>

These operations do not exist in the Spec Tree:

- **Close** a spec — Specs describe product truth. Truth isn't closed.
- **Move** a spec to "done" — There is no done directory. The spec stays where it is.
- **Archive** a spec — If it's true, it stays. If it's no longer true, prune it.
- **Assign status** — Status is derived from the lock file, not set by a human or agent.
- **Mark as complete** — Completion is proven by tests passing and the lock file being valid.

</prohibited_operations>

<lock_file_durability>

The lock file (`spx-lock.yaml`) makes the durable map observable:

- **No lock file** = needs work. The spec exists but has no evidence.
- **Stale lock** = evidence needs refreshing. The spec or tests changed since the last lock. This is not "broken" — it means the product evolved and evidence needs catching up.
- **Valid lock** = evidence is current. The spec and its tests were in agreement when the lock was written.

Stale is a natural, healthy state. Every spec edit makes the node stale. This is the system working as designed — not a problem to fix urgently.

</lock_file_durability>

<common_agent_mistakes>

| Agent impulse                  | Correct response                                  |
| ------------------------------ | ------------------------------------------------- |
| "Task complete, closing story" | Nothing to close. If tests pass, lock the node.   |
| "Moving to done"               | There is no done. The spec stays where it is.     |
| "Archiving completed work"     | Do not archive. The spec is the permanent record. |
| "Setting status to complete"   | Do not set status. Run tests and write the lock.  |
| "This spec is outdated"        | Either it's still true (keep it) or prune it.     |
| "Creating a new ticket for X"  | Create or edit a spec. Specs are not tickets.     |

</common_agent_mistakes>
