# NarshaMCP Privacy Policy

**Effective Date**: 2026-09-29
**Version**: 2.8

---

## 📋 Summary

NarshaMCP respects your privacy. It sends usage data to us **only if you explicitly consent**, and it never sends your code. You can change your mind at any time, and every feature works the same with telemetry turned off.

**Key Points**:
- ✅ **Off by default**: Nothing is sent to us until you opt in
- ✅ **Data minimization**: No source code, file contents, file paths, project names, personal information, or identifier derived from your machine
- ✅ **Easy withdrawal**: Turn it off at any time
- ✅ **GDPR compliant**: Follows EU data protection regulations
- ✅ **CCPA compliant**: Meets California privacy requirements

---

## 1. What Data We Collect

### 1.1 Overview

NarshaMCP sends one kind of data to us: **pseudonymous usage data**, and only after you opt in (Section 3). Everything else it stores stays on your computer (Section 1.4). Separately, it asks GitHub whether a newer version exists (Section 1.5). That request goes to GitHub, not to us.

**We never collect**:
- Source code, file contents, or search queries
- Project names or file paths
- The values you or your AI client pass to tools, other than the operation name described in Section 1.2
- Personal information such as your name or email address
- Hostname, hardware serial numbers, MAC address, or any value derived from them
- Authentication tokens

Like any web request, the requests described in Sections 1.2 and 1.5 reach Google and GitHub with your IP address.

### 1.2 Pseudonymous Usage Data (With Your Consent)

If you opt in, NarshaMCP sends the following to Google Analytics:

| Data | What is sent | Purpose |
|------|--------------|---------|
| **Install ID** | A random UUIDv4 generated on your computer and stored in `.telemetry_client_id` (Section 4.2). It is not derived from your hostname, hardware, OS, account, or project. | Counting installs without identifying anyone |
| **Session** | NarshaMCP version, Unreal Engine version (when known), OS and CPU architecture, a random session ID, session length, and number of tool calls | Compatibility and usage volume |
| **Tool calls** | Tool name and operation name as requested by your AI client, parameter key names (never values), success or failure, execution time, position within the session, and whether NarshaMCP or Epic's Unreal MCP server handled the call | Feature priorities and performance |
| **Errors** | The tool name, an error type from a fixed list (for example `timeout` or `not_found`) and, for build errors, the compiler error code (for example `C2065`), plus execution time and whether the error was recoverable. Never the error message. | Reliability |
| **Startup** | Five yes/no flags describing whether local caches were reused at startup | Startup performance |
| **Crashes** | Crash reason (`external_kill` or `panic`), exit code, NarshaMCP and Unreal Engine versions, OS and CPU architecture, and, when available, an antivirus probable-cause label and the Windows Defender threat name. Sent by the next start of NarshaMCP from a local crash record. No file paths, messages, backtraces, or tool history. | Stability |
| **Flaky-test check** | Only when you run the automation-test flakiness check: a random report ID, the number of runs examined and of flaky tests, a hash of the check's settings, whether the report was built from sample test data, and, for up to 10 flaky tests, the first 16 hexadecimal characters of a SHA-256 hash of the test name. Never the test name itself. | Test reliability features |

Each event also carries a timestamp. Names are shortened to a fixed maximum length before they are sent.

### 1.3 Optional Sign-In

NarshaMCP includes an optional command-line sign-in, `narshamcp auth login`, that uses your Google account. Google handles the sign-in under its own privacy policy. The resulting email address and tokens are stored only on your computer, in `%USERPROFILE%\.uecodegen\credentials.json` on Windows or `~/.config/uecodegen/credentials.json` on macOS and Linux. They are not sent to us, and no NarshaMCP feature requires you to sign in. Run `narshamcp auth logout` to sign out.

### 1.4 Data That Stays on Your Computer

- **Consent record and install ID**: see Section 4.2.
- **Sign-in details**: see Section 1.3.
- **Tool call log**: So that the Unreal Editor can show tool results, NarshaMCP records each tool call, with its arguments and results (large results shortened), in `{project}/Saved/NarshaMCP/tool-call-log.jsonl`. Older copies are kept beside it up to a size limit. The log is not sent anywhere. Set `NARSHAMCP_TOOL_CALL_LOG_DISABLED=1` to turn it off; this does not affect the log files below.
- **Log files**: NarshaMCP writes diagnostic logs, including the names and arguments of tool calls, to `~/.narshamcp/logs/`. A new file is started each day, and old files stay until you delete them. They are not sent anywhere.
- **Diagnostic reports**: The recovery tool can prepare a diagnostic report with masked logs and settings. NarshaMCP saves it in `~/.narshamcp/error_reports/` on your computer and does not send it anywhere. You can delete these files at any time, and whether to share one with us, for example by attaching it to a support email, is up to you.

### 1.5 Update Check

