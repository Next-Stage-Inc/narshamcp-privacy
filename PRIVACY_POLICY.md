# NarshaMCP Privacy Policy

<!-- cspell:ignore CCPA firebasestorage Suyeonggangbyeon daero Haeundae Busan Pseudonymization -->

**Effective Date**: 2026-08-13
**Version**: 2.6
**Issue**: [#3438](https://github.com/Next-Stage-Inc/ue-code-mcp/issues/3438), [#6716](https://github.com/Next-Stage-Inc/ue-code-mcp/issues/6716), [#11118](https://github.com/Next-Stage-Inc/ue-code-mcp/issues/11118), [#15825](https://github.com/Next-Stage-Inc/ue-code-mcp/issues/15825), [#25802](https://github.com/Next-Stage-Inc/ue-code-mcp/issues/25802)

---

## 📋 Summary

NarshaMCP respects your privacy. We collect **pseudonymous usage data only if you explicitly consent**. You can opt out anytime with no impact on functionality.

**Key Points**:
- ✅ **Opt-in by default**: Telemetry disabled unless you consent
- ✅ **Data minimization**: No code, file paths, personal information, or machine-derived fingerprint
- ✅ **Easy withdrawal**: Change your mind anytime
- ✅ **GDPR compliant**: Follows EU data protection regulations
- ✅ **CCPA compliant**: Meets California privacy requirements

---

## 1. What Data We Collect

### 1.1 Data Collection Tiers

NarshaMCP defines three data tiers with increasing scope. Tier 1 (anonymous, opt-in) and Tier 2 (per-call explicit opt-in for diagnostic error reports) are active; Tier 3 is never collected.

| Tier | Scope | Consent | Status |
|------|-------|---------|--------|
| **Tier 1** | Pseudonymous (random per-install UUID; no machine fingerprint) | Opt-in | Active |
| **Tier 2** | Pseudonymized (random install UUID) — diagnostic error reports only | **Per-call** explicit opt-in | Active (Issue #11118) |
| **Tier 3** | Personal data | Never collected | N/A |

**Tier 1 data** (current):
- MCP server version, UE engine version, OS/architecture
- Random UUIDv4 used as the Google Analytics `client_id`; generated locally per NarshaMCP project/install data directory and never derived from hostname, hardware, OS, or architecture
- Tool call counts (tool name, operation name, parameter key names — no parameter values)
- Tool call sequence (anonymous ordinal position within session)
- Session start/end times
- Error categories (types only, not messages)
- Performance metrics (execution time)
- Skill invocation counts (skill name only — no skill arguments or output; Issue #10748). Skill names are sanitized client-side: control characters are replaced with `_` and the value is truncated to 100 characters before transmission.
- Daemon crash events (`daemon_crashed`, Issue #15825): crash reason (`external_kill` / `panic`), OS exit code, engine version (best-effort proxy — the reporting daemon's, as the crash record carries none), and AV probable-cause / Defender threat-name labels — no file paths, no crash message, no backtrace, no tool-call history. Emitted by the *next* daemon startup from local crash records so soak gates are remotely observable.

**Tier 3 — never collected**:
- Source code or search queries
- Project names or file paths
- Function/class names or parameters
- Personal information (name, email, IP address)
- Raw or machine-derived device identifiers and hardware fingerprints. The Tier 1 random UUIDv4 is generated independently for each install and cannot reveal the hostname or hardware. See Section 4.2 for storage details.
- Authentication tokens

### 1.2 Pseudonymous Telemetry (With Your Consent)

If you enable pseudonymous telemetry, we collect Tier 1 data:

| Data Type | Examples | Purpose |
|-----------|----------|---------|
| **Tool usage** | Which MCP tools used, operation names, parameter key names, frequency | Feature prioritization |
| **Skill usage** | Which slash-commands invoked (skill name only — no skill arguments), frequency. Sanitized: control chars stripped, 100-char cap. (Issue #10748) | Skill effectiveness ranking on `/leaderboard` |
| **Performance metrics** | Tool execution time, error rates | Performance optimization |
| **Error categories** | Error types (not messages) | Reliability improvements |
| **Sequence tracking** | Tool call order within session | Workflow analysis |
| **Platform info** | OS, UE version, MCP server version | Compatibility testing |
| **Per-install client ID** | Random UUIDv4, stored locally; not derived from machine properties | GA4 deduplication and aggregate usage measurement |
| **Daemon crashes** | Crash reason (`external_kill`/`panic`), OS exit code, engine version (proxy), AV probable-cause / Defender threat-name label — no paths, no backtrace, no tool history (Issue #15825) | Daemon stability / soak-gate monitoring |

**What we do NOT collect**:
- Source code or search queries
- Project names or file paths
- Function/class names or parameters
- Personal information (name, email, IP address)
- Authentication tokens
- Hostname, hardware serials, MAC address, or any hash derived from those values

> **`daemon_crashed` and `host`**: Tier 1 events carry the random per-install
> GA4 `client_id`, but **no** host/PC-derived identifier. A raw `host` (hostname) plus `anon_install_id` (the
> same pseudonymous install UUID described for Tier 2 in §1.2a) are added **only**
> under the internal-research channel — gated by the three-condition
> `is_internal_research_active` check: the `internal_research` build feature
> **and** `NARSHA_INTERNAL_RESEARCH=1` **and** an authenticated internal-team
> email (`@nextstage.kr` / `@nextstage.co.kr` / `@studionextstage.com`). External
> installs never compile-in or collect a hostname.

### 1.2a Tier 2 — Diagnostic Error Reports (Per-Call Explicit Consent, Issue #11118)

Tier 2 covers a single, narrowly-scoped channel: error reports submitted by
the [`/release-recovery`](../../.claude/skills/release-recovery/SKILL.md) skill
when an end-user explicitly opts in via `--send-telemetry`. Tier 1 standing
consent does **not** authorize Tier 2 submissions; each `--send-telemetry`
invocation requires the user to type the flag themselves.

**Tier 2 payload contents** (full schema: [ERROR_REPORTS_SCHEMA.md](../telemetry/ERROR_REPORTS_SCHEMA.md)):

| Field | Notes |
|-------|-------|
| `narshamcp_version`, `ue_version`, `platform.{os,arch}` | Same shape as Tier 1 |
| `phase_a_severity` (`OK` / `WARN` / `CRITICAL`) | Diagnostic verdict only |
| `scenarios[]`, `issues[]` | Diagnose-stage classifications (e.g. `stale_path`, `mcp_json_invalid`); these are short well-known codes, not free-form |
| `log_tail[]` (≤ 200 entries, masked) | Trailing log lines from `~/.narshamcp/logs/mcp_server.log` AFTER passing the [client masker](../../.claude/skills/release-recovery/scripts/masking.py) |
| `mcp_json_redacted` (single masked string) | The user's `.mcp.json` contents with project + engine + user-home paths replaced by `<PROJECT>` / `<ENGINE>` / `<USER_HOME>` labels |
| `env_vars` (allow-listed keys) | Only env vars matching `^(UECODEGEN_|NARSHAMCP_|RUST_LOG)`. Values whose key ends with `_TOKEN`, `_KEY`, `_SECRET`, `_PASSWORD`, `_PASSWD` are pre-redacted to `<REDACTED>` before transmission |
| `anon_install_id` (sha256 of install UUID) | Pseudonymous; lets us detect "same user reported twice" without identifying who |
| `consent_version` | ISO date of this policy at time of consent. A policy bump invalidates standing local-staging files until the user re-consents. |

**Tier 2 enforcement layers** (defense-in-depth — any single layer's
failure must not produce a leak):

1. **Client masker** — `release-recovery/scripts/masking.py` runs over every
   file before it lands in the support-bundle ZIP and again before payload
   submission.
2. **Mutex with `--no-mask`** — `bundle.py` hard-fails if `--send-telemetry`
   and `--no-mask` are passed together.
3. **Server re-validation** — `ue_telemetry::send_error_report` re-runs the
   static PII pattern set against the inbound payload and rejects any
   match. The Rust pattern list is kept in lockstep with the Python one
   (test parity: `test_masking.py` + `tests::test_pii_detect_covers_all_categories`).
4. **Per-install rate limit** — 1 submission / hour. Prevents accidental
   loops + bulk leakage.
5. **Size cap** — 1 MiB per document. Larger payloads are rejected before
   any disk write or network call.

**Withdrawal**: Tier 2 has no standing state to revoke — there's nothing
between submissions. To delete a previously-submitted document, file a
ticket with the `report_id` returned from the submission; the document
will be deleted within 30 days. Aggregate counters (severity histograms,
scenario frequencies) cannot be retroactively redacted because they are
already irreversibly anonymized.

**Tier 2 retention**: 90 days from `created_at`, then automatic deletion
via Firestore TTL policy. Local staging at `~/.narshamcp/error_reports/`
is **not** auto-cleaned — users may delete those files manually.

### 1.3 Authentication Data (Always Collected)

If you choose to login:

| Data Type | Legal Basis | Purpose |
|-----------|-------------|---------|
| **Email address** | Contract (GDPR Art. 6(1)(b)) | Account identification |
| **User ID** | Contract (GDPR Art. 6(1)(b)) | Authentication |

**Important**: Login is optional. Authentication and telemetry are separate systems.

---

## 2. Legal Basis for Processing

Under GDPR Article 6, we process personal data based on:

### 2.1 Consent (Article 6(1)(a))

- **What**: Anonymous telemetry data
- **When**: Only if you explicitly consent
- **Withdrawal**: Anytime via settings CLI

### 2.2 Contract (Article 6(1)(b))

- **What**: Authentication data (email, user ID)
- **When**: Only if you choose to login
- **Purpose**: Provide authenticated features

**No Bundling**: Telemetry consent is NOT required for login (GDPR Art. 7(4) compliant).

---

## 3. How to Manage Your Privacy

### 3.1 First-Run Consent

On first use, you'll see:

```text
======================================================================
📊 NarshaMCP Telemetry
======================================================================

Help improve NarshaMCP by sending pseudonymous usage data?

✅ What we collect:
  - Tool usage (which tools, frequency)
  - Performance metrics (execution time)
  - Error categories

❌ What we DON'T collect:
  - Your code or search queries
  - Project names or file paths
  - Personal information

Send pseudonymous usage data? [y/N]:
```

- Type **Y** or **yes**: Enable pseudonymous telemetry
- Press **Enter**, type **n/no**, or provide any other answer: Decline telemetry (no data sent)

**MCP STDIO mode** (Fab/plugin installations): Interactive prompts are not available. Telemetry is **disabled by default**. To opt in, use `ue_auth(operation="telemetry_enable")`. The `ue_check_health` tool displays a one-time notice about telemetry availability.

### 3.2 Change Your Settings Anytime

```python
# Check current status (shows sync status if logged in)
ue_auth(operation="telemetry_status")

# Enable telemetry
ue_auth(operation="telemetry_enable")

# Disable telemetry
ue_auth(operation="telemetry_disable")
```

**Sync Status**: If logged in, status command shows:
- Current telemetry level
- Last updated timestamp
- Sync source (local or firestore)
- User email (if synced)
- Login tip (if not logged in)

### 3.3 Environment Variable Override

Force disable telemetry (highest priority):

```bash
# New (recommended)
export NARSHA_TELEMETRY_DISABLED=1

# Old (deprecated, but still works)
export UECODEGEN_TELEMETRY_DISABLED=1
```

---

## 4. Data Storage and Retention

### 4.1 Telemetry Data

- **Storage**: Google Firebase Analytics + BigQuery
- **Retention**: 1 year (auto-deleted after 12 months)
- **Location**: Google Cloud (US)
- **Encryption**: TLS 1.3 in transit, AES-256 at rest

#### 4.1.1 Aggregated Weekly Snapshots (Issue #10418)

For internal dashboard use only, the
[`weekly-telemetry-publish`](../../.github/workflows/weekly-telemetry-publish.yml)
GitHub Actions workflow runs every Sunday 04:00 KST and writes 5 aggregated
JSON artifacts to Firebase Storage at `gs://uecodegen.firebasestorage.app/data/telemetry/`:

- `unified/{YYYY-WNN}.json` — weekly snapshot of skill usage / quality metrics
- `skill_rankings.json` — ranked skill effectiveness scores
- `routing_accuracy_results.json` — per-skill routing precision/recall
- `proxy_satisfaction_results.json` — behavior-derived satisfaction proxy
- `improvement_findings.json` — auto-detected problem patterns

These artifacts contain **only aggregated counts and percentages** — no
session content, no tool arguments, no user identifiers. Read access is gated
by Firebase authentication and a domain whitelist (`VITE_AUTH_ALLOWED_DOMAINS`,
fail-closed) — see [`tools/firebase-dashboard/README.md`](../../tools/firebase-dashboard/README.md)
and [Issue #10419](https://github.com/Next-Stage-Inc/ue-code-mcp/issues/10419).
Source-collection coverage and the current write-path gaps (e.g.,
`tool_executions` is not yet populated by the Rust uploader) are documented in
[`docs/telemetry/COLLECTION_WRITE_PATHS.md`](../telemetry/COLLECTION_WRITE_PATHS.md).

#### 4.1.2 Internal SPA Dashboard (Issue #10428)

The internal SPA dashboard at `https://uecodegen.web.app` (source:
[`tools/firebase-dashboard/`](../../tools/firebase-dashboard/)) **visualizes
the same telemetry data already documented in §4.1.1** — no new data is
collected or exposed beyond what is described in §3 (Data Collection) and
§4.1 (Telemetry Data). The dashboard:

- Restricts access to authenticated users from the configured domain
  whitelist (`VITE_AUTH_ALLOWED_DOMAINS`); external users are unaffected.
- Reads only from existing data sources: Firebase Storage artifacts (§4.1.1),
  GA4 Reporting API (via `ga4Proxy` Cloud Function with same domain check),
  and Firestore collections gated by [`firestore.rules`](../../tools/firebase-dashboard/firestore.rules)
  (which mirror the same allow-list).
- Has 6 tabs: Overview, Tools, Leaderboard, Quality, Realtime, Conversations
  (the originally-planned 7th `/daemon` tab was dropped in
  [PR #10846](https://github.com/Next-Stage-Inc/ue-code-mcp/pull/10846) —
  `dashboard_server` is a local PC tool unrelated to telemetry).
- See [`docs/user-guides/FIREBASE_DASHBOARD_GUIDE.md`](../user-guides/FIREBASE_DASHBOARD_GUIDE.md)
  for per-tab data sources and the new-member onboarding procedure.

### 4.2 Consent Records

**Local Storage** (Always):

Consent is stored **per Unreal project** when NarshaMCP knows which project
it is serving. This matches the existing per-project license token location
and follows Unreal's convention of keeping generated state under `Intermediate/`.

- **Primary location (project-scoped)**: `{project}/Intermediate/NarshaMCP/.telemetry_consent`
  - Deleting the project's `Intermediate/` directory resets NarshaMCP state for
    that project (including consent)
  - Each project tracks its own consent — enabling you to opt into telemetry
    on some projects while opting out on others
- **Fallback (no project)**: `~/.narshamcp/.telemetry_consent`
  - Used by `narshamcp setup`, CLI helpers, and any path that does not know
    the active Unreal project
- **Contents**: Your choice ("anonymous" or "disabled") + timestamp + schema version
- **Retention**: Until you delete it
- **Who has access**: Only you (stored on your machine)
- **Override**: Set `NARSHAMCP_DATA_DIR` to relocate the NarshaMCP data directory (advanced)

When NarshaMCP starts, it also maintains a separate `.telemetry_client_id`
file in the same resolved data directory:

- **Contents**: One randomly generated UUIDv4 used as GA4's `client_id`
- **Isolation**: Two project/install data directories on the same machine receive different values
- **No fingerprinting**: The value is not derived from hostname, hardware, OS, architecture, account, or project contents
- **Transmission**: It is sent only when Tier 1 telemetry is enabled; creation and local storage do not enable telemetry
- **Retention/deletion**: It remains until you delete `.telemetry_client_id` or its containing `Intermediate/NarshaMCP` / `~/.narshamcp` data directory. NarshaMCP generates a new random value on the next start.

> **Migration note (Issue #8796)**: Earlier versions stored the consent file at
> `%APPDATA%\narshamcp\` (Windows) or `~/.narshamcp/` (Unix). On first launch the
> current release **copies** that legacy file into the project-scoped location,
> **never moving it**, so any additional Unreal projects you open keep inheriting
> the original global preference until you explicitly change consent for each.

**Cloud Sync** (Optional - If Logged In):
- **Storage**: Google Firestore (collection: `users/{user_id}/consents/{consent_id}`)
- **Purpose**: Sync consent settings across multiple devices
- **Contents**: Telemetry level, timestamp, device ID (anonymized), client version
- **Retention**: 1 year (auto-deleted after 12 months)
- **Who has access**: Only you (tied to your Firebase account)

**Important**:
- Cloud sync is **optional** - you can use NarshaMCP without login and keep settings local only
- Login is NOT required for telemetry - these are separate systems
- Multi-device sync only activates if you choose to login

### 4.3 Authentication Data

- **Storage**: Firebase Authentication
- **Retention**: Until account deletion
- **Location**: Google Cloud (US)
- **Encryption**: TLS 1.3 in transit, AES-256 at rest

---

## 5. Your Rights (GDPR)

Under GDPR Articles 15-22, you have the right to:

| Right | How to Exercise |
|-------|----------------|
| **Access** | Request copy of your data: privacy@narshamcp.com |
| **Rectification** | Update your email via Firebase console |
| **Erasure** | Delete account: `ue_auth(operation="logout")` + delete `~/.narshamcp/` |
| **Restriction** | Disable telemetry: `ue_auth(operation="telemetry_disable")` |
| **Data portability** | Request export: privacy@narshamcp.com |
| **Object** | Withdraw consent anytime (see Section 3.2) |
| **Automated decisions** | Not applicable (no automated profiling) |

**Response Time**: Within 30 days

---

## 5A. Your Rights (CCPA — California Residents)

Under the California Consumer Privacy Act (CCPA, Cal. Civ. Code 1798.100-199.100):

| Right | Description | How to Exercise |
|-------|-------------|-----------------|
| **Right to Know** | Request what data we collect | privacy@narshamcp.com |
| **Right to Delete** | Request deletion of your data | `ue_auth(operation="telemetry_disable")` + privacy@narshamcp.com |
| **Right to Opt-Out of Sale** | We **never sell** your personal information | N/A — no sale occurs |
| **Right to Non-Discrimination** | Equal service regardless of privacy choices | All features work without telemetry |

**Notice at Collection**: Categories of information collected are described in Section 1.1. With consent, we collect minimized pseudonymous usage statistics solely for product improvement.

**Do Not Sell**: NarshaMCP does **not** sell, share, or disclose personal information to third parties for monetary or other valuable consideration.

---

## 6. Data Sharing

### 6.1 Third-Party Services

We use:

| Service | Purpose | Data Shared | Privacy Policy |
|---------|---------|-------------|----------------|
| **Google Firebase** | Analytics, Auth | Pseudonymous usage with random install UUID; email only if logged in | [Firebase Privacy](https://firebase.google.com/support/privacy) |
| **Google BigQuery** | Analytics storage | Pseudonymous usage and aggregated statistics | [BigQuery Privacy](https://cloud.google.com/bigquery/docs/data-governance) |

### 6.2 No Data Selling

We **never sell** your data to third parties.

---

## 7. Children's Privacy

NarshaMCP is not intended for users under 13. We do not knowingly collect data from children.

If you believe we have collected data from a child under 13, contact us immediately at privacy@narshamcp.com.

---

## 8. International Data Transfers

- **Primary location**: United States (Google Cloud)
- **GDPR compliance**: Google Cloud is GDPR-compliant with Standard Contractual Clauses (SCCs)
- **Your rights**: Same GDPR rights apply regardless of location

---

## 9. Changes to This Policy

We may update this policy to reflect:
- New features or services
- Legal or regulatory changes
- Improved privacy practices

**Notification**: We'll notify you via:
- Updated version number in this document
- Changelog entry
- Consent re-prompt (if material changes)

**History**:
- Version 2.6 (2026-08-13): Replaced the machine-derived GA4 client fingerprint with a random locally stored per-install UUIDv4, changed first-run bare Enter to decline so only typed `y`/`yes` consents, and published a buyer-readable public policy endpoint (Issue #25802)
- Version 2.5 (2026-06-19): Added the Tier 1 pseudonymous `daemon_crashed` event — daemon-death telemetry (crash reason `external_kill`/`panic`, OS exit code, AV probable-cause / Defender threat-name labels; no paths, backtrace, or tool history) so soak gates are remotely observable. Within the existing telemetry consent (no re-consent prompt). The internal-research variant additionally carries `host` + `anon_install_id`, gated on `is_internal_research_active` (Issue #15825)
- Version 2.4 (2026-05-09): Added Tier 2 (per-call explicit opt-in) for `/release-recovery --send-telemetry` diagnostic error reports. New § 1.2a defines the payload, the 5-layer enforcement (client masker, mutex with `--no-mask`, server re-validation, rate limit, size cap), and 90-day retention. Tier 1 status row updated to reflect dual-tier active state (Issue #11118)
- Version 2.3 (2026-05-09): Added § 4.1.2 clarifying internal SPA dashboard visualization scope — domain-whitelist auth, no new data collection beyond § 4.1.1, 6-tab structure post-#10846 (Issue #10428, completes Epic #10417 Phase 4)
- Version 2.2 (2026-05-02): Added § 4.1.1 documenting weekly aggregated telemetry artifacts published to Firebase Storage (Issue #10418, parent Epic #10417)
- Version 2.1 (2026-04-14): Removed machine ID collection from license verification (Issue #8793 — GDPR simplification), added Data Controller address, EU Representative conditional statement
- Version 2.0 (2026-03-15): Added CCPA compliance, data tier definitions, Fab distribution section, MCP STDIO consent UX (Issue #6716)
- Version 1.0 (2026-01-10): Initial GDPR-compliant policy

---

## 10. Contact Us

**Privacy Questions**: privacy@narshamcp.com
**Security Issues**: security@narshamcp.com
**General Support**: https://github.com/Next-Stage-Inc/ue-code-mcp/issues

**Data Controller**:
Next Stage Inc.
814ho, 140 Suyeonggangbyeon-daero, Haeundae-gu, Busan, 48058, Republic of Korea

**EU Representative** (GDPR Art. 27):
Currently, NarshaMCP does not actively target EU users and therefore does not
require an Article 27 Representative. If we begin targeting EU users via Fab
or other channels, an EU Representative will be appointed and listed here.
General data protection inquiries (EU or otherwise) may be sent to
privacy@narshamcp.com — this address is the controller contact, not an
Article 27 Representative.

---

## 11. Technical Implementation

### 11.1 Privacy by Design

- **Default disabled**: Telemetry off unless you consent (GDPR Art. 25)
- **Data minimization**: Only essential data collected (GDPR Art. 5(1)(c))
- **Purpose limitation**: Data used only for stated purposes (GDPR Art. 5(1)(b))
- **Pseudonymization**: Random per-install identifier; no code, file paths, or machine-derived fingerprint (GDPR Art. 4(5))

### 11.2 Consent Requirements

Our consent mechanism meets GDPR Art. 7 requirements:

- ✅ **Freely given**: No impact on functionality if you decline
- ✅ **Specific**: Separate consent for telemetry vs authentication
- ✅ **Informed**: Clear explanation of what we collect
- ✅ **Unambiguous**: Only typed `y`/`yes` enables telemetry; Enter declines
- ✅ **Withdrawable**: Easy opt-out anytime

### 11.3 Security Measures

- **Circuit breaker**: Auto-disable after 3 failures (prevent data leaks)
- **Never crashes**: Telemetry errors don't affect main functionality
- **MCP protocol safe**: No stdout pollution (see [CRITICAL_RULES.md](../reference/CRITICAL_RULES.md))
- **Local consent**: Consent stored on your machine (not transmitted)

---

## 12. GDPR Compliance Checklist

| Requirement | Status | Implementation |
|-------------|--------|----------------|
| **Art. 5(1)(a) - Lawfulness** | ✅ | Consent (telemetry) + Contract (auth) |
| **Art. 5(1)(b) - Purpose limitation** | ✅ | Data used only for stated purposes |
| **Art. 5(1)(c) - Data minimization** | ✅ | Pseudonymous aggregate data only, no code/paths or machine fingerprint |
| **Art. 5(1)(d) - Accuracy** | ✅ | Self-reported consent, user controls |
| **Art. 5(1)(e) - Storage limitation** | ✅ | 1-year retention, auto-deleted |
| **Art. 5(1)(f) - Integrity** | ✅ | TLS 1.3, AES-256, circuit breaker |
| **Art. 6(1)(a) - Consent** | ✅ | Freely given, specific, informed |
| **Art. 7(4) - No bundling** | ✅ | Telemetry separate from login |
| **Art. 13 - Transparency** | ✅ | This privacy policy |
| **Art. 15-22 - User rights** | ✅ | Access, erasure, portability |
| **Art. 25 - Privacy by design** | ✅ | Default disabled, local storage |

---

## 12A. Fab Marketplace Distribution

NarshaMCP is distributed via Epic Games' Fab marketplace. The following applies to Fab users:

- **Default disabled**: Telemetry is off by default. No data is collected until you explicitly opt in.
- **No interactive prompts**: MCP runs in STDIO mode — consent is managed via `ue_auth(telemetry_enable/disable)`.
- **Health check notice**: The `ue_check_health` tool includes a `telemetry_notice` field while consent is `NotConfigured`. Once you enable or disable telemetry, the notice no longer appears.
- **Fab compliance**: Data collection practices comply with Epic Games' marketplace guidelines. Only pseudonymous usage statistics are collected with explicit consent.
- **Uninstall**: Removing the NarshaMCP plugin stops all data collection. Local `.telemetry_consent` and `.telemetry_client_id` files can be deleted manually from the resolved NarshaMCP data directory.

---

## 13. Industry Standards Comparison

NarshaMCP follows best practices from:

| Organization | Standard | Compliance |
|--------------|----------|------------|
| **JetBrains** | [Data Collection v1.4](https://www.jetbrains.com/legal/docs/terms/product_data_collection/) | ✅ Similar opt-in approach |
| **Microsoft VSCode** | [Telemetry](https://code.visualstudio.com/docs/configure/telemetry) | ✅ First-run prompt + settings |
| **GitHub CLI** | OAuth + Usage tracking | ✅ Separate auth/telemetry |
| **OpenTelemetry** | [Security](https://opentelemetry.io/docs/security/handling-sensitive-data/) | ✅ No sensitive data collection |

---

## 14. FAQ

**Q: Will NarshaMCP work if I disable telemetry?**
A: Yes! All features work identically whether telemetry is enabled or disabled.

**Q: Can I login without enabling telemetry?**
A: Yes! Authentication and telemetry are completely separate systems.

**Q: What happens to my data if I disable telemetry?**
A: No new data is sent. Existing data remains in Firebase until auto-deleted after 1 year.

**Q: How do I delete my account entirely?**
A: Logout (`ue_auth(operation="logout")`) and delete `~/.narshamcp/` folder.

**Q: Is my code sent to your servers?**
A: No. We never collect your code, search queries, file paths, or project names.

**Q: Why do you need telemetry?**
A: Pseudonymous aggregate usage data helps us prioritize features and fix bugs that affect the most users.

**Q: Can I trust Google Firebase with my data?**
A: Firebase is used by millions of developers worldwide. We send only the minimized pseudonymous data described in Sections 1 and 4 when you consent.

**Q: Do my consent settings sync across devices?**
A: Yes, if you login! When logged in, your consent settings sync via Firebase Firestore across all your devices. If not logged in, settings are stored locally only.

**Q: What data is synced when I login?**
A: Only your consent choice ("anonymous" or "disabled"), timestamp, anonymized device ID, and client version. No code or personal data.

**Q: Can I use multi-device sync without enabling telemetry?**
A: Yes! You can login (for sync) and disable telemetry. Authentication and telemetry are separate systems.

---

**Last Updated**: 2026-08-13
**Version**: 2.6
**Contact**: privacy@narshamcp.com
