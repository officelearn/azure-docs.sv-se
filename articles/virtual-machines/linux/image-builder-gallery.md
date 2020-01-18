---
title: Använda Azure Image Builder med ett bild galleri för virtuella Linux-datorer (för hands version)
description: Skapa virtuella Linux-avbildningar med Azure Image Builder och delade avbildnings galleriet.
author: cynthn
ms.author: cynthn
ms.date: 04/20/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 08441a98d9104109b4cfc130ab6adb31dc4fce45
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2020
ms.locfileid: "76260522"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>För hands version: skapa en Linux-avbildning och distribuera den till ett delat avbildnings Galleri 

Den här artikeln visar hur du kan använda Azure Image Builder och Azure CLI för att skapa en avbildnings version i ett [delat avbildnings Galleri](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)och sedan distribuera avbildningen globalt. Du kan också göra detta med hjälp av [Azure PowerShell](../windows/image-builder-gallery.md).


Vi kommer att använda en Sample. JSON-mall för att konfigurera avbildningen. JSON-filen som vi använder är här: [helloImageTemplateforSIG. JSON](https://github.com/danielsollondon/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json). 

För att distribuera avbildningen till ett delat avbildnings Galleri använder mallen [sharedImage](image-builder-json.md#distribute-sharedimage) som värde för avsnittet `distribute` i mallen.

> [!IMPORTANT]
> Azure Image Builder är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Registrera funktionerna
Om du vill använda Azure Image Builder i för hands versionen måste du registrera den nya funktionen.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Kontrol lera status för funktions registreringen.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Kontrol lera registreringen.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

Om de inte säger att de är registrerade kör du följande:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="set-variables-and-permissions"></a>Ange variabler och behörigheter 

Vi kommer att använda vissa delar av informationen flera gånger, så vi skapar några variabler för att lagra informationen.

För för hands versionen stöder Image Builder bara att skapa anpassade avbildningar i samma resurs grupp som den hanterade avbildningen. Uppdatera resurs grupp namnet i det här exemplet så att det blir samma resurs grupp som din käll hanterade avbildning.

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

Skapa en variabel för ditt prenumerations-ID. Du kan få detta med `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Skapa resursgruppen.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```


Ge Azure Image Builder behörighet att skapa resurser i den resurs gruppen. `--assignee`-värdet är appens registrerings-ID för tjänsten Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```





## <a name="create-an-image-definition-and-gallery"></a>Skapa en bild definition och ett galleri

Om du vill använda Image Builder med ett delat bild galleri måste du ha ett befintligt bild galleri och en bild definition. Image Builder skapar inte bild galleriet och bild definitionen åt dig.

Om du inte redan har ett galleri och en bild definition som ska användas börjar du med att skapa dem. Börja med att skapa ett bild galleri.

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Skapa sedan en avbildnings definition.

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


## <a name="download-and-configure-the-json"></a>Hämta och konfigurera. JSON

Hämta. JSON-mallen och konfigurera den med dina variabler.

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

## <a name="create-the-image-version"></a>Skapa avbildnings versionen

Nästa del kommer att skapa avbildnings versionen i galleriet. 

Skicka avbildnings konfigurationen till Azure Image Builder-tjänsten.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Starta avbildnings versionen.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

Det kan ta en stund att skapa avbildningen och replikera den till båda regionerna. Vänta tills den här delen är klar innan du fortsätter med att skapa en virtuell dator.


## <a name="create-the-vm"></a>Skapa den virtuella datorn

Skapa en virtuell dator från avbildnings versionen som skapades av Azure Image Builder.

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

Du bör se att avbildningen har anpassats till ett *meddelande om dygnet* så snart din SSH-anslutning har upprättats!

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du nu vill försöka anpassa avbildnings versionen för att skapa en ny version av samma avbildning, hoppar du över nästa steg och fortsätter med att [använda Azure Image Builder för att skapa en annan avbildnings version](image-builder-gallery-update-image-version.md).


Detta tar bort den avbildning som har skapats, tillsammans med alla andra resursfiler. Kontrol lera att du är färdig med distributionen innan du tar bort resurserna.

När du tar bort avbildnings Galleri resurser måste du ta bort alla avbildnings versioner innan du kan ta bort avbildnings definitionen som används för att skapa dem. Om du vill ta bort ett galleri måste du först ta bort alla bild definitionerna i galleriet.

Ta bort Image Builder-mallen.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Hämta avbildnings versionen som skapats av Image Builder, detta börjar alltid med `0.`och ta sedan bort avbildnings versionen

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


Ta bort avbildnings definitionen.

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

Ta bort resurs gruppen.

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Azures delade bild gallerier](shared-image-galleries.md).