Creating a VM image wasn't as straightforward as Microsoft says, and I think what bugs me is that they have code that you think "Yay, I can just copy and paste."  NOOOOO, it is never 100% correct.  The CLI Help was a great resource to fix some of the code that Microsoft had. 

Decided why not use CLI this time.

    # Created VM before I did this I did use the "az group create" to create the resource group.  I then used this to create a VM.  It also created a VNET, NIC, etc. 
    az vm create --name FLtoVAVM --resource-group FLtoVAResourceGroup --image Win2022AzureEditionCore --generate-ssh-keys

    # I then created an image gallery 
    az sig create --resource-group FLtoVAResourceGroup --gallery-name WinVMGallery 

An example of what I was talkin about.  This is what Microsoft said to use (of course I changed the variables) for the image definition:

    az sig image-definition create \
       --resource-group FLtoVAResourceGroup \
       --gallery-name WinVMGallery \
       --gallery-image-definition myWinmageDefinition \
      --publisher GreatPublisher \
     --offer GreatOffer \
    —sku GreatSku \
       --os-type Windows \
       --os-state specialized

But when I plugged it in, it didn't work.  CLI gave me suggestions, and this did work:

    az sig image-definition create --resource-group FLtoVAResourceGroup --gallery-name WinVMGallery --gallery-image-definition MyImage 
        --publisher GreatPublisher --offer GreatOffer --sku GreatSku --os-type Windows

I then used the CLI recommended by the Intellisense to create a VM version:

    az sig image-version create --resource-group FLtoVAResourceGroup --gallery-name WinVMGallery --gallery-image-definition         myImageDefinition --gallery-image-version 1.0.0 --target-regions “eastus" “eastus2” "eastus=1=standard_zrs" --replica-count 2 --managed-    image "/subscriptions/224b21e7-ab59-4f9d-bfa3-    a0c6261321ad/resourceGroups/FLtoVAResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"

RESOURCE_GROUP="FLtoVAResourceGroup" \
GALLERY_NAME="WinVMGallery" \
IMAGE_DEFINITION=“WinVMImageDefinition" \
VERSION="1.0.0" \
SOURCE_IMAGE_ID="/subscriptions/224b21e7-ab59-4f9d-bfa3-a0c6261321ad/resourceGroups/FLtoVAResourceGroup}/providers/Microsoft.Compute/images/MyImage \
TARGET_REGION="eastus" \

az sig image-version create \
  --resource-group $RESOURCE_GROUP \
  --gallery-name $GALLERY_NAME \
  --gallery-image-definition $IMAGE_DEFINITION \
  --gallery-image-version $VERSION \
  --target-regions $TARGET_REGION \
  --replica-count 1 \
  --managed-image $SOURCE_IMAGE_ID


az sig image-definition list --resource-group FLtoVAResourceGroup --gallery-name WinVMGallery --output table


...

$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace image
$offerName = "windows-data-science-vm"
$skuName = "windows2016"
$version = "19.01.14"
$vmConfig = Set-AzVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version

# Set the Marketplace plan information, if needed
$publisherName = "microsoft-ads"
$productName = "windows-data-science-vm"
$planName = "windows2016"
$vmConfig = Set-AzVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

...

az group delete --name FLtoVAResourceGroup
