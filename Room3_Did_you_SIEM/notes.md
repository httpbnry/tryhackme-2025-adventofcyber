# ROOM 3 - Splunk Basics - Did you SIEM (httpbnry)

This room is about Splunk Log Analysis. The context is the following:

*When it was almost christmas in Wareville, the team of TBFC (The Best Festival Company) was busy preparing for the big celebration, until the SOC dashboard got hacked by a ransomware made by King Malhare. With McSkiddy Missing and the network under attack, we will use Splunk to determine how the ransomware entered the system and deal with it.*

We are going to:

- Ingest and interpret log data in **Splunk**.
- Create and apply custom field extractions.
- Use **Search Processing Language (SPL)** to filter and refine search results. 
- Conduct an investigation within Splunk to uncover key highlights.

When we start the machine, we then connect via browser to the link provided to us:

`https://lab_web_url.p.thmlabs.com/`

> You might have to wait a few minutes until Splunk loads entirely.

---

## EXPLORING THE LOGS

First of all in the *Splunk Interface* we are going to head to the **Search & Reporting** section on the left panel.

Where were going to go into de *Seach* and type in the following:

`index=main` and put `All time` as the **time frame** from the drop down on the right of the search bar.

After executing the query, we will filter by dataset (there are 2 types `web_traffic` and `firewall_logs`).

> firewall_logs - firewall logs has a local ip assigned to the web server.

---

Firstly we are going to start a basic search acroos the *index* using the custom source type `web_traffic` using the following query.

`index=main sourcetype=web_traffic`

- This query retrives all the events from the **main index** that were tagged with the source type `web_traffic`.
- The time range means that we are going to look through the entire history, not just todays or this week's.
- The timeline is a visual histogram that shows the distribution of the 17 000 events. 

> We can see theres a traffic spike of high activity (most likely the attack window)

- Selected Fields: fields currently chosen to be displayed in the summary column that represent basic metadata about the logs.

- Interesting fields: This pane lists all fields that Splunk has automatically extracted or manually added. Fields prefixed with #.
- Event details and field extraction section shows the parsed details of a single event with extracted fiedls.

---

## VISUALIZING THE LOGS TIMELINE

Lets chart the total event count overtime, grouped by day, to determine the number of events per day. This will help us identify an **abnormal number of logs**.

Lets introduce the folling seach query:

`index=main sourcetype=web_traffic | timechart span=1d count`

The results show:

dates ----- count

So we scan through the results, but a more visual way to do it is by clicking on **Visualization**, that generates us a graph.

As we can see a clear period of intense activity, to figure out dates we can edit the search query to show up the most events in a list.

`index=main sourcetype=web_traffic | timechart span=1d count | sort by count | reverse`

---

## ANOMALY DETECTION

Now that we know the dates of the abnormal logs, lets use the same search query to examine varios fields to hunt for suspicious values.

Firstly, `user_agent` that shows us details of the user agents captured. Clicking on the left panel on the `user_agent` field, we can see a large number of recieving suspicious user agents that we need to investigate.

Secondly, `client_ip` contains the ip addresses of the clients accessing, we can see by clicking the left panel that one stands out.

Lastly, `path` contains the URI being requested and accessed by the *client IP*.


---

## FILTERING OUT BENIGN VALUES

King Malhare doesnt use standard browsers, so lets filter out those, we can do this by using:

`user_agent!=Chrome` etc...

After adding this to the search query: 

`index=main sourcetype=web_traffic user_agent!=*Mozilla* user_agent!=*Chrome* user_agent!=*Safari* user_agent!=*Firefox*`

We can see that by clicking on `client_ip` we can see a single IP address responsible for all the suspicious user agents.


---

## NARROWING DOWN SUSPICIOUS IPs

When we counter varios IP addresses that are constantly attempting to attack a server, we would narrow down the search.

We use the following query:

`sourcetype=web_traffic user_agent!=*Mozilla* user_agent!=*Chrome* user_agent!=*Safari* user_agent!=*Firefox* | stats count by client_ip | sort -count | head 5`

The result confirms the top IP is used by the **Bandit Bunnies**, lets pick the address and filter out to see what the footprints activities captured.

> The `-` in the `sort -count` sorts the result count in reverse order.


---

## TRACING THE ATTACK CHAIN

Now that we know the attackers IP, we can trace back their steps chronologically, confirming the use of mutilple tools.

#### Reconissance (Footprinting)

We will start searching for the initial probing of exposed configuration files using the query below:

`sourcetype=web_traffic client_ip="<REDACTED>" AND path IN ("/.env", "/*phpinfo*", "/.git*") | table _time, path, user_agent, status`

The result confirms the attacked used curl and wget, and was met with *404/403/401* status codes.

#### Enumeration (Vulnerability Testing)

Now we search for common path traversal and open redirect vulnerabilities, using the search query:

`sourcetype=web_traffic client_ip="<REDACTED>" AND path="*..*" OR path="*redirect*"`

The output shows the resources the attackes are trying to access, lets add the `| stats count by path`

Search Query: `sourcetype=web_traffic client_ip="<REDACTED>" AND path="*..\/..\/*" OR path="*redirect*" | stats count by path`

This reveals attempts to read system files, showing vulnerability testing.

> The second search query is important for later as well, because it will help resolve a question because /etc/password is a sensitive file. 

#### SQL Injection Attack

We can find the automated attack tool and its payload by adding `AND user_agent IN ("*sqlmap*", "*Havij*")`

So the search query would be:

`sourcetype=web_traffic client_ip="<REDACTED>" AND user_agent IN ("*sqlmap*", "*Havij*") | table _time, path, status`

Here we can see a lot of attack strings with `SLEEP(5)` that confirm a time-based SQL injection attack.

> A time-based SQL injection attack is when an attacker injects SQL commands that deliberately cause a delay (e.g., SLEEP(5)) to infer whether a condition is true based on the server’s response time, even when no data is visibly returned. This allows blind data extraction through timing differences.

> Find more about this in the TryHackMe Sql Injection Room

[SQL Injection Room](https://tryhackme.com/room/sqlinjectionlm)

One of the questions that is not answered in the room, is *How many path traversal attempts to access sensitive files on the server were observed?*

This can be answered really easily by adding `| sort -count` and also deleting the `"*sqlmap*"` useragent from the search query,

It would result in:

`sourcetype=web_traffic client_ip="<REDACTED>" AND user_agent IN ("*Havij*") | table _time, path, status | sort -count`

---

## EXFILTRATION ATTEMPS

We search for exfiltration attempts by searching events attempting to download large, sensitive files.

Using the query below we can identify them:

`sourcetype=web_traffic client_ip="<REDACTED>" AND path IN ("*backup.zip*", "*logs.tar.gz*") | table _time path, user_agent`

The results indicate that the *Bandit Bunnies* were exfiltrating large chunks of compressed log files using tools like `curl` and `zgrab`.

---

## RANSOMWARE STAGING & RCE

These requests of sensitive data indicate the attacker is gathering data for double extortion, because in the logs we identified some request to *bunnylock* and *shell.php*.

We can find more about these search queries with:

`sourcetype=web_traffic client_ip="<REDACTED>" AND path IN ("*bunnylock.bin*", "*shell.php?cmd=*") | table _time, path, user_agent, status`

The results confirm us that there was a succesfull webshell, letting the attacker gain full control over the webserver and gaining the capability to run commands.

This kind of attack is called **Remote Code Execution (RCE)**. The execution of `/shell.php?cmd=./bunnylock.bin` indicates a ransomware program executed on the server.

---

## CORRELATE OUTBOUND C2 COMMUNICATION

Now were going to search our `sourcetype` to `firewall_logs`, this will prove that the server established a outbound connection the attackers C2 IP on the suspicious `DEST_PORT`.

Were going to use the following query:

`sourcetype=firewall_logs src_ip="10.10.1.5" AND dest_ip="<REDACTED>" AND action="ALLOWED" | table _time, action, protocol, src_ip, dest_ip, dest_port, reason`

The `ACTION=ALLOWED` and `REASON=C2_CONTACT` fields confirm the malware communication.

---

## VOLUME OF DATA EXFILTRATED

We can also use the **sum** function to sum the bytes transferred to the *Bandit Bunnies*, using the `bytes_transfered` field.

Lets do that:

`sourcetype=firewall_logs src_ip="10.10.1.5" AND dest_ip="<REDACTED>" AND action="ALLOWED" | stats sum(bytes_transferred) by src_ip`

The results show that there was a lot of data transferred to the C2 server.

---

## SUMMARY / FEED BACK

- I love that it was a bit more complicated, but i still think they should let us figure more stuff answers out in other rooms.

- We learnt a lot!!!
