# API course for API sec university
Note** incase if lab stop working use diffrent options.


#													LAB SETUP
## start burp:
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


#													API Recon

- Public api is ment to found in ads of api provider.
- For public api end user is given manual hoe to use that api.
- Look in sign in page in web portal for finding api.
- HTTPO req and response header.
- JSON and XML is good indicator for api.

# PASSIVE Recon

- API documentation can tell us how to test api.

## google dorks

- `intitle:"api" site:"google.com"`
- `inurl:"/api/v1" site:"microsoft.com"`
- `intitle:json site:ebay.com`

## Use github dork

- `extension:json nasa`
- `"authorization: Bearer"`
- `filename:swagger.json`

## Shodan

- `coinbase port:443`
- `"content-type: application/json"`
- `"wp-json"`

## wayback machine

- just use it 

<!-- left from active reconniance -->

# Active recon

## run nmap on our target
## amass
- `amass enmum -list`
- `amass enum -active -d crapi.apisec.ai`
- `amass enum -active -d microsoft.com | grep api`
## run directory brute forcing with gobuster
- `gobuster dir -u http://127.0.0.1:8888 -w /usr/share/wordlists/dirbuster/... -b 200`
- `gobuster dir -u http://127.0.0.1:8888 -w /usr/share/wordlists/dirbuster/...` 
- `gobuster dir -u http://127.0.0.1:8888/workshop -w /usr/share/wordlists/dirbuster/...` 
### use dev tools in browser (network tab)
### In network capture form network section search for api, also see for auth token
### To intract that request form postman we need to copy that request form browser as cURL.
### Then import that request in postman.(Import it as raw text)

# Endpoint analysis
## Reverse Engineering API
- If API is not documented we need to build up our own collection.
- There are two process of it.

1. Using Postman to build our collection.
- Make a proxy collection with enabling proxy in postman.
- After enabling proxy with specified port 5555 and then turn on proxy in web browser as foxyproxy and good to go.
- Then nagivate around and do stuff and capture all request.
- After doint all stuffs stop the capture.
- Select all api releted request and differ it.
- Organise it with folder.

2. Using mitmweb
- Start mitmweb form terminal. 
- change the proxy to 8080. 
- Do all the stuff and see the capture. 
- Save all that capture. 
- Then use swagger.
`sudo mitmproxy2swagger -i /path/to/file -o output.yml -p http://crapi.apisec.ai -f filename(here flow)`
- Output file will be created see that file.
- Remove ignore text form api requests form file, yaml file.
- Save the file and then run that exat same command one more time. (can use --examples at end)
- then use editor.swagger.io to smilpify that output file.
- At this stage we can find vurnebility like exesive data expsore.
- we can also import this in postman
- And boom after adding we see that it's well managed.
- boi we get it it's done.

<!-- levt form vurne..  -->
<!-- Fix the import error in postman -->
Note** if an file is not inporting import the folder
# Finding exessive data exposure during endpoint analysis
- Review the documentation.
- We can replace id.
- We can edit the collection E.g add bearer token, edit the variables.
- When we create an enviroment we can setup there manny variables.
- Variables are used to edit at severa collection
- In editing section we can use pre-request script here we can run the script before seding.
- We can setp the test in editing section.
- We can set anything as variable here we are seeting ID as variable.
- We can also add key to a request.
- We can see hidden headers.
- send the login request in postman.
- After sending we wil get token.
- Add that token to edit > auth > bearer(here) > paste.
- Check that token is working correctly.
- In our collection try to find exessive data exposure request.
- We find the vurnebility at an community section.
- Then use burpsuite to capure request of /form section.
- After forwarding request in burp send that to reperter.
- And boom exessive data exposure, we found it.

# Finding Security misconf
- Scan the target with nikto.
`nikto -h crapi.apisec.ai`
- Finded 3 vurnibility in there.
- Scan with zap
`zaproxy`
- Enter the targer in GUI.
- Start the automatic scan.
- Save the scan.
- Import openapi ... and in that select the output ymal file from swagger

## manual scan
- Use manual explore in zap.
- Enable the HUD.
- Then launch browser
- In browser do all things form signup to buying all things again.
- Boom scan is compleate.
- To check for false positive see the request and reasponse in context of this. See the timestamps.

# Classic Authentication attack

## Bruteforce using burp
- We can use burp suite here for password sparying using intruder.
- intersept login request in burp 
- send it to Intruder
- Select email press add
- Select password press add
- Go to payload page
- select payload set 1 add emails there
- select payload set 2 add passwords there and start attack

for more
- Payload processing > add rule
- encode 
- base64
- click attack and click repeate

