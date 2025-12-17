# ROOM 11 - XSS - Merry XSSMAs (httpbnry)

After a major automation and tech upgrade, Santa’s workshop has a new secure messaging portal for contacting McSkidy. Recently, however, the system logs have shown strange activity—odd messages, suspicious search queries, and even Santa’s letters appearing as scripts or random code.

Your task is to investigate these logs, identify the source of the mischief, and determine who is attempting to interfere with McSkidy’s portal.

### Learning Objectives
- Understand how Cross-Site Scripting (XSS) attacks work
- Learn techniques to prevent XSS vulnerabilities

---

## EQUIPMENT CHECK

In this room we will be using a web app found in `http://MACHINE_IP`. We will be accesing it via our web browser.

## XSS Overview

Cross-Site Scripting (XSS) is a web application vulnerability that allows attackers to inject malicious code—usually JavaScript—into user input fields that are later viewed by other users. When applications fail to properly validate, sanitize, or escape user input, that input may be executed as code instead of being treated as plain text.

XSS attacks can be used to:
- Steal credentials or session cookies
- Impersonate users
- Deface web pages
- Display fake login prompts

This task focuses on **Reflected XSS** and **Stored XSS**.

---

## Reflected XSS

Reflected XSS occurs when malicious input is immediately returned in a server response and executed by the browser.

### Example

A normal search:

`https://trygiftme.thm/search?term=gift`

A malicious search:

`https://trygiftme.thm/search?term=<script>alert(atob("VEhNe0V2aWxfQnVubnl9"))</script>`

If a user clicks the malicious link, the injected script executes in their browser.

---

## Stored XSS

Stored XSS occurs when malicious input is saved on the server and executed every time a user views the affected page. This is more dangerous because it impacts all users who access the stored content.

**Normal Comment Submission:**

```
POST /post/comment HTTP/1.1
comment=This gift set my carpet on fire but my kid loved it!
```

**Malicious Comment Submission:**

```
POST /post/comment HTTP/1.1
comment=<script>alert(atob("VEhNe0V2aWxfU3RvcmVkX0VnZ30="))</script>
```

Because the comment is stored, the script runs for every visitor to the page.

---

## CHALLENGE

**Question 1:** This question is answered by reading the room theory.

**Question 2:** Lets get to work on this.

Lets see if we can get the flag by doing a Reflected XSS Explotitaion. 

Lets insert: `<script>alert('Stored Meow Meow')</script>` into the search bar and hit enter.

It worked!!! It Showed the pop up and then the search results gave us the flag!!

**Question 3:** Now lets find the Stored XSS flag. 

Lets do this with the *Send a Message to McSkidy* function on the webpage.

Lets insert the same script in it.

It worked!! It showed the pop up, and the flag is shown in the **Recent Messages** section of the web page.

---

## SUMMARY / FEEDBACK

- This was a really simple room, it showed 2 basic XSS exploitation.

- It was really interesting though.

