# ROOM 12 - Phishing - Phishmas Greetings (httpbnry)

With McSkidy missing, TBFC’s defenses have weakened and the Email Protection Platform is offline. As a result, staff must manually review all suspicious emails. The SOC Team believes Malhare’s Eggsploit Bunnies are sending phishing messages to steal credentials and disrupt SOC-mas.

You’ve joined the Incident Response Task Force to analyze incoming emails and determine which are legitimate and which are phishing attempts. Some messages are cleverly disguised as normal TBFC operations, volunteer requests, or event logistics—making accurate judgment critical.

### Learning Objectives
- Identify phishing emails 
- Understand common and emerging phishing techniques 
- Learn the difference between spam and phishing

Lets start the machine, we need to access *Wareville's Email Threat Inspector* via `https://lab_web_url.p.thmlabs.com/`.

---

# Phishing and Email Security Overview

## Phishing Overview

Phishing is one of the oldest cyber tricks but remains highly effective. As companies like TBFC enhance their defenses with advanced filters and security tools, attackers evolve their tactics to stay ahead.

Modern phishing relies on **precision and persuasion**, often mimicking real people, portals, or internal processes to deceive even cautious users.

**Common goals of phishing messages:**
- **Credential theft:** Tricking users into revealing passwords or login details  
- **Malware delivery:** Disguising malicious attachments or links as safe content  
- **Data exfiltration:** Collecting sensitive personal or company information  
- **Financial fraud:** Persuading victims to transfer money or approve fake invoices  

*Example:* An attacker impersonates an employee using a free email domain to modify payroll for financial gain.

> Despite fortified corporate perimeters, phishing remains the easiest path to initial access because it targets the human element.

---

## Why Spam Is Not Phishing

**Spam** is digital noise: annoying but generally harmless.  
**Phishing** is a targeted attack crafted to deceive specific users and compromise security.

**Spam focuses on volume, not precision**. Typical spam goals include:
- **Promotion:** Advertising products, services, or events  
- **Scams:** Fake offers or “get rich quick” schemes  
- **Traffic generation (clickbait):** Driving users to external sites  
- **Data harvesting:** Collecting email addresses for future campaigns  

> Not all unwanted emails are threats; some are harmless spam.

---

## Common Phishing Techniques

### Impersonation
Attackers may act as a trusted person, department, or service to gain credibility.  
*Example:* Email claiming “URGENT: McSkidy VPN access” from a free Gmail domain.

### Social Engineering
Manipulates people’s emotions (fear, urgency, helpfulness) to induce action.

**Techniques in phishing emails:**
- Impersonation of a trusted individual  
- Sense of urgency with words like *“urgent”* or *“immediately”*  
- Side channels to avoid standard company communication  
- Malicious intent (stealing credentials, approving payments, opening malware)

### Typosquatting and Punycode
Attackers register lookalike domains to deceive users:

- **Typosquatting:** Common misspellings, e.g., `glthub.com` instead of `github.com`  
- **Punycode:** Uses Unicode characters that look like Latin letters (e.g., `ƒ` instead of `f`)  

These tricks make malicious domains appear legitimate.

### Spoofing
Attackers fake the sender address to appear as a trusted domain.  
- Check **Authentication-Results**: SPF, DKIM, and DMARC  
- Failed checks + mismatched **Return-Path** indicate spoofing

*Example:* Real email appears from TBFC, but `Return-Path` shows `zxwsedr@easterbb.com`.

### Malicious Attachments
Classic phishing method where attachments are disguised as legitimate files:  
- Can install malware, steal passwords, or provide system access  
- HTML/HTA files are common because they run without browser sandboxing

### Use of Legitimate Applications
Attackers use trusted platforms (Dropbox, Google Drive, OneDrive) to:
- Deliver malicious files  
- Redirect users to fake login pages  
- Present appealing proposals (salary raises, laptop upgrades)  

### Fake Login Pages
Mimic real services to steal credentials:
- Email platforms  
- Microsoft Office 365  
- Google accounts  

### Side-Channel Communications
Attackers move interactions off email to less-controlled channels:
- SMS, WhatsApp, Telegram  
- Phone or video calls  
- Texted links or shared document platforms  

> This allows attackers to continue social engineering without corporate oversight.

---

## Mission: Spot the Phish

Your task:
1. Triage emails and separate spam from phishing  
2. Identify **three clear signals** (e.g., spoofing, impersonation, social engineering) for each phishing email  
3. Collect flags for each properly submitted phishing email to secure TBFC’s SOC-mas  

> Stay vigilant, verify every email, and outsmart the Eggsploit Bunnies!

---

## SUMARRY/FEEDBACK

- It was really hard documenting this, and when i did, i didnt have permission to write :/

- Easy task though.


