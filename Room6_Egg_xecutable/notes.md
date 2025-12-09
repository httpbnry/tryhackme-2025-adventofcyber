# ROOM 6 - Malware Analysis - Egg-xectuable (httpbnry)

In this room we are going to learn about some common tooling for malware analysis.

The context is the following:

*Late at night in Wareville, the SOC team at The Best Festival Company (TBFC) receives a strange email from* **Elf McClause** *at 3AM, containing an attachment named `HopHelper.exe`. The email claims this program will be used for creating team schedules, but the unusual timing and executable attachment raise immediate suspicion.*

*Elf McBlue, a senior SOC member, advises against opening unexpected executables and begins a malware investigation instead. With McSkidy unavailable, Elf McBlue leads the analysis to determine whether the file is malicious and what threat it may pose to the company.*

### Mission
Investigate the suspicious executable and determine if it contains malware.

### Learning Objectives
- Understand the principles of malware analysis
- Learn the use of sandboxes for safe analysis 
- Differentiate between static and dynamic analysis 
- Use industry tools such as **PeStudio**, **ProcMon**, and **Regshot** 

---

## WHAT IS MALWARE ANALYSIS?

Malware analysis is the process of trying to figure out how the peice of malware functions, how it operates and the methods we can use to defend ourselves againts it. We can do all this by analyzing the malicious file/application.

When a malicious file infects our computer, it can leave traces on the machine, we can use these to figure out if it has spread to other devices. 

Theres two types of **Malware Analysis**:

- **Static:** Focuses on inspecting a file without executing it.

- **Dynamic:** Involves the execution of the file.

#### SANDBOXES

Sandboxes are used to execute potentially malicious code, its like a isolated box where malicious code can run without risking other systems or our data.

They are very important because **you should never run a malicious program / dangerous code in a device you care about**.

Most of the times, sandboxes are VMs (*Virtual Machines*), these are very popular in **Malware Analysis** because you can control how the system operates, create snapshots (allows us to create and restore various stages of the computer - Like a time machine).

---

## STATIC ANALYSIS

First we are going to use **static analysis**, that as we explained before, is about gathering information without executing the program.

Some information that can be gathered is:

- **Checksums -** Are used within cybersecurity to track and catalogue files and executables. (By googling the the checksum to see if someone else has identified the file).

- **Strings -** Are readable sequences within an executable (IP, addresses, urls, etc...)

- **Imports -** Are a list of libraries and functions that the application relies on. (These are usefull to see how the application interacts with the system)

- **Resources -** Contain data as the icon that is displayed to the user.

> We **have to know** that we **DONT** know how a program is going to run until we run it, it might appear less dangerous than it actually is.

#### PeStudio

PEStudio is a static malware analysis tool that inspects Windows executables for suspicious indicators without running them.

Lets use this application to help us figure out some things about `HopHelper.exe` (*Our malicious program*).

So we start up **PeStudio** and **Drag and Drop** `HopHelper.exe` (*in the folder `HopHelper`*) in the program.

Now we can see a lot of information about the file without having to execute it.

First of all things you can see `file > sha256`, this is the **Checksum**, which is a identifier for the executable. Lets keep a note of this to answer **QUESTION 1 of the room**
