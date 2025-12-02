# ROOM 1 - LINUX CLI - SHELLS BELLS

**server name:** tbdc-web01 (linux server)

We connect via ssh with the password

**username:** mcskidy
**password:** AoC2025!

---------
## PART 1 - FINDING THE GUIDE

First of all we see a *README.txt* file after logging and executing `ls`, that we cat out.

```
cat README.txt
```

We figure out its a note by mcskidy that says that theres an exploit in the server and that
Wareville is in danger. It also says that he will write a security guide and hide it from plain
view. 

This gives us a hint of a hidden file.

--------

So we make our way into the directory *Guides* where we execute:
```
ls -la
```
This shows up a file named *.guide.txt*
That when we cat out gets the first flag
```
cat .guide.txt
```
This file also tells us King Malhares plan and gives us a Guide that tells us to
head to */var/log* and to use `grep` to check the logs.
He says to look for eggs that want to hide, and check their shells.

--------
## PART 2 - FILTERING LOGS

We head to */var/log/* using `cd /var/log/`

After we list the directories using `ls -la`, we many different files, but one sticks out: *auth.logs*

Now, using `grep` we see any failed login attempts.

`grep "Failed password" auth.log`

I scroll through it and one catches my eye.

```
2025-10-13T01:46:15.878653+00:00 tbfc-web01 sshd[2453]: message repeated 2 times: [ Failed password for socmas
 from eggbox-196.hopsec.thm port 14040 ssh2] 
```

This says someone tried and failed 2 times to login into the **socmas** account. Lets see if they actually
got in and left some malware.


--------------------
## PART 3 - ANALYZING SOCMAS

We head into the users **socmas home directory**, *using `cd /home/socmas`. We explore the *2025 directory*
where we find various files, but especially a *eggstrike.sh* file (sh files are shell script files that 
contain commands for the linux shell to run automatically). 

We cat it out and we find the next flag.

`cat eggstrike.sh`

------------------
## PART 4 - ROOT USER / BASH HISTORY

To enter root mode just execute `sudo su`, that normally requires a password but in this server it's not
necessary. 

To finish this room, we need to head back to */root*  where we need to cat out the file called
*.bash_history* (file that lists all commands used). 

```
cd /root
ls -la
cat .bash_history
```
This will give you the final flag.

----------------
## PART 5 - INTERMEDIATE CHALLANGE

The intermediate challange is located in the */home/mcskidy/Documents* directory. Lets head over there
using `cd /home/mcskidy/Documents`. 

When we cat the only file in the folder (*read-me-please.txt*), we figure out its a note from McSkidy 
telling us that in the account **eddi_knapp** theres three "easter eggs" that will combine into a passcode.

He gives us a username and a password:

**Username:** eddi_knapp
**Password:** S0mething1Sc0ming

He also gives us three clues. 

Clues (one for each egg):

1) I ride with your session, not with your chest of files. Open the little bag your shell carries when you arrive. 

2) The tree shows today; the rings remember yesterday. Read the ledger’s older pages.

3) When pixels sleep, their tails sometimes whisper plain words. Listen to the tail.

He says that when we finish, join the fragments in order and use the passcode to decrypt the message i left.

-------------------

Lets log in to his account

`su eddi_knapp`
*I didn't need to use a password but if you do use the one provided by McSkidy*

Then we head to */home/eddi_knapp/*. Where we list all his files using: `ls -la`

### FIRST CLUE 

The first clue reffers to something that exists only when you are logged in, and is not a regular file.
It says that we need to open it when we arrive (im guessing this is when we log in).

My conclusion is that it reffers to the enviroment variables.

So lets see.

`env`

This prints out all the enviroment variables, and when i scroll through, we get the first fragment.

### SECOND CLUE

The second clue reffers to the history and the past, giving comparison to a trees rings. It really gives it
out with *Read the ledger's older pages*. 

My conclusion is the logs. 

Ok so i was wrong, after a few hours i figured out it was actually **.git**. So i played around with it.

First of all i entered */home/eddi_knapp/.secret_git* and i did `git status` where i saw that a file called
secret_message.txt was deleted, so with the help of **logs** and `git revert [hash]` i was able to revert
changes and cat out **PASSFRAG3** 

### THIRD CLUE

The third clue talks about pixels sleeping and saying something, it was hard to guess but with some help
i got to the conclusion of image files having text at the end.

So i went to */home/eddi_knapp/Pinctures/* and listed all the pictures.

`ls -la`

Then i saw a hidden picture called **.easter_egg**. 

Then i used the strings command to print out the **PASSFRAG2**

`strings .easter_egg`

-----------------

## PART 6 - FIXING THE SITE

Okay, so now that we have the entire passphrase, we can go to the encrypted message in */home/eddi_knapp/Documents*
and we decode it with:

`gpg --decrypt mcskidy_note.txt.gpg`

This will show a message that tells us that if we replace the */home/socmas/2025/wishlist.txt* with the list
inside that message. The page will be 'saved'. 

So im going to do that.

Once ive replaced it with the list provided to me, the website updates and gives us a message to decode, 
using a code given to us in the decrypyed message.

`openssl enc -d -aes-256-cbc -pbkdf2 -iter 200000 -salt -base64 -in message_to_decode -out decoded_message.txt -pass pass:'(code given by mcskidy)' `

Then we cat out the decoded message 

`cat decoded_message`

This tells us that when we head to */home/eddi_knapp/.secret* we can use the flag given to us by the
**decoded_message** we decoded last step to decrypt yet another gpg file (*dir.tar.gz.gpg*).

Im going to use the same command as before:

`gpg --decrypt dir.tar.gz.gpg` 

-----------------
## SUMMARY / FEEDBACK

- This room covered many features like hidden files, investigating logs, identifying malicious accounts, etc..

- My only feedback is that unless you go through the walkthrough, you cant really know to go to the root 
directory and cat out that exact file.
