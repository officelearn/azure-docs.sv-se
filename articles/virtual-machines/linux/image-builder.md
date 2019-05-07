---
title: Skapa en Linux-VM med Azure Image Builder (förhandsversion)
description: Skapa en virtuell Linux-dator med Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: 854645af95d780053d94668921e41ac189bbbfb7
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159517"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>Förhandsversion: Skapa en virtuell Linux-dator med Azure Image Builder

Den här artikeln visar hur du kan skapa en anpassad Linux-avbildning med hjälp av Azure Image Builder och Azure CLI. I exemplet i den här artikeln använder tre olika [anpassare](image-builder-json.md#properties-customize) för att anpassa avbildningen:

- Gränssnitt (ScriptUri) - nedladdningar och körs en [kommandoskriptet](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh).
- Shell (intern) - körningar specifika kommandon. I det här exemplet är infogade kommandon skapar en katalog och uppdaterar Operativsystemet.
- Fil - kopior en [filen från GitHub](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) i en katalog på den virtuella datorn.

Vi kommer att använda en exempelmall .json konfigurera avbildningen. JSON-fil som använder vi finns här: [helloImageTemplateLinux.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json). 

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
```

Om de inte svarar registrerade, kör du följande:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Vi kommer att använda vissa typer av information flera gånger, så att vi ska skapa några variabler för att lagra informationen.


```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myImageBuilerRGLinux
# Datacenter location - we are using West US 2 in this example
location=WestUS2
# Name for the image - we are using myBuilderImage in this example
imageName=myBuilderImage
# Run output name
runOutputName=aibLinux
```

Skapa en variabel för ditt prenumerations-ID. Du kan hämta den här med `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

Skapa resursgruppen.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```


Ge Image Builder behörighet att skapa resurser i resursgruppen. Den `--assignee` värdet är app registrerings-ID för Image Builder-tjänsten. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-json-example"></a>Ladda ned .json-exempel

Ladda ned exempel-JSON-fil och konfigurera den med de variabler som du skapade.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

## <a name="create-the-image"></a>Skapa avbildningen
Skicka bildkonfiguration till VM Image Builder-tjänsten

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Starta avbildningen-version.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

Vänta tills versionen har slutförts. Det kan ta cirka 15 minuter.


## <a name="create-the-vm"></a>Skapa den virtuella datorn

Skapa den virtuella datorn med den avbildning som du skapat.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Hämta IP-adressen från utdata för att skapa den virtuella datorn och använda den för att SSH till den virtuella datorn.

```azurecli-interactive
ssh azureuser@<pubIp>
```

Du bör se bilden har anpassats med ett meddelande om dagen när din SSH-anslutning upprättas!

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Typ `exit` när du är klar för att Stäng SSH-anslutningen.

## <a name="check-the-source"></a>Kontrollera källan

I bild Builder mallen i ”egenskaper”, visas Källavbildningen, anpassning skripta den körs, och var den ska distribueras.

```azurecli-interactive
cat helloImageTemplateLinux.json
```

Mer detaljerad information om den här JSON-fil finns i [Image builder-mallreferensen](image-builder-json.md)

## <a name="clean-up"></a>Rensa

När du är klar, ta bort resurserna.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01

az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Nästa steg

Mer information om komponenterna i JSON-fil som används i den här artikeln finns [Image Builder-mallreferensen](image-builder-json.md).