# Skills Assessment — apigee-opdk-cassandra-rebuild

> **Skill domain:** Multi-datacenter Cassandra ring administration — Apigee Edge Private Cloud (OPDK). Part of the broader Apigee platform-operations portfolio; see the [`bap_coe` portfolio hub →](https://github.com/carlosfrias/apigee-hybrid-workspace/blob/master/SKILLS-ASSESSMENT.md) for the cloud-native (Hybrid/K8s) counterpart and the full corpus.

---

## Why this role is notable

- **The right primitive for the job.** `rebuild dc-<source>` streams from a *named source DC* — the correct operation for adding a region or recovering a DC, distinct from `repair` (anti-entropy) or `bootstrap`.
- **Correct pre/post sequencing.** `flush` before streaming (operate on durable data), `cleanup` after (drop SSTables for token ranges the node no longer owns).
- **Host-scoped and guarded.** Every command is `-h <private_address>` with the right `JAVA_HOME`, gated by `removes: "{{ nodetool }}"` as a precondition.
- **Composed, not standalone.** The atomic rebuild primitive used by the broader OPDK runbooks for add-DC, dead-node recovery, and replication-factor changes.

---

## Expertise demonstrated

> Ansible is the medium. The engineering evidence lives in the [project README →](README.md). What follows is the skills assessment for the business reader.

- **`nodetool` operations fluency** — correct `flush` → `rebuild` → `cleanup` sequencing; host-scoped invocation; `JAVA_HOME` management.
- **Multi-DC ring topology** — `rebuild dc-<region_num>` streams from a *named source DC* — the right primitive for adding a region or recovering a DC, distinct from `repair` or `bootstrap`.
- **Token-range ownership semantics** — the trailing `cleanup` shows awareness that after a topology change a node may hold SSTables for ranges it no longer owns.
- **Precondition discipline** — `assert` that `nodetool`, `java_home`, `private_address`, `region` are defined; `removes:` guard.

---

## How this shows the expertise

This role is a Cassandra cluster-administration operation expressed as code. The expertise is not "running `nodetool`" — it is knowing **why the `flush → rebuild → cleanup` sequence matters**, **why `rebuild <source-dc>` is used instead of `repair`**, and **what token-range ownership semantics make the final `cleanup` necessary**.

The clearest single signal: the choice of `rebuild dc-<source>` over `repair`. `repair` is the anti-entropy primitive for correcting divergence; `rebuild <source-dc>` is the streaming primitive for re-populating a node or DC from a named source. Choosing the right primitive — and sequencing `flush` before and `cleanup` after — is the Cassandra-administration judgment, with Ansible as the medium.

---

## Related expertise

| Skill | Repository | Assessment |
|-------|-----------|-----------|
| Rolling upgrade / DR / traffic fencing | [`apigee-opdk-playbook-maintenance-opdk-upgrade`](https://github.com/carlosfrias/apigee-opdk-playbook-maintenance-opdk-upgrade) | [SKILLS-ASSESSMENT.md →](https://github.com/carlosfrias/apigee-opdk-playbook-maintenance-opdk-upgrade/blob/master/SKILLS-ASSESSMENT.md) ✅ |
| Postgres HA / controlled switchover | [`apigee-opdk-setup-postgres-failover`](https://github.com/carlosfrias/apigee-opdk-setup-postgres-failover) | [SKILLS-ASSESSMENT.md →](https://github.com/carlosfrias/apigee-opdk-setup-postgres-failover/blob/master/SKILLS-ASSESSMENT.md) *(pending retrofit)* |
| OPDK framework (flagship monorepo) | [`apigee-edge-opdk`](https://github.com/carlosfrias/apigee-edge-opdk) | [SKILLS-ASSESSMENT.md →](https://github.com/carlosfrias/apigee-edge-opdk/blob/master/SKILLS-ASSESSMENT.md) *(pending retrofit)* |
| Cloud-native (Hybrid/K8s) counterpart | [`apigee-hybrid-workspace`](https://github.com/carlosfrias/apigee-hybrid-workspace) | [SKILLS-ASSESSMENT.md →](https://github.com/carlosfrias/apigee-hybrid-workspace/blob/master/SKILLS-ASSESSMENT.md) ✅ portfolio hub |

---

## Provenance

Authored and maintained by **Carlos Frias** during his tenure on Apigee Edge Private Cloud. This skills assessment is the companion to the engineering [README →](README.md). For the full engineering detail — task sequence, variables, and composition — see the project README.

## License

See [LICENSE](./LICENSE).