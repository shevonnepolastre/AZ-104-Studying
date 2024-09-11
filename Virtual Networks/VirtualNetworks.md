
Virtual networks are a way to contain resources. They need to have at least one subnet.  I started off by doing the Microsoft Learn lab where the criteria is:

1. You need to create two virtual networks
2. Create two virtual machines
3. Make sure the two VMs can speak to one another

It was easy.  You have to go RDP into each virtual machine and make sure to turn off the firewall before you can try to ping to check the first VM can talk to the second one.  You have to use "ping vm2" from VM1. 

One thing I learned is that each virtual machines comes with Powershell.  I did not know that, so that was something that I learned.  

REMINDER: Stop your VMs when you are done because I've been reading on Reddit some crazy stories of people piling on thousdands of dollars, and I do not want that to be me.  Stop them right after you finish the lab.  You can always turn them on.  
