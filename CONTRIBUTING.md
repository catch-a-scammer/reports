# Contributing Guidelines

Thank you for helping document malware campaigns targeting developers. This guide covers how to submit a report, what to include, and how to handle malicious code safely.

---

## Before You Start

- **Never execute malicious code.** All analysis must be static — read the source, don't run it.
- **Never clone a suspicious repo normally.** Use `GIT_TEMPLATE_DIR=/dev/null git clone --no-checkout` to prevent hook execution, then inspect files with `git show` or `git ls-tree`.
- **Never include live malware binaries.** Preserve source code as `.txt` files only. If you have binary samples, submit hashes (SHA-256) instead.
- **Never include personal information** about victims, only about the attacker's infrastructure.

---

## How to Submit a Report

### 1. Create the Report Folder

```
reports/YYYY-MM-DD_short-name/
├── report.md        # Full writeup (use templates/report-template.md)
├── iocs.csv         # Machine-readable IOCs
└── samples/         # Source code of malicious files (as .txt)
```

Use the date you performed the analysis, not the date the repo was created. The short name should be the repo name or campaign identifier (e.g., `blockstar-hiring`, `fake-npm-package`).

### 2. Write the Report

Copy `templates/report-template.md` to your report folder as `report.md` and fill in every section. At minimum, every report must include:

- **Summary** — What the repo pretends to be and what it actually does.
- **Attack chain** — Step-by-step breakdown of how a victim gets compromised.
- **Indicators of compromise** — Domains, IPs, URLs, file hashes, GitHub identifiers.
- **Reporting status** — Which platforms have been notified and when.

### 3. Create the IOCs File

The `iocs.csv` must follow this schema:

```csv
type,value,context,first_seen,status
```

| Field | Description | Example |
|---|---|---|
| `type` | Indicator type | `domain`, `ipv4`, `ipv6`, `url`, `sha256`, `email`, `github_repo`, `github_org` |
| `value` | The indicator itself | `evil-domain.com` |
| `context` | What it's used for | `C2 server`, `Payload endpoint` |
| `first_seen` | Date discovered (YYYY-MM-DD) | `2026-04-28` |
| `status` | Current state | `active`, `taken_down`, `sinkholed`, `unknown` |

### 4. Preserve Evidence

Save copies of malicious source code in the `samples/` folder with a `.txt` extension to prevent accidental execution. Include at minimum:

- The malicious scripts/hooks/configs
- The README or instructions that lure the victim

Do **not** include large binary files, PDFs, or images from the repo unless they contain embedded payloads.

### 5. Update the Index

Add a row to the table in the root `README.md`:

```markdown
| 2026-04-28 | [short-name](reports/2026-04-28_short-name/report.md) | Target description | Reported to X, Y |
```

### 6. Submit a Pull Request

Open a PR with a title like: `Report: org/repo-name — threat type`

---

## Safe Analysis Workflow

Here's the recommended workflow for investigating a suspicious repository:

```bash
# 1. Clone without checkout (prevents hook execution)
GIT_TEMPLATE_DIR=/dev/null git clone --no-checkout https://github.com/suspect/repo.git
cd repo

# 2. List all files without touching the working tree
git ls-tree -r --name-only HEAD

# 3. Read individual files safely
git show HEAD:path/to/file

# 4. Check commit history for suspicious patterns
git log --all --oneline

# 5. Look for hidden files and unusual directories
git ls-tree -r --name-only HEAD | grep -E '^\.|/\.'

# 6. Inspect hooks, CI configs, and build scripts
git show HEAD:.githooks/       # Custom hooks
git show HEAD:.github/workflows/ # GitHub Actions
git show HEAD:package.json     # npm scripts (postinstall, preinstall)
git show HEAD:Makefile          # Build targets

# 7. Check for obfuscated or encoded content
git show HEAD:suspicious-file | base64 -d  # If base64 encoded
git show HEAD:suspicious-file | xxd | head  # Hex dump

# 8. DNS/WHOIS on any domains found
dig suspect-domain.com A +short
dig suspect-domain.com NS +short
whois suspect-domain.com

# 9. Clean up — remove the repo when done
cd .. && rm -rf repo
```

---

## Common Attack Vectors to Check

When analyzing a repo, always inspect these files and patterns:

| Vector | What to Look For |
|---|---|
| **Git hooks** | `.githooks/`, `.husky/`, or instructions to set `core.hooksPath` |
| **npm lifecycle scripts** | `preinstall`, `postinstall`, `prepare` in `package.json` |
| **Python setup hooks** | `setup.py` with `cmdclass` overrides, `__init__.py` with imports that execute on load |
| **Makefiles** | Targets that download and execute remote code |
| **GitHub Actions** | Workflows that exfiltrate secrets or run on `pull_request_target` |
| **Docker** | `Dockerfile` or `docker-compose.yml` that pulls from suspicious registries |
| **Environment files** | `.env` files or scripts that set env vars pointing to C2 |
| **Obfuscation** | Base64 blobs, hex-encoded strings, `eval()`, minified one-liners |
| **Encoded in assets** | Payloads hidden in images (steganography), PDFs, or font files |

---

## Reporting Channels

Once your analysis is complete, report to as many channels as applicable:

| Channel | URL | What to Report |
|---|---|---|
| GitHub Abuse | https://github.com/contact/report-abuse | The malicious repository |
| GitHub Malware | https://support.github.com/contact/report-malware | The malicious repository |
| Hosting Provider | Check WHOIS/SOA for abuse email | The C2 domain and hosting account |
| Google Safe Browsing | https://safebrowsing.google.com/safebrowsing/report_badware/ | Malicious URLs |
| URLhaus | https://urlhaus.abuse.ch/ | Malware distribution URLs |
| PhishTank | https://phishtank.org/ | Phishing pages |
| VirusTotal | https://www.virustotal.com/ | File hashes and URLs for community flagging |
| National CERT | Varies by country | Significant campaigns |

Update the **Reporting Status** table in your report as you file each report.

---

## Code of Conduct

- This repo exists for **defensive purposes only**. Do not use the information here to attack, harass, or dox anyone.
- Do not interact with or contact the threat actors directly.
- Do not include speculation about real-world identities of attackers — stick to technical indicators and known threat group attributions from published research.
- If you discover a campaign that appears to be actively compromising people, prioritize reporting to platforms and CERTs before writing a public report.
