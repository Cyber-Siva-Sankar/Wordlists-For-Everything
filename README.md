# Wordlists-For-Everything
This Repository contains wordlists for all types of CMS &amp; Web Application Attacks

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

## Note
These lists point at *known, publicly documented* paths (the kind already
indexed by tools like SecLists, gobuster's built-in wordlists, and each
platform's own file layout) — they don't include exploit code or attack
payloads. Only run these against systems you own or have explicit written
authorization to test; unauthorized scanning can be illegal in many
jurisdictions.

## Missing anything?
If you want additional stacks (PrestaShop, TYPO3, OpenCart, Ghost, Grafana,
Jenkins, Kibana/Elasticsearch, IIS/ASP.NET, Spring Boot specifically, etc.)
just say which ones and I'll add matching lists in the same format.