When NarshaMCP starts, it asks GitHub's public API whether a newer release is available, normally at most once a day. This check does not depend on your telemetry choice. The request goes to GitHub, not to us. It carries the NarshaMCP version in its User-Agent header, and GitHub receives your IP address as with any web request (see the [GitHub General Privacy Statement](https://docs.github.com/en/site-policy/privacy-policies/github-general-privacy-statement)). Set `NARSHAMCP_AUTO_UPDATE_DISABLED=1` before starting NarshaMCP to turn the automatic check off. Checking for or downloading an update on request, for example with the self-update tool, also contacts GitHub. An update is downloaded only when you ask for it.

---

## 2. Legal Basis for Processing

Under GDPR Article 6, we process the usage data in Section 1.2 based on:

### 2.1 Consent (Article 6(1)(a))

- **What**: Pseudonymous usage data (Section 1.2)
- **When**: Only if you explicitly consent
- **Withdrawal**: At any time, as described in Section 3.2

**No Bundling**: Telemetry consent is not required to use any NarshaMCP feature (GDPR Art. 7(4)).

---

## 3. How to Manage Your Privacy

### 3.1 First-Run Consent

When NarshaMCP starts in a terminal for the first time, you'll see:

```text
📊 Usage Telemetry

NarshaMCP collects pseudonymous usage data to improve the
tool. No source code, file paths, or personal info is
ever collected.

Collected: tool call counts, operation names,
  parameter names, execution time, routing server
  (NarshaMCP vs Epic), session duration,
  error categories, MCP version, engine version, OS type

Disable on next start: NARSHA_TELEMETRY_DISABLED=1
Privacy policy: public link printed below.

Allow pseudonymous telemetry? [y/N]:
```

The prompt also prints the address of this policy.

- Type **y** or **yes**: Enable pseudonymous telemetry
- Press **Enter**, type **n/no**, or give any other answer: Decline (no data is sent)

The NarshaMCP dashboard asks the same question until you make a choice.

**MCP STDIO mode** (Fab/plugin installations): No prompt can be shown, so telemetry stays **off**. You can opt in later from the dashboard's Settings tab. While you have not made a choice, the `ue_check_health` tool shows a `telemetry_notice`.

### 3.2 Change Your Settings

- **Dashboard**: Open the Settings tab and use the **Privacy & Telemetry** switch. The change takes effect immediately.
- **Kill switch**: Set `NARSHA_TELEMETRY_DISABLED=1` (`true`, `yes`, and `on` also work) before starting NarshaMCP, and restart any process that is already running. The kill switch overrides your consent. The older name `UECODEGEN_TELEMETRY_DISABLED` works the same way. Removing the kill switch does not grant consent.
- **Withdraw by deleting the consent record**: Delete `.telemetry_consent` and restart NarshaMCP. It is in `{project}/Intermediate/NarshaMCP/` when NarshaMCP knows your Unreal project, and in `~/.narshamcp/` otherwise. `NARSHAMCP_DATA_DIR` overrides both locations.
- **Check your current choice**: The dashboard's Settings tab shows it. `ue_check_health` shows `telemetry_notice` only while no choice has been made.

```bash
# Windows (PowerShell)
$env:NARSHA_TELEMETRY_DISABLED = "1"

# macOS / Linux
export NARSHA_TELEMETRY_DISABLED=1
```

---

## 4. Data Storage and Retention

### 4.1 Usage Data

- **Storage**: Google Analytics (Firebase)
- **Retention**: Kept only as long as needed to improve NarshaMCP. Google Analytics deletes it automatically according to our data retention setting.
- **Location**: Google Cloud (US)
- **Encryption**: TLS 1.3 in transit, AES-256 at rest
- **Access**: Only authorized company staff can view the collected data. Statistics we derive from it contain only counts and percentages.

### 4.2 Records on Your Computer

**Consent record**: Consent is stored **per Unreal project** when NarshaMCP knows which project it is serving, following Unreal's convention of keeping generated state under `Intermediate/`.

- **Primary location (project-scoped)**: `{project}/Intermediate/NarshaMCP/.telemetry_consent`
  - Deleting the project's `Intermediate/` directory resets NarshaMCP state for that project, including consent
  - Each project keeps its own choice, so you can opt in for some projects and not others
- **Fallback (no project)**: `~/.narshamcp/.telemetry_consent`
- **Contents**: Your choice ("anonymous" or "disabled"), a timestamp, and format information
- **Retention**: Until you delete it
- **Who has access**: Only you (stored on your machine)
- **Override**: Set `NARSHAMCP_DATA_DIR` to relocate the NarshaMCP data directory (advanced)

