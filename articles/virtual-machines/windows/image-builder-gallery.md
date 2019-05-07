---
title: Använd Azure Image Builder med ett galleri med avbildningar för Windows-datorer (förhandsversion)
description: Skapa Windows-avbildningar med Azure Image Builder och delade bildgalleriet.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-widows
manager: jeconnoc
ms.openlocfilehash: 2453d37720bcf48b95b428cf78c6186de40b31aa
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65160117"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Förhandsversion: Skapa en Windows-avbildning och distribuera den till en delad bildgalleri 

Den här artikeln är att visa dig hur du kan använda Azure Image Builder för att skapa en Avbildningsversion i ett [delad bildgalleriet](shared-image-galleries.md), distribuera avbildningen globalt.

Vi kommer att använda en JSON-mall till bild på Konfigurera. JSON-fil som använder vi finns här: [helloImageTemplateforWinSIG.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/helloImageTemplateforWinSIG.json). 

Om du vill distribuera avbildningen till en delad bildgalleriet i mallen används [sharedImage](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) som värde för den `distribute` avsnitt i mallen.

> [!IMPORTANT]
> Azure Image Builder är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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
az provider show -n Microsoft.Compute | grep registrationState
```

Om de inte svarar registrerade, kör du följande:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Compute
```

## <a name="set-variables-and-permissions"></a>Ange variabler och behörigheter 

Vi kommer att använda vissa typer av information flera gånger, så att vi ska skapa några variabler för att lagra informationen. Ersätt värdena för variablerna som `username` och `vmpassword`, med din egen information.

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
username="azureuser"
vmpassword="passwordfortheVM"
```

Skapa en variabel för ditt prenumerations-ID. Du kan hämta den här med `az account show | grep id`.

```azurecli-interactive
subscriptionID="Subscription ID"
```

Skapa resursgruppen.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


Ge Azure Image Builder behörighet att skapa resurser i resursgruppen. Den `--assignee` värdet är app registrerings-ID för Image Builder-tjänsten. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```





## <a name="create-an-image-definition-and-gallery"></a>Skapa en avbildningsdefinitionen och galleriet

Skapa ett avbildningsgalleri. 

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Skapa en definition för avbildningen.

```azurecli-interactive
az sig image-definition create \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --publisher corpIT \
   --offer myOffer \
   --sku 2019 \
   --os-type Windows
```


## <a name="download-and-configure-the-json"></a>Hämta och konfigurera .json

Ladda ned JSON-mall och konfigurera det med dina variabler.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/helloImageTemplateforWinSIG.json -o helloImageTemplateforWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforWinSIG.json
```

## <a name="create-the-image-version"></a>Skapa versionsnumret för avbildningen

Den här nästa del skapar versionsnumret för avbildningen i galleriet. 

Skicka bildkonfiguration till tjänsten Azure Image Builder.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforWinSIG01
```

Starta avbildningen-version.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforWinSIG01 \
     --action Run 
```

Skapa avbildningen och replikera dem till båda regionerna kan ta en stund. Vänta tills den här delen är klar innan du fortsätter att skapa en virtuell dator.


## <a name="create-the-vm"></a>Skapa den virtuella datorn

Skapa en virtuell dator från versionsnumret för avbildningen som har skapats med Azure Image Builder.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm001 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```


## <a name="verify-the-customization"></a>Verifiera anpassning
Skapa en fjärrskrivbordsanslutning till den virtuella datorn med användarnamnet och lösenordet du angav när du skapade den virtuella datorn. Öppna en kommandotolk och Skriv inuti den virtuella datorn:

```console
dir c:\
```

Du bör se en katalog med namnet `buildActions` som skapades under avbildningen anpassning.


## <a name="clean-up-resources"></a>Rensa resurser
Om du vill prova igen anpassa versionsnumret för avbildningen för att skapa en ny version av samma avbildning **hoppa över detta steg** och gå in på [Använd Azure Image Builder för att skapa en annan Avbildningsversion](image-builder-gallery-update-image-version.md).


Detta tar bort den avbildning som har skapats, tillsammans med alla andra resursfiler. Kontrollera att du är klar med den här distributionen innan du tar bort resurserna.

När du tar bort avbildning galleriresurser måste du ta bort alla avbildningsversioner innan du kan ta bort avbildningsdefinitionen som används för att skapa dem. Om du vill ta bort ett galleri, måste du först har tagit bort alla definitioner för avbildning i galleriet.

Ta bort image builder-mall.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforWinSIG01
```

Hämta versionsnumret för avbildningen som skapas i image builder, det alltid börjar med `0.`, och ta sedan bort versionsnumret för avbildningen

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


Ta bort avbildningsdefinitionen.

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

Läs hur du uppdaterar versionsnumret för avbildningen som du skapade i [Använd Azure Image Builder för att skapa en annan Avbildningsversion](image-builder-gallery-update-image-version.md).