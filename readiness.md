# Beacon release review

## Pipeline audit
Gap: production ran independently of validate. Risk: failed tests cannot block promotion.
Repair: production needs validate; default success condition blocks failed validation.
Retain environment: production. Configure required reviewers separately in GitHub
Settings > Environments > production, restrict release sources, prevent self-review
where supported. None of these server-side controls is demonstrated by local PASS.
Secret: REGISTRY_TOKEN uses secrets context, never printed. Configure production
secret in protected environment when real deployment exists; no secret created here.

## Traceability
v2.3.1 is PATCH: backward-compatible bug fix from v2.3.0.
Candidate manifest now references beacon-service:v2.3.1, not latest.
Annotated Git tag v2.3.1 marks final committed review artefacts.
Record tag -> code commit in PDF using git rev-parse commands.
Version image tags are mutable unless registry policy prevents overwrite;
production should pin a verified digest and record build provenance.
No image built, published, pulled, or deployed in this review.

## Incident recovery
Supplied hypothetical fixture: 35% failures, rising, two of three readiness checks
fail, broad impact, unclear cause. Roll back rather than debug under user impact.
Known-good beacon-service:v2.3.0 is available and schema-compatible per fixture.
Proposed command only (not executed):
kubectl set image deployment/beacon beacon=beacon-service:v2.3.0
Then kubectl rollout status deployment/beacon; check Ready replicas, endpoint 200,
readiness and error-rate recovery against baseline. Reassess if recovery fails.
No actual incident observed or rollback performed.

## Release note and decision
Scope: repair release dependency, candidate image reference, rollback target.
Proof: one local application unit test passes; three static contract checks pass.
Risk: production reviewer settings, registry artifact identity and runtime readiness
not verified. Owner: release owner; mitigation: configure reviewers, verify artifact
and staging evidence, obtain accountable sign-off before deployment.
Current production decision: NO-GO. Approval NOT REQUESTED; NOT DEPLOYED.
Local repair completion is not production readiness or proof incident was resolved.
Terraform provisions infrastructure; Kubernetes operates application workloads.
