---
title: Använda Azure Image Builder med ett bild galleri för virtuella Windows-datorer (för hands version)
description: Skapa avbildningar av virtuella Windows-datorer med Azure Image Builder och delade avbildnings galleriet.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-windows
manager: gwallace
ms.openlocfilehash: 1d9763ccc5f5967b9fc9932a11fff655e6120fd0
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74976085"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>För hands version: skapa en Windows-avbildning och distribuera den till ett delat avbildnings Galleri 

Den här artikeln visar hur du kan använda Azure Image Builder för att skapa en avbildnings version i ett [delat avbildnings Galleri](shared-image-galleries.md)och sedan distribuera avbildningen globalt.

Vi kommer att använda en. JSON-mall för att konfigurera avbildningen. JSON-filen som vi använder är här: [helloImageTemplateforWinSIG. JSON](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/helloImageTemplateforWinSIG.json). 

För att distribuera avbildningen till ett delat avbildnings Galleri använder mallen [sharedImage](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#distribute-sharedimage) som värde för avsnittet `distribute` i mallen.

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
az provider show -n Microsoft.Compute | grep registrationState
```

Om de inte säger att de är registrerade kör du följande:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Compute
```

## <a name="set-variables-and-permissions"></a>Ange variabler och behörigheter 

Vi kommer att använda vissa delar av informationen flera gånger, så vi skapar några variabler för att lagra informationen. Ersätt värdena för variablerna, t. ex. `username` och `vmpassword`, med din egen information.

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

Skapa en variabel för ditt prenumerations-ID. Du kan få detta med `az account show | grep id`.

```azurecli-interactive
subscriptionID="Subscription ID"
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
   --publisher corpIT \
   --offer myOffer \
   --sku 2019 \
   --os-type Windows
```


## <a name="download-and-configure-the-json"></a>Hämta och konfigurera. JSON

Hämta. JSON-mallen och konfigurera den med dina variabler.

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

## <a name="create-the-image-version"></a>Skapa avbildnings versionen

Nästa del kommer att skapa avbildnings versionen i galleriet. 

Skicka avbildnings konfigurationen till Azure Image Builder-tjänsten.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforWinSIG01
```

Starta avbildnings versionen.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforWinSIG01 \
     --action Run 
```

Det kan ta en stund att skapa avbildningen och replikera den till båda regionerna. Vänta tills den här delen är klar innan du fortsätter med att skapa en virtuell dator.


## <a name="create-the-vm"></a>Skapa den virtuella datorn

Skapa en virtuell dator från avbildnings versionen som skapades av Azure Image Builder.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm001 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```


## <a name="verify-the-customization"></a>Verifiera anpassningen
Skapa en fjärr skrivbords anslutning till den virtuella datorn med det användar namn och lösen ord som du angav när du skapade den virtuella datorn. Öppna en kommando tolk i den virtuella datorn och skriv:

```console
dir c:\
```

Du bör se en katalog med namnet `buildActions` som skapades under bild anpassningen.


## <a name="clean-up-resources"></a>Rensa resurser
Om du nu vill försöka anpassa avbildnings versionen för att skapa en ny version av samma avbildning, **hoppar du över det här steget** och fortsätter med att [använda Azure Image Builder för att skapa en annan avbildnings version](image-builder-gallery-update-image-version.md).


Detta tar bort den avbildning som har skapats, tillsammans med alla andra resursfiler. Kontrol lera att du är färdig med distributionen innan du tar bort resurserna.

När du tar bort avbildnings Galleri resurser måste du ta bort alla avbildnings versioner innan du kan ta bort avbildnings definitionen som används för att skapa dem. Om du vill ta bort ett galleri måste du först ta bort alla bild definitionerna i galleriet.

Ta bort Image Builder-mallen.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforWinSIG01
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

Information om hur du uppdaterar avbildnings versionen som du skapade finns i [använda Azure Image Builder för att skapa en annan avbildnings version](image-builder-gallery-update-image-version.md).