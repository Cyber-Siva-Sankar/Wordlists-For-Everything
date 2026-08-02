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
