# Naming convention

Starting out with virtual machines, you'll need a naming convention.  Being a former technical writer, I know how important it is to have a structure that people can quickly pick up and understand.  Name the VM so it's easy to understand and easy to look at.  

A resource group should also make it easy for someone to figure out what the resource is. Yes, it usually has the resource type on the list, but just make it the resource name. It's also nice to have when you search in Azure for something. Making the resource type part of the name also helps you identify it quickly and remember it better. You can use this to manage multiple resources of the same type, like virtual machines and storage accounts. Furthermore, having the type of resource in the name helps the Azure search engine find it.   The naming convention could be something like:

- Resource Type
- Environment
- Location
- Application
- What is in the VM

So something like this <code style="color : cyan">"vm-dev-eastus-lms-dns"</code>

Make sure you figure out a good naming convention for your environment.

