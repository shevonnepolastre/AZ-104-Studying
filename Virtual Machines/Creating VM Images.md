Whenever you have trouble deciding what projects to help you gain Azure experience, Microsoft offers exceptional labs and demos that you can use. For learning about virtual machines, I used the one that I am <a href="https://github.com/MicrosoftLearning/AZ-104-MicrosoftAzureAdministrator/blob/master/Instructions/Labs/LAB_08-Manage_Virtual_Machines.md">linking here</a>.  Additionally, I decided to create a virtual machine image that wasn't available in the labs, but I saw on a Udemy course that I am taking. 

As a result, I updated the PowerShell code I initially wrote.  One thing to know is that you can always improve upon your code.  I made a few updates, particularly to the Network Security group. I optimized the code to ensure its ease of use without any warnings or errors during compilation. Below is the code detailing the virtual machine PowerShell changes I made:

    #Used to login to Azure 
    Connect-AzAccount

    #Select the Azure subscription that you want to work on 
    Set-AzContext -SubscriptionId "224b21e7-ab59-4f9d-bfa3-a0c6261321ad"

    #Variables that you will use later in your code.  Good practice so you dont have to keep entering the actual names over and over again
    $resourceGroup = “vmLearnResourceGroup"

    $location = "EastUS"

    #Cmdlet to create a new resource group 
    New-AzResourceGroup -Name $resourceGroup -Location $location

    #Virtual network and subnet creation 
    $vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location -Name "myVnet" -AddressPrefix "10.0.0.0/16"

    $subnet = Add-AzVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix "10.0.0.0/24" -VirtualNetwork $vnet

    $vnet | Set-AzVirtualNetwork

    #Instead of using Get, I changed it to New and this resolved some of the warnings I got before 
    $nsg = New-AzNetworkSecurityGroup -Name "myNSG" -ResourceGroupName $resourceGroup

    # Add a new Network Security Rule to the NSG
    $rule = New-AzNetworkSecurityRuleConfig -Name "rdp-rule" -Description "Allow RDP" -Access "Allow" -Protocol "Tcp" -Direction           "Inbound" -Priority 100 -SourceAddressPrefix "Internet" -SourcePortRange "*" -DestinationAddressPrefix "*" -DestinationPortRange 3389

   
    $nsg.SecurityRules.Add($rule)

    $nsg | Set-AzNetworkSecurityGroup

    #Create a public IP
    $ip = @{

     Name = 'myPublicIP'

     Sku = 'Standard'

     AllocationMethod = 'Static'

     IpAddressVersion = 'IPv4'

     Zone = 1,2,3

    }


    $IP1 = @{

     Name = 'ipconfig1'

     Subnet = $vnet.Subnets[0]

     PrivateIpAddressVersion = 'IPv4'

     PublicIPAddress = $pubIP

    }

      $IP1Config = New-AzNetworkInterfaceIpConfig @IP1 -Primary

      #Create a network interface card 

      $nic = @{

       Name = 'myNIC'

       ResourceGroupName = $resourceGroup

       Location = $location

         IpConfiguration = $IP1Config

        }

    New-AzNetworkInterface @nic$cred = Get-Credential

    #Now the pieces fall into place to create the VM
    New-AzVm -ResourceGroupName $resourceGroup -Name 'devVM4Learn' -Location $location -Image         'MicrosoftWindowsServer:WindowsServer:2022-datacenter-azure-edition:latest' -VirtualNetworkName $vnet -SubnetName $subnet -    SecurityGroupName $nsg -PublicIpAddressName $ip -OpenPorts 80,3389


You're probably going to take a 2-4 of days to work on this project especially if you have other commitments like work, school, kids, hobbies, etc.  Therefore, make sure that you STOP YOUR VM(s).  I learned that the hard way.   You do not want to log on and have a huge cost to pay.

With Azure's bicep, you can push Infrastructure as Code (IaC), so it's good to know how to use it.  It's more user-friendly than Powershell, but you still need to use CLI or PowerShell to actually deploy the files, just like with ARM Templates.  

Microsoft gets you started with a <a  href="https://learn.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-bicep?tabs=PowerShell">template</a> that's already ready to go. I got stuck on the fact that even though visual studio code shows that I installed the Bicep extension, I didn't actually install Bicep. It took me searching for this error to figure it out:

        New-AzResourceGroupDeployment: Cannot retrieve the dynamic parameters for the cmdlet

I then installed Bicep.  Eventually I got past this error, but the deployment still didn't work.  Based on what I read about the bicep syntax, and what I saw in ChatGPT, I hadn't defined the username and password.  I made sure to do that, and it worked! Besides the VNet, SubNet, etc, it also deployed the other resources. 

Though I still need to learn Powershell and CLI, I like bicep.
