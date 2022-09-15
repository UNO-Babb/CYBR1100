# CYBR 1100 - Labs
Support Materials for CYBR1100 Labs

## Lab 5
### Q1
Download the Contact Page zip file
- [ContactPage.zip](ContactPage.zip)
- Move contents to **/var/www/html/** folder.
###Q2
In the terminal, type the following command:
`/etc/init.d/apache2 start`
`/etc/init.d/mysql start`

Back in Firefox, navigate to the URL: **localhost/index.html**
### Q3
Back in the Terminal, navigate to the folder where you put the **createCybr1100.sql** file.

`cd /var/www/html

mysql < createCybr1100.sql

mysql

use cybr1100;

show tables;`

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
