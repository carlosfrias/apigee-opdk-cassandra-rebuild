# apigee-opdk-cassandra-rebuild — Multi-Datacenter Cassandra Ring Rebuild

> **An Ansible role that re-streams data into a Cassandra node or datacenter by running `nodetool rebuild` against a named source datacenter** — the canonical operation for adding a region, recovering a node, or restoring replication after a topology change in an Apigee Edge Private Cloud (OPDK) planet.

> [!NOTE]
> Engineering portfolio note — this project demonstrates multi-datacenter Cassandra ring administration and `nodetool` operations fluency. See the [skills assessment →](SKILLS-ASSESSMENT.md) for the expertise applied.

A Cassandra cluster-administration operation expressed as code: knowing **why the `flush → rebuild → cleanup` sequence matters**, **why `rebuild <source-dc>` is used instead of `repair`**, and **what token-range ownership semantics make the final `cleanup` necessary**.

<!-- BEGIN Google Required Disclaimer -->

## Not Google Product Clause

This is not an officially supported Google product.
<!-- END Google Required Disclaimer -->

---

## What the role actually does

`tasks/main.yml` runs a precise three-step sequence against a Cassandra node:

1. **`nodetool flush`** — flush Memtables to SSTables before streaming, so rebuild operates on durable data.
2. **`nodetool -h <private_address> rebuild dc-<region_num>`** — re-stream data into the local node from a **named source datacenter** (`dc-<N>`). This is the multi-DC rebuild invocation: the target node pulls only the token ranges it owns from a healthy source DC, rather than a full-cluster repair.
3. **`nodetool cleanup`** — after rebuild, drop SSTables for token ranges the node no longer owns (post-topology-change superseded data).

Every command is host-scoped (`-h {{ private_address }}`), runs with the correct `JAVA_HOME`, and is guarded by `removes: "{{ nodetool }}"` as a precondition.

---

## When this role is used

This role is composed into the broader OPDK runbooks for:
- **Adding a datacenter** to an existing planet (the new DC streams from an established one).
- **Dead-node recovery** after `replace_address` bootstrap.
- **Replication-factor / topology changes** that require data re-streaming.

The source DC must be healthy and fully replicated before the target streams from it. See the [`apigee-edge-opdk`](https://github.com/carlosfrias/apigee-edge-opdk) framework and the `apigee-opdk-*` role corpus for the composition playbooks.

---

## Role variables

| Variable | Required | Description |
|----------|----------|-------------|
| `nodetool` | yes | Path to the `nodetool` executable |
| `java_home` | yes | `JAVA_HOME` for Cassandra/JMX commands |
| `private_address` | yes | The target node's private IP (host-scoped operation) |
| `region` | yes | The local region identifier (used to resolve the source DC) |
| `region_num` | — | The numeric source DC suffix (`rebuild dc-{{ region_num }}`) |

---

## Provenance

Authored and maintained by **Carlos Frias** during his tenure on Apigee Edge Private Cloud. One of the Cassandra-administration roles in the `apigee-opdk-*` corpus — the same expertise is aggregated in the [`apigee-edge-opdk`](https://github.com/carlosfrias/apigee-edge-opdk) framework.

Contributions welcome — see [CONTRIBUTING.md](./CONTRIBUTING.md).

## License

See [LICENSE](./LICENSE).