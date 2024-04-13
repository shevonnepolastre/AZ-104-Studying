ChangeRUs LLC is moving their on-premises environment to Azure.  Since they need conditional access and just-in-time access, they bought P2 licenses. They've already created their tenant.  Now they're ready to create the AD structure, including the groups and users.   

Their decision was to have two subscriptions for test and production, and department names. The company also wants to make sure its users have the right roles and groups.   It's not uncommon for ChangeRUs LLC to have guest accounts with companies it contracts with.  

I created this diagram to help me navigate this imaginary company and their types of users and groups: 

<img src="https://github.com/shevonnepolastre/AZ-104-Studying/blob/main/Manage%20Microsoft%20Entra%20users%20and%20groups/Create%20Users%20and%20Groups.drawio.png">

The portal made it easy to create users and assign them to security groups, their roles, and if I created adminstrative units, I could attach them to the created user.

The difference between a guest and an external user confused me. The first time I saw it, I thought there was a difference, but it's just a person who wants their identity and their email address to remain confidential.  If I'm wrong, let me know. After creating an external user, and looking at documentation, that's all I can find.

# Dynamic Groups

If you want to use dynamic groups, you'll need a P2 license.  I was going to start the free trial, but it was forcing me to create a new tenant.  Yes, tenants are tied to Entra, but I don't want to create a new tenant when I already have these users in my existing one.  But then I dug deeper and made a new user with Global Administrator. After opening an incognito window and logging in as my newly-created user, I got free P2 license trial with 100 licenses.  

Dynamic groups are great when you want to assign a bunch of people and devices to different groups.  Definitely saves time.  Using bulk imports is good to start, but you won't do that forever.  The best way is to dynamically add users and devices.  

# Self-Service Password Resets (SSRPs)

For users to reset their own passwords, you need a P1 or P2 license. I remember working on an Internet Provider's help desk when I was 17.  I always wanted people to be able to reset their own passwords since most calls were for password resets.  I'm so glad this is the norm now. 


