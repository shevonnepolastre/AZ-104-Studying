Microsoft Learn is a great resource for learning Azure.  It's obvious they focused on making their learning material better because previously it was like reading radio instructions.  Now it's so much easier to use and the UI's much better. There are some great projects to help you get familiar with Azure.   Two I'd recommend for understanding Storage Accounts are the <a href="https://learn.microsoft.com/en-us/training/modules/guided-project-azure-files-azure-blobs/1-introduction">Guided Project - Azure Files and Azure Blobs</a> and the <a href="https://learn.microsoft.com/en-us/credentials/applied-skills/secure-storage-azure-files-azure-blob-storage/">Applied Skills</a> for storage accounts.  

This write-up (while I work on the projects) will be about those efforts.  Then I'll work on the file-sharing system, which will be in its own folder.

## Table of Contents

  - [Mini Project 1 - Provide storage for the IT department testing and training](#mini-project-1---provide-storage-for-the-it-department-testing-and-training)
  - [Mini Project 2 - Provide storage for the public website](#mini-project-2---provide-storage-for-the-public-website)
  - [Mini Project 3 - Provide private storage for internal company documents](#mini-project-3---provide-private-storage-for-internal-company-documents)
  - [Mini Project 4 - Provide shared file storage for the company offices](#mini-project-4---provide-shared-file-storage-for-the-company-offices)
  - [Mini Project 5 - Provide storage for a new company app](#mini-project-5---provide-storage-for-a-new-company-app)

# Guided Projects - Azure Files and Azure Blobs 


### Mini Project 1 - Provide storage for the IT department testing and training
 This first mini project is pretty simple.  As you see, I came up with a naming convention that was basic.  It was just:  

1. What kind of resource is it?
2. What department owns it? 

<img src="https://github.com/shevonnepolastre/AZ-104-Studying/blob/main/StorageAccounts/Mini%20Project%201/Mini_Proj_1_Simple_Storage_Account_Setting.png">

I also made it Locally-redundant storage (LRS) and cool being that summary indicated that the data is not important. 

I created two storage accounts: one in the portal and the other using PowerShell (<a href="https://learn.microsoft.com/en-us/azure/storage/common/storage-account-create?tabs=azure-powershell">Storage Account Powershell Commands</a>) trying to learn scripting so why not?) Super simple and it was created faster than the one in the portal
   
      $resourceGroup = "storage_account_rz_grp"
      $location = "East US 2"

      New-AzStorageAccount -ResourceGroupName $resourceGroup -Name storageaccountitdept2 -Location $location -SkuName Standard_LRS -AllowBlobPublicAccess $false

The only update I had to make was changing the tier from "Hot" to "Cold." 

<img src="https://github.com/shevonnepolastre/AZ-104-Studying/blob/main/StorageAccounts/Mini%20Project%201/Mini_Proj_1_Simple_Storage_Account_Powershell.png">

Before moving on to the next project, I deleted the two storage accounts.  I did one using the portal, and the other using Powershell.

<img src="https://github.com/shevonnepolastre/AZ-104-Studying/blob/main/StorageAccounts/Mini%20Project%201/Mini_Proj_1_Simple_Storage_Account_Delete_Portal.png">

<img src="https://github.com/shevonnepolastre/AZ-104-Studying/blob/main/StorageAccounts/Mini%20Project%201/Mini_Proj_1_Simple_Storage_Account_Delete_Powershell.png">

### Mini Project 2 - Provide storage for the public website

To complete this project, you'll need a high availability storage account, anonymous access, and blobs.  To make a storage account high availability, you have to change the redundancy. Our first project only used local-redudant storage (LRS), which only keeps three copies in the same region.  High availability requires zone-redundant storage (ZRS).  Microsoft even explains each redundancy option so you can pick the right one. 

<img src="https://github.com/shevonnepolastre/AZ-104-Studying/blob/main/StorageAccounts/Mini%20Project%202/Mini_Proj_2_HA_Storage_Account_Redundancy.png" width="600">

The Security area is where you will want to indicate anonymous access is allowed. 

Creating a blob container is easy, but of course I want to challenge myself so wanted to create one using Powershell as well.  What a challenge?! Some of the instructions were wrong.  

First, I want to use Visual Studio Code. I had no problems using it on my Windows laptop, but my personal laptop is a Mac, and that was much difficult. 

Went to the Visual Studio Code extensions and installed Powershell and the Azure packages that were bundled up.  However, that still didn't do the entire trick.  I was getting a JSON error.  I had to go to my Terminal app and install Powershell. There are a few ways to do it, but I decided to install the package, and then run this command to get it to 100% install.  

    sudo installer -pkg ./Downloads/powershell-7.4.2-osx-x64.pkg -target /

I was then able to connect to my account 

    Connect-AzAccount 

I tried using the <a href="https://learn.microsoft.com/en-us/azure/storage/blobs/blob-containers-powershell">these instructions</a> and using this command:

    # Create a context object using Azure AD credentials
     $ctx = New-AzStorageContext -StorageAccountName <storage account name> -UseConnectedAccount

Started getting errors, so I realized that wasn't going to cut it. 

Tried this other command, and I kept getting "The client 'f774a339-7628-49ff-9829-49c522b6d49c' with object id 'f774a339-7628-49ff-9829-49c522b6d49c' does not have the authorization to perform action 'Microsoft.Resources/subscriptions/resourceGroups/read' over scope '/subscriptions/3535caf0-dd76-4e49-8666-cdbb6f15aa55'"

Decided to use ChatGPT to see what it came up with, and it worked BUT was still getting the error so I searched online and someone indicated that you might have to actually select the subscription using

    "Connect-AzAccount" go to "Select-AzSubscription -SubscriptionName 'X'

Did that, and it worked! 

    # Variables
    $resourceGroupName = “storage_account_rz_grp”
    $storageAccountName = “storageaccounthrdept”
    $containerName = “blogpowershell”


    # Get storage account context
    $storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
    $storageContext = $storageAccount.Context

    # Create new blob container
    New-AzStorageContainer -Name $containerName -Context $storageContext -Permission Off

ONLY thing I set manually was the anonymous access.  

<img src="https://github.com/shevonnepolastre/AZ-104-Studying/blob/main/StorageAccounts/Mini%20Project%202/Mini_Proj_2_HA_Storage_BlobPSSuccess.png">



### Mini Project 3 - Provide private storage for internal company documents

Requirements were:

1. The company needs a storage account for its private documents.

2. Adding redundancy to your storage account.

3. Setting up a shared access signature so partners can access a file.

4. Keeping the storage on the public website backed up.

5. Moving content to cool tiers with lifecycle management.

In light of this, I went ahead and deleted the storage account I used for my first two projects and made a new one.  I could have created a private endpoint, but I will save that when I get to VNets.  

When I created the storage account, I had to create a Shared Access Signature (SAS).  I did this pretty easily. In your storage account, you append it to the link for a file and the person can access it.  The downside is that it comes with some risks, so I might get fancier and use Entra ID.  

I made the redundancy to ZRS because the requirements required high availability.  When I tried to upgrade to GZRS, I got an error.  GRS was grayed out. 

Lifecycle management was easy, and I made a rule that moved data to a cool tier after 15 days.  

The backup part stumped me.  My head was spinning, and I didn't want to look at the solution yet. 

After staring at the options for a while, I decided to check out Data Protection, and there it was (or so I thought).  I created a backup vault and policy, but then I got an error about object replication.  

After seeing that, I clicked on Object Replication.  Added a second storage account and blob container, and created a rule to backup from one to the other.  I looked at the solution and that was the way to go.  However, what was Azure Backup?

I found this answer while searching: "Azure Backup and Azure replication are both Azure storage options that maintain copies of data. Azure Backup can restore deleted or corrupted data, while Azure replication maintains multiple copies of data."



### Mini Project 4 - Provide shared file storage for the company offices

The requirements are: 

1. Create a storage account.

2. Configure a file share and directory.

3. Configure snapshots and practice restoring files.

4. Restrict access to a specific virtual network and subnet.

Went ahead and used Powershell to create the storage account with success.  Creating the file share in the portal was easy.  I then tried to do it in Powershell but decided CLI was easier. 

    $resourceGroupName = "storage_account_rz_grp"

    $storageAcct = "storageaccountitdept2”
    
    $shareName = "myshare"

    az storage share-rm create --resource-group $resourceGroupName --storage-account $storageAcct --name $shareName --quota 1024 --enabled-protocols SMB --output none

For the snapshot, I went to Lifecycle Management and chose snapshots for 15 days and then it moves to a Cool tier. However, the solution indicated that the File Share had its own snapshots.  When I went, I am able to create a manual one but not automated. 

For the last requirement, I went to Networking and was able to restrict it to a VNet and Subnet I created. 

### Mini Project 5 - Provide storage for a new company app

The requirements are: 

1. Create the storage account and managed identity.
2. Secure access to the storage account with a key vault and key.
3. Configure the storage account to use the customer managed key in the key vault
4. Configure an time-based retention policy and an encryption scope.

After the storage account was created, I went to Encryption on the sidebar, and changed it from "Microsoft managed keys" to "Customer managed keys" 

<img src="https://github.com/shevonnepolastre/AZ-104-Studying/blob/main/StorageAccounts/Mini%20Project%205/storage_account_customer%20managed%20keys.png">

Creating the Key Vault was super easy, but then you have to remember that Zero Trust is a huge thing so that means the Key Vault doesn't automatically have access to the storage account.  I found this CLI command:
 
    az role assignment create --role "Storage Account Key Operator Service Role" --assignee "https://vault.azure.net" --scope "/subscriptions/2469c19e-4e43-431b-978a-   
    87dfe97a37fb/resourceGroups/storage_account_rz_grp/providers/Microsoft.Storage/storageAccounts/stoaccount4thewin"\

You will see it under the Storage Accounts IAM

<img src="https://github.com/shevonnepolastre/AZ-104-Studying/blob/main/StorageAccounts/Mini%20Project%205/storage_account_keyvaultacess.png">

HOWEVER, that didn't work.  I kept getting the error "The operation is not allowed by RBAC. If role assignments were recently changed, please wait several minutes for role assignments to become effective."

I was right that you do have to assign permissions BUT to the Key Vault, so I added the Key Vault Administrator role to myself and it worked.

<img src="https://github.com/shevonnepolastre/AZ-104-Studying/blob/main/StorageAccounts/Mini%20Project%205/storage_account_key%20vault%20creation.png">

The requirements also indicated that a Managed Identity needed to be tied to the storage account, so I went to Managed Identity and created one.

I made sure to apply the Key Vault Admnistrator to the Managed Identity and success! 

## Lessons Learned

As with anything, not everything was easy, and I'm glad because it made me learn. 

1. You have to apply permissions to everything in Azure.  Kind of heard my coworker say that yesterday when we were talking about Zero Trust and it made things much easier to understand.
2. Learning Powershell and CLI will make your life much easier
3. Find different ways of doing things and do not give up
4. Tons of resources out there to help you when you are troubleshooting
5. Keep practicing as with anything in life 
