# ACI L3Out ↔ Cilium BGP integration

**Design contract; no APIC configuration has been applied.** This directory intentionally does not contain invented NAC module fields. Use the official NAC L3Out data model at https://netascode.cisco.com/docs/data_models/apic/tenants/l3out/ to build the executable environment-specific tenant YAML only after APIC topology, interface types, version and peering method are known.

## Design principles
- Use an explicit routed BGP boundary. Cisco ACI L3Out peering is defined in node/interface profiles; a normal server EPG alone does not provide a BGP adjacency.
- Keep INFRA and SERVICE separate as required by security. Decide VRF sharing, inter-VRF routing and contract scope deliberately.
- Advertise **opt-in LoadBalancer VIP host routes first**; PodCIDR route advertisements require separate routing/IPAM tests.
- Configure ingress prefix filters, maximum-prefix protections, route policy, ECMP/failover and approved TCP/179 reachability at the chosen routed peer.
- Avoid accidental duplicate ownership of ACI tenant roots between Terraform states.

## Environment inputs required
See [topology inventory](topology.example.yaml). Replace all placeholders before producing a deployable NAC data model.

## Execution stages
1. Collect APIC version, L3Out node/interface profiles, physical leaf ports, routing domain, peer addresses, local/remote ASNs and routed transit topology.
2. Confirm the selected NAC release schema and Terraform provider compatibility with APIC.
3. Generate and validate environment-specific NAC YAML, using the official model; do not infer L3Out connectivity from ordinary EPG attachment.
4. Run Terraform validate/plan with remote encrypted locked state, require peer review of every deletion/change and capture APIC backup.
5. Apply in isolated lab only after explicit approval.
6. Configure Cilium BGP v2 CRDs, export one test VIP, verify advertised/received routes and return traffic.
7. Exercise peer and leaf failure, agent restart, withdrawals and recovery.
8. Roll back by approved change and verify RIB/FIB; Git revert alone is not a fabric rollback.

## Cross-repository references
- https://github.com/Bifrost-Ops/kubernetes/tree/main/networking/bgp
- https://github.com/Bifrost-Ops/docs/blob/main/BGP-VALIDATION-RUNBOOK.md
- https://github.com/Bifrost-Ops/docs/blob/main/BGP-FIRST-REFERENCE-ARCHITECTURE.md
