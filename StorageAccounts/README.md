Microsoft Learn is a great resource for learning Azure.  It's obvious they focused on making their learning material better because previously it was like reading radio instructions.  Now it's so much easier to use and the UI's much better. There are some great projects to help you get familiar with Azure.   Two I'd recommend for understanding Storage Accounts are the <a href="https://learn.microsoft.com/en-us/training/modules/guided-project-azure-files-azure-blobs/1-introduction">Guided Project - Azure Files and Azure Blobs</a> and the <a href="https://learn.microsoft.com/en-us/credentials/applied-skills/secure-storage-azure-files-azure-blob-storage/">Applied Skills</a> for storage accounts.  

This write-up (while I work on the projects) will be about those efforts.  Then I'll work on the file-sharing system, which will be in its own folder.

## Guided Projects - Azure Files and Azure Blobs 

### Mini Project 1 - Provide storage for the IT department testing and training
 This first mini project is pretty simple.  As you see, I came up with a naming convention that was basic.  It was just:  

1. What kind of resource is it?
2. What department owns it? 

<img src="https://github.com/shevonnepolastre/AZ-104-Studying/blob/main/StorageAccounts/Mini_Proj_1_Simple_Storage_Account_Setting.png">

I also made it Locally-redundant storage (LRS) and cool being that summary indicated that the data is not important. 

I created two storage accounts: one in the portal and the other using PowerShell trying to learn scripting so why not?) Super simple and it was created faster than the one in the portal
   
      $resourceGroup = "storage_account_rz_grp"
      $location = "East US 2"

      New-AzStorageAccount -ResourceGroupName $resourceGroup -Name storageaccountitdept2 -Location $location -SkuName Standard_LRS -AllowBlobPublicAccess $false

The only update I had to make was changing the tier from "Hot" to "Cold." 

### Mini Project 2 - Provide storage for the public website

### Mini Project 3 - Provide private storage for internal company documents

### Mini Project 4 - Provide shared file storage for the company offices

### Mini Project 5 - Provide storage for a new company app
