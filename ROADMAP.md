# LocalStack .NET roadmap

Last reviewed: 2026-10-19

This is the organization view of priorities and dependencies across the LocalStack .NET projects. **Now, Next and Later are planning stages, not release promises.** A proposal or open issue needs an accepted scope before delivery; merged source, a published package and verified runtime behavior are separate milestones. Product roadmaps own detailed sequencing and acceptance criteria.

## Products and current baseline

| Product | Role and source of detail |
| --- | --- |
| [Client](https://github.com/localstack-dotnet/localstack-dotnet-client) | AWS client configuration for LocalStack and dependency injection. [Architecture and support boundary][client-platform]; current proposals below. |
| [Aspire Hosting](https://github.com/localstack-dotnet/dotnet-aspire-for-localstack) | AppHost integration, resources and application wiring. [Product roadmap][aspire-roadmap]. |
| [BadgeSmith](https://github.com/localstack-dotnet/badge-smith) | Package and test badges; an integration consumer and deployment example. [Product roadmap][badge-roadmap]. |
| [Serverless Demo](https://github.com/localstack-dotnet/localstack-serverless-dotnet-demo) | End-to-end application examples. [Demo roadmap phases][demo-roadmap]. |

The public NuGet stable baseline at review is **LocalStack.Client 2.0.0**, **LocalStack.Client.Extensions 2.0.1** and **LocalStack.Aspire.Hosting 13.4.0**. Aspire [PR #36][aspire36] is merged; its 13.6.0 source version is not present in the public stable feed at this snapshot. Release and consumer planning must check the intended feed/channel again. [Client feed][client-feed], [Extensions feed][extensions-feed], [Aspire feed][aspire-feed], [Aspire changelog][aspire-changelog].

## Now — qualify the leading candidates

### Client: Native AOT and framework direction

Native AOT friendliness and .NET 10/framework modernization lead the Client candidate queue. First assess the [draft AOT PR #49][aot49] against current source and define supported entry points, frameworks, SDK versions and publish/run evidence. Generator and AOT-console files exist in the draft; their presence and its unchecked checklist establish neither completion nor current AOT support.

Keep these questions separable:

- Core LocalStack-mode metadata discovery and client/configuration construction still use [reflection][core-reflection].
- Extensions AWS-mode uses an [internal SDK factory][extensions-factory]. The deferred public `AWSOptions.CreateServiceClient<T>()` alternative needs its own options fallback, logging and DI behavior decision, as the [accepted ADR][factory-adr] explains.
- Extensions [configuration binding][extensions-binding] and supported overloads need trimming/AOT evaluation independently of the factory.
- The generator/UnsafeAccessor strategy remains a proposal. Removing warning annotations or the linker descriptor is conditional on proven replacement paths and trimming/AOT evidence.

Define .NET 10 adoption and the continuing support/migration policy for existing frameworks together. Core already targets **net472**, alongside netstandard2.0/net8.0/net9.0; Extensions targets netstandard2.0/net8.0/net9.0. .NET Framework support is an existing compatibility decision to revisit, rather than a missing target to add. [Core project][core-project], [Extensions project][extensions-project].

### Shared baseline: authenticated current-image testing

Agree a reproducible image matrix before treating dependency upgrades as validated. Client currently pins older functional images (3.7.1/4.6.0), while Aspire declares 4.12.0. Those declarations do not prove compatibility with a current image. [Client quality gates][client-quality], [Aspire image declaration][aspire-image].

LocalStack's [2026.03.0 release][auth-release] documents the unified-image Auth Token boundary. A current-image test profile needs an exact tag/digest, applicable entitlement/service scope, CI token ownership and injection, trusted-event/fork behavior, and selected legacy regression coverage. Decide these explicitly; current account entitlement and CI provisioning are not established here. **LocalStack Auth Tokens activate the platform; AWS API credentials configure/sign service requests.** Keep their setup and troubleshooting distinct. [Token documentation][auth-tokens], [AWS-compatible credentials][aws-credentials]. Client [#51][client51] and Aspire [#25][aspire25] share this dependency but own different integration surfaces.

## Next — execute validated upgrades and consumer adoption

Follow the actual dependency graph: **LocalStack image → Aspire Hosting integration → BadgeSmith and applicable demos**, plus direct Client consumers. Select the latest compatible published channel and record reproducible pins; for **AWSSDK.Core**, deliberately evaluate a recent, widely downloaded compatible candidate instead of automatically choosing the highest version. The [Client maintenance playbook][maintenance-playbook] describes the review procedure.

Refresh each consumer's declared references, then separately establish the restored transitive graph and tested runtime. The checked-in baseline illustrates why each edge matters:

| Consumer | Declared LocalStack dependencies at review |
| --- | --- |
| [Aspire source][aspire-packages] | Client 2.0.0 / Extensions 2.0.1; source dependencies differ from the [published 13.4.0 package][aspire-nuspec]. |
| [BadgeSmith][badge-packages] | Aspire Hosting 13.4.0; Client 2.0.0 / Extensions 2.0.1. |
| [Serverless Demo][demo-packages] | Aspire Hosting 13.4.0; Client 2.0.0 / Extensions 2.0.0, with [direct application references][demo-profile-project] and an [AppHost using the older overload][demo-apphost]. |

For each accepted upgrade, qualify the relevant client creation/configuration, AppHost wiring and application scenario before widening support claims. Refresh AWS service/catalog mappings and consumer guidance as part of maintenance. Evaluate transitive audit visibility and any dependency fixes against a fresh graph, distinguishing test-only exposure from published libraries; historical pin suggestions do not establish current vulnerabilities or effective fixes. Keep migration guidance clear about v2/SDK v4 and historical version mixing without restoring [ended v1 support][client-security].

## Later — proposals retained under their product owners

| Lane | Candidate groups and boundaries |
| --- | --- |
| Client configuration | Preserve separate investigations for [presigned S3 URLs (#50)][client50], [native AWS endpoint overrides (#27)][client27], [configuration parity (#32)][client32], [application environment precedence (#25)][client25] and [legacy-port migration (#26)][client26]. Related symptoms do not establish duplicates or one remedy. |
| Aspire reliability and API | Reproduce [socket/DOCKER_HOST (#35)][aspire35], [multi-resource health identity (#34)][aspire34] and [host/container port topology (#24)][aspire24]. Product roadmap work also covers backend intent/auto-wiring, callback/bootstrap/lifecycle tests, options/CDK ordering and package/API quality. |
| Aspire integration research | Licensed features/Lambda debugging and persistence/image options; SES provisioning [#26][aspire26]; dashboard/discovery; endpoints [#12][aspire12]; analyzer, TypeScript/ATS and catalog strategy; upstream Lambda Test Tool/Core and API Gateway reproducers. Hybrid mode, Compose publication, browser/presigned guidance and CodeQL rechecks remain inbox proposals. [Product roadmap][aspire-roadmap], [upstream watch][aspire-upstream]. |
| BadgeSmith | Qualify exact deployed artifact/source and effective hosted runtime before claiming ARM64/AL2023 or INIT/memory improvements. The [deploy-and-smoke workflow succeeded][badge-deploy]; complete provenance and performance remain unproven. Keep measurement design separate from a deferred performance CLI. Operational candidates include logging, retention/PITR, abuse controls, retries/alerts and publication/action polish. [#1][badge1] is a preventive route-buffer guard; [#4][badge4] requests CI prerelease-channel selection. [Product roadmap][badge-roadmap]. |
| Serverless Demo | Its foundation is complete in source. Lambda deployment, API Gateway, Blazor UI and modular CDK examples precede proposed tests/CI, a CloudFormation alternative and advanced multi-region/EventBridge/Step Functions scenarios in its [own phases][demo-roadmap]. The [production-deployment question (#8)][demo8] does not accept that scope or promise an AWS rollout. |

Completed repairs and rejected approaches stay in product history. In particular, the Client constructor repair/Extensions 2.0.1 release and existing SDK canary are not new backlog; the rejected pre-floor SDK compatibility track stays retired. [Client changelog][client-changelog], [compatibility ADR][factory-adr].

## Needs decision — scope and supporting repositories

- **Support and acceptance:** choose the AOT/framework contract, current-image matrix/CI identity, breaking-change migration policy and product-specific acceptance criteria before implementation or support announcements.
- **Product direction:** decide whether LocalStack for Azure ([Aspire #18][aspire18]) belongs in a separate product effort, and distinguish demo examples from production deployment guidance.
- **Supporting repositories:** [localstack-docs][docs-readme] contains [documentation content][docs-content] despite its starter README; maintenance/upstream ownership needs clarification before README cleanup, coverage/tutorial polish or Glue/MSK examples become organization commitments. [akkadotnet-code-samples][akka-readme] credits and routes readers to Petabridge; its maintenance role is also pending. Existing inventory-change code does not become backlog merely because nearby comments say TODO.
- **Historical repositories:** archived [localstack-nuget-badge-lambda][old-badge-readme] is superseded by BadgeSmith; archived [localstack-awscli-local][old-cli-readme] preserves the old CLI wrapper. Neither is scheduled for reactivation. Historical badge queries/Gist contracts require a new consumer need before any parity work is proposed.

Update this organization view when priorities, cross-product dependencies or evidence change. Keep detailed tasks and delivered history in their owning repositories, and label source changes, package publication and runtime verification separately.

[client-platform]: https://github.com/localstack-dotnet/localstack-dotnet-client/blob/e0707e00c7fd3624f7b5483c89943a28d175f139/docs/architecture/platform-and-packaging.md
[client-quality]: https://github.com/localstack-dotnet/localstack-dotnet-client/blob/e0707e00c7fd3624f7b5483c89943a28d175f139/docs/engineering/quality-gates.md
[client-feed]: https://api.nuget.org/v3-flatcontainer/localstack.client/index.json
[extensions-feed]: https://api.nuget.org/v3-flatcontainer/localstack.client.extensions/index.json
[aspire-feed]: https://api.nuget.org/v3-flatcontainer/localstack.aspire.hosting/index.json
[aspire-roadmap]: https://github.com/localstack-dotnet/dotnet-aspire-for-localstack/blob/master/docs/ROADMAP.md
[badge-roadmap]: https://github.com/localstack-dotnet/badge-smith/blob/master/docs/ROADMAP.md
[demo-roadmap]: https://github.com/localstack-dotnet/localstack-serverless-dotnet-demo/blob/master/README.md#roadmap
[aspire36]: https://github.com/localstack-dotnet/dotnet-aspire-for-localstack/pull/36
[aspire-changelog]: https://github.com/localstack-dotnet/dotnet-aspire-for-localstack/blob/7ad7698e9f8c5c5e1ee265f405a2e7d3ea49bc66/CHANGELOG.md
[aot49]: https://github.com/localstack-dotnet/localstack-dotnet-client/pull/49
[core-reflection]: https://github.com/localstack-dotnet/localstack-dotnet-client/blob/e0707e00c7fd3624f7b5483c89943a28d175f139/src/LocalStack.Client/Utils/SessionReflection.cs
[extensions-factory]: https://github.com/localstack-dotnet/localstack-dotnet-client/blob/e0707e00c7fd3624f7b5483c89943a28d175f139/src/LocalStack.Client.Extensions/AwsClientFactoryWrapper.cs
[extensions-binding]: https://github.com/localstack-dotnet/localstack-dotnet-client/blob/e0707e00c7fd3624f7b5483c89943a28d175f139/src/LocalStack.Client.Extensions/ServiceCollectionExtensions.cs
[factory-adr]: https://github.com/localstack-dotnet/localstack-dotnet-client/blob/e0707e00c7fd3624f7b5483c89943a28d175f139/docs/adr/0002-aws-setup-constructor-compatibility.md
[core-project]: https://github.com/localstack-dotnet/localstack-dotnet-client/blob/e0707e00c7fd3624f7b5483c89943a28d175f139/src/LocalStack.Client/LocalStack.Client.csproj
[extensions-project]: https://github.com/localstack-dotnet/localstack-dotnet-client/blob/e0707e00c7fd3624f7b5483c89943a28d175f139/src/LocalStack.Client.Extensions/LocalStack.Client.Extensions.csproj
[aspire-image]: https://github.com/localstack-dotnet/dotnet-aspire-for-localstack/blob/7ad7698e9f8c5c5e1ee265f405a2e7d3ea49bc66/src/Aspire.Hosting.LocalStack/Container/LocalStackContainerImageTags.cs
[auth-release]: https://blog.localstack.cloud/localstack-for-aws-release-2026-03-0/
[auth-tokens]: https://docs.localstack.cloud/aws/getting-started/auth-token/
[aws-credentials]: https://docs.localstack.cloud/aws/connecting/credentials/
[maintenance-playbook]: https://github.com/localstack-dotnet/localstack-dotnet-client/blob/master/docs/engineering/maintenance-playbook.md
[aspire-packages]: https://github.com/localstack-dotnet/dotnet-aspire-for-localstack/blob/7ad7698e9f8c5c5e1ee265f405a2e7d3ea49bc66/Directory.Packages.props
[aspire-nuspec]: https://api.nuget.org/v3-flatcontainer/localstack.aspire.hosting/13.4.0/localstack.aspire.hosting.nuspec
[badge-packages]: https://github.com/localstack-dotnet/badge-smith/blob/fdf4c4a13df477bae6d4607ecfd922469faec2cc/Directory.Packages.props
[demo-packages]: https://github.com/localstack-dotnet/localstack-serverless-dotnet-demo/blob/6bc6fdfb6fa7c2691104cfd22e4f2a89525ee29a/Directory.Packages.props
[demo-profile-project]: https://github.com/localstack-dotnet/localstack-serverless-dotnet-demo/blob/6bc6fdfb6fa7c2691104cfd22e4f2a89525ee29a/src/LocalStack.Services.ProfileApi/LocalStack.Services.ProfileApi.csproj
[demo-apphost]: https://github.com/localstack-dotnet/localstack-serverless-dotnet-demo/blob/6bc6fdfb6fa7c2691104cfd22e4f2a89525ee29a/src/LocalStack.Host/AppHost.cs
[client-security]: https://github.com/localstack-dotnet/localstack-dotnet-client/blob/e0707e00c7fd3624f7b5483c89943a28d175f139/.github/SECURITY.md
[client-changelog]: https://github.com/localstack-dotnet/localstack-dotnet-client/blob/e0707e00c7fd3624f7b5483c89943a28d175f139/CHANGELOG.md
[aspire-upstream]: https://github.com/localstack-dotnet/dotnet-aspire-for-localstack/blob/7ad7698e9f8c5c5e1ee265f405a2e7d3ea49bc66/docs/upstream-watch.md
[badge-deploy]: https://github.com/localstack-dotnet/badge-smith/actions/runs/39049154134
[client51]: https://github.com/localstack-dotnet/localstack-dotnet-client/issues/51
[client50]: https://github.com/localstack-dotnet/localstack-dotnet-client/issues/50
[client27]: https://github.com/localstack-dotnet/localstack-dotnet-client/issues/27
[client32]: https://github.com/localstack-dotnet/localstack-dotnet-client/issues/32
[client25]: https://github.com/localstack-dotnet/localstack-dotnet-client/issues/25
[client26]: https://github.com/localstack-dotnet/localstack-dotnet-client/issues/26
[aspire25]: https://github.com/localstack-dotnet/dotnet-aspire-for-localstack/issues/25
[aspire35]: https://github.com/localstack-dotnet/dotnet-aspire-for-localstack/issues/35
[aspire34]: https://github.com/localstack-dotnet/dotnet-aspire-for-localstack/issues/34
[aspire24]: https://github.com/localstack-dotnet/dotnet-aspire-for-localstack/issues/24
[aspire26]: https://github.com/localstack-dotnet/dotnet-aspire-for-localstack/issues/26
[aspire12]: https://github.com/localstack-dotnet/dotnet-aspire-for-localstack/issues/12
[aspire18]: https://github.com/localstack-dotnet/dotnet-aspire-for-localstack/issues/18
[badge1]: https://github.com/localstack-dotnet/badge-smith/issues/1
[badge4]: https://github.com/localstack-dotnet/badge-smith/issues/4
[demo8]: https://github.com/localstack-dotnet/localstack-serverless-dotnet-demo/issues/8
[docs-readme]: https://github.com/localstack-dotnet/localstack-docs/blob/2fcff49874f084fb085f36d331385e227aac3c2e/README.md
[docs-content]: https://api.github.com/repos/localstack-dotnet/localstack-docs/git/trees/2fcff49874f084fb085f36d331385e227aac3c2e?recursive=1
[akka-readme]: https://github.com/localstack-dotnet/akkadotnet-code-samples/blob/d8d1d7626e9187dfa50fae660cc68e26e897f807/README.md
[old-badge-readme]: https://github.com/localstack-dotnet/localstack-nuget-badge-lambda/blob/865e520d862b33f816f9b0aab0ee66927e782042/README.md
[old-cli-readme]: https://github.com/localstack-dotnet/localstack-awscli-local/blob/1e3b9cc7ed3bf1ed57300c11ce204ae92f7c88b2/README.md