**Install ID**: After you explicitly enable telemetry, NarshaMCP keeps a separate `.telemetry_client_id` file in the same data directory. It does not create this file (or its lock file) before you consent, or while `NARSHA_TELEMETRY_DISABLED` / `UECODEGEN_TELEMETRY_DISABLED` is active. A valid file left by an earlier opt-in may be read without being rewritten.

- **Contents**: One randomly generated UUIDv4 used as the Google Analytics client ID
- **Isolation**: Two project/install data directories on the same machine receive different values
- **No fingerprinting**: The value is not derived from hostname, hardware, OS, architecture, account, or project contents
- **Creation and sending**: A missing file is created, and its ID is sent, only when telemetry is enabled. An existing invalid file may be repaired locally.
- **Retention/deletion**: It remains until you delete `.telemetry_client_id` or its containing `Intermediate/NarshaMCP` / `~/.narshamcp` data directory. NarshaMCP generates a new random value on the next start.

---

## 5. Your Rights (GDPR)

Under GDPR Articles 15-22, you have the right to:

| Right | How to Exercise |
|-------|----------------|
| **Access** | Contact business@narshaadk.ai (see the note below) |
| **Rectification** | Contact business@narshaadk.ai (see the note below) |
| **Erasure** | Delete the local records (Section 4.2) and sign out with `narshamcp auth logout`. Usage data already sent carries only a random install ID, so we cannot link it to you; it is deleted automatically (Section 4.1). |
| **Restriction** | Turn telemetry off (Section 3.2) |
| **Data portability** | Contact business@narshaadk.ai (see the note below) |
| **Object** | Withdraw consent at any time (Section 3.2) |
| **Automated decisions** | Not applicable (no automated profiling) |

**Note**: Usage data carries only a random install ID, so we cannot link it to you (GDPR Art. 11). NarshaMCP collects no other data about you.

**Response Time**: Within 30 days

---

## 5A. Your Rights (CCPA — California Residents)

Under the California Consumer Privacy Act (CCPA, Cal. Civ. Code 1798.100-199.100):

| Right | Description | How to Exercise |
|-------|-------------|-----------------|
| **Right to Know** | Request what data we collect | business@narshaadk.ai |
| **Right to Delete** | Request deletion of your data | Delete the local records (Section 4.2). Usage data already sent cannot be linked to you and is deleted automatically (Section 4.1). |
| **Right to Opt-Out of Sale** | We **never sell** your personal information | N/A — no sale occurs |
| **Right to Non-Discrimination** | Equal service regardless of privacy choices | All features work without telemetry |

**Notice at Collection**: The categories of information collected are described in Section 1.2. With consent, we collect minimized pseudonymous usage statistics solely for product improvement.

**Do Not Sell**: NarshaMCP does **not** sell, share, or disclose personal information to third parties for monetary or other valuable consideration.

---

## 6. Data Sharing

### 6.1 Third-Party Services

