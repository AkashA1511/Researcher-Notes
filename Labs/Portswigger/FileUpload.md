
# LAB : Web shell uplaod via extension blacklist bypass 

`Sometimes .php extension is block to upload we can bypasss that block thing using these two extenion which is .php5 and .shtml`

Goal : Certain file extensions are blacklisted, but this defense can be bypassed due to a fundamental flaw in the configuration of this blacklist.
To solve the lab, upload a basic PHP web shell, then use it to exfiltrate the contents of the file `/home/carlos/secret`

Approch : 
-  My Approch : 
here i defently know that i have to do a dir traversal payload which i used in the last lab of this so i tried using that and lets see what the server respond so get a key. 

gonna use the last payload which i used 
```php
<?php echo file_get_contents('/home/carlos/secret'); ?>
```
---

### Objective

Bypass a blacklist that blocks `.php` files by abusing an Apache `.htaccess` configuration file.

### Why are two files required?

**1. `.htaccess` file**

* A `.htaccess` file is an Apache configuration file.
* It does **not** contain the PHP payload.
* Its purpose is to tell Apache to treat a different file extension (e.g., `.l33t`) as PHP.

Example:

```apache
AddType application/x-httpd-php .l33t
```

This means:

> "Whenever a file ends with `.l33t`, execute it as PHP."

---

**2. `exploit.l33t` file**

* This file contains the actual PHP web shell or payload.
* Normally, Apache would serve a `.l33t` file as plain text.
* Because of the `.htaccess` rule, Apache executes it as PHP.

Example:

```php
<?php echo file_get_contents('/home/carlos/secret'); ?>
```

---

### Attack Flow

1. Upload `.htaccess`.
2. Apache reads the configuration.
3. Upload `exploit.l33t` containing the PHP payload.
4. Visit `/files/avatars/exploit.l33t`.
5. Apache executes the PHP code and returns the output instead of the source code.

---

### Why not put PHP inside `.htaccess`?

`.htaccess` is a **configuration file**, not a PHP file. Apache reads it only for directives such as `AddType`, `AddHandler`, or rewrite rules. Any PHP code inside it is treated as plain text and is never executed.

---

### Key Concept

* **`.htaccess` = Changes server behavior**
* **`.l33t` = Contains the PHP payload**
* The attack works because the server trusts user-uploaded `.htaccess` files and allows them to redefine how file extensions are handled.

---

### Interview One-Liner

> "In this attack, we first upload a malicious `.htaccess` file that instructs Apache to execute a non-standard extension like `.l33t` as PHP. We then upload our PHP payload using that extension. When the payload file is requested, Apache interprets it as PHP, bypassing the blacklist that only blocks `.php` files."
