Whenever you have trouble deciding what projects to help you gain Azure experience, Microsoft offers exceptional labs and demos that you can use. For learning about virtual machines, I used the one that I am linking here.  Additionally, I decided to create a virtual machine image that wasn't available in the labs, but I saw on a Udemy course that I am taking. 

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