## Using wfuzz for fuzzing api
- lunch crapi homelab
- `wfuzz -d '{"email":"anand@email.com","password":"Password"}' -H 'Content-Type: application/json' -z file,/usr/share/wordlists/rockyou.txt -u http://127.0.0.1/identity/api/auth/login`
- `wfuzz -d '{"email":"anand@email.com","password":"Password"}' -H 'Content-Type: application/json' -z file,/usr/share/wordlists/rockyou.txt -u http://127.0.0.1/identity/api/auth/login --hc 500`
- the above --hc 500 means hide code 500 here if response code is 500 hide it means only show 200 ok code(here)

## API token attack
- Start postman 
- Update the bearer token
- setup proxy to proxy through burp
- sent login request through burp
- send that request to sequenser
- in burp -> Costum location configure select bearer token highlight it, click ok
- Start live capture
- It will send 20000 request
- then analyse it
- For analysing view difftent tabs llike character level analysis.
- Now get some bad token swaech for that in git you wil find some (https://raw.githubusercontent.com/hAPI-hacker/Hacking-APIs/    main/bad_tokens)
- then in burp sequencer > manual load locate that badtkoen file or paste it.
- we can see in tokens mainly last three characters are updated so that is a vurnibility.
- we find it using burp.
- here token's complexity is checked.
- lets try to brute force the tokens.
- capture login requesr send it to intruder.
- copy any bad token paste that token in replacement of password for request.
- in last three place add attack position by clicking on add.
- change attack type to cluster bomb and payload to bruteforce
- as per we know the number is lowercase single digit so set min max lenght to one and payload to a-z.
- payload set change to 2.
- so here second position is a character so payload type bruteforce min max 1 and a-z.
- payload set 3 we know its number so just 0-9 there ane min max is 1, and lunch the attack.

- Using jwt_tool
- capture the bearer token
- then use `jwt_tool <bearer_token_here>`
- then use `jwt_tool -t http://127.0.0.1/dashboard -rh "Authorization: Bearer eyJhbGciOiJSUzI1NiJ9.eyJzdWIiOiJ0ZXN0QGVtYWlsLmNvbSIsInJvbGUiOiJ1c2VyIiwiaWF0IjoxNjgxODMwNjY0LCJleHAiOjE2ODI0MzU0NjR9.BjhOdsen5Y3Fnx2FyoO0rVoxMJ-RouFGxeJND1k20ZB01IJP1GfEvzId5mdhZDo8CtwDmZCgGFh9611LKNUlTIUqNnOs-l-IVmMdhE6qqEx0MQmNoekNeKFyaOodLkuz2dDHxk9CtQh-YFvbGq6LepQCxiyBkGASVXax9yD1HMBtsxiv-7REwQIV-lgLWGUSasnGO6c92emlPtBW-QaZflDT_eq7b9BozdFY_f5uFgUKdWl7J-q_vrnBiCuCHdPah-KvFeITbOE-Nb2AOMw-EIyjT5ccQE6cgQ0m2GQR3w_k7D0GOj6yIlfuHhr-21yOj3EWD5jSXoCA-RN3xp5TFQ" -M pb`
- then for making new token `jwt_tool eyJhbGciOiJSUzI1NiJ9.eyJzdWIiOiJ0ZXN0QGVtYWlsLmNvbSIsInJvbGUiOiJ1c2VyIiwiaWF0IjoxNjgxODMwNjY0LCJleHAiOjE2ODI0MzU0NjR9.BjhOdsen5Y3Fnx2FyoO0rVoxMJ-RouFGxeJND1k20ZB01IJP1GfEvzId5mdhZDo8CtwDmZCgGFh9611LKNUlTIUqNnOs-l-IVmMdhE6qqEx0MQmNoekNeKFyaOodLkuz2dDHxk9CtQh-YFvbGq6LepQCxiyBkGASVXax9yD1HMBtsxiv-7REwQIV-lgLWGUSasnGO6c92emlPtBW-QaZflDT_eq7b9BozdFY_f5uFgUKdWl7J-q_vrnBiCuCHdPah-KvFeITbOE-Nb2AOMw-EIyjT5ccQE6cgQ0m2GQR3w_k7D0GOj6yIlfuHhr-21yOj3EWD5jSXoCA-RN3xp5TFQ -X a`
- this will generate new token which will only work in JWT (token ends with .)


# Exploting api auth
- BOLA - This is the ability of user A to see bank account balance of user B.
- BFLA - Ability of user A to transfer fund form user B's account, back to user A's account

# BOLA
- For successful exploitation we need three things
1. Resource ID
2. Request that access resource 
3. Missing or flawed access control

- Find request with user id
- here it is /v2/vedios...
- 

