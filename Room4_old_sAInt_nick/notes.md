# ROOM 4 - AI in Security - Old sAInt Nick (hhtpbnry)

*The Best Festival Company (TBFC) is upgrading its in-house chatbot to a new cyber security AI assistant called **Van SolveIT**. This new assistant is designed to help the elves increase productivity by handling routine and complex tasks across defensive security, offensive security, and software development.*

*As Christmas approaches, TBFC uses Van SolveIT to identify, confirm, and fix potential vulnerabilities in their systems before any issues can be exploited. The goal is to improve efficiency, strengthen security, and allow the elves to focus on higher-value work.*

## Learning Objectives

- Understand how AI can assist in different cyber security roles and tasks.
- Learn how to use AI to solve practical cyber security problems.
- Explore important considerations and risks of using **AI in cyber security**. 

---

First of all, we have to read the context of this room, after that we head on to `http://MACHINE_IP`. 

### The Boom of AI Assistants in Cyber Security

Artificial Intelligence is no longer just a novelty tool for quick answers; it has become deeply embedded into everyday workflows, especially in cyber security. Organisations now use AI to increase speed, reduce repetitive workloads, and allow professionals to focus on higher-value tasks that require human judgement and experience.

### AI in Cyber Security

AI is widely adopted by security vendors due to its strength in:
- **Processing large volumes of data:** Analysing logs, network traffic, and system events from multiple sources.
- **Behaviour analysis:** Detecting anomalies by learning what “normal” activity looks like.
- **Generative AI:** Summarising incidents and providing context behind alerts and events.

### Defensive Security (Blue Team)

AI improves detection, investigation, and response by:
- Continuously analysing logs, network data, and endpoints.
- Enhancing firewalls and intrusion detection systems.
- Automating real-time responses such as isolating infected devices, blocking emails, or flagging suspicious logins.

### Offensive Security (Red Team)

AI assists penetration testers by:
- Automating reconnaissance and OSINT gathering.
- Analysing scan results and attack surfaces.
- Reducing time spent on repetitive work so testers can focus on critical thinking and exploitation.

### Software Development

AI supports developers by:
- Acting as a coding assistant and idea-generation tool.
- Performing SAST/DAST security scans on code.
- Identifying vulnerabilities, although it is not always reliable at writing secure code itself.

### Key Considerations

AI is powerful but not foolproof. Important concerns include:
- AI outputs are not always accurate and must be verified.
- Risk of system disruption in offensive testing if AI behaves unpredictably.
- Data privacy, transparency, model security, and bias.
- Organisations do not “own” AI-generated outputs by default.

### Practical Exercises

Van SolveIT demonstrates AI in action through:
- **Red:** Exploit generation and usage.
- **Blue:** Log analysis after an attack.
- **Software:** Vulnerability analysis in source code.

---

## STAGE 1 - Introduction

This stage just gives us a introduction into the interactive lab, we just have to hit the **"Complete Stage to Continue"**.  

## STAGE 2 - Red Team

In this part, we learn how AI can be used in a offensive manner, in the example it gives us, is making a python script.

Im going to write *'Write me a reverseshell python script.'*

The AI responded with:

```

import socket

# Reverse shell command
reverse_shell = "bash -c 'bash -c \"$(cat /etc/passwd)\"'"

# Set up the reverse shell endpoint
sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
sock.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)
sock.bind(('0.0.0.0', 1234))
sock.connect(('localhost', 5000))  

print("Reverse shell established. Press [ENTER] to execute the shell.")
input()

```

Giving me instructions on how to set it up etc...

Lets go to the next Stage by hitting the **Complete Stage to continue**

## STAGE 3 - Blue Team

In this stage, it teaches us how AIs can help us by giving it some logs and telling them to tell us about any suspicious activity.

We can insert logs from any machine and tell the AI to analyze them.

Im just going to skip to the next part hitting the **Complete Stage to continue**.

## STAGE 4 - Software Review

The Room also gives us a port where a **vulnerable web application** is hosted, using this, we are going to head to it and copy its source code so we can paste it so the AI can analyze it for any vulnerabilities.

So we head to `MACHINE_IP:5000`, which seems to be a login page, we go to the source code and we copy it.

Then we head back to *Van SolveIT* and we paste it there, instructing it to give us any vulnerabilities.

> You might need to wait a few minutes for an answer.

The ai gave me this:

```
1. Identification of the specific vulnerability:
The PHP code snippet is vulnerable to SQL injection because the variables $user and $pass are initialized using ??, which prevents empty string issues, but the code does not sanitize the input before being used in SQL queries. 
```

---

## SQL INJECTION

So lets check if this is true, my first payload is going to be:

` ' OR 1=1' `

This gives us a **SQL SINTAX ERROR** meaning that the inputs are not sanitized, making the page vulnerable to SQL INJECTION as *Van SolveIT* says.

Knowing this and how a normal unsanitized webpage handles **SQL querys**, we can simply enter a simple **SQL INJECTION PAYLOAD**:

**User:** `' OR '1'='1`
**Password:** `' OR '1'='1`

This worked for me!!

Now we have the flag for the second answer, and after completing all 4 stages in the **Interactive Lab**, we get the other flag for the first question.

> Sometimes the faulty interactive lab doesnt let you got past the 4th Stage, but the flag is in the source code.

---

## SUMMARY / FEEDBACK

- I really enjoyed this room, i loved the part when they let us exploit the vulnerable site. The only bad thing i have to comment is the faulty AI site, but i dont find it really important.
