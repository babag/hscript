# hscript: Hola Unblocker Script
Scripting language for configuring the Hola VPN proxy

## hscript package example:
**[Hola Redirect Demo](http://hola.org/share?sid=ca7bb33efed0bc08):**
```json
  {
    "name": "Hola Redirect Demo",
    "description": "Route requests to Maxmind's IP to Geographical location page through Hola peers around the world",
    "author": "Hola",
    "site": "https://github.com/HolaAPI",
    "icon": "http://hola.org/img/logo.png",
    "unblocker_rules": {
      "maxmind_UK": {
        "description": "Route requests to Maxmind from the UK",
        "link": "maxmind.com/en/locate_my_ip",
        "icon": "http://icongal.com/gallery/image/132178/uk_flag.png",
        "cmds": [{"hosts": ["maxmind.com"], "then": "PROXY GB"}]
      },
      "maxmind_US": {
        "description": "Route requests to Maxmind from the US",
        "link": "maxmind.com/en/locate_my_ip",
        "icon": "http://icongal.com/gallery/image/229138/us_flag.png",
        "cmds": [{"hosts": ["maxmind.com"], "then": "PROXY US"}]
      },
      "maxmind_ES": {
        "description": "Route requests to Maxmind from Spain",
        "link": "maxmind.com/en/locate_my_ip",
        "icon": "http://icongal.com/gallery/image/8135/es_flag.png",
        "cmds": [{"hosts": ["maxmind.com"], "then": "PROXY ES"}]
      }
    }
  }
```
> Access the website by routing ONLY the URLs that are authenticated through peers in that country  
(and leave the rest of the URLs unrouted).

**[Hola Facebook Demo - Android](http://hola.org/share?sid=0232ffff56b8ab0f):**
```json
  {
    "name": "Hola Facebook Demo",
    "description": "Access Facebook from countries that block it or from behind a corporate Firewall. Android only",
    "author": "Hola",
    "site": "https://github.com/HolaAPI",
    "icon": "http://hola.org/img/logo.png",
    "unblocker_rules": {
      "facebook": {
        "description": "Unblock Facebook app",
        "link": "facebook.com",
        "icon": "http://icongal.com/gallery/image/11148/facebook.png",
        "os": ["android"],
        "cmds": [{
          "hosts": ["facebook.com","facebook.net","fbcdn.com","fbcdn.net","fbstatic-a.akamaihd.net",
            "fbcdn-dragon-a.akamaihd.net"],
          "dst_dns": "true",
          "then": "PROXY US"
        }]
      }
    }
  }
```
> This Facebook.com hscript instructs Hola Unblocker requests to route through PROXY US agents on the Hola network  
(e.g. allows Iranian residents to freely use Facebook on Android). Note that routing all traffic through the Hola
proxies is required for such a case, but should not be used in cases where not needed since this significantly
slows down the browsing.

## Intro:
hscript, or Hola script, by [Hola](http://hola.org) lets you:  
* Configure routing rules for a set of URLs for use with the Hola Unblocker VPN Proxy  
* Create an hscript package by including several rules within one hscript  
* Easily share your hscript by posting a simple URL on your site, Facebook, Twitter, email, etc.  

Know **iptables**? You'll find writing hscript rules a breeze!  
We recommend using **FireBug** or **Chrome Developer Tools** to assist in rule development.  

## API explained:
* `name` *optional string*: **name of the hscript package**  
* `description` *optional string*: **Description of hscript package**  
* `author` *optional string*: **Author name**  
  e.g.: `John Doe`  
* `site` *optional string*: **Author's website URL**  
  e.g.: `john-doe-blog.com`, `facebook.com/john.doe`, `twitter.com/john.doe`, `github.com/john.doe`.  
* `icon` *optional string*: **Author's Image URL**  
  Image that represents the author, package, etc: `john-doe-blog.com/john.png`   
  Currenly not displayed.  
* `unblocker_rules` *required map*: **Routing rules for the sites to Unblock**  
* `unblocker_rules.RULE1` *required map*: **Unblocker rule/site ID**  
  Replace `RULE1` with the rule ID.
  * `unblocker_rules.fb` for unblocking facebook.com  
  * `unblocker_rules.wiki` for unblocking wikipedia.com  
  * `unblocker_rules.goog-us` for unblocking google.com via the US  
  One hscript package can have multiple rules/sites. User can enable each site independantly. 
* `unblocker_rules.RULE1.description` *optional string*: **Description of the rule**   
* `unblocker_rules.RULE1.link` *optional string*: **URL of the site this rule Unblocks**   
* `unblocker_rules.RULE1.icon` *optional string*: **Image URL of the rule/site**  
* `unblocker_rules.RULE1.os` *optional array of strings*: **Define the OS support**  
  Default when undefined: `["windows","windows8","android"]`.  
  You may need to specify the host per OS when the site or app has different IPs from one another.   
* `unblocker_rules.RULE1.def-ext` *optional array of strings*: **Define the default extensions**  
  Default when undefined: `["gif","png","jpg","mp3","js","css","mp4","wmv","flv","swf","json","mkv"]`.  
  Note: `def-ext` is used by default by `if` cmds when undefined in `RULES1.cmds[].if[].ext`  
* `unblocker_rules.RULE1.cmds[]` *required array*: **Commands to be executed for each URL the browser requests**  
  The purpose of these rules is to make a decision `if` a URL belongs to the `RULE1` site, and if it does,
  should the URL be proxied or should the browser connect directly to the web server, for faster surfing.   
  Each site rule matching starts with a fast path match using hash on the hosts, the host array contain all the
  hosts that produce URLs that have GeoIP checks in them.  
  When a request arrives the domain of the request (host) is fast searched in the currently defined hosts hash
  (created from the hosts array), if a match is found the other parts of the command are processed
  (the `if` statements) and a `then` is fired, if no match is found the request bypasses all the rules and
  send via the default path (usually directly to the web server).  
  The cmds are executed one after another. This is very similar in concept to iptables/ipf/firewall rule programming.  
* `unblocker_rules.RULE1.cmds[].hosts[]` *required array*: **URL of host domain**  
  Define as many hosts as you need.  
* `unblocker_rules.RULE1.cmds[].if[]` *optional array*: **`if` can accept rules from either of 3 sources
  host, url and ext.**  
* `unblocker_rules.RULE1.cmds[].if[].host` *optional array of strings*: **Hosts to be included in the routing**  
  e.g. `"if": [{"host": "^subdomain.\*\\.domain\\.com$", "type": "=~", "then": "DIRECT"}]`  
* `unblocker_rules.RULE1.cmds[].if[].url` *optional array of strings*: **URLs to be included in the routing**  
  e.g. `"if": [{"url": "http://subdomain.domain.com/geoip_check", "type": "!=", "then": "DIRECT"}]`  
* `unblocker_rules.RULE1.cmds[].if[].ext` *optional array of strings*: **File extensions to be
  included in the routing**  
  Define `ext` commands when you need to modify the default extension list.  
  e.g. `"if": [{"ext": "aaa", "type": "==", "then": "PROXY US"}]`  
* `unblocker_rules.RULE1.cmds[].if[].type` *optional string*: **`type` of value in host/url/ext**  
  * `==` equal matching of string value  
  * `!=` not equal matching of string value  
  * `=~` regex matching: `^foo\..*\.com$` will match foo.bar.com foo.b.a.r.com but not foo.com  
  * `!~` regex not matching.  
  * `in` array matching: `["foo.com", "bar.foo.com"]` will match bar.foo.com but not other.foo.com  
  * `not_in` array not matching.  
* `unblocker_rules.RULE1.cmds[].if[].then` *optional string*: **Define the route**  
  * `"DIRECT"` Dont proxy. Send requests directly from your browser.  
  * `"PROXY US"` Route through Hola peers in the United States  
  * `"PROXY GB"` Route through the United Kingdom  
  * `"PROXY ES"` Route through Spain  
  We'll be adding more countries soon!
  <a href="mailto:api@hola.org?Subject=Request%20to%20add%20a%20new%20hscript%20region">
  Send us region requests for new countries</a>  
* `unblocker_rules.RULE1.cmds[].if[].dst_dns` *optional string*: **Define destination DNS resolution**  
  **ONLY** use when DNS resolution is needed and all traffic must go through the proxy, as this will
  have extremely slow page loads!  
  This is for when you need to get around country/corporate/university Firewalls.  
  Define as `"true"` to activate DNS resolution on the host.  
* `unblocker_rules.RULE1.cmds[].then` *required string*: **Define the route**  
  `"DIRECT"`  
  `"PROXY US"`  
  `"PROXY GB"`  
  `"PROXY ES"`  
* `unblocker_rules.RULE2` *optional map*: **Name of the next rule**  
  More than one rule makes a package!  

### Note on performance
Further explanation of `"host"` and fast vs. slow matching:  
*Example*: Say you have mysite.com which has 2 domains from which URLs are requested:  
`www.mysite.com` and `img.mysite.com`, and only the www subdomain has a GeoIP check on url `"/authentication"`.  
There are basically 2 ways to go about defining the site rules:

```json
  {
    "cmds": [{
      "hosts": ["mysite.com"],
      "if": [{"host": "img.mysite.com", "type": "==","then": "DIRECT"},
        {"url": ".*/authentication$", "type": "!~", "then": "DIRECT"}],
      "then": "PROXY GB"
    }]
  }
```
or
```json
  {
    "cmds": [{
      "hosts": ["www.mysite.com"],
      "if": [{"url": ".*/authentication$", "type": "!~", "then": "DIRECT"}],
      "then": "PROXY GB"
    }]
  }
```

Both methods work, but the second one is much faster because it improves browser responsiveness,  
as the fast path matching of hosts is more accurate and we don't waste time on slow `"if"` matchings.  

## Creating hscripts
Creating new hscripts is easy!  

1. Get started:  
  * [Hola on Github](https://github.com/HolaAPI/HScript)  
  * From [Unblocker settings page](http://client.hola.org/client_cgi/),  
    click: 'I am smart, I can write a new Unblocker script'  
    or click: 'View script...' on any of the site buttons  
  * Search the web for "hscript" or "Hola script" + keywords  
    e.g. Google: "Social sites hscript"  
  * Copy an hscript whose functionality resembles your desired hscript, and modify it to your needs.  
2. Fork us, or open your own Github for the hscripts you create.  
3. When you create an hscript, you receive a unique link to the hscript which you can post or share online,
   anywhere you choose!  
  * Try sending an hscript link now with the Hola Redirect Demo
    [http://hola.org/share?sid=ca7bb33efed0bc08](http://hola.org/share?sid=ca7bb33efed0bc08)!  

## Installing Hola Unblocker
[Hola](http://hola.org) is available for Windows/Android/Chrome/Firefox.  
Linux/Mac/ChromeOS is supported by the Hola Unblocker Chrome and Firefox browser extensions.  

## Receiving hscripts
Sharing hscripts is even easier!  

1. Add an hscript to your Hola Unblocker by clicking the hscript link.  
2. Clicking the link will direct your browser to the Hola Unblocker setting page,  
   where you must approve adding the hscript to your list of Unblocked sites.  
  * If Hola is not installed or running, you will get prompted to open Hola or install now at
   [http://hola.org](http://hola.org).  
3. Add an hscript now with the [Hola Redirect Demo](http://hola.org/share?sid=ca7bb33efed0bc08)!  
4. Share and remove hscripts from the Hola Unblocker settings page at any time.  

## Recommend changes and feedback
We would love your help to make the Hola API and hscript even better!  

1. Fork us on Github!  
2. Improve the documentation or the Hola API, etc.  
3. We would love your help with creating howto documentation for Chrome debugger and
   Firefox Firebug for building hscripts.  
4. Send us a pull request and we'll update our Github page often!  

Email any other feedback to 
<a href="mailto:api@hola.org?Subject=hscript%20feedback%20from%20Github%20user">api@hola.org</a>!  
