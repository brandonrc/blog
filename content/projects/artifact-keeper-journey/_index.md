---
title: "Four Weeks From Nothing to Open-Source Artifactory Alternative"
date: 2026-02-13T22:00:00-00:00
draft: false
tags: ["artifact-keeper", "rust", "open-source", "ai-assisted", "claude-code", "devops", "artifactory"]
categories: ["Project"]
author: "Brandon Geraci"
showToc: true
TocOpen: false
description: "How I went from grabbing my laptop in bed with an idea to a full open-source artifact registry with 45+ package formats, native mobile apps, end-to-end testing on self-hosted infrastructure, and a Hacker News front page launch -- all in four weeks."
cover:
  image: "/images/artifact-keeper-journey/feature-graphic.png"
  alt: "Artifact Keeper - Your packages. Your servers. Your freedom. 45+ Package Formats, Self-Hosted, Free"
  caption: ""
  relative: false
weight: 1
---

## It Started in Bed

I grabbed my laptop at midnight, propped up on pillows, and started typing. No grand plan. No business case. Just an itch that wouldn't go away: **why does self-hosted artifact management cost so much and lock you in so hard?**

I'd seen teams paying six figures a year for JFrog Artifactory. I'd watched Nexus deployments turn into maintenance nightmares. And I kept thinking -- it's 2026, we have Rust, we have modern tooling, we have AI that can actually help write code. What if someone just... built the thing? Open source, MIT licensed, no enterprise tier, no feature gates.

Four weeks later, [Artifact Keeper](https://github.com/artifact-keeper/artifact-keeper) exists. And what happened in those four weeks still feels surreal.

![Gource visualization of all 569 commits across 8 repositories](/images/artifact-keeper-journey/gource-animation.gif)
*Every file touched across all 8 repos, visualized with Gource. 569 commits in 30 days. [Watch the full quality video (MP4)](/images/artifact-keeper-journey/gource-animation.mp4)*

## The Numbers

Let me just put these here because I still don't fully believe them:

| Metric | Count |
|--------|-------|
| Calendar days | 30 |
| Repositories | 8 |
| Total commits | 569 |
| Lines added | 650,809 |
| Lines removed | 262,385 |
| Net new lines | +388,424 |
| Files changed | 5,586 |
| Package formats | 45+ |
| API operations | 165 |
| Generated SDKs | 5 (TypeScript, Kotlin, Swift, Rust, Python) |
| Unit tests | 874+ |
| PostgreSQL migrations | 48 |

One person. Thirty days. That's not a humble brag -- it's a statement about what's possible right now with the right tools.

## Week 1: The Backend Sprint (Jan 14-20)

The first commit landed January 14th. Rust, Axum, SQLx, PostgreSQL. The core architecture fell into place fast: layered handlers, services, and storage backends. By the end of week one I had:

- Repository CRUD with multi-format support
- User authentication (JWT, API keys)
- Basic artifact upload/download
- The first handful of format handlers: Maven, NPM, PyPI, Docker/OCI

The decision to use Rust wasn't just about performance -- it was about correctness. When you're building something that stores every artifact your organization depends on, you don't want null pointer exceptions at 3 AM.

## Week 2: Format Explosion + Security (Jan 21-31)

This is where things got wild. Format handlers started multiplying: NuGet, Cargo, Go modules, Helm, CocoaPods, Composer, Gradle plugins, Ruby gems, Conda, CPAN, Swift packages, Conan, Dart pub, Vagrant, RPM, Debian, Alpine APK...

Each one implements the native wire protocol. `pip install` just works. `npm publish` just works. `docker push` just works. No special client needed.

I also wired up the security stack:

- **Trivy** for container and filesystem scanning
- **Grype** for vulnerability matching
- **OpenSCAP** for compliance checks
- **Dependency-Track** integration for SBOM analysis
- SSO support: OIDC, LDAP, SAML

And the WASM plugin system -- so anyone can write a custom format handler in any language that compiles to WebAssembly, upload it, and the registry starts handling that format immediately. Hot-reload, sandboxed, no server restart.

## Week 3: The Platform Push (Feb 1-9)

February 1st was a turning point. I went from "backend project" to "platform." In rapid succession:

- **OpenAPI spec** generated from utoipa annotations, with a CI pipeline that auto-generates 5 client SDKs
- **Next.js 15 web frontend** with dark mode, split-pane repository browser, TanStack Query
- **SwiftUI iOS app** with 5-section tab bar, multi-server support, CVE dashboard
- **Kotlin/Compose Android app** with the same feature set
- **Documentation site** on Astro/Starlight
- **Helm charts + Terraform** for Kubernetes deployment

The SDK generation pipeline is one of the things I'm proudest of. Change a handler annotation in Rust, and TypeScript, Kotlin, Swift, Rust, and Python clients all update automatically through CI. The mobile apps consume generated SDKs -- no hand-written API clients.

On **February 3rd**, we cut `v1.0.0-rc.1` across all repos simultaneously.

## Week 4: Production Hardening + Launch (Feb 10-13)

The final push was all about proving it works for real:

- **Self-hosted ARC runners** on my own Kubernetes cluster for E2E testing
- **P2P mesh replication** replacing the old hub-and-spoke edge node architecture
- **874+ unit tests** with 6 bugs found and fixed during the coverage push
- **Artifact health scoring and quality gates** (SonarQube-inspired)
- **Protobuf/BSR format support** -- community requested, built and shipped same day
- OpenTelemetry tracing and Kubernetes health probes

And then I posted it to Hacker News.

## The Hacker News Moment

The [Show HN post](https://news.ycombinator.com/item?id=46909037) hit the front page. 166 points. And the comments were exactly the kind of engagement I was hoping for.

The one that stopped me cold was from **stroebs**:

> Our organization pays in excess of **$500k/year on artifact storage** with JFrog Enterprise+, using 140TB (up from 30TB last year).

Half a million dollars. Per year. For storing packages.

That's not an edge case. That's a common story in enterprise. And it validates every late night and early morning of this project. There's a real problem here, and people are paying real money for solutions that lock them in.

The discussion was honest and valuable. People raised real concerns:

- **"Is it production-ready?"** -- Fair question. It's an RC, not a 1.0. But we have 874 unit tests, end-to-end tests across 10 package formats, and it's running on real infrastructure.
- **"Can one person maintain this?"** -- Also fair. This is why it's MIT licensed with no enterprise tier. The goal is community, not a consulting business.
- **"Is AI-generated code reliable for security-critical infrastructure?"** -- The most important question. My answer: end-to-end testing is what catches bugs, not whether a human or AI wrote the code. We test with real `pip install`, real `docker push`, real `helm install`.

## The Community Response

Within days of the launch, the community showed up:

- **346 stars** on the main repo
- **12 forks**
- **14 discussions** including 10 RFCs from community members
- Feature requests for S3 redirect support, Artifactory migration tooling, and mirroring/proxy capabilities
- Someone asked for Protobuf support -- [I built and shipped it the same day](https://github.com/artifact-keeper/artifact-keeper/pull/119)

The discussions are what excite me most. People aren't just starring and moving on -- they're filing RFCs about [HA and disaster recovery](https://github.com/artifact-keeper/artifact-keeper/discussions/43), [SBOM workflows](https://github.com/artifact-keeper/artifact-keeper/discussions/35), and [migration tooling from Artifactory/Nexus](https://github.com/artifact-keeper/artifact-keeper/discussions/41). That's people thinking about using this for real.

## The AI Question

I built this with [Claude Code](https://claude.ai/code) (Opus 4.6). I'm not going to pretend otherwise or minimize it. This is relevant to the story because **it changes what a single developer can accomplish**.

Here's what AI-assisted development actually looks like in practice:

- I architect. Claude implements. I review, test, iterate.
- Format handlers follow patterns. Once Maven works, teaching the AI to build NuGet follows the same structure.
- The boring parts get automated: OpenAPI annotations, database migrations, error handling boilerplate.
- The interesting parts stay human: architecture decisions, security model design, what to build next.

Could I have built all of this in four weeks without AI? Absolutely not. Not the backend, five frontends, infrastructure-as-code, documentation site, CI/CD pipelines, and end-to-end tests. Not as a solo developer.

But here's the thing -- the AI didn't make the architectural decisions. It didn't decide to use WASM for plugins, or design the peer-to-peer mesh replication, or choose to implement native wire protocols instead of a proprietary upload API. Those decisions are what make the project work, and they came from years of watching what goes wrong with artifact registries in production.

The AI is a force multiplier. But you still need something worth multiplying.

## What's Next

This is just the beginning. The roadmap is driven by what the community is asking for:

- **S3 direct download** (302 redirects for CDN-backed storage)
- **Artifactory/Nexus migration tooling** (import your existing repos)
- **Proxy/mirror mode** (cache upstream registries like npmjs.org, pypi.org)
- **Multi-cloud storage** with CDN integration
- **HA clustering** with automatic failover

If your organization is spending five or six figures on artifact storage, or you're frustrated with the vendor lock-in of commercial registries, [come take a look](https://github.com/artifact-keeper/artifact-keeper). File an issue. Start a discussion. Or just `docker compose up` and see if it works for your use case.

The journey is just starting. And I'm floored by how far Opus 4.6 pushed me -- from grabbing my laptop in bed to full end-to-end testing on my own hardware and hosted containers online. What happens if this keeps going?

I guess we'll find out.

---

**Links:**
- [GitHub: artifact-keeper](https://github.com/artifact-keeper/artifact-keeper)
- [Documentation: artifactkeeper.com](https://artifactkeeper.com)
- [Hacker News Discussion](https://news.ycombinator.com/item?id=46909037)
- [Show HN Post](https://news.ycombinator.com/item?id=46909037)
