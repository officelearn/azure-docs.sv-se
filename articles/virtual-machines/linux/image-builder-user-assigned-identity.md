---
title: Skapa en avbildning för virtuella datorer och använda en användartilldelad hanterad identitet för att komma åt filer i Azure Storage (förhandsversion)
description: Skapa avbildning av virtuella datorer med Azure Image Builder, som kan komma åt filer som lagras i Azure Storage med hjälp av användartilldelade hanterade identitet.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: 27f4073efc8647d331faa14afbda0e15f92b8d50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060739"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>Skapa en avbildning och använda en användartilldelad hanterad identitet för att komma åt filer i Azure Storage 

Azure Image Builder stöder användning av skript eller kopiering av filer från flera platser, till exempel GitHub och Azure storage etc. För att kunna använda dessa måste de ha varit externt tillgängliga för Azure Image Builder, men du kan skydda Azure Storage-blobbar med SAS-token.

Den här artikeln visar hur du skapar en anpassad avbildning med Hjälp av Azure VM Image Builder, där tjänsten kommer att använda en [användartilldelade hanterad identitet](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) för att komma åt filer i Azure-lagring för avbildningsanpassningen, utan att du behöver göra filerna tillgängliga för allmänheten eller konfigurera SAS-token.

I exemplet nedan skapar du två resursgrupper, en kommer att användas för den anpassade avbildningen och den andra kommer att vara värd för ett Azure Storage-konto som innehåller en skriptfil. Detta simulerar ett verkligt scenario, där du kan ha skapa artefakter eller bildfiler i olika lagringskonton, utanför Image Builder. Du skapar en användartilldelad identitet och beviljar sedan läsbehörigheter för skriptfilen, men du anger inte någon offentlig åtkomst till filen. Du kommer då att använda Shell-anpassaren för att hämta och köra skriptet från lagringskontot.


> [!IMPORTANT]
> Azure Image Builder är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Registrera funktionerna
Om du vill använda Azure Image Builder under förhandsversionen måste du registrera den nya funktionen.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Kontrollera status för funktionsregistreringen.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Kontrollera din registrering.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

Om de inte säger registrerade, kör följande:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```


## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Vi kommer att använda vissa bitar av information upprepade gånger, så vi kommer att skapa några variabler för att lagra denna information.


```console
# Image resource group name 
imageResourceGroup=aibmdimsi
# storage resource group
strResourceGroup=aibmdimsistor
# Location 
location=WestUS2
# name of the image to be created
imageName=aibCustLinuxImgMsi01
# image distribution metadata reference name
runOutputName=u1804ManImgMsiro
```

Skapa en variabel för ditt prenumerations-ID. Du kan få `az account show | grep id`detta med .

```console
subscriptionID=<Your subscription ID>
```

Skapa resursgrupperna för både avbildningen och skriptlagringen.

```console
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```


Skapa lagringen och kopiera exempelskriptet till det från GitHub.

```azurecli-interactive
# script storage account
scriptStorageAcc=aibstorscript$(date +'%s')

# script container
scriptStorageAccContainer=scriptscont$(date +'%s')

# script url
scriptUrl=https://$scriptStorageAcc.blob.core.windows.net/$scriptStorageAccContainer/customizeScript.sh

# create storage account and blob in resource group
az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS

az storage container create -n $scriptStorageAccContainer --fail-on-exist --account-name $scriptStorageAcc

# copy in an example script from the GitHub repo 
az storage blob copy start \
    --destination-blob customizeScript.sh \
    --destination-container $scriptStorageAccContainer \
    --account-name $scriptStorageAcc \
    --source-uri https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh
```



Ge Image Builder behörighet att skapa resurser i bildresursgruppen. `--assignee` Värdet är appregistrerings-ID för Image Builder-tjänsten. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="create-user-assigned-managed-identity"></a>Skapa användartilldelade hanterade identitet

Skapa identiteten och tilldela behörigheter för skriptlagringskontot. Mer information finns i [Användartilldelade hanterade identitet](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

```azurecli-interactive
# Create the user assigned identity 
identityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $identityName
# assign the identity permissions to the storage account, so it can read the script blob
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$strResourceGroup/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/$scriptStorageAccContainer 
# create the user identity URI
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$identityName
```


## <a name="modify-the-example"></a>Ändra exemplet

Hämta exempelfilen .json och konfigurera den med de variabler som du har skapat.

```console
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage/helloImageTemplateMsi.json -o helloImageTemplateMsi.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateMsi.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateMsi.json
sed -i -e "s/<region>/$location/g" helloImageTemplateMsi.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateMsi.json
sed -i -e "s%<scriptUrl>%$scriptUrl%g" helloImageTemplateMsi.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateMsi.json
sed -i -e "s%<runOutputName>%$runOutputName%g" helloImageTemplateMsi.json
```

## <a name="create-the-image"></a>Skapa avbildningen

Skicka avbildningskonfigurationen till Azure Image Builder-tjänsten.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

Starta bildversionen.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

Vänta tills bygget är klart. Detta kan ta ungefär 15 minuter.

## <a name="create-a-vm"></a>Skapa en virtuell dator

Skapa en virtuell dator från avbildningen. 

```azurecli
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

När den virtuella datorn har skapats startar du en SSH-session med den virtuella datorn.

```console
ssh aibuser@<publicIp>
```

Du bör se bilden anpassades med ett meddelande för dagen så fort din SSH-anslutning är etablerad!

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>Rensa

När du är klar kan du ta bort resurserna om de inte längre behövs.

```azurecli-interactive
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Om du har några problem med att arbeta med Azure Image Builder läser [du Felsöka](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json).