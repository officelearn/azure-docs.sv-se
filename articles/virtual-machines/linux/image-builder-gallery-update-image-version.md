---
title: Skapa en ny VM-avbildningsversion från en befintlig avbildningsversion med Azure Image Builder (förhandsgranskning)
description: Skapa en ny VM-avbildningsversion från en befintlig avbildningsversion med Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: 5766e91dc6a17d50c46d396dd8a68d17081e0926
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246814"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Preview: Skapa en ny VM-avbildningsversion från en befintlig avbildningsversion med Azure Image Builder

I den här artikeln visas hur du tar en befintlig bildversion i ett [delat bildgalleri,](shared-image-galleries.md)uppdaterar den och publicerar den som en ny bildversion i galleriet.

Vi kommer att använda ett exempel .json mall för att konfigurera avbildningen. Den .json filen vi använder är här: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json). 


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

Om du använde [Skapa en bild och distribuera till ett delat bildgalleri](image-builder-gallery.md) för att skapa ditt delade bildgalleri har du redan skapat några av de variabler vi behöver. Om inte, ställ in några variabler som ska användas för det här exemplet.

För förhandsgranskning stöder bildverktyget bara att skapa anpassade bilder i samma resursgrupp som källhanterad avbildning. Uppdatera resursgruppsnamnet i det här exemplet så att det är samma resursgrupp som källhanterad avbildning.


```console
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

Skapa en variabel för ditt prenumerations-ID. Du kan få `az account show | grep id`detta med .

```console
subscriptionID=<Subscription ID>
```

Hämta den bildversion som du vill uppdatera.

```azurecli
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```


Om du redan har ett eget delat bildgalleri och inte följt föregående exempel måste du tilldela behörigheter för Image Builder för att komma åt resursgruppen, så att det kan komma åt galleriet.


```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="modify-helloimage-example"></a>Ändra exempel på helloImage
Du kan granska exemplet vi är på väg att använda genom att öppna .json-filen här: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) tillsammans med [Image Builder-mallreferensen](image-builder-json.md). 


Hämta .json-exemplet och konfigurera det med dina variabler. 

```console
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

Skicka avbildningskonfigurationen till tjänsten VM Image Builder.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIGfromSIG01
```

Starta bildversionen.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIGfromSIG01 \
     --action Run 
```

Vänta tills avbildningen har byggts och replikeringen innan du går vidare till nästa steg.


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

Skapa en SSH-anslutning till den virtuella datorn med den offentliga IP-adressen för den virtuella datorn.

```console
ssh azureuser@<pubIp>
```

Du bör se att bilden har anpassats med en "Dagens meddelande" så snart din SSH-anslutning har upprättats.

```output
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Skriv `exit` för att stänga SSH-anslutningen.

Du kan också lista de bildversioner som nu är tillgängliga i galleriet.

```azurecli-interactive
az sig image-version list -g $sigResourceGroup -r $sigName -i $imageDefName -o table
```


## <a name="next-steps"></a>Nästa steg

Mer information om komponenterna i .json-filen som används i den här artikeln finns i [Referens för mallreferens för bildbyggare](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).