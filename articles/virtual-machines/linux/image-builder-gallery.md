---
title: Använda Azure Image Builder med ett avbildningsgalleri för virtuella Linux-datorer (förhandsgranskning)
description: Skapa Linux VM-avbildningar med Azure Image Builder och Shared Image Gallery.
author: cynthn
ms.author: cynthn
ms.date: 04/20/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.openlocfilehash: bf1dca61ec6b39e52d4f76c1c77cd3def6973ab8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945028"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>Preview: Skapa en Linux-avbildning och distribuera den till ett delat bildgalleri 

Den här artikeln visar hur du kan använda Azure Image Builder och Azure CLI för att skapa en avbildningsversion i ett [delat avbildningsgalleri](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)och sedan distribuera avbildningen globalt. Du kan också göra detta med [Azure PowerShell](../windows/image-builder-gallery.md).


Vi kommer att använda ett exempel .json mall för att konfigurera avbildningen. Den .json filen vi använder är här: [helloImageTemplateforSIG.json](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json). 

Om du vill distribuera bilden till ett delat bildgalleri använder `distribute` mallen [sharedImage](image-builder-json.md#distribute-sharedimage) som värde för avsnittet i mallen.

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

## <a name="set-variables-and-permissions"></a>Ange variabler och behörigheter 

Vi kommer att använda vissa bitar av information upprepade gånger, så vi kommer att skapa några variabler för att lagra denna information.

För förhandsgranskning stöder bildverktyget bara att skapa anpassade bilder i samma resursgrupp som källhanterad avbildning. Uppdatera resursgruppsnamnet i det här exemplet så att det är samma resursgrupp som källhanterad avbildning.

```azurecli-interactive
# Resource group name - we are using ibLinuxGalleryRG in this example
sigResourceGroup=ibLinuxGalleryRG
# Datacenter location - we are using West US 2 in this example
location=westus2
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=myIbGallery
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibLinuxSIG
```

Skapa en variabel för ditt prenumerations-ID. Du kan få `az account show | grep id`detta med .

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Skapa resursgruppen.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


Ge Azure Image Builder behörighet att skapa resurser i den resursgruppen. `--assignee` Värdet är appregistrerings-ID för Image Builder-tjänsten. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```





## <a name="create-an-image-definition-and-gallery"></a>Skapa en bilddefinition och ett bildgalleri

Om du vill använda Image Builder med ett delat bildgalleri måste du ha ett befintligt bildgalleri och en bilddefinition. Image Builder skapar inte bildgalleriet och bilddefinitionen åt dig.

Om du inte redan har ett galleri och en bilddefinition att använda börjar du med att skapa dem. Skapa först ett bildgalleri.

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Skapa sedan en bilddefinition.

```azurecli-interactive
az sig image-definition create \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --publisher myIbPublisher \
   --offer myOffer \
   --sku 18.04-LTS \
   --os-type Linux
```


## <a name="download-and-configure-the-json"></a>Hämta och konfigurera .json

Hämta .json-mallen och konfigurera den med dina variabler.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json -o helloImageTemplateforSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIG.json
```

## <a name="create-the-image-version"></a>Skapa bildversionen

Nästa del skapar bildversionen i galleriet. 

Skicka avbildningskonfigurationen till Azure Image Builder-tjänsten.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Starta bildversionen.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

Det kan ta en stund att skapa avbildningen och replikera den till båda regionerna. Vänta tills den här delen är klar innan du går vidare till att skapa en virtuell dator.


## <a name="create-the-vm"></a>Skapa den virtuella datorn

Skapa en virtuell dator från avbildningsversionen som skapades av Azure Image Builder.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name myAibGalleryVM \
  --admin-username aibuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

SSH till den virtuella datorn.

```azurecli-interactive
ssh aibuser@<publicIpAddress>
```

Du bör se bilden anpassades med ett *meddelande för dagen* så fort din SSH-anslutning är etablerad!

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du nu vill försöka anpassa avbildningsversionen igen för att skapa en ny version av samma avbildning hoppar du över nästa steg och fortsätter med att [använda Azure Image Builder för att skapa en annan avbildningsversion](image-builder-gallery-update-image-version.md).


Då tas bilden bort, tillsammans med alla andra resursfiler. Kontrollera att du är klar med den här distributionen innan du tar bort resurserna.

När du tar bort resurser för bildgalleriet måste du ta bort alla bildversioner innan du kan ta bort den bilddefinition som används för att skapa dem. Om du vill ta bort ett galleri måste du först ha tagit bort alla bilddefinitioner i galleriet.

Ta bort bildverktygets mall.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Få bilden version skapad av bildbyggare, detta börjar alltid med `0.`, och sedan ta bort bilden version

```azurecli-interactive
sigDefImgVersion=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'name' -o json | grep 0. | tr -d '"')
az sig image-version delete \
   -g $sigResourceGroup \
   --gallery-image-version $sigDefImgVersion \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```   


Ta bort bilddefinitionen.

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

Ta bort galleriet.

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

Ta bort resursgruppen.

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>Nästa steg

Läs mer om [Azure Shared Image Galleries](shared-image-galleries.md).