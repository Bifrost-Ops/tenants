# Bifrost Ops — ACI Tenants

Day 1 declarative Cisco ACI tenant policy: tenants, VRFs, bridge domains, application profiles, EPGs, contracts and optional L3Outs.

## Status
Documentation-first scaffold; no automatic apply.

## Change lifecycle
1. Review the [architecture and runbooks](https://github.com/Bifrost-Ops/docs).
2. Document policy intent, dependencies, traffic impact and rollback.
3. Validate YAML and Terraform inputs.
4. Review a Terraform plan against existing APIC state.
5. Apply only after explicit approval and verify intended contracts and reachability.

## State and recovery
Use a separate state boundary from `fabric`. Back up state securely, retain known-good commits and check all destructive plan actions. Reverting configuration may delete live network objects; restoration requires a reviewed plan and post-change traffic validation.
