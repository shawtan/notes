# Web Security

## HTTP
- Trust nothing
- Stateless
	- Auth with sessions or jwt
- GET requests puts data in url which leaks
	- Visible in bookmarks, history, logs, `referrer` request header, search engines
	- GETs are cacheable
- **Put data in headers or payload**
- Use TLS for everything
- Only sensitive data over HTTPS POST

#### Headers
##### Request
- Don't trust user agent
- Data in url is visible in `referrer`
	- Use `rel="noferrer"`
	- "referrer" meta tag
	- Or interstitial landing page

##### Response
- Don't expose server version information
- Access-Control-Allow-Origin: specify domains allowed to make requests to you
	- Enforced by browser
- X-Frame-Options: load page in a frame
	- Prevents Clickjacking
- X-XSS-Protection: 1; no-audit; mode=block
	- Not super secure, but good to have
- X-Content-Type-Options: nosniff
	- Prevents mime sniffing 
- Strict-Transport-Security: max-age=10000000; includeSubdomains
	- Enforces HTTPS at browser level (All connections must be HTTPS)
- Disable Browser Cache for sensitive pages
	- Expires: -1
	- Cache-Control: no-store, no-cache, must-revalidate, max-age=0
	- Last-modified: {now} GMT
	- Pragma: no-cache

## SQL
- **Parameterize everything**	
	- Use `PreparedStatement().setString` (java)
	- Stored Procedure (SQL)
- `1=1` `2 > 1 + 0.3`
- `-- ` to escape queries
- Timing attacks for blind queries (`waitfor delay '00:00:10'`)
- `shaw'or'1'!='@email.com`
- Principle of least privilege

## Cross Site Request Forgery
- Hide requests in `img` tags, iframe forms
	- Session cookies are sent with

### Defense
- Header verification
	- Check origin (fallback to referrer) against actual domain
- Synchronizer Token Pattern
	- Generated random token in the session
	- Add token to every sensitive form and url
	- Compare token and session value
- Challenge Response
	- Captcha, Password, Multi-Factor
- Double Cookie Submit Strategies
	- Anti-CSRF value as cookie
	- Send requests with token in request parameter
	- Server compares cookie and request parameter
	* Useless if an XSS flaw exists
	- Cookie
		- `httpOnly` (can be overwritten)
		- `SameSite=Strict` Best solution, implementation is slow
		- Local storage has no control

## Content Spoofing
- Getting a website to show content from an attacker

### Methods
- Image Tag Injection
	- Can steal content on the page
- Form Rerouting
	- Steals sensitive form content
- `<base>` jumping
	- Changes requests to relative urls
- Element Override
- Hanging `<textarea>`

## XSS
- Payload delivery methods
	- Reflective: data from user (in url) is put in the HTML
	- Stored feature: something in paylaod that is rendered later
	- Injected by router (HTTP)
- Attacks
	- Redirect to phishing site
	- Cookie theft
	- Virtual Site defacement

### Defense
- Bad solutions
	- Sanitize: won't be completely effective
	- Eliminate special characters: depends on requirements
	- Assume all variables added to a UI are dangerous
- Escape text in UI depending on where the content is going
	- OWASP Java Encoder Project / AntiXSS.NET
	- HTML entity encoding
	- CSS hex encoding
- HTML sanitization
- Sandboxing
	- `Object.seal(obj)`
	- `<iframe>`
		- allow-same-origin, allow-top-navigation, allow-forms, allow-scripts

## CSP
- Policy concerning sourses of data
- Blocks XSS
- Run it as report level first to make sure it doesn't break the website
- Whitelist
	- Be specific with CDNs
	- `'nonce-2415124'` for inline JS
		- `'strict-dynamic'` propagates trust (chained dependencies) and discards whitelists
		- `'unsafe-inline'` for old browsers (disabled by nonce)
- CSP evaluator for checking
		- `https:`

## React
- Has auto-escaping as a security layer
- Validate `href`s (check scheme isn't `javascript`)
- Don't prefetch data into `window.__INITIAL_STATE = JSON.stringify(data)`
- Keep React up to date (>0.14)
- `retire.js` to find out of date packages
- Beware of third party components
- `nsp` node security platform

## 3rd Party Libaries
- Keep them updated
- Check CVE, security lists
- OWASP dependency-check

## Webservices
- API security is a risk
- Avoid GETs for webservices due to risk of sensitive information

### Server Side Request Forgery
- SQL injection for urls
- Sanitize

### TLS (HTTPS)
- Use a SSL config generator
- Get a SSL report
- Use a strong cipher
	- Forward Secrecy: finding the key won't let you decrypt old data
- Don't mix non secure content
	- `Strict-Transport-Security: max-age=10000000; includeSubdomains`

### Access Control
- Check by permissions not roles

### JSON
- Validate against a schema
- Use a secure parser (Java gson)

### JSON Web Token
- Use to transfer and store tokens
- Use cookie sessions in web (and store jwt in the session)
- jwts for mobile and desktop
- Flow
	1. Login server issues jwt with secure private key
	2. Send jwt to client
	3. Other servers verify jwt using the public key
	4. Logout: Tell every server (or the gateway) that a certain jwt is revoked until expiry time (complicated)
- Token Binding: Binds token to the browser (future)