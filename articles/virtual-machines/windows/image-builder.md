---
title: Skapa en Virtuell Windows-dator med Azure Image Builder (förhandsversion)
description: Skapa en Virtuell Windows-dator med Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 07/31/2019
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.openlocfilehash: 269b2f4674f2c99fc438c1a7be65e5660ca58d08
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869498"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Preview: Skapa en Virtuell Windows-dator med Azure Image Builder

Den här artikeln är att visa dig hur du kan skapa en anpassad Windows-avbildning med Hjälp av Azure VM Image Builder. I exemplet i den här artikeln används [anpassare](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#properties-customize) för att anpassa avbildningen:
- PowerShell (ScriptUri) - ladda ner och köra ett [PowerShell-skript](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1).
- Windows Restart - startar om den virtuella datorn.
- PowerShell (infogad) - kör ett specifikt kommando. I det här exemplet skapas en `mkdir c:\\buildActions`katalog på den virtuella datorn med .
- Fil - kopiera en fil från GitHub till den virtuella datorn. I det här exemplet `c:\buildArtifacts\index.html` [kopieras index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) till på den virtuella datorn.

Du kan också `buildTimeoutInMinutes`ange en . Standardvärdet är 240 minuter och du kan öka en byggtid för att möjliggöra längre löpversioner.

Vi kommer att använda ett exempel .json mall för att konfigurera avbildningen. Den .json filen vi använder är här: [helloImageTemplateWin.json](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


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

## <a name="set-variables"></a>Ange variabler

Vi kommer att använda vissa bitar av information upprepade gånger, så vi kommer att skapa några variabler för att lagra denna information.


```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myWinImgBuilderRG
# Region location 
location=WestUS2
# Name for the image 
imageName=myWinBuilderImage
# Run output name
runOutputName=aibWindows
# name of the image to be created
imageName=aibWinImage
```

Skapa en variabel för ditt prenumerations-ID. Du kan få `az account show | grep id`detta med .

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Den här resursgruppen används för att lagra avbildningskonfigurationsmallens artefakt och avbildningen.


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Ange behörigheter för resursgruppen

Ge Image Builder "deltagare" behörighet att skapa bilden i resursgruppen. Utan detta kommer bildversionen att misslyckas. 

`--assignee` Värdet är appregistrerings-ID för Image Builder-tjänsten. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```


## <a name="download-the-image-configuration-template-example"></a>Ladda ned exempel på bildkonfigurationsmall

En parameteriserad avbildningskonfigurationsmall har skapats för att du ska kunna prova. Hämta exempelfilen .json och konfigurera den med de variabler som du angav tidigare.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json

```

Du kan ändra det här exemplet i `vi`terminalen med hjälp av en textredigerare som .

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> För källavbildningen måste du alltid ange `latest`en [version](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure), du kan inte använda .
> Om du lägger till eller ändrar resursgruppen där bilden distribueras till måste du ange [att behörigheterna anges](#set-permissions-on-the-resource-group) i resursgruppen.
 
## <a name="create-the-image"></a>Skapa avbildningen

Skicka avbildningskonfigurationen till tjänsten VM Image Builder

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

När du är klar returnerar detta ett lyckat `Image Builder Configuration Template` meddelande `$imageResourceGroup`tillbaka till konsolen och skapar ett i . Du kan se den här resursen i resursgruppen i Azure-portalen om du aktiverar Visa dolda typer.

I bakgrunden skapar Image Builder också en mellanlagringsresursgrupp i prenumerationen. Den här resursgruppen används för avbildningsversionen. Det kommer att vara i detta format:`IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> Du får inte ta bort mellanlagringsresursgruppen direkt. Ta först bort bildmallens artefakt, vilket gör att mellanlagringsresursgruppen tas bort.

Om tjänsten rapporterar ett fel under inlämningen av avbildningskonfigurationsmallen:
-  Läs de här [felsökningsstegen.](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) 
- Du måste ta bort mallen med hjälp av följande kodavsnitt innan du försöker skicka in den igen.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Starta bildversionen
Starta bildbyggnadsprocessen med [az-resurs-invoke-action](/cli/azure/resource#az-resource-invoke-action).

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Vänta tills bygget är klart. Detta kan ta ungefär 15 minuter.

Om du stöter på några [troubleshooting](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) fel kan du läsa de här felsökningsstegen.


## <a name="create-the-vm"></a>Skapa den virtuella datorn

Skapa den virtuella datorn med den avbildning du har skapat. Ersätt * \<lösenord>* med ditt eget `aibuser` lösenord för den virtuella datorn.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>Verifiera anpassningen

Skapa en anslutning till fjärrskrivbord till den virtuella datorn med det användarnamn och lösenord som du angav när du skapade den virtuella datorn. Öppna en cmd-prompt i den virtuella datorn och skriv:

```console
dir c:\
```

Du bör se dessa två kataloger som skapats under bildanpassning:
- buildActions
- buildArtifacts

## <a name="clean-up"></a>Rensa

När du är klar tar du bort resurserna.

### <a name="delete-the-image-builder-template"></a>Ta bort bildverktygets mall

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-image-resource-group"></a>Ta bort bildresursgruppen

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Nästa steg

Mer information om komponenterna i .json-filen som används i den här artikeln finns i [Referens för mallreferens för bildbyggare](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
