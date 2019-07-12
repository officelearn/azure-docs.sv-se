---
title: Skapa en Linux-VM med Azure Image Builder (förhandsversion)
description: Skapa en virtuell Linux-dator med Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 2966a1803d0664312d71ba992a5ba65f73b27370
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67667514"
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

## <a name="setup-example-variables"></a>Konfigurera exempel variabler

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

## <a name="create-the-resource-group"></a>Skapa resursgruppen.
Det här används för att lagra avbildningen configuration mall artefakten och image.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Ange behörigheter för resursgruppen.
Ge Image Builder ”bidragsgivar” behörighet att skapa avbildningen i resursgruppen. Utan rätt behörighet misslyckas bild-versionen. 

Den `--assignee` värdet är app registrerings-ID för Image Builder-tjänsten. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>Ladda ned mall-exempel

En parametriserade bild configuration exempelmall har skapats för dig att använda. Hämta JSON-filen och konfigurera den med de variabler som du angav tidigare.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

Du kan ändra det här exemplet .json efter behov. Exempelvis kan du öka värdet för `buildTimeoutInMinutes` för längre körs versioner. Du kan redigera filen i Cloud Shell med `vi`.

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> För Källavbildningen, måste du alltid [ange en version](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure), du kan inte använda `latest`.
>
> Om du lägger till eller ändra resursgruppen där avbildningen distribueras, måste du kontrollera att den [behörigheter som har angetts för resursgruppen](#set-permissions-on-the-resource-group).


## <a name="submit-the-image-configuration"></a>Skicka avbildningen-konfiguration
Skicka bildkonfiguration till VM Image Builder-tjänsten

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Om den har slutförts, den returnerar ett meddelande och skapa en image builder configuration mall artefakt i $imageResourceGroup. Du kan se resursgruppen i portalen om du aktiverar ”Visa dolda typer”.

Dessutom skapas Image Builder i bakgrunden, en mellanlagrings resursgrupp i din prenumeration. Image Builder använder mellanlagrings resursgruppen för versionen av avbildningen. Namnet på resursgruppen som kommer att visas i följande format: `IT_<DestinationResourceGroup>_<TemplateName>`.

> [!IMPORTANT]
> Ta inte bort mellanlagring resursgruppen direkt. Om du tar bort avbildning mall artefakten raderas automatiskt mellanlagring resursgruppen. Mer information finns i den [Rensa](#clean-up) i slutet av den här artikeln.

Om tjänsten rapporterar ett fel vid avbildning configuration mall överföring, finns i den [felsökning](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) steg. Du måste också ta bort mallen innan du försöker skicka bygget. Ta bort mallen:

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Starta avbildningen-version

Starta avbildningen-version.


```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

Vänta tills versionen har slutförts i det här exemplet, det kan ta 10 – 15 minuter.

Om det uppstår några fel läser dessa [felsökning](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) steg.


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

När du är klar kan du ta bort resurserna.

Ta bort image builder-mall.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Ta bort resursgruppen bild.

```bash
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Nästa steg

Mer information om komponenterna i JSON-fil som används i den här artikeln finns [Image Builder-mallreferensen](image-builder-json.md).
