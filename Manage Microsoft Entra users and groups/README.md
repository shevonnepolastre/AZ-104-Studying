# Manage Microsoft Entra Users and Groups 

The Entra ID is essentially the tenant's ID. I was surprised that the cost wasn't linked to the tenant, but then I realized subscriptions were where the resources were stored, so it's easier to budget and invoice for what you use in those subscriptions rather than having everything in one tenant, especially if you can have more than one subscription.

## Tenant Deletion

My tenant was already there (you can have more than one, but I wanted to start over).  I learned that you can't just delete a tenant on a whim.  I had to:

1. Delete the subscriptions

2. Delete everyone except the Global Admin

There were other items like deleting:

1. App registrations

2. Enterprise applications

3. Identity providers

I thought Microsoft only gave a 30-day soft delete.  For instance, if you accidentally delete a tenant or subscription, you can submit a request and Microsoft will restore it.  However, it seems it's more 60 days according to the email I received.  

<img src="https://shevonnepolastre.com/wp-content/uploads/2024/04/Screenshot-2024-04-08-at-4.56.12 PM.png" width="500">

## Tenant Creation 

Tenant creation was easy, BUT selecting the type of tenant.  
