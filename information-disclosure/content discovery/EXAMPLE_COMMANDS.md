wget https://raw.githubusercontent.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study/main/wordlists/burp-labs-wordlist.txt

ffuf -c -w ./burp-labs-wordlist.txt -u https://0acd00d403d11cae80ece97500f700c0.web-security-academy.net/FUZZ -x http://127.0.0.1:8080

