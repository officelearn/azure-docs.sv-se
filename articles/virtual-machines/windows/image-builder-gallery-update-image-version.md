---
title: Skapa en ny avbildningsversion från en befintlig avbildningsversion med Azure Image Builder (förhandsgranskning)
description: Skapa en ny VM-avbildningsversion från en befintlig avbildningsversion med Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: 60a37588ddcac339d9545fb6f597ef7bdc17ccb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247375"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder"></a>Preview: Skapa en ny VM-avbildningsversion från en befintlig avbildningsversion med Azure Image Builder

I den här artikeln visas hur du tar en befintlig bildversion i ett [delat bildgalleri,](shared-image-galleries.md)uppdaterar den och publicerar den som en ny bildversion i galleriet.

Vi kommer att använda ett exempel .json mall för att konfigurera avbildningen. Den .json filen vi använder är här: [helloImageTemplateforSIGfromWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json). 

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
az provider show -n Microsoft.Compute | grep registrationState
```

Om de inte säger registrerade, kör följande:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Compute
```


## <a name="set-variables-and-permissions"></a>Ange variabler och behörigheter

Om du använde [Skapa en bild och distribuera till ett delat bildgalleri](image-builder-gallery.md) för att skapa ditt delade bildgalleri har du redan skapat de variabler vi behöver. Om inte, ställ in några variabler som ska användas för det här exemplet.

För förhandsgranskning stöder bildverktyget bara att skapa anpassade bilder i samma resursgrupp som källhanterad avbildning. Uppdatera resursgruppsnamnet i det här exemplet så att det är samma resursgrupp som källhanterad avbildning.

```azurecli-interactive
# Resource group name - we are using ibsigRG in this example
sigResourceGroup=myIBWinRG
# Datacenter location - we are using West US 2 in this example
location=westus
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=my22stSIG
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=winSvrimages
# image distribution metadata reference name
runOutputName=w2019SigRo
# User name and password for the VM
username="user name for the VM"
vmpassword="password for the VM"
```

Skapa en variabel för ditt prenumerations-ID. Du kan få `az account show | grep id`detta med .

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Hämta den bildversion som du vill uppdatera.

```azurecli-interactive
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
Du kan granska exemplet vi är på väg att använda genom att öppna .json-filen här: [helloImageTemplateforSIGfromSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) tillsammans med [Image Builder-mallreferensen](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 


Hämta .json-exemplet och konfigurera det med dina variabler. 

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json -o helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromWinSIG.json
```

## <a name="create-the-image"></a>Skapa avbildningen

Skicka avbildningskonfigurationen till tjänsten VM Image Builder.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n imageTemplateforSIGfromWinSIG01
```

Starta bildversionen.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n imageTemplateforSIGfromWinSIG01 \
     --action Run 
```

Vänta tills avbildningen har byggts och replikeringen innan du går vidare till nästa steg.


## <a name="create-the-vm"></a>Skapa den virtuella datorn

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm002 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```

## <a name="verify-the-customization"></a>Verifiera anpassningen
Skapa en anslutning till fjärrskrivbord till den virtuella datorn med det användarnamn och lösenord som du angav när du skapade den virtuella datorn. Öppna en cmd-prompt i den virtuella datorn och skriv:

```console
dir c:\
```

Du bör nu se två kataloger:
- `buildActions`som skapades i den första bildversionen.
- `buildActions2`som skapades som en del upp uppdatera den första bildversionen för att skapa den andra bildversionen.


## <a name="next-steps"></a>Nästa steg

Mer information om komponenterna i .json-filen som används i den här artikeln finns i [Referens för mallreferens för bildbyggare](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).