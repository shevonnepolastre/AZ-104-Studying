# Creating a VM

I have already created a VM several times through the portal.  I've also found an <a href="https://learn.microsoft.com/en-us/samples/browse/?expanded=azure&products=azure-resource-manager">Azure Quickstart Template Gallery</a>.  I went and deployed a simple VM.

## How about in PowerShell? 

I started off in ChatGPT that the script was probably 40% accurate.  After that, I went to the Microsoft pages on Powershell script for the different resources I was creating. FINALLY, after tweaking here and there, I got the VM created with the other resources like the VNET, Network Security Group, and Network Interface.  Here is the code:

    Connect-AzAccount


    Set-AzContext -SubscriptionId "224b21e7-ab59-4f9d-bfa3-a0c6261321ad"


    $resourceGroup = “vmLearnResourceGroup"

    $location = "EastUS"


    New-AzResourceGroup -Name $resourceGroup -Location $location


    $vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location -Name "myVnet" -AddressPrefix "10.0.0.0/16"


    $subnet = Add-AzVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix "10.0.0.0/24" -VirtualNetwork $vnet

    $vnet | Set-AzVirtualNetwork


    $nsg = Get-AzNetworkSecurityGroup -Name "myNSG" -ResourceGroupName $resourceGroup


    $nsg | Add-AzNetworkSecurityRuleConfig -Name allowAppPort$port -Description "Allow app port" -Access Allow -Protocol  -Direction Inbound -Priority 1000 -SourceAddressPrefix "" -SourcePortRange  -DestinationAddressPrefix  -DestinationPortRange 3389


    $nsg | Set-AzNetworkSecurityGroup


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


    $nic = @{

     Name = 'myNIC'

     ResourceGroupName = $resourceGroup

     Location = $location

     IpConfiguration = $IP1Config

    }

    New-AzNetworkInterface @nic$cred = Get-Credential


    New-AzVm -ResourceGroupName $resourceGroup -Name 'devVM4Learn' -Location $location -Image 'MicrosoftWindowsServer:WindowsServer:2022-datacenter-azure-edition:latest' -VirtualNetworkName $vnet -SubnetName $subnet -SecurityGroupName $nsg -PublicIpAddressName $ip -OpenPorts 80,3389

The good thing is that now I have a script to create:

- Resource Group
- Virtual Network
- Subnet
- Network Security Group
- Network Security Group Rule
- Network Interface
- Virtual Machine




