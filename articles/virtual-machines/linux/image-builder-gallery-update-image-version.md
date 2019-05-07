---
title: Skapa en ny Avbildningsversion från en befintlig Avbildningsversion med hjälp av Azure Image Builder (förhandsversion)
description: Skapa en ny Avbildningsversion från en befintlig Avbildningsversion med hjälp av Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: 31ef53abcf9b416500ee70e42cc3cbd12cb11f35
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159547"
---
# <a name="preview-create-a-new-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Förhandsversion: Skapa en ny Avbildningsversion från en befintlig Avbildningsversion med hjälp av Azure Image Builder

Den här artikeln visar hur du utför en befintlig Avbildningsversion i en [delad bildgalleriet](shared-image-galleries.md), uppdaterar det och publicera den som en ny Bildversion i galleriet.

Vi kommer att använda en exempelmall .json konfigurera avbildningen. JSON-fil som använder vi finns här: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json). 


## <a name="register-the-features"></a>Registrera funktionerna
Du måste registrera den nya funktionen för att använda Azure Image Builder i förhandsversionen.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Kontrollera status för funktionen registreringen.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Kontrollera din registrering.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

Om de inte svarar registrerade, kör du följande:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```


## <a name="set-variables-and-permissions"></a>Ange variabler och behörigheter

Om du har använt [skapa en avbildning och distribuera till en delad bildgalleriet](image-builder-gallery.md) bildgalleriet delad skapar du redan har skapat några av de variabler som vi behöver. Om inte, Ställ in några variabler som ska användas för det här exemplet.

I förhandsvisningen kommer image builder bara stöder skapandet av anpassade avbildningar i samma resursgrupp som hanterade Källavbildningen. Uppdatera resursgruppens namn i det här exemplet ska vara samma resursgrupp som din hanterade Källavbildningen.


```azurecli-interactive
# Resource group name 
sigResourceGroup=ibLinuxGalleryRG
# Gallery location 
location=westus2
# Additional region to replicate the image version to 
additionalregion=eastus
# Name of the shared image gallery 
sigName=myIbGallery
# Name of the image definition to use
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibSIGLinuxUpdate
```

Skapa en variabel för ditt prenumerations-ID. Du kan hämta den här med `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Hämta versionsnumret för avbildningen som du vill uppdatera.

```
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```


Om du redan har bildgalleri egna delad och inte följde de föregående exempel, kommer du behöva tilldela behörigheter för Image Builder komma åt resursgruppen, så att den kan komma åt i galleriet.


```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="modify-helloimage-example"></a>Ändra helloImage exempel
Du kan granska i exempel som vi kommer nu att använda genom att öppna JSON-fil här: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) tillsammans med den [Image Builder-mallreferensen](image-builder-json.md). 


Hämta .json-exempel och konfigurera den med dina variabler. 

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json -o helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromSIG.json
```

## <a name="create-the-image"></a>Skapa avbildningen

Skicka bildkonfiguration till VM Image Builder Service.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIGfromSIG01
```

Starta avbildningen-version.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIGfromSIG01 \
     --action Run 
```

Vänta tills avbildningen har skapats och replikering innan du fortsätter till nästa steg.


## <a name="create-the-vm"></a>Skapa den virtuella datorn

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgVm001 \
  --admin-username azureuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

Skapa en SSH-anslutning till den virtuella datorn med hjälp av offentliga IP-adressen för den virtuella datorn.

```azurecli-interactive
ssh azureuser@<pubIp>
```

Du bör se bilden har anpassats med en ”meddelande av the Day” när din SSH-anslutning upprättas.

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Typ `exit` att Stäng SSH-anslutningen.

Du kan också ange image-versioner som är nu tillgängliga i galleriet.

```azurecli-interactive
az sig image-version list -g $sigResourceGroup -r $sigName -i $imageDefName -o table
```


## <a name="next-steps"></a>Nästa steg

Mer information om komponenterna i JSON-fil som används i den här artikeln finns [Image builder-mallreferensen](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).