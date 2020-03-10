---
title: Skapa en virtuell Linux-dator med Azure Image Builder (för hands version)
description: Skapa en virtuell Linux-dator med Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.openlocfilehash: 15a3b39b1466ffec87971b8f054ca916567d89d7
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944945"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>För hands version: skapa en virtuell Linux-dator med Azure Image Builder

Den här artikeln visar hur du kan skapa en anpassad Linux-avbildning med hjälp av Azure Image Builder och Azure CLI. Exemplet i den här artikeln använder tre olika [anpassningar](image-builder-json.md#properties-customize) för att anpassa avbildningen:

- Shell (ScriptUri) – hämtar och kör ett [Shell-skript](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh).
- Shell (infogat) – kör specifika kommandon. I det här exemplet inkluderar de infogade kommandona hur du skapar en katalog och uppdaterar operativ systemet.
- Fil – kopierar en [fil från GitHub](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) till en katalog på den virtuella datorn.

Du kan också ange en `buildTimeoutInMinutes`. Standardvärdet är 240 minuter, och du kan öka Bygg tiden för att kunna köra versioner längre.

Vi kommer att använda en Sample. JSON-mall för att konfigurera avbildningen. JSON-filen som vi använder är här: [helloImageTemplateLinux. JSON](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json). 

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

## <a name="setup-example-variables"></a>Exempel på variabler för konfigurering

Vi kommer att använda vissa delar av informationen flera gånger, så vi skapar några variabler för att lagra informationen.


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

Skapa en variabel för ditt prenumerations-ID. Du kan få detta med `az account show | grep id`.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

## <a name="create-the-resource-group"></a>Skapa resursgruppen.
Detta används för att lagra bild konfigurations mal len artefakt och avbildningen.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Ange behörigheter för resurs gruppen
Ge avbildnings verktyget deltagare behörighet att skapa avbildningen i resurs gruppen. Utan rätt behörigheter går det inte att generera avbildningen. 

`--assignee`-värdet är appens registrerings-ID för tjänsten Image Builder. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>Hämta mal Lav exemplet

En parameter som exempel på en avbildnings konfiguration har skapats som du kan använda. Hämta exempel-. JSON-filen och konfigurera den med de variabler du angav tidigare.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

Du kan ändra det här exemplet. JSON efter behov. Du kan till exempel öka värdet för `buildTimeoutInMinutes` så att du kan köra versioner längre. Du kan redigera filen i Cloud Shell med hjälp av en text redigerare som `vi`.

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> För käll avbildningen måste du alltid [Ange en version](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure), du kan inte använda `latest`.
>
> Om du lägger till eller ändrar resurs gruppen där avbildningen distribueras måste du kontrol lera att [behörigheterna har angetts för resurs gruppen](#set-permissions-on-the-resource-group).


## <a name="submit-the-image-configuration"></a>Skicka avbildnings konfigurationen
Skicka avbildnings konfigurationen till tjänsten VM Image Builder

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Om det är klart kommer det att returnera ett meddelande om att det är klart och skapa en avbildning för konfigurations mal len för avbildnings byggare i $imageResourceGroup. Du kan se resurs gruppen i portalen om du aktiverar Visa dolda typer.

I bakgrunden skapar Image Builder en resurs grupp för mellanlagring i din prenumeration. Image Builder använder mellanlagrings resurs gruppen för avbildnings versionen. Namnet på resurs gruppen kommer att ha följande format: `IT_<DestinationResourceGroup>_<TemplateName>`.

> [!IMPORTANT]
> Ta inte bort den mellanlagrings resurs gruppen direkt. Om du tar bort bild mal len artefakt tas den mellanlagrings resurs gruppen bort automatiskt. Mer information finns i avsnittet [Rensa](#clean-up) i slutet av den här artikeln.

Om tjänsten rapporterar ett fel under sändningen av avbildnings konfigurations mal len, se [fel söknings](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) stegen. Du måste också ta bort mallen innan du försöker skicka in bygget igen. Så här tar du bort mallen:

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Starta Image-versionen

Starta avbildnings versionen.


```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

Vänta tills versionen har slutförts, det kan ta 10-15 minuter för det här exemplet.

Om du stöter på några fel kan du läsa följande [fel söknings](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) steg.


## <a name="create-the-vm"></a>Skapa den virtuella datorn

Skapa den virtuella datorn med den avbildning som du har skapat.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Hämta IP-adressen från utdata från att skapa den virtuella datorn och Använd den för SSH till den virtuella datorn.

```azurecli-interactive
ssh azureuser@<pubIp>
```

Du bör se att avbildningen har anpassats till ett meddelande om dygnet så snart din SSH-anslutning har upprättats!

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

Skriv `exit` när du är färdig för att stänga SSH-anslutningen.

## <a name="check-the-source"></a>Kontrol lera källan

I Image Builder-mallen, i "Properties", kommer du att se käll avbildningen, anpassnings skriptet som den körs och var den distribueras.

```azurecli-interactive
cat helloImageTemplateLinux.json
```

Mer detaljerad information om denna. JSON-fil finns i [referens för Image Builder-mallar](image-builder-json.md)

## <a name="clean-up"></a>Rensa

När du är färdig kan du ta bort resurserna.

Ta bort Image Builder-mallen.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Ta bort resurs gruppen avbildning.

```bash
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Nästa steg

Mer information om komponenterna i. JSON-filen som används i den här artikeln finns i [referens för Image Builder-mallar](image-builder-json.md).