| Service | Purpose | Data Shared | Privacy Policy |
|---------|---------|-------------|----------------|
| **Google Analytics (Firebase)** | Usage analytics | Pseudonymous usage data with a random install ID, only with your consent | [Firebase Privacy](https://firebase.google.com/support/privacy) |
| **GitHub** | Update check (Section 1.5) | NarshaMCP version; GitHub also receives your IP address as part of the request | [GitHub Privacy](https://docs.github.com/en/site-policy/privacy-policies/github-general-privacy-statement) |

### 6.2 No Data Selling

We **never sell** your data to third parties.

---

## 7. Children's Privacy

NarshaMCP is not intended for users under 13. We do not knowingly collect data from children.

If you believe we have collected data from a child under 13, contact us immediately at business@narshaadk.ai.

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
- Version 2.8 (2026-09-29): Rewrote the policy to describe only what the distributed NarshaMCP build does. Listed every usage event, including the startup cache flags and the flaky-test check. Removed descriptions of things that send nothing to us (diagnostic report upload, account-based settings sync, skill usage counts). Added the update check and the optional command-line sign-in. Updated the ways to change your telemetry choice. Removed BigQuery, which is not used, and described retention by the criteria we apply instead of a fixed period.
- Version 2.7 (2026-08-14): Corrected the instructions for turning telemetry off
- Version 2.6 (2026-08-13): Replaced the machine-derived analytics identifier with a random locally stored per-install UUIDv4, made a bare Enter at the first-run prompt decline so only a typed `y`/`yes` consents, and published this policy at a public address
- Version 2.5 (2026-06-19): Added the daemon crash event
- Version 2.4 (2026-05-09): Described diagnostic error reports
- Version 2.3 (2026-05-09): Clarified who can view aggregated usage data
- Version 2.2 (2026-05-02): Described weekly aggregated usage statistics
- Version 2.1 (2026-04-14): Removed machine ID collection from license verification, added the Data Controller address and the EU Representative statement
- Version 2.0 (2026-03-15): Added CCPA compliance, data tier definitions, the Fab distribution section, and the consent flow for MCP STDIO mode
- Version 1.0 (2026-01-10): Initial GDPR-compliant policy

---

## 10. Contact Us

**Privacy Questions**: business@narshaadk.ai
**Security Issues**: business@narshaadk.ai
**General Support**: narsha-support@narshaadk.ai

**Data Controller**:
Next Stage Inc.
814ho, 140 Suyeonggangbyeon-daero, Haeundae-gu, Busan, 48058, Republic of Korea

**EU Representative** (GDPR Art. 27):
Currently, NarshaMCP does not actively target EU users and therefore does not
require an Article 27 Representative. If we begin targeting EU users via Fab
or other channels, an EU Representative will be appointed and listed here.
General data protection inquiries (EU or otherwise) may be sent to
business@narshaadk.ai — this address is the controller contact, not an
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
- ✅ **Specific**: Telemetry consent is asked for on its own
- ✅ **Informed**: Clear explanation of what we collect
- ✅ **Unambiguous**: Only typed `y`/`yes` enables telemetry; Enter declines
- ✅ **Withdrawable**: Easy opt-out anytime

### 11.3 Security Measures

- **Circuit breaker**: After 3 consecutive network failures, NarshaMCP stops sending usage data until it restarts
- **Never crashes**: Telemetry errors don't affect main functionality
- **MCP protocol safe**: Telemetry never writes to the MCP protocol stream
- **Local consent**: Consent stored on your machine (not transmitted)

---

## 12. GDPR Compliance Checklist

| Requirement | Status | Implementation |
|-------------|--------|----------------|
| **Art. 5(1)(a) - Lawfulness** | ✅ | Consent (usage data) |
| **Art. 5(1)(b) - Purpose limitation** | ✅ | Data used only for stated purposes |
| **Art. 5(1)(c) - Data minimization** | ✅ | Pseudonymous aggregate data only, no code/paths or machine fingerprint |
| **Art. 5(1)(d) - Accuracy** | ✅ | Self-reported consent, user controls |
| **Art. 5(1)(e) - Storage limitation** | ✅ | Kept only as long as needed, auto-deleted |
| **Art. 5(1)(f) - Integrity** | ✅ | TLS 1.3, AES-256, circuit breaker |
| **Art. 6(1)(a) - Consent** | ✅ | Freely given, specific, informed |
| **Art. 7(4) - No bundling** | ✅ | No feature requires telemetry |
| **Art. 13 - Transparency** | ✅ | This privacy policy |
| **Art. 15-22 - User rights** | ✅ | Access, erasure, portability |
| **Art. 25 - Privacy by design** | ✅ | Default disabled, local storage |

---

## 12A. Fab Marketplace Distribution

NarshaMCP is distributed via Epic Games' Fab marketplace. The following applies to Fab users:

- **Default disabled**: Telemetry is off by default. No usage data is sent until you explicitly opt in.
- **No terminal prompt**: MCP runs in STDIO mode, where no prompt can be shown. Opt in or out from the dashboard's Settings tab, or use the kill switch or the consent record (Section 3.2).
- **Health check notice**: The `ue_check_health` tool includes a `telemetry_notice` field while you have not made a choice. Once you enable or disable telemetry, the notice no longer appears.
- **Fab compliance**: Data collection practices comply with Epic Games' marketplace guidelines. Only pseudonymous usage statistics are collected, and only with explicit consent.
- **Uninstall**: Removing the NarshaMCP plugin stops all data collection. Local `.telemetry_consent` and `.telemetry_client_id` files can be deleted manually from the NarshaMCP data directory.

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

**Q: Do I need to sign in?**
A: No. The optional command-line sign-in is not required by any feature, and your sign-in details stay on your computer.

**Q: What happens to my data if I disable telemetry?**
A: No new data is sent. Data already sent stays in Google Analytics until it is deleted automatically (Section 4.1).

**Q: How do I remove the data NarshaMCP keeps on my computer?**
A: Sign out with `narshamcp auth logout`, then delete `~/.narshamcp/` and, in each project, the `Intermediate/NarshaMCP/` and `Saved/NarshaMCP/` folders.

**Q: Is my code sent to your servers?**
A: No. We never collect your code, search queries, file paths, or project names.

**Q: Does NarshaMCP connect to the internet without my consent?**
A: Only to ask GitHub, normally at most once a day, whether a newer version exists (Section 1.5). You can turn that automatic check off.

**Q: Why do you need telemetry?**
A: Pseudonymous aggregate usage data helps us prioritize features and fix bugs that affect the most users.

**Q: Can I trust Google with my data?**
A: Google Analytics is used by millions of developers worldwide. We send only the minimized pseudonymous data described in Section 1.2, and only when you consent.

---

**Last Updated**: 2026-09-29
**Version**: 2.8
**Contact**: business@narshaadk.ai
