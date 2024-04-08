The Entra ID is essentially the tenant's ID. I was surprised that the cost wasn't linked to the tenant, but then I realized subscriptions were where the resources were stored, so it's easier to budget and invoice for what you use in those subscriptions rather than having everything in one tenant, especially if you can have more than one subscription.

My tenant was already there (you can have more than one, but I wanted to start over).  I learned that you can't just delete a tenant on a whim.  I had to:

1. Delete the subscriptions

2. Delete everyone except the Global Admin

There were other items like deleting:

1. App registrations

2. Enterprise applications

3. Identity providers

Microsoft usually gives you a 30 day soft delete.  For instance, if you accidentally delete a tenant or subscription, you can submit a request and Microsoft will restore it. 
