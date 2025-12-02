# ROOM 2 - Phishing - Merry Clickmas (httpbnry)

The context of this room is the following:

*This task simulates an authorized phishing campaign against TBFC to test employee security awareness. You will act as part of the red team alongside three characters during penetration testing. You will learn about social engineering and different types of phishing attacks. You will also create fake login pages and send phishing emails using specialized tools.*
(summarized version)

---

## PHISHING

Phishing is a form of social engineering that uses messages such as emails, texts, calls, QR codes, and social media to trick users into revealing information or money. As attacks become harder to detect, TBFC trains employees using two S.T.O.P. methods to identify suspicious messages. The first focuses on questioning emails that seem urgent, too good to be true, or request clicks. The second emphasizes slowing down, avoiding links, verifying senders, and not opening unexpected content.

---

## SETTING UP THE TRAP

In this part, the room asks us to set up a fishing attack. It tells us about phishing attacks and tells us that
our objective is to setup a *fake TBFC login page*, which we will attach to a email and send to a target.

It gives us a script that already sets up and host a fake login page in */Rooms/AoC2025/Day02/./server.py*.

So we direct into the directory via `cd`

`cd /rooms/AoC2025/Day02`

Here we start the server given to us with:

`./server.py`

This will start a server on **port 8000**. 

To confirm this, we can head to the following pages:

> http://MACHINE_IP:8000
> http://127.0.0.1:8000

---

## DELIVERY (via Social-Engineer Toolkit)

Here we are going to learn to use **SET** to send a malicious phishing email to our target users. Its a tool
created by David Kennedy for social engineering attacks. It offers a wide range of features. In particular, it lets you compose and send a phishing email. In the current scenario, we will use this tool to create and send a phishing email to the target user. 

Lets start by creating the phishing email. In the machine type:

`setoolkit`

Then a menu will show up containing various options, here we select **1) Social-Engineering Attacks**.

`set> 1`

Then its going to give us various types of *Social-Engineering Attacks*, in our case we are going to use type number **5**.

`set> 5`

After that, its going to give us an option between attacking a **single email address or various**, we are only
going to attack 1. So we select that option

`set> 1`

Now it will ask as many questions, the answer to them are the following:


- **Send email to:** Let’s begin by targeting factory@wareville.thm
- **How to deliver the email:** We will choose Use your own server or open relay
- **From address:** We know that the guys at the toy factory communicate regularly with Flying Deer, the shipping company, so that we will use updates@flyingdeer.thm as the source email address
- **From name:** Let’s use the name Flying Deer
- **Username for open-relay:** We will leave it blank and just hit the Enter key
- **Password for open-relay:** We will leave it blank and just hit the Enter key
- **SMTP email server address:** We will deliver directly to the TBFC mail server by entering the machine ip.
- **Port number for the SMTP server:** We leave the default value of 25 and just hit the Enter key

Then answer **no** for Flag as High Priority, Attach files and incline file..

Now we actually start reciting the email.

- **Subject:** Shipping Schedule Changes
- **Email Format:** We have a choice between plain or html text, we are going to use plain text so we just hit **ENTER**.
- **Body of Message:** Type the email one line at a time (including the link for the phishing attack) and when you finish type `END`

Then hit `return` when finished to confirm sending the emails.

---

## CONFIRMATION

Now we wait on the python server we started to see if the phishing worked!!!

And with the information given, you can answer *What is the password used to access the TBFC portal?*


---

## EXTRA

Then we need to browse to *http://TARGET_IP* to access the mailbox of the **factory user**to see if the admin password
has been reused and to answer the second and last question of **the number of total toys expected for delivery**.

When we try to login as `user: factory` and `password: passwordretrievedbefore` we see that the password did in fact
get reused. So we scroll through the emails and marta gives us the exact number of toys needed to answer the final question.

---

## SUMMARY / FEEDBACK

- In this room we learn all about Fishing Attacks, why they are dangerous, how to do them, and what tools are needed.

- My only feed back is that it was pretty simple, and i kinda wished for another side quest. 
