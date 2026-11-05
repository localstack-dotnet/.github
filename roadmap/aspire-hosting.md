# Aspire Hosting roadmap

Moved on 2026-11-05 from `dotnet-aspire-for-localstack/docs/ROADMAP.md` as groomed on the work-in-progress branch `feature/current-runtime-baseline` (`d0d723c`, authored 2026-10-17), which is newer than the copy on `master`. Statements about that branch's implementation describe unmerged work. The [organization roadmap](../ROADMAP.md) owns cross-product priorities; this file owns Aspire Hosting's detailed workstreams. Links to research and plans are pinned to the public revision they were moved from; that evidence is now kept in the organization's private archive. Current behavior and rules live in the [Aspire Hosting repository](https://github.com/localstack-dotnet/dotnet-aspire-for-localstack).

Date: 2026-10-17

## How To Use This Document

This is the prioritized backlog and agreed delivery direction, not an implementation plan or authorization to change code, tests or CI. Open work needs focused design or missing acceptance evidence where stated. Status refers to implementation; **done does not mean published**. Release history belongs in [CHANGELOG.md](https://github.com/localstack-dotnet/dotnet-aspire-for-localstack/blob/master/CHANGELOG.md), existing runtime/API rationale in [the hosting model](https://github.com/localstack-dotnet/dotnet-aspire-for-localstack/blob/master/docs/architecture/hosting-model.md), and SDK/tool defects and closing triggers in [Upstream Watch](https://github.com/localstack-dotnet/dotnet-aspire-for-localstack/blob/master/docs/upstream-watch.md).

Evidence baseline: [the October research](https://github.com/localstack-dotnet/dotnet-aspire-for-localstack/blob/d0d723c099a723e86a6ed8a9b663173e080bfce5/docs/plans/2026-10-16-aspire-aws-evolution-research.md), observed 2026-10-16, plus owner decisions from the subsequent discussion. PR [#36](https://github.com/localstack-dotnet/dotnet-aspire-for-localstack/pull/36) merged as `7ad7698`; its source tree matches researched `9bd2b67`. WS3A, the October dependency refresh and the MessagePack fix are merged, but remain in the unreleased changelog entry. Package versions come from `Directory.Packages.props`; image defaults come from `src/Aspire.Hosting.LocalStack/Container/LocalStackContainerImageTags.cs`. This groom did not repeat upstream discovery, refresh issue states or execute runtime acceptance. Decisions below supersede the research's unselected product recommendations, not its evidence or explicit unknowns.

## Status

🔜 Not implemented · 🔬 Focused design / evidence still needed · 🔨 In progress · ✅ Implemented / completed

Delivery placement replaces the old blanket P0–P3 ordering. **First delivery** means the selected slices below, not every item in those workstreams. Maintenance and later candidates remain visible without becoming release prerequisites.

| WS | Title | Placement | Status | Evidence / next step |
| --- | --- | --- | --- | --- |
| WS0 | Analyzer modernization | Completed | ✅ | `80c9566`; policy follow-up retained in WS0.5 |
| WS0.5 | Analyzer arbitration rebase | Maintenance backlog | 🔬 | Repository-specific policy review; not a first-delivery blocker |
| WS1 | Package update | Completed + ongoing maintenance | ✅ | October refresh and security floor merged; versions owned by manifests |
| WS1.5 | CDK routing evidence and fix | Completed | ✅ | `8fd8c99`; released in `13.4.0` |
| WS2 | Aspire/AWS modernization | Completed | ✅ | `9d60d80`; released in `13.4.0`; new opportunities routed below |
| WS3A | AppHost API boundary + endpoint-conflict warning | Merged, awaiting release | ✅ | `7b078e5` included in #36; retain compatibility constraints |
| WS3B | Resource-wiring correctness | First-delivery contract fixes; other shapes later | 🔜 | Architecture/surface research complete; focused regression/design work remains |
| WS4 | Bugs & correctness | First-delivery contract fixes; other issues triaged | 🔜 | #24/#34 and equivalent contract failures; #35 still needs behavioral evidence |
| WS5 | Test integrity | First-delivery shared runtime matrix + backlog | 🔜 | Same common suite on current and legacy; existing assertion/fixture gaps retained |
| WS6 | Refactoring / API quality | Only necessary changes in first delivery | 🔬 | No blanket callback/API rewrite; independent cleanup remains queued |
| WS7 | LocalStack runtime and platform tracking | First delivery + routine maintenance | 🚧 | [Implementation underway](https://github.com/localstack-dotnet/dotnet-aspire-for-localstack/blob/d0d723c099a723e86a6ed8a9b663173e080bfce5/docs/plans/2026-10-17-current-runtime-baseline-implementation-plan.md): activation/preset wiring and shared22-case public-API runs pass both candidates; direct contract fixes, trusted CI and final acceptance pending |
| WS8 | Observability / UX features | Later candidates; HQ is distant vision | 🔜 | Preserve local-resource workbench, seed and diagnostic opportunities |
| WS9 | Docs & internal-docs consolidation | Consolidation completed; docs follow delivery | ✅ | Migration/configuration/playground guidance accompanies its owning workstream |
| WS10 | Aspire ecosystem integration | TS is the next new product capability | 🔬 | October research complete; first TS contract still needs design |
| WS11 | Upstream reports | External follow-up / maintenance | 🔬 | SDK-tool draft and controlled source/model findings; no filing performed |

## Release Direction

- Preserve backward compatibility through additive, opt-in evolution. Retained Client-shaped APIs are non-error `[Obsolete]` adapters until the next major, as recorded in the unreleased changelog.
- The integration targets the **selected LocalStack container**, not a Community/Hobby/Pro product tier. Paid-token users and custom registry/image/tag selection remain valid. The old Community default was a zero-setup convenience, not a support boundary. Vendor entitlements and AWS emulation fidelity are not package guarantees; correct reference, endpoint, region, credential and startup adaptation are package responsibilities.
- **First delivery — reliable current-runtime baseline:** a fixed qualified current default, secure activation/onboarding, explicit tested legacy selection, generic image overrides, shared tests and direct contract fixes are implemented in the unreleased working branch. Missing activation must not downgrade or switch to AWS. Qualification is not release acceptance: the latest current full run has a Lambda redirect timeout, and trusted clean-source execution plus final review remain required.
- **Common acceptance:** run the same shared behavior/integration suite against current and legacy configurations, including the legacy selection path. Both must pass before release; missing-token/not-executed checks are not success. Secret-free fork PR checks and trusted authenticated CI need distinct handling. The owner-suggested `localstack-dotnet-client` fixture/shared-scenario pattern is now inspected in the linked design; adapt the principle to TUnit/Aspire rather than copying its old framework/images. No runtime test was executed by that structural read.
- The matrix means separate current/legacy runs, not a new same-AppHost modern+legacy or selective LocalStack/real-AWS hybrid feature. It does not excuse existing multiple-resource failures such as #34. Legacy preserves tested existing flows, not automatic parity for every future feature or a vendor security-backport guarantee.
- **Next new product capability — TS AppHost:** follows the baseline delivery, with unchanged C#/Client/null/health semantics. Its first authoring/provisioning scope is not yet selected. Other product opportunities remain candidates; no dates or version numbers are assigned to them.
- **Not a backlog-wide cleanup release:** include contract-breaking correctness work even when current playgrounds do not exercise it; require a failing regression or concrete reproducer before fixing. Classify bugs versus support extensions by the published contract, not by issue labels. General refactors, analyzer arbitration, new native SDK conveniences and HQ are not automatic first-delivery prerequisites.
- **Playground vision (owner intent, not a delivery gate):** as roadmap work is selected, look for useful ways to advance the playground applications alongside it: meaningful end-to-end journeys, developer UX and visible demonstrations of supported capabilities. Evaluate opportunities rather than requiring a new sample for every feature. No playground scope or implementation is selected by this note.

## Completed Work

- **WS0:** analyzer configuration modernization landed in `80c9566`. That completed pass is separate from the arbitration rebase below.
- **WS1:** the foundation update and October refresh (`b62ec39`) are implemented; `1609129` aligned the unreleased version with Aspire's major.minor. `9bd2b67` added the patched MessagePack dependency floor and merged in #36. Recheck direct-parent fixes when maintaining that floor; do not silently remove it or introduce general transitive pinning. Refresh exact upstream source against manifests when future compatibility work needs it; the upgrade itself is not unfinished.
- **WS1.5:** CDK asset-upload STS/S3 routing landed in `8fd8c99`, closing #29/#30. The reason for the SDK-hook approach is in [the hosting model](https://github.com/localstack-dotnet/dotnet-aspire-for-localstack/blob/master/docs/architecture/hosting-model.md#the-cdk-asset-uploader-is-redirected-through-aws-sdk-hooks-not-process-environment).
- **WS2:** DynamoDB Streams helper wiring, the asynchronous QR flow, and playground frontends shipped in `13.4.0`. Existing runtime evidence is tied to the tested image. The original AWS 9.3→13.x discovery tasks are historical, not pending work. The October full AWS surface inventory found no new polling family in its interval; changed contracts and useful compositions remain real candidates. Complementary AgentCore and artifact-only publish/build reuse are retained below without a native deployment-parity commitment.
- **WS3A:** package-owned hosting options, canonical configuration, compatibility overloads, immutable hosting state and the read-only endpoint-conflict warning are merged via #36, not yet released in the changelog. That changelog owns the API inventory; [the hosting model](https://github.com/localstack-dotnet/dotnet-aspire-for-localstack/blob/master/docs/architecture/hosting-model.md) owns overload constraints and warning-ordering limits. `LocalStack.Client` remains a runtime dependency; Client-shaped workload env is preserved. No general native-endpoint emission feature was added. DynamoDB Local remains deliberately rejected by enabled global `UseLocalStack()`.
- **WS9:** `internal-docs/` consolidation (`3f04f4f`), consumer-doc correction (`055696e`) and agent/architecture consolidation (`a443aba`, `c70d16f`) are completed. `828fad7` retired the duplicate known-notes file. Test risks live in WS5, architecture rationale in the hosting model, upstream SDK/tool defects in Upstream Watch, and dated primary-source research in the October report. No duplicate known-issues document is needed.

## Open Workstreams

### WS0.5 — Analyzer Arbitration Rebase · Maintenance Backlog

Research the arbitration model in [`Blind-Striker/opencode-sdk-dotnet`](https://github.com/Blind-Striker/opencode-sdk-dotnet), especially its `docs/engineering/quality-gates.md` and `.editorconfig`, then derive a policy for this repository rather than copying it. Candidate direction: shipped defaults for default-on rules, deliberate overrides with reasons and removal triggers, scoped test exceptions, and compilation-wide configuration where disabling an analyzer rather than only its diagnostics is justified by measurement.

**Baseline correction:** Meziantou, BannedApiAnalyzers, VisualStudio.Threading, and NSubstitute analyzers are already installed through the root/test build props; their versions live in `Directory.Packages.props`. `BannedSymbols.txt` already bans `DateTime.Now` and related local-time APIs. This is a policy review, not a missing-package task. Evaluate whether the AWS SDK's mutually exclusive endpoint/region setters warrant additional bans without preventing legitimate configuration paths.

`c86e4fb` already removed the dead `CA1801` entry and corrected the suppression heading. Enumeration still needs review, but that removed entry is historical evidence, not a current defect. Overrides can also become stale; validate them against diagnostics that actually fire, using known-live controls and distinguishing no measurement from a measured absence.

Keep analyzer policy for this repository's build distinct from public API compiler compatibility: net8.0 consumers can still use a C# 12 compiler, which constrains the overload contract documented in the hosting model. Implementation of this rebase is outside the current documentation groom.

### WS3B — Resource-wiring Correctness · Selected Baseline Fixes / Later Extensions

The [architecture/reference-shape map](https://github.com/localstack-dotnet/dotnet-aspire-for-localstack/blob/d0d723c099a723e86a6ed8a9b663173e080bfce5/docs/plans/2026-10-16-aspire-aws-evolution-research.md#2-actual-lifecycle-dataflow-and-adapter-interfaces) and full AWS-family fit are now available. Do not restart the broad investigation. **The first-delivery criterion is correcting an existing promised contract, not supporting only today's samples or rewriting every dispatch path.** Solutions still need focused design and regression evidence.

- **Bare explicit references — implemented, unreleased:** a focused regression reproduced zero Client keys; the callback now emits all nine for explicitly referenced environment/wait destinations without requiring a CF relationship, preserving source identity, stock connection strings and waits. Shared runtime acceptance includes a real Client request from a bare workload.
- **Imports, individual outputs and construct references:** the explicit imported-stack client-dispatch defect is corrected and exercised with an exact output and DescribeStacks-only recorder; no second provisioner was added. Output-only/construct discovery and automatic attachment remain separate later-shape questions. Keep manual official client assignment and whole-parent composition as alternatives, not evidence of broader automatic support.
- **Target-aware helper attachment:** source/model evidence confirms type-name dispatch adds local env without checking retained backend intent. Actual eventual QueueUrl destination remains unmeasured. Preserve the current global opt-in meaning; intent recovery/opt-out/selective routing is a separate compatibility/design question, not an assumed hybrid promise.
- **App-like resource coverage:** Project/Executable-derived apps can qualify; automatic Container/custom-shape discovery and later registrations differ from explicit qualifying references. Retain the capability-based wiring/order investigation, but distinguish contract repair from new automatic coverage. Native SDK configuration is separate from author/workload language and Client env.
- **Ownership and lifecycle:** retain selected-backend identity, pre-marked/repeated-CDK bootstrap waits, late registration, callback precedence and global asset-hook isolation questions. Source-only permutations are not reproduced runtime failures. Do not move callbacks or broaden private-type dispatch merely to tidy code; coordinate necessary fixes with WS4/WS6.

### WS4 — Bugs & Correctness · Baseline Contract Failures / Focused Triage

- **#24 — host/internal-port mismatch:** the managed-consumer regression reproduced host-side localhost/port leakage. Deferred Host/Port expressions now preserve consumer context; real host and Aspire-managed-container Client requests and controlled wrong-target failures passed both profiles. CF/health/assets keep AppHost-side URIs. Docker-socket-spawned/native Lambda reachability and advertised-host behavior are not globally rewritten or certified; do not close that broader topology scope from these checks.
- **#34 — health-check names — implemented, unreleased:** permanent real-DI regression reproduced the duplicate key. Registration and attachment now use resource-qualified literal keys; exact-URI controls prove one resource's failed probe cannot be replaced by another's success. Existing lazy/eager predicates are preserved. This does not certify multi-backend CDK isolation.
- **#35 — Docker socket configuration:** unconditional `DOCKER_HOST` with opt-in/default-off socket mount is source-confirmed; startup failure is not. Preserve the issue and obtain relevant behavioral evidence before changing the variable/default or merely documenting it. Environment configuration is not socket access; native runtime/security implications belong with WS7.
- SDK/tool signing restrictions are external closing-trigger work in [Upstream Watch](https://github.com/localstack-dotnet/dotnet-aspire-for-localstack/blob/master/docs/upstream-watch.md), not reasons to weaken these regressions or silently change regions.

### WS5 — Test Integrity · Shared Runtime Matrix First

- **Common multi-image suite — baseline:** parameterize the same meaningful behavior/integration tests across the verified current default and explicit legacy path. The C# command now invokes the full project, including the six configuration/manifest cases omitted by the former common-only runner. Native legacy execution passed30/30 on Linux with reporting enabled and config reload left unchanged. This verifies that joint configuration, not the independent necessity of class isolation; current and native Windows/macOS qualification remain pending. Keep the independent image oracle and the same-scenario gate.
- **Owned integration scratch storage / tooling correction — partial:** Python tooling and its wrappers have been replaced by a thin C# entry point, a testable tools library and native TUnit session hooks. Linux success, startup-failure and platform-timeout probes left no wrapper directories. External Ctrl+C delivery remains unverified in the headless harness: its readiness-gated signal probe did not reach the managed handler, so the earlier forced termination is not cleanup acceptance or proof of a product root cause. Preserve reporting and diagnostics; do not add signal machinery or mark this migration complete from that probe. The upstream cause and closing trigger live in [Upstream Watch](https://github.com/localstack-dotnet/dotnet-aspire-for-localstack/blob/d0d723c099a723e86a6ed8a9b663173e080bfce5/docs/upstream-watch.md#live-aws-lambda-wrapper-projects-accumulate-in-the-system-temp-directory).
- **Current acceptance blocker:** the latest full shared run passed legacy24/24 but current23/24, with the analytics redirect request hitting its unchanged90-second HTTP timeout. An isolated5/5diagnostic replay did not establish a cause or fix. Preserve both records; do not increase timeouts, add retries/skips or substitute an earlier passing pair. Next diagnostic work needs resolved-instance gateway/Redirector logs and a tighter reproducer; no upstream attribution is established.
- **Latest verification limits:** the new tooling has30TUnitcontrols; all327unitcases pass on each net8/net9/net10 locally, on Linux only. The C# SDK8 source/old-binary consumer and both negative controls passed. A new authenticated runtime run is NOT RUN because activation was unavailable in the current environment/central-source load attempt. The external-consumer Slopwatch SW006, hosted CI, native OS/cancellation checks and fresh whole-baseline review remain open; no suppression or release waiver was added.
- **Release/CI evidence — baseline:** the execution-bound validator and trusted scheduling code are implemented. PRs use secret-free legacy; current uses the dedicated CI secret only on canonical master pushes or explicitly approved exact-SHA manual runs. Feature publication now requires the trusted manual path, not a secret-bearing PR checkout. Both complete clean same-revision profiles gate every publication; actual hosted scheduling, secret configuration and publication remain unexecuted/unvalidated.
- **Callback coverage:** direct CDK, DynamoDB Streams, SQS, bare workload, whole-template and imported-stack dispatch now have behavior controls. Retain further `LOCALSTACK_HOST`/ordering/global-hook coverage as selected work; configurator and runtime-request checks are separate evidence strengths.
- **Fixed waits — implemented:** the three blind analytics sleeps are replaced by bounded AWS-outcome polling, retaining exact counts, event values and PNG assertions. The QR path's bounded status polling remains. This is not claimed as a fix for the separate intermittent redirect HTTP timeout.
- **Reflected-type guards:** `ConstantsTests` already checks existence and expected interfaces/base types for all three AWS internal types. Keep these guards aligned with dependency updates; do not recreate them. Behavior-level dispatch coverage and any shared helper abstraction remain separate work (WS3B/WS6).
- **App-like/reference tests:** retain C#/JavaScript/Node/Vite/Python/container/Dockerfile coverage after WS3B defines each contract. Include bare/whole/import/output/construct and null/disabled controls where selected; no automatic support expansion by test naming.
- **Assertion strength and fixture isolation:** preserve exact Client/Streams env values and actual PNG/analytics outcomes; several facade/configurator tests assert only annotation/object presence. Add focused behavior protection rather than recreating all tests. Retain slow-test grouping, hardcoded region assumptions, shared data and global-hook cleanup/concurrency review beyond the existing non-parallel group.
- **Bootstrap/error/assets acceptance:** extraction/hash/thread tests are not official freshness, multiprocess, actual CF property or custom FILE/ZIP upload proof. Keep real byte/ZIP/readback and error/cancellation/security acceptance as selected work; current samples do not exercise custom assets. The failed research graph fixture executed no assertions and is not a passing regression.
- **Consumer-test guidance:** retain allocated endpoints/readiness/SDK outcomes, persistent-lifetime and isolated parallel-AppHost investigation; add published/local-packed, CPM/file-based and oldest compiler/binary acceptance where selected. Later TS generated-language validation is WS10. Opt-in testing-dashboard APIs are available, but actual fixture MCP discovery remains unmeasured; do not reassert universal in-process invisibility.
- **Historical SQS failure note — unverified:** the old backlog mentioned failing emulator tests without a current reproducer. The tests remain active; this groom did not execute them. Require a failing run before classifying the behavior as a LocalStack limitation or proposing a skip.

The annotation-collection startup race is closed in [Upstream Watch](https://github.com/localstack-dotnet/dotnet-aspire-for-localstack/blob/master/docs/upstream-watch.md#closed-aspire-annotation-collection-race-on-linux-ci); it is not a pending test fix. The separate callback-registration design item remains in WS6.

### WS6 — Refactoring / API Quality · Necessary Changes Only in Baseline

- First-delivery fixes may require focused changes here; independent cleanup remains queued. Preserve C# 12-compatible overloads, non-error legacy adapters, Client-shaped workload env, immutable resolved-state semantics and null/disabled behavior. A newer method name is not a modernization requirement.
- **Container options immutability:** decide whether init-only/record options can coexist with the mutation-based `configureContainer` contract.
- **CDK bootstrap ordering:** `UseLocalStack()` still uses resource Remove/Insert. Evaluate eventing-based ordering rather than assuming a particular hook replaces it correctly.
- **Environment callback timing:** consider registering deferred environment callbacks at model construction, keeping only non-annotation side effects in the connection-string event. Preserve or deliberately redesign precedence: the current late registration makes these callbacks run last. See [the hosting model](https://github.com/localstack-dotnet/dotnet-aspire-for-localstack/blob/master/docs/architecture/hosting-model.md#wiring-is-deferred-to-the-connection-string-callback).
- **Helper dispatch duplication:** evaluate a small internal abstraction for SQS/DynamoDB Streams type names, environment emission, and reference/wait attachment across both dispatch chains. Coordinate with WS3B.
- **Helper visibility:** evaluate hiding implementation-detail resources in coordination with WS8; visibility is distinct from wiring correctness.
- **Health checks:** keep lazy/eager service-aware meaning. Research shows standard HTTP/probes are not drop-in equivalents (status/body/timeout/cancellation/aggregation differ); resource-qualified registration and public custom-check composition can still help. Naming correction belongs to WS4; a larger replacement is not selected.
- **Client warning noise / no-SSO experience:** retain investigation of `"Failed to connect to AWS using AWS SDK config..."`. Configured/default SDK validation, assigned CF clients and separate asset clients are different paths; per-config validation=false is not global no-egress. Prefer truthful safe diagnostics and a proven fix over suppression or process-wide AWS-env mutation.
- **Eager-service error wording:** a missing LocalStack.Client CLI-name mapping currently reports that the service is unsupported by LocalStack. Correct the diagnostic when that work is approved.
- **Package identity:** published builds supply `PackageId=LocalStack.Aspire.Hosting` in CI, but the project file does not own that ID. Align local pack and published identity; any project/CI change needs approval.
- **Container capability exposure:** concrete LocalStackResource has container/file/volume/pull capabilities hidden by normal interface-return generic constraints. Evaluate additive bridges when a selected journey needs them; do not break the public return type or duplicate already-working generic secret/Ready APIs.
- **CDK global ownership:** preserve the rationale for the current SDK hooks, while tracking process-global credentials/pipeline scope, overlap, cleanup and replaced-client disposal limits. Restore-on-stop can clobber another host; no cheap generic external-service replacement or new hybrid guarantee is assumed.
- **Parked:** exposing Client session/configuration helpers publicly has no established hosting-API requirement. Revisit only with a concrete consumer case.

### WS7 — LocalStack Runtime and Platform Tracking · First Delivery + Maintenance

- **#25 — current default / explicit legacy:** implementation is underway. G0 selected current `2026.10.0` and token-free legacy `4.14.0` on Linux/amd64 with supplied current entitlement, followed by independent execution/contract checks. The default, WithLegacy, deferred secret activation and selected contract fixes are implemented; the shared suite grew to24cases with independent image receipts. Earlier public-API pairs passed24/24each, but the newest current run has the WS5 failure above. This remains dirty development evidence, not release certification. Registry/image/tag overrides stay independent, no downgrade is inferred, and legacy support lifetime remains open.
- **Activation/onboarding — baseline:** transfer a user-supplied token securely to the container, not to Client/AWS workload credentials. The existing string-env escape hatch works by source; approved design selects env plus WithAuthToken input, explicit fluent above extra-env above host key, and preserved secret parameters. This is a design choice, not shipped API. Explain missing/invalid/expired activation without exposing values. Account/license management stays with the user/vendor: no implicit browser login, CLI install or global credential/profile mutation. Optional interactions need explicit scope; not every Interaction API shares an experimental gate.
- **Runtime qualification — baseline:** use the [dated platform evidence](https://github.com/localstack-dotnet/dotnet-aspire-for-localstack/blob/d0d723c099a723e86a6ed8a9b663173e080bfce5/docs/plans/2026-10-16-aspire-aws-evolution-research.md#7-runtime-ecosystem-and-official-bootstrap-maintenance), not a fresh broad research loop. Current images require auth even on Hobby; that plan's coverage is neither our product boundary nor a universal release gate. Validate actual bootstrap/CF/CDK/assets and SQS/Streams tool flows under the selected image/entitlements; token alone proves no API/property parity. Missing/invalid/valid activation, safe failure/dependency behavior and actual test totals need acceptance, not weaker health checks.
- **Platform migration effects:** retain new provider/default-engine, CF update/removal, old-state incompatibility, removed in-image tooling, URL/DNS/TLS/signing, license-cache/offline and child-runtime-image risks. The old platform watch's no-token-first trigger predates this product decision; refresh that dated guidance with the owning runtime/docs work. No provider, plan, security or token-free certification follows from public source/registry metadata.
- **CDK bootstrap routine maintenance:** [official-source audit](https://github.com/localstack-dotnet/dotnet-aspire-for-localstack/blob/d0d723c099a723e86a6ed8a9b663173e080bfce5/docs/plans/2026-10-16-aspire-aws-evolution-research.md#71-official-bootstrap-current-payload-is-already-current-maintenance-still-matters) found the current v32 payload already identical to the observed published toolkit template. No production refresh is needed now. Retain exact-source/version/hash and full semantic/IAM review on relevant dependency/release maintenance; equal bytes mean verified/no change. Do not confuse CLI/lib/schema/bootstrap versions or raw Aspire CF defaults with CLI parameter choices. Scheduled advisory drift automation is optional, not approved CI work.
- **Native/licensed capabilities:** retain Lambda debugging/layers/runtime/socket research and feature-specific entitlement acceptance without separate package editions. Native LocalStack Lambda differs from the Test Tool. Container retention, license cache, durable data, persistence volumes, image digest/pull policy and interactive service selection are distinct future choices, not automatic additions to the baseline.

### WS8 — Observability / UX Features · Later Candidates / Distant HQ Vision

- **Future LocalStack HQ / developer workbench:** consolidate dashboard child-resources and the standalone resource-viewer idea (old todo #1/#16). Owner intent: make resources actually running inside the selected LocalStack instance visible and approachable, rather than showing only the emulator container or links to real AWS.
  - Owner-reported UX gap, not yet reproduced in this session: emulated AWS resources are not visible in the Aspire dashboard, and some resource links lead to real AWS. Verify the exact resource/link types and target instance before classifying or fixing the behavior.
  - Long-term quality-of-life direction: discover and browse local stacks, Lambdas, buckets, queues and tables, with useful local details and deliberately scoped test interactions. The Lambda Test Tool's UI for submitting event JSON is the experience reference, not a requirement to reimplement that tool or promise native Lambda support.
  - Evaluate Aspire dashboard integration, existing LocalStack UI/tools/APIs, a thin local companion UI, or a combination before building another console. Preserve allocated-endpoint/backend identity, image/plan compatibility, secret handling and explicit safeguards for mutating actions; no implicit real-AWS fallback.
  - **Future candidate only:** no chosen architecture, implementation scope, delivery date or new priority. Do not build both a dashboard projection and a standalone UI by default. Explore a playground demonstration when this work is selected, consistent with the playground vision above.
- **#26 — provisioning/container escape hatch:** retain init/file/Ready or post-stack seed-completion options for SES and datasets. A completed init stage can contain failed scripts; require actual success and avoid a LocalStack→CF→LocalStack wait cycle. SESv1 API seeding differs from SESv2 CloudFormation identity support; current release notes do not close the issue or establish plan/property acceptance. Research mechanisms exist; a selected package hook still needs design.
- **Dashboard/toolbox adoption:** retain commands, URLs, icons, grouping/parent-child relationships, safe backend explanation and deliberate MCP visibility. Existing Aspire UI/telemetry/history and LocalStack browser/Inspector/tooling should be assessed before building a parallel console/state store. Hidden is not MCP exclusion, and visibility/confirmation is not authorization. Coordinate helper visibility with WS6.
- **Local navigation versus routing correctness:** the reported real-AWS browser links and missing emulated-resource visibility need exact resource/link verification. A display/navigation defect is not proof SDK requests escaped to AWS; an actual routing-contract violation belongs in the baseline, not behind the distant HQ feature.
- Consider a runnable eager-loading playground; current coverage lives in integration tests.
- Preserve service-flow opportunities beyond current samples: ingestion/fan-out, Kinesis workers/indexed views, conditional local Cognito/SES auth, native Scheduler/Web Functions, and official AgentCore emulation with local business data. Use ordinary supported constructs/APIs where possible, not one package wrapper per AWS service; acceptance and vendor prerequisites stay explicit. See the opportunity routing below.

### WS9 — Documentation Maintenance Ownership · Completed Consolidation / Delivery-Aligned Docs

The consolidation is complete (see Completed Work). Remaining feature-specific docs travel with their owning workstream: topology with WS4, consumer-test guidance with WS5, platform/token guidance with WS7, and ecosystem guidance with WS10. New documentation defects can reopen WS9, but resolved template wording and matching image defaults are not outstanding bugs.

Baseline guidance must explain current/legacy choice, token-versus-AWS-credential separation, custom image overrides, migration prerequisites and trusted/fork CI limits, and keep the existing playground journeys runnable. Refresh older platform-watch and eager-service allow-list prose using the October evidence: provider filtering/dependency expansion and requested-service health are not one unconditional set. Research-time statements and issue/publication snapshots remain dated; they are not new runtime certification or policy edits.

### WS10 — Aspire Ecosystem Integration · TS Next After the Baseline

**The broad refresh is complete; delivery design is not.** Use the [October architecture/runtime/AWS/Core research](https://github.com/localstack-dotnet/dotnet-aspire-for-localstack/blob/d0d723c099a723e86a6ed8a9b663173e080bfce5/docs/plans/2026-10-16-aspire-aws-evolution-research.md). The [July document](https://github.com/localstack-dotnet/dotnet-aspire-for-localstack/blob/d0d723c099a723e86a6ed8a9b663173e080bfce5/docs/plans/ws10-aspire-ecosystem-integration-research.md) is historical context, not a current API/setup recipe. The first new product capability after the baseline is **TS AppHost authoring without user-written C#**, not every researched opportunity in one release.

- **First TS contract — design next:** choose the initial authoring/provisioning subset, then preserve add/use/reference, waits/markers, enabled/disabled/null, allocated values, Client env and service-aware health. Creator export alone is insufficient; CF provisioning destinations need a distinct path from env-capable workload reference hooks. Do not promise all CF/CDK projection or rewrite public C# return types by default.
- **Generated/binary acceptance:** validate actual export/loading/typecheck/startup and chosen enabled/disabled/reference journeys plus existing C#/old-compiler consumers. Bundled analyzer opt-in, exports, tags, package loading and catalog discovery are separate. Guest authoring is not SDK-free managed execution or native workload SDK configuration; Go is a separate experimental candidate, not automatic TS parity.
- **Discovery/acquisition/update strategy:** preserve manual package/project consumption as an option. The current prefix restriction and package/assembly/CI identity differences are researched, not solved by adding a tag. CommunityToolkit re-homing or upstream filter widening require a separate choice; neither is a prerequisite assumed for all TS support. Include packed identity/README, project-reference and CPM/file-based consumer evidence; guest update can conditionally replace a project-valued reference, so never promise blanket updater protection.
- **Modern Aspire mechanisms:** use the complete module-fit map rather than re-auditing the whole ecosystem. Thread-safe annotations, Lambda defaults/macOS fixes and other inherited improvements are not duplicate implementation tasks. Custom health, contextual endpoints, files/volume/pull bridges, Ready, secret parameters and diagnostics belong to the selected owning journey; a newer API does not justify an unrelated rewrite.
- Route support extensions/native SDK convenience/hybrid/Compose/UX work to WS3B/WS4/WS5/WS6/WS7/WS8 instead of duplicating tasks. WS3A is merged; it is not an unresolved prerequisite. The baseline's wiring fixes precede TS projection, but no general architecture cleanup is required merely to start that design.

### WS11 — Upstream Reports · External Follow-up

[Upstream Watch](https://github.com/localstack-dotnet/dotnet-aspire-for-localstack/blob/master/docs/upstream-watch.md) owns the signing evidence, tool versions, and closing triggers; keep measurements there rather than reproducing them in this backlog.

- **`aws/aws-lambda-dotnet`:** retain the existing Lambda Test Tool bundled-SDK draft; correct its first-two-rows explanation as in Upstream Watch before sending. The AppHost SDK fix does not update the separate tool graph. Filing remains with Deniz; after filing, attach the URL to the closing trigger. Newer tool UI/REST/reverse-proxy value and install/override policy remain useful separately from signing; no tool installation or region-pin removal is approved here.
- **`aws/integrations-on-dotnet-aspire-for-aws` — Gateway routes:** October source/controlled configuration evidence now explains same-Lambda route-key overwrite; distinct-resource controls survive. Reuse that evidence and obtain the minimal HTTP reproduction needed for a runtime claim before filing. Do not repeat the broad July investigation or treat the missing historical design note as the only evidence.
- **`aws/integrations-on-dotnet-aspire-for-aws` — S3 removal helper:** retain the source-only candidate where the SQS removal convenience calls the creation helper; generic EventType is an alternative. Verify the focused synth/event behavior and duplicate search before proposing an upstream report. No issue was filed or emulator event executed by this groom.

## Research Opportunity Routing

These are the twelve researched opportunity families, not twelve new mandatory workstreams or a release sequence. Existing-contract portions are assessed for the baseline under WS3B/WS4; **TS alone is selected as the next new capability**. All other additions remain candidates with explicit prerequisites/acceptance in the [research cards](https://github.com/localstack-dotnet/dotnet-aspire-for-localstack/blob/d0d723c099a723e86a6ed8a9b663173e080bfce5/docs/plans/2026-10-16-aspire-aws-evolution-research.md#5-twelve-unordered-opportunity-cards).

| Card | Distinct user value retained | Owning work / disposition |
| --- | --- | --- |
| O01 | S3 ingestion and SNS/SQS fan-out lab | WS8/WS3B/WS5; candidate, including hidden native CDK helper prerequisites |
| O02 | Least-knowledge individual construct/output references | WS3B; separate contract repair from new convenience |
| O03 | Read existing local CF stacks without applying them | WS3B/WS5; import contract/manual-client path versus extension |
| O04 | Successful seeded-ready data, not merely edge-ready | WS8 #26/WS7/WS5; candidate with script-success and acyclic waits |
| O05 | Client-compatible and intentional native SDK workload lanes | WS3B/WS4/WS10; preserve Client lane, new native convenience unselected |
| O06 | No SSO surprises and truthful no-egress guidance | WS6/WS3B/WS7; needed baseline diagnostics versus stronger future guarantees |
| O07 | Real FILE/ZIP assets and optional packaged-native parity | WS3B/WS5/WS7; bytes/error proof distinct from optional native Lambda |
| O08 | Kinesis worker and indexed/streamed materialized view | WS8/WS5/WS7; candidate, not an invented new Aspire poller |
| O09 | Official AgentCore emulator with LocalStack business tools | WS8/WS10/WS5; complementary candidate, not native AgentCore parity |
| O10 | Hermetic deployment-artifact drift checks | WS5/WS10/WS6; useful build/reference reuse, not wholesale local deployment |
| O11 | Safe selected-backend explanation/diagnostics | WS8/WS6; candidate, no secret or mutation/routing guarantee |
| O12 | TS authoring with unchanged adapter semantics | WS10; next new capability, initial projection scope still open |

## Historical todo.md Dispositions

These are old **note IDs**, not GitHub issue numbers or test-completion checkboxes. Workstream sections above own the actionable details.

| Note ID | Topic | Disposition |
| --- | --- | --- |
| 1 / 16 | Dashboard resources / standalone viewer | Consolidated in WS8 |
| 2 | Redirect DynamoDB Local to LocalStack | Rejected; existing fail-fast contract |
| 3 | Public `CreateServiceConfig<T>()` / reflection | Parked in WS6 |
| 4 | Container options immutability | WS6 design decision |
| 5 / 6 | Lambda debugging / historical Pro support | WS7 native/runtime/entitlement qualification; no separate integration edition |
| 7 | Client host/port as source of truth | AppHost endpoint ownership implemented; remaining topology defect is WS4 #24 |
| 8 | Callback tests | Partial coverage; remaining gaps in WS5 |
| 9 | Host port from Client `Config.EdgePort` | Retired as an implementation proposal: allocated-endpoint ownership supersedes it; remaining consumer-view mismatch is #24, not a second port source |
| 10 | Remove Client options from `AddLocalStack` | WS3A added owned overloads and deprecated the retained compatibility path |
| 11 / 12 / 13 | Unit review / integration enrichment / slow grouping | WS5 |
| 14 | Reflected AWS-type guards / wrappers | Guards exist; dispatch coverage WS5, abstraction WS6 |
| 15 | Client-side AWS config warning | WS6 |

## Open GitHub Issues

GitHub state snapshot: **2026-10-16**, not re-polled during this groom. The workstream owns the disposition; research and roadmap decisions do not close issues automatically.

| Issue | Topic | Disposition |
| --- | --- | --- |
| #12 | Endpoint interface for bare SDK consumers | Existing connection-string expression covers the recorded scenario; no interface addition selected |
| #24 | `LOCALSTACK_HOST` port mismatch | WS4 baseline contract regression/correction; live network acceptance still needed |
| #25 | New-image auth token | WS7 baseline in progress; qualified image candidates selected, activation wiring/final acceptance pending |
| #26 | SES / container access / provisioning | WS8 + WS7; preserve API-seed versus CF-provider distinction |
| #34 | Shared health-check name | WS4 baseline; existing controlled DI reproduction to become permanent regression |
| #35 | Unconditional `DOCKER_HOST` | WS4 focused behavioral triage, not an assumed startup failure |
| #18 | LocalStack for Azure | Out of scope for this release; separate effort |

## Open Decisions

- Select exact verified current/legacy image tags and any deliberate digest/pull strategy; verify the legacy boundary instead of naming an unexecuted candidate. Determine legacy support lifetime/deprecation separately from vendor backports.
- Implement and verify the approved legacy/token design without breaking generic image overrides or taking over account/login management; secret lifecycle, late-hook/parameter behavior and persistent reuse still need the plan's acceptance evidence. Env+fluent and their precedence are settled, not another product questionnaire.
- Design the common-suite matrix, process/concurrency isolation, trusted CI token handling and fork behavior. Both runtime results are required for release; exact workflow implementation is not approved by this backlog.
- Turn each first-delivery contract failure into a focused reproducer and bounded fix design; classify remaining import/output/container/late-registration behavior against published promises rather than automatically including or excluding it.
- Choose migration communication/release mechanics for an account-requiring default and set the first TS authoring/provisioning scope after the baseline. No release date/version or full CF/CDK export contract is selected here.
- Retain independent choices: container-options immutability/callback/global ownership (WS6), catalog/package strategy (WS10/WS6), persistence/socket/offline policies (WS7), and seed/HQ surfaces (WS8). These do not reopen the agreed current-default/explicit-legacy direction or TS ordering.

## Inbox / Untriaged

Capture new ideas here, then move them to an owning workstream during grooming. Already-triaged catalog discoverability belongs only to WS10.

- Hybrid real-AWS mode: retain per-resource LocalStack ↔ AWS switching as a later candidate, outside the first delivery. `AddExternalService()` was an earlier idea, not a solution to backend identity or process-global CDK hooks.
- LocalStack MCP: a separate server is documented; a container-owned endpoint and automatic Aspire discovery remain unproved. Evaluate token/tool/allocated-endpoint targeting, mutating-action safety and annotations only for a selected workflow; preserve the original watch without pretending no ecosystem MCP exists.
- Optional opt-in Compose publishing for CI while keeping the default dev-only manifest exclusion; feasibility and docker.sock/security implications need research.
- Consumer URL guidance: distinguish proxy-routed API requests from browser-facing/presigned URL generation. The playground uses a LocalStack-aware helper; validate any alternative registration advice before documenting it.
- CodeQL analysis quality: a July run reported low call-target coverage under default C# `build-mode: none`. Re-check the warning before proposing advanced setup; no current quality measurement was made during this groom, and CI changes need approval.
