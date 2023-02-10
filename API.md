API course for API sec university


													LAB SETUP
start burp:
- Go to bwapp store and instal autorize (install it's dependices)
- Foxyproxy installation to mozilla
- make a proxy for burp and postman on port 8080 and 5555 respictively
- add burp cert
- install and setup postman
- install docker-compose
- install arjun
- install mitmproxy2swagger
- install jwt_tool
- install kiterunner
- install go-lang
- install zapproxy
- install amass
- update packges in zapproxy
- setup crAPI and vAPI


													API Recon

Public api is ment to found in ads of api provider.
For public api end user is given manual hoe to use that api.
Look in sign in page in web portal for finding api.
HTTPO req and response header.
JSON and XML is good indicator for api.

PASSIVE Recon

API documentation can tell us how to test api.

- google dorks

intitle:"api" site:"google.com"
inurl:"/api/v1" site:"microsoft.com"
intitle:json site:ebay.com

- Use github dork

extension:json nasa
"authorization: Bearer"
filename:swagger.json

- Shodan

coinbase port:443
"content-type: application/json"
"wp-json"

- wayback machine

just use it 

<!-- left from active reconniance -->

Active recon

- run nmap on our target
- amass
amass enmum -list
amass enum -active -d crapi.apisec.ai
amass enum -active -d microsoft.com | grep api
- run directory brute forcing with gobuster
gobuster dir -u http://127.0.0.1:8888 -w /usr/share/wordlists/dirbuster/... -b 200
gobuster dir -u http://127.0.0.1:8888 -w /usr/share/wordlists/dirbuster/... 
gobuster dir -u http://127.0.0.1:8888/workshop -w /usr/share/wordlists/dirbuster/... 
- use dev tools in browser (network tab)


