---
title: Skapa en virtuell Linux-dator med Azure Image Builder (förhandsversion)
description: Skapa en virtuell Linux-dator med Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.openlocfilehash: 0d36d7db4d85ece8de77040925c535305951562b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066674"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>Preview: Skapa en virtuell Linux-dator med Azure Image Builder

Den här artikeln visar hur du kan skapa en anpassad Linux-avbildning med Azure Image Builder och Azure CLI. I exemplet i den här artikeln används tre olika [anpassare](image-builder-json.md#properties-customize) för att anpassa avbildningen:

- Shell (ScriptUri) - hämtar och kör ett [skalskript](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh).
- Shell (infogad) - kör specifika kommandon. I det här exemplet omfattar infogade kommandon att skapa en katalog och uppdatera operativsystemet.
- Fil - kopierar en [fil från GitHub](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) till en katalog på den virtuella datorn.

Du kan också `buildTimeoutInMinutes`ange en . Standardvärdet är 240 minuter och du kan öka en byggtid för att möjliggöra längre löpversioner.

Vi kommer att använda ett exempel .json mall för att konfigurera avbildningen. Den .json filen vi använder är här: [helloImageTemplateLinux.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json). 

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

## <a name="setup-example-variables"></a>Exempelvariabler för inställningar

Vi kommer att använda vissa bitar av information upprepade gånger, så vi kommer att skapa några variabler för att lagra denna information.


```console
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myImageBuilerRGLinux
# Datacenter location - we are using West US 2 in this example
location=WestUS2
# Name for the image - we are using myBuilderImage in this example
imageName=myBuilderImage
# Run output name
runOutputName=aibLinux
```

Skapa en variabel för ditt prenumerations-ID. Du kan få `az account show | grep id`detta med .

```console
subscriptionID=<Your subscription ID>
```

## <a name="create-the-resource-group"></a>Skapa resursgruppen.
Detta används för att lagra avbildningskonfigurationsmallens artefakt och avbildningen.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Ange behörigheter för resursgruppen
Ge Image Builder "deltagare" behörighet att skapa bilden i resursgruppen. Utan rätt behörigheter misslyckas bildversionen. 

`--assignee` Värdet är appregistrerings-ID för Image Builder-tjänsten. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>Ladda ned mallexemplet

En parameteriserad exempelavbildningskonfigurationsmall har skapats som du kan använda. Hämta exempelfilen .json och konfigurera den med de variabler som du angav tidigare.

```bash
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

Du kan ändra det här exemplet .json efter behov. Du kan till exempel öka `buildTimeoutInMinutes` värdet för att tillåta längre löpversioner. Du kan redigera filen i Cloud Shell `vi`med hjälp av en textredigerare som .

```bash
vi helloImageTemplateLinux.json
```

> [!NOTE]
> För källavbildning måste du alltid ange `latest`en [version](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure), du kan inte använda .
>
> Om du lägger till eller ändrar resursgruppen där bilden distribueras måste du kontrollera att [behörigheterna är inställda för resursgruppen](#set-permissions-on-the-resource-group).


## <a name="submit-the-image-configuration"></a>Skicka bildkonfigurationen
Skicka avbildningskonfigurationen till tjänsten VM Image Builder

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Om den slutförs returneras ett meddelande om lyckade lösningar och en konfigurationsmall för avbildningsverktyget skapas i $imageResourceGroup. Du kan se resursgruppen i portalen om du aktiverar Visa dolda typer.

I bakgrunden skapar Image Builder också en mellanlagringsresursgrupp i din prenumeration. Image Builder använder mellanlagringsresursgruppen för avbildningsversionen. Namnet på resursgruppen kommer att vara `IT_<DestinationResourceGroup>_<TemplateName>`i det här formatet: .

> [!IMPORTANT]
> Ta inte bort mellanlagringsresursgruppen direkt. Om du tar bort bildmallens artefakt tas mellanlagringsresursgruppen bort automatiskt. Mer information finns i avsnittet [Rensa i](#clean-up) slutet av den här artikeln.

Om tjänsten rapporterar ett fel under inlämningen av avbildningskonfigurationsmallen läser du [felsökningsstegen.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) Du måste också ta bort mallen innan du försöker skicka in versionen igen. Så här tar du bort mallen:

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Starta bildversionen

Starta bildversionen.


```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

Vänta tills bygget är klart, för det här exemplet kan det ta 10-15 minuter.

Om du stöter på några [troubleshooting](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) fel kan du läsa de här felsökningsstegen.


## <a name="create-the-vm"></a>Skapa den virtuella datorn

Skapa den virtuella datorn med den avbildning du har skapat.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Hämta IP-adressen från utdata för att skapa den virtuella datorn och använda den till SSH till den virtuella datorn.

```bash
ssh azureuser@<pubIp>
```

Du bör se bilden anpassades med ett meddelande för dagen så fort din SSH-anslutning är etablerad!

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Skriv `exit` när du är klar för att stänga SSH-anslutningen.

## <a name="check-the-source"></a>Kontrollera källan

I Image Builder-mallen, i "Egenskaper", ser du källbilden, anpassningsskriptet som den körs och var den distribueras.

```bash
cat helloImageTemplateLinux.json
```

Mer detaljerad information om den här .json-filen finns i [Referens för mallreferens för bildbyggare](image-builder-json.md)

## <a name="clean-up"></a>Rensa

När du är klar kan du ta bort resurserna.

Ta bort bildverktygets mall.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Ta bort bildresursgruppen.

```azurecli
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Nästa steg

Mer information om komponenterna i .json-filen som används i den här artikeln finns i [Image Builder-mallreferensen](image-builder-json.md).
