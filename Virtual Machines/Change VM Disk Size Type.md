A CSA at work asked someone to create a Powershell script to change the disk space of multiple VMs from Premium SSD to Standard.  I decided to try to figure it out myself so I could learn while doing it.  The first thing I did was create the different variables and add the components that I knew how to do like 

  Connect-AzAccount

Set the subscription 

    # Set your subscription ID
    $subscriptionId = "110155ef-224c-44a0-8776-7efa3531e59b"

    Update-AzConfig -DefaultSubscriptionForLogin $subscriptionId

And get virtual machines in a resource group

      Get-AzVM -ResourceGroupName "PremtoStd"

After that, I was stuck because I didn't know which loop to use and how to do it in Powershell.  This is when I used ChatGPT.  It suggested the "foreach" loop.  As always, I had to do my own research to tweak it.   The first thing I found is that before you can change the disk type of a VM, you need to stop (or dellocate) all the VMs that are in scope. I looked on the Microsoft site and found out how to do it using: 

      Stop-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name -Force

Foreach loop is used when you every item in your criteria to have something happen to them.  Therefore, this one says, stop all VMS, and then for each data disk in the VMS, chnage the SKU from Premium to Standard.  

I also found out how useful the WriteOutput command is because it will display on the screen a message that you choose.  It's good when you are trying to troubleshoot or make sure the right action is being performed.   

Example is:

    Write-Output "Changing disk SKU for VM: $($vm.Name), Disk: $($disk.Name)"

After the disk SKU is changed, it will restart the VMs. 

Whole script: 

    # Connect to your Azure account
    Connect-AzAccount

    # Set your subscription ID
    $subscriptionId = "110155ef-224c-44a0-8776-7efa3531e59b"

    Set-AzContext -SubscriptionId $subscriptionId

    # Variables for resource group and VM names
    $resourceGroupName = "PremtoStd"

    # Get all the VMs in the specified resource group
    $vms = Get-AzVM -ResourceGroupName "$PremtoStd"

    foreach ($vm in $vms) {
        # Deallocate the VM
        Write-Output "Deallocating VM: $($vm.Name)"
    Stop-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name -Force

    # Get the VM details
    $vmDetails = Get-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name

    # Loop through each data disk
    foreach ($disk in $vmDetails.StorageProfile.DataDisks) {
            # Get the managed disk details
            $managedDisk = Get-AzDisk -ResourceGroupName $vm.ResourceGroupName -DiskName $disk.Name

            # Change the SKU from Premium_LRS to Standard_LRS
            if ($managedDisk.Sku.Name -eq 'Premium_LRS') {
                $managedDisk.Sku.Name = 'Standard_LRS'
                Write-Output "Changing disk SKU for VM: $($vm.Name), Disk: $($disk.Name)"

                # Update the managed disk with the new SKU
                Update-AzDisk -ResourceGroupName $vm.ResourceGroupName -DiskName $managedDisk.Name -Disk $managedDisk
                Write-Output "Updated disk: $($disk.Name) to Standard_LRS"
            }
          }

        # Loop through the OS disk
        $osDisk = $vmDetails.StorageProfile.OsDisk
        $osDiskDetails = Get-AzDisk -ResourceGroupName $vm.ResourceGroupName -DiskName $osDisk.Name

        if ($osDiskDetails.Sku.Name -eq 'Premium_LRS') {
            $osDiskDetails.Sku.Name = 'Standard_LRS'
            Write-Output "Changing OS disk SKU for VM: $($vm.Name)"

            # Update the OS disk with the new SKU
            Update-AzDisk -ResourceGroupName $vm.ResourceGroupName -DiskName $osDiskDetails.Name -Disk $osDiskDetails
            Write-Output "Updated OS disk to Standard_LRS"
        }

        # Start the VM
        Write-Output "Starting VM: $($vm.Name)"
        Start-AzVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
        Write-Output "VM: $($vm.Name) is now running with Standard SSD disks"
}
