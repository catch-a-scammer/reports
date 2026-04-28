# Malware & Scam Reports

A public archive of malware campaigns, hiring scams, and other cyber threats targeting developers. Each report includes a detailed technical analysis, indicators of compromise (IOCs), and reporting status.

The goal is to document these threats publicly so that other developers can identify them before falling victim, and to provide ready-made evidence for abuse reports to hosting providers, registrars, and platforms.

---

## Reports

| Date | Threat | Target | Status |
|---|---|---|---|
| 2026-04-28 | [blockstar-hiring](reports/2026-04-28_blockstar-hiring/report.md) | Software developers (fake job) | Reported to GitHub, Hostinger |

---

## Report Structure

Each report lives in `reports/YYYY-MM-DD_short-name/` and contains:

| File | Purpose |
|---|---|
| `report.md` | Full technical writeup |
| `iocs.csv` | Machine-readable indicators of compromise |
| `samples/` | Preserved source code, screenshots, or other evidence |

---

## How IOCs Are Formatted

The `iocs.csv` file in each report uses this schema:

```
type,value,context,first_seen,status
```

Types: `domain`, `ipv4`, `ipv6`, `url`, `sha256`, `email`, `github_repo`, `github_org`

---

## Reporting Channels

When a new threat is identified, reports are filed with:

- **GitHub** — [Report abuse](https://github.com/contact/report-abuse) / [Report malware](https://support.github.com/contact/report-malware)
- **Hosting providers** — via abuse contact (check WHOIS/SOA)
- **Google Safe Browsing** — https://safebrowsing.google.com/safebrowsing/report_badware/
- **URLhaus** — https://urlhaus.abuse.ch/
- **PhishTank** — https://phishtank.org/

---

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on submitting reports, safe analysis workflows, and common attack vectors to check.

---

## Disclaimer

All analysis is performed through static code review only — no malicious code is executed. Malware samples and source code are preserved for documentation purposes. Use this information responsibly and only for defensive purposes.

---

## License

This work is released under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). You are free to share and adapt this material with attribution.
