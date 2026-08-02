# Web App Fuzzing Wordlists

Created by **Cyber_Siva**

A collection of hand-built wordlists for fuzzing common web application vulnerability classes with tools like `ffuf`, `wfuzz`, or Burp Intruder. Companion HTML manual included with usage steps and worked examples.

> ⚠️ **For authorized security testing only.** Every payload in this repo is meant for environments you own or have explicit written permission to test — a bug bounty scope, a pentest engagement, a CTF, or your own lab. Do not use these against systems without authorization. See [Legal](#legal--disclaimer) below.

## Contents

| File | Vulnerability class | Delivery point |
|---|---|---|
| `open_redirect_fuzz_wordlist.txt` | Open Redirect | URL query param |
| `xss_fuzz_wordlist.txt` | Cross-Site Scripting (XSS) | URL query param |
| `lfi_wordlist.txt` | Local File Inclusion (LFI) | URL query param |
| `path_traversal_wordlist.txt` | Path Traversal | URL query param |
| `command_injection_wordlist.txt` | OS Command Injection | URL query param |
| `ssti_wordlist.txt` | Server-Side Template Injection (SSTI) | URL query param / form field |
| `ssrf_wordlist.txt` | Server-Side Request Forgery (SSRF) | URL query param |
| `sqli_wordlist.txt` | SQL Injection | URL query param |
| `nosqli_wordlist.txt` | NoSQL Injection (MongoDB) | JSON body / form field |
| `ldap_injection_wordlist.txt` | LDAP Injection | Login/search field |
| `prototype_pollution_wordlist.txt` | Prototype Pollution | JSON body / query param |
| `xxe_wordlist.txt` | XML External Entity (XXE) | Full request body |
| `crlf_host_header_wordlist.txt` | CRLF Injection / Host Header Injection | Query param / Host header |
| `file_upload_bypass_wordlist.txt` | File Upload Bypass | Filename / Content-Type / multipart |
| `cors_origins_wordlist.txt` | CORS Misconfiguration | Origin header |
| `wordlist_manual.html` | — | Full manual: usage, commands, worked examples |

## Quick start

Most lists are used directly with `ffuf`, substituting `FUZZ` at the injection point:

```bash
# URL-param based (most files)
ffuf -u "http://target.com/search?q=FUZZ" -w xss_fuzz_wordlist.txt -mr "alert\(1\)"

# Body-based (XXE, NoSQLi, Prototype Pollution)
ffuf -u "http://target.com/api/upload" -X POST \
  -H "Content-Type: application/xml" -d FUZZ \
  -w xxe_wordlist.txt -mr "root:.*:0:0"

# Header-based (CORS, Host header)
ffuf -u "http://target.com/api/data" -H "Origin: FUZZ" \
  -w cors_origins_wordlist.txt -mr "Access-Control-Allow-Origin"

# Multipart-based (File Upload Bypass)
ffuf -u "http://target.com/upload" -X POST \
  -F "file=@shell.php;filename=shell.FUZZ;type=image/jpeg" \
  -w file_upload_bypass_wordlist.txt -mc 200
```

For anything with the payload in the request body or a header, Burp Intruder is often the more practical tool — wrap the injection point in `§§`, load the `.txt` file as a Simple List payload set.

**Full usage guide:** open `wordlist_manual.html` in a browser for per-file commands, match conditions, and four step-by-step worked examples (XSS, SQLi, XXE, CORS) from first probe to confirmed finding.

## Before you fuzz

1. Replace placeholder domains (`evil.com`, `trusted.com`, `your-id.oastify.com`) with your own controlled test domain — a [Burp Collaborator](https://portswigger.net/burp/documentation/collaborator) or [interact.sh](https://github.com/projectdiscovery/interactsh) endpoint works well for out-of-band confirmation.
2. Confirm blind/destructive-adjacent payloads (command injection, SQLi, SSTI RCE gadgets) with harmless commands (`id`, `whoami`, `sleep 5`) before anything that modifies data.
3. Rate-limit scans against anything production-like (`ffuf -p 0.1 -t 5`) to avoid unintentional denial of service.
4. Each `.txt` file has a `NOTES` section at the bottom with class-specific guidance (e.g. which DBMS syntax to try first for SQLi, how to interpret SSTI probe responses).

# CMS / Server Fingerprinting Wordlists

Separate path lists for identifying and enumerating common platforms during
**authorized** security testing (pentests, bug bounty programs you're scoped
for, or your own infrastructure).

## Files
- `wordpress.txt` — WordPress core/plugin/config paths
- `laravel.txt` — Laravel framework paths (.env, artisan, storage, routes)
- `magento.txt` — Magento 1/2 admin, config, API paths
- `jupyter.txt` — Jupyter Notebook/Lab/Hub API and UI paths
- `apache_tomcat.txt` — Apache HTTPD + Tomcat manager/status paths
- `drupal.txt` — Drupal core/module/settings paths
- `joomla.txt` — Joomla admin/component paths
- `nginx.txt` — Nginx status/config paths
- `generic_common.txt` — Cross-platform: .git exposure, .env, admin panels,
  Docker/K8s configs, Spring Boot actuator, Swagger/GraphQL, backups, etc.

## How these get used
Typically paired with a directory-brute-force tool against a target you're
authorized to test:

```bash
# ffuf
ffuf -u https://target.tld/FUZZ -w wordpress.txt -mc 200,301,302,403

# gobuster
gobuster dir -u https://target.tld -w laravel.txt -x php

# feroxbuster (good for recursive)
feroxbuster -u https://target.tld -w generic_common.txt
```

A common workflow: run `generic_common.txt` + a fast tech-fingerprinting
step (e.g. `whatweb`, `wappalyzer`, response headers, `/robots.txt`,
`/readme.html` etc.) first to identify the stack, *then* run the matching
platform-specific list.

# Extension-Based Content Discovery Wordlists

Base word list: 4,731 curated terms (SecLists `common.txt` + manually added
admin-panel / auth / config / backup / API / CMS terms), each combined with
every extension in its group.

## Files (grouped by language/platform)

| File | Extensions covered | Notes |
|---|---|---|
| wordlist_php.txt | .php .php3 .php4 .php5 .php7 .phtml .phar | PHP apps |
| wordlist_asp.txt | .asp | Classic ASP |
| wordlist_aspx.txt | .aspx .ashx .asmx .axd | ASP.NET (pages, handlers, web services, HTTP handlers) |
| wordlist_jsp.txt | .jsp .jspx .jsw .jsv | Java (Tomcat/JSP) |
| wordlist_do.txt | .do .action | Java Struts (Struts1 uses .do, Struts2 uses .action) |
| wordlist_html.txt | .html .htm .shtml .xhtml | Static/legacy HTML |
| wordlist_cfm.txt | .cfm .cfml .cfc | ColdFusion |
| wordlist_js.txt | .js .mjs | JavaScript/Node endpoints |
| wordlist_json.txt | .json | API/config data |
| wordlist_xml.txt | .xml | Config/API/sitemap |
| wordlist_txt.txt | .txt | Notes, readmes, robots-adjacent files |
| wordlist_cgi.txt | .cgi | CGI-BIN scripts |
| wordlist_pl.txt | .pl .pm .cgi | Perl |
| wordlist_py.txt | .py .pyc | Python |
| wordlist_rb.txt | .rb .erb | Ruby/Rails |
| wordlist_bak.txt | .bak .old .backup .orig .save .swp .tmp | Backup/leftover files |
| wordlist_sql.txt | .sql .sqlite .db | Database dumps |
| wordlist_conf.txt | .conf .config .cfg .ini .env .yml .yaml | Config files |
| wordlist_log.txt | .log | Log files |
| wordlist_inc.txt | .inc | PHP/legacy includes |
| wordlist_archive.txt | .zip .tar .tar.gz .gz .rar .7z | Compressed backups/source dumps |

Plus `wordlist_ALL_EXTENSIONS_combined.txt` (one level up) — every word x every
extension, deduplicated (~293K lines) if you want a single mega-list.

`base_wordlist.txt` (one level up) has just the raw words with no extension,
in case you want to add your own extensions or reuse it for other tools
(e.g. subdomain or parameter fuzzing).

## Usage examples

**ffuf**
```
ffuf -u https://target.com/FUZZ -w wordlist_php.txt -mc 200,301,302,403
```

**gobuster**
```
gobuster dir -u https://target.com -w wordlist_aspx.txt -x aspx,ashx
```

**dirsearch** (natively supports extension flags, so you can also just use
`base_wordlist.txt` with `-e php,aspx,jsp,do,html,...`)
```
dirsearch -u https://target.com -w base_wordlist.txt -e php,jsp,do,aspx,html
```

## Extending this list
If you tell me the specific tech stack (e.g. "WordPress on PHP", "Spring Boot
Java", "Django") I can build a much more targeted, higher-hit-rate wordlist —
generic lists like this cast a wide net but a stack-specific one performs far
better in practice.

Only use these against systems you own or are explicitly authorized to test.


## Note
These lists point at *known, publicly documented* paths (the kind already
indexed by tools like SecLists, gobuster's built-in wordlists, and each
platform's own file layout) — they don't include exploit code or attack
payloads. Only run these against systems you own or have explicit written
authorization to test; unauthorized scanning can be illegal in many
jurisdictions.

## Legal / disclaimer

These wordlists are provided for educational purposes and authorized security testing (bug bounty programs, penetration tests with signed scope agreements, CTFs, and personal lab environments) only.

Running these payloads against systems you do not own or do not have explicit written authorization to test is illegal in most jurisdictions and may violate laws such as the U.S. Computer Fraud and Abuse Act (CFAA), the UK Computer Misuse Act, or equivalent legislation elsewhere. The author(s) of this repository accept no liability for misuse.

## Contributing

PRs adding new payload categories, fixing false-positive-prone entries, or improving match-condition documentation are welcome. Please keep additions organized under the existing `SECTION` headers within each file and update this README's table if adding a new file.

## License

<!-- Choose one, e.g.: -->
MIT — see `LICENSE` for details.
