# 🔍 Google Dorking

> **MOC:** [[MOC - Reconnaissance]] | **Tags:** `#google-dorking` `#osint` `#reconnaissance`
> Back to [[Reconnaissance - Overview]]

---

## Definition

**Google Dorking** (also called **Google Hacking**) uses **advanced Google search operators** to uncover sensitive files, login pages, databases, and other hidden information that is **publicly accessible but not intended to be easily discovered**.

> 💡 Reveals hidden, confidential, or mistakenly indexed data.

---

## Key Features

- Uses **Google's own indexing** against websites with poor security
- **Legal to search** — the data is publicly indexed
- ⚠️ Using found data maliciously is **illegal**
- Google Hacking Database (GHDB) — can't access live camera feeds since 2017

---

## Operators Reference

| Operator | Function | Example |
|---|---|---|
| `site:` | Limit search to a specific site | `site:target.com` |
| `filetype:` | Find specific file types | `filetype:pdf "confidential"` |
| `inurl:` | Search within URL | `inurl:wp-admin` |
| `intitle:` | Search page titles | `intitle:"index of"` |
| `intext:` | Search page body text | `intext:"password"` |
| `link:` | Find pages linking to a URL | `link:target.com` |
| `cache:` | View Google's cached version | `cache:target.com` |
| `allintext:` | All words in body text | `allintext:"username password"` |
| `ext:` | File extension (same as filetype) | `ext:xls "salary"` |
| `related:` | Find related websites | `related:target.com` |
| `OR` | Either term | `"admin" OR "login"` |
| `AND` | Both terms | `inurl:admin AND filetype:php` |

---

## Top Google Dorking Tools

1. **Google Hacking Database (GHDB)** — collection of known dorks
2. **DorkScanner** — automated dork scanning
3. **SQLmap** — for database-related dorks
4. **GoogD0rker** — automated Google dorking
5. **Google Dork Automation** — scripted dorking

---

## 10 Different Advanced Operators for Google Dorking

*(Know at least 10 for assignments)*

| # | Operator | Example | What It Finds |
|---|---|---|---|
| 1 | `filetype:pdf` | `site:corp.com filetype:pdf "internal"` | Internal PDF documents |
| 2 | `filetype:xls` | `site:corp.com filetype:xls "salary"` | Salary spreadsheets |
| 3 | `inurl:wp-admin` | `site:target.com inurl:wp-admin` | WordPress admin panel |
| 4 | `inurl:wp-login.php` | `site:target.com inurl:wp-login.php` | WordPress login page |
| 5 | `intitle:"index of"` | `intitle:"index of" /backup` | Directory listings |
| 6 | `intext:"password"` | `site:target.com intext:"password"` | Pages with passwords |
| 7 | `ext:log` | `site:target.com ext:log` | Exposed log files |
| 8 | `ext:env` | `site:target.com ext:env` | Environment config files |
| 9 | `allintext:username password` | `site:target.com allintext:username password` | Login credential hints |
| 10 | `cache:` | `cache:target.com` | Cached old version of page |

---

## Google Dorking for Ethical Hacking & OSINT

1. **Finding leaked credentials** — exposed password files
2. **Identifying publicly available documentation** — internal docs
3. **Checking for misconfigured servers** — directory listings
4. **Tracking down website vulnerabilities** — login pages, old software
5. **Investigating cyber threats** — threat actor profiles

---

## Dangers of Google Dorking

- Aids (inadvertently) in **data breaches, identity theft, cyber espionage**
- Can **reveal vulnerabilities** exploitable by attackers
- Reveals sensitive documents, login panels, databases

---

## ⚖️ Legal Note

> Know the **Google laws** so you can legally get into exposed info without violation.
> Searching is legal; **exploiting** the found data without authorization is **illegal**.

---

## How to Prevent Google Dork Infiltration

1. **Restrict information** on public-facing sites
2. **Implement a robust robots.txt file**
3. **Use "NoIndex" and "NoFollow" tags**
4. **Regularly conduct website audits**
5. **Limit file and directory permissions**

---

## FastRoute.inc Case — Dork Examples

*(From your assignment Q - See [[QA - Reconnaissance Scenarios]])*

**1. Exposed Internal Documents (PDFs):**
```
site:fastroute.inc filetype:pdf "internal" OR "confidential"
```

**2. Exposed Spreadsheets:**
```
site:fastroute.inc filetype:xls OR filetype:xlsx "salary" OR "network"
```

**3. Exposed WordPress Admin or Login Panel:**
```
site:fastroute.inc inurl:wp-admin OR inurl:wp-login.php
```

---

## Related Topics
- [[Reconnaissance - Overview]]
- [[OSINT Techniques]]
- [[Footprinting - Active and Passive]]
- [[WHOIS and DNS Footprinting]]
- [[QA - Reconnaissance Scenarios]]
