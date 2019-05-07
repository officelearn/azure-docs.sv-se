---
title: Skapa en Azure Image Builder-mall (förhandsversion)
description: Lär dig hur du skapar en mall som ska användas med Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: jeconnoc
ms.openlocfilehash: b4646879eb7eeecf41852baab7ab64e4053b05e1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159607"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Förhandsversion: Skapa en Azure Image Builder-mall 

Azure Image Builder använder en JSON-fil för att skicka information till Image Builder-tjänsten. I den här artikeln ska vi gå igenom avsnitten i json-filen så att du kan skapa dina egna. Exempel på fullständiga .json-filer finns i den [Azure Image Builder GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).

Det här är den grundläggande mallformat:

```json
 { 
    "type": "Microsoft.VirtualMachineImages/imageTemplates", 
    "apiVersion": "2019-05-01-preview", 
    "location": "<region>", 
    "tags": {
        "<name": "<value>",
        "<name>": "<value>"
             }
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "<build timeout in minutes>": {}, 
        "build": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Typ och API-version

Den `type` är resurstyp som måste vara `"Microsoft.VirtualMachineImages/imageTemplates"`. Den `apiVersion` ändras med tiden då API-ändringar, men bör vara `"2019-05-01-preview"` för förhandsversionen.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Location

Platsen är den region där den anpassade avbildningen skapas. För Image Builder-förhandsversionen stöds följande regioner:

- Östra USA
- USA, östra 2
- Västra centrala USA
- Västra USA
- Västra USA 2


```json
    "location": "<region>",
```
    
## <a name="depends-on-optional"></a>Beror på (valfritt)

Det här valfria avsnittet kan användas för att säkerställa att beroenden har slutförts innan du fortsätter. 

```json
    "dependsOn": [],
```

Mer information finns i [definiera resursberoenden](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson).

## <a name="identity"></a>Identitet
Som standard Image Builder stöder med hjälp av skript eller kopiera filer från flera platser, t.ex GitHub och Azure storage. Om du vill använda dem måste de vara allmänt tillgänglig.

Du kan också använda en Azure User-Assigned hanterad identitet, definieras av dig, för att tillåta åtkomst för Image Builder Azure Storage, så länge identiteten har beviljats 'Storage Blob Data-läsare ”minst på Azure storage-kontot. Det innebär att du inte behöver göra storage-blobbar som är externt tillgänglig eller installationen SAS-token.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

Ett komplett exempel finns i [ använder en Azure User-Assigned hanterad identitet för att komma åt filer i Azure Storage](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

Image Builder-stöd för en User-Assigned identitet: • en enda identitet har endast stöd för • inte har stöd för anpassade domännamn

Mer information finns i [vad är hanterade identiteter för Azure-resurser?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
Mer information om hur du distribuerar den här funktionen finns i [konfigurera hanterade identiteter för Azure-resurser på en Azure virtuell dator med Azure CLI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

## <a name="properties-source"></a>Egenskaper: källa

Den `source` avsnittet innehåller information om den käll-avbildning som ska användas av Image Builder.

API: et kräver ett 'SourceType' som definierar källan för bild-build, det finns tre typer:
- ISO - Använd det här när källan är en RHEL ISO.
- PlatformImage - angett Källavbildningen är en Marketplace-avbildning.
- ManagedImage – Använd det här när du startar från en vanlig hanterad avbildning.
- SharedImageVersion - används när du använder en Avbildningsversion i en delad Avbildningsgalleri som källa.

### <a name="iso-source"></a>ISO-källa

Azure Image Builder stöder endast med hjälp av publicerade Red Hat Enterprise Linux 7.x binära DVD ISO, för förhandsgranskning. Image Builder stöder:
- RHEL 7.3 
- RHEL 7.4 
- RHEL 7.5 
 
```json
"source": {
       "type": "ISO",
       "sourceURI": "<sourceURI from the download center>",
       "sha256Checksum": "<checksum associated with ISO>"
}
```

Att hämta den `sourceURI` och `sha256Checksum` värden, gå till `https://access.redhat.com/downloads` Välj produkten **Red Hat Enterprise Linux**, och en version som stöds. 

I listan över **installationsprogram och avbildningar för Red Hat Enterprise Linux Server**, måste du kopiera länken för Red Hat Enterprise Linux 7.x binära DVD och kontrollsumman.

> [!NOTE]
> Åtkomsttoken av länkar uppdateras regelbundet, så att varje gång som du vill skicka in en mall måste du kontrollera om RH länkar adressen har ändrats.
 
### <a name="platformimage-source"></a>PlatformImage källa 
Azure Image Builder har stöd för följande Azure Marketplace-avbildningar:
* Ubuntu 18.04
* Ubuntu 16.04
* RHEL 7.6
* CentOS 7.6
* Windows 2016
* Windows 2019

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "18.04.201903060"
        },
```


De här egenskaperna är samma som används för att skapa Virtuella datorer, med hjälp av AZ CLI, kör den nedan för att hämta egenskaperna: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

> [!NOTE]
> Versionen får inte vara ”senaste”, måste du använda kommandot ovan för att få ett versionsnummer. 

### <a name="managedimage-source"></a>ManagedImage källa

Anger Källavbildningen som en befintlig hanterad avbildning av en generaliserad virtuell Hårddisk eller virtuell dator. Hanterade Källavbildningen måste vara av ett operativsystem som stöds och vara i samma region som din Azure Image Builder-mall. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

Den `imageId` ska vara ResourceId av hanterad avbildning. Använd `az image list` att lista tillgängliga avbildningar.


### <a name="sharedimageversion-source"></a>SharedImageVersion källa
Anger Källavbildningen en befintlig Avbildningsversion i en delad Avbildningsgalleri. Versionsnumret för avbildningen måste vara av ett operativsystem som stöds och avbildningen måste replikeras till samma region som din Azure Image Builder-mall. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

Den `imageVersionId` ska vara ResourceId av versionsnumret för avbildningen. Använd [az sig versionsnumret för avbildningen lista](/cli/azure/sig/image-version#az-sig-image-version-list) lista bild versioner.

## <a name="properties-customize"></a>Egenskaper: anpassa


Image Builder har stöd för flera anpassare. Anpassare är funktioner som används för att anpassa din avbildning, till exempel köra skript eller omstart av servrarna. 

När du använder `customize`: 
- Du kan använda flera anpassare, men de måste ha ett unikt `name`.
- Anpassare köra i den ordning som anges i mallen.
- Om en anpassare misslyckas, och sedan komponenten hela anpassning misslyckas och rapportera tillbaka ett fel.
- Överväg hur lång tid din avbildning version kommer kräver och justera egenskapen 'buildTimeoutInMinutes' om du vill tillåta image builder tillräckligt med tid att slutföra.
- Det rekommenderas starkt att du noggrant Testa skriptet innan du använder den i en mall. Felsöka skriptet på din egen virtuella dator blir enklare.
- Placera inte känsliga data i skripten. 
- Skript-platser måste vara offentligt åtkomlig, såvida du inte använder [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

```json
        "customize": [
            {
                "type": "Shell",
                "name": "<name>",
                "scriptUri": "<path to script>"
            },
            {
                "type": "Shell",
                "name": "<name>",
                "inline": [
                    "<command to run inline>",
                ]
            }

        ],
```     

 
Anpassa-avsnittet är en matris. Azure Image Builder körs via anpassare i sekventiell ordning. Eventuella fel i alla systemanpassare misslyckas skapandeprocessen. 
 
 
### <a name="shell-customizer"></a>Shell systemanpassare

Systemanpassare shell stöder köra shell-skript, måste dessa vara offentligt tillgänglig för IB får åtkomst till dem.

```json
    "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "scriptUri": "<link to script>"        
        }, 
    ], 
        "customize": [ 
        { 
            "type": "Shell", 
            "name": "<name>", 
            "inline": "<commands to run>"
        }, 
    ], 
```

OS-Support: Linux 
 
Anpassa egenskaper:

- **typ** – skal 
- **namn på** - namn för att spåra anpassning 
- **scriptUri** -URI: N till platsen för filen 
- **infogad** -matris med shell-kommandon, avgränsade med kommatecken.
 
> [!NOTE]
> När du kör shell systemanpassare med RHEL ISO-källkod som du behöver se till att din första anpassning shell hanterar registrering med en Red Hat rätt server innan någon anpassning sker. När anpassningen är klar bör avregistrera skriptet med rätt server.

### <a name="windows-restart-customizer"></a>Windows startar om systemanpassare 
Ja, starta om anpassning kan du starta om en virtuell Windows-dator och vänta tills den ansluts igen, på så sätt kan du installera programvara som kräver en omstart.  

```json 
     "customize": [ 
            "type{ ": "WindowsRestart", 
            "restartCommand": "shutdown /r /f /t 0 /c", 
            "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > buildArtifacts/azureImageBuilderRestart.txt",
            "restartTimeout": "5m"
         }],
```

OS-Support: Windows
 
Anpassa egenskaper:
- **Typ**: WindowsRestart
- **restartCommand** -kommando för att köra omstarten (valfritt). Standardvärdet är `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** – kommando för att kontrollera om omstart lyckades (valfritt). 
- **restartTimeout** -startar om timeout som har angetts som en sträng med omfattningen och enhet. Till exempel `5m` (5 minuter) eller `2h` (2 timmar). Standardvärdet är: ”5m”


### <a name="powershell-customizer"></a>PowerShell systemanpassare 
Systemanpassare shell stöder Kör PowerShell-skript och infogade kommandot, måste skripten vara offentligt tillgänglig för IB får åtkomst till dem.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "valid_exit_codes": "<exit code>" 
         } 
    ], 
```

OS-support: Windows och Linux

Anpassa egenskaper:

- **typ** – PowerShell.
- **scriptUri** -URI: N till platsen för PowerShell-skriptfil. 
- **infogad** – infogade kommandon som ska köras, avgränsade med kommatecken.
- **valid_exit_codes** – valfritt, giltiga koder som kan returneras från skript/infogade kommandot på så sätt undviker rapporterade fel för kommandot skript/infogade.

### <a name="file-customizer"></a>Filen systemanpassare

Filen systemanpassare kan image builder ladda ned en fil från en GitHub- eller Azure storage. Om du har en avbildning build-pipeline som förlitar sig på byggartefakter kan du ange filen systemanpassare att ladda ned från build-resursen och flytta artefakter i avbildningen.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>" 
         }
     ]
```

OS-support: Linux och Windows 

Systemanpassare filegenskaper:

- **sourceUri** -slutpunkt för en tillgänglig lagring detta kan vara GitHub eller Azure storage. Du kan bara ladda ned en fil, inte en hel katalog. Om du vill hämta en katalog kan använda en komprimerad fil och sedan expandera den med hjälp av gränssnittet eller PowerShell anpassare. 
- **mål** – det här är den fullständiga sökvägen och filnamnet målnamn. Alla refererad sökväg och underkataloger måste finns, använder Shell eller PowerShell anpassare för att konfigurera dessa i förväg. Du kan använda skriptet anpassare för att skapa sökvägen. 

Detta stöds av kataloger för Windows och Linux-sökvägar, men det finns några skillnader: 
- Linux operativsystem – endast sökvägen Image builder kan skriva till är/tmp.
- Windows – ingen begränsning för sökväg, men sökvägen måste finnas.
 
 
Om det finns ett fel att ladda ned filen eller placerar den i en angiven katalog anpassa steg misslyckas och detta kommer att finnas i customization.log.

Filer i filen systemanpassare kan laddas ned från Azure Storage med hjälp av [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

### <a name="generalize"></a>Generalisera 
Som standard kan Azure Image Builder också köras ”avetablera' kod i slutet av varje avbildning anpassningsfasen att generalisera avbildningen. Generalisera är en process där avbildningen har ställts in så att den kan användas för att skapa flera virtuella datorer. Azure Image Builder använder Sysprep för Windows-datorer. För Linux, Azure Image Builder körs ' waagent-avetablering ”. 

Kommandona Image Builder-användare att generalisera kanske inte lämpar sig för alla situationer, så Azure Image Builder gör att du kan anpassa det här kommandot om det behövs. 

Om du migrerar befintliga anpassning och du använder olika Sysprep/waagent-kommandon, du kan använda Image Builder allmänna kommandon och om skapar virtuella datorn misslyckas använder du Sysprep eller waagent kommandon.

Om Azure Image Builder skapar en anpassad Windows-avbildning har och du skapar en virtuell dator från den sedan hitta som skapar virtuella datorn slutar fungera eller inte slutförs korrekt, behöver du vill granska i Windows Server Sysprep-dokumentationen eller ställa en supportförfrågan med den Team Services för Windows Server Sysprep kundsupport som kan felsöka och ge råd om rätt Sysprep-användning.


#### <a name="default-sysprep-command"></a>Standard Sysprep-kommando
```powershell
echo '>>> Waiting for GA to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
echo '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force} & $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit
while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 5  } else { break } }
```
#### <a name="default-linux-deprovision-command"></a>Standardkommandot Linux avetablering

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Åsidosätta kommandon
Om du vill åsidosätta kommandona använder provisioners för PowerShell eller Shell-skript för att skapa kommandofiler med det exakta filnamnet och placerar dem på rätt kataloger:

* Windows: c:\DeprovisioningScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

Image Builder kommer att läsa dessa kommandon, de skrivs till loggarna AIB 'customization.log'. Se [felsökning](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs) om hur du samla in loggar.
 
## <a name="properties-distribute"></a>Egenskaper: distribuera

Azure Image Builder har stöd för tre distribution mål: 

- **managedImage** – hanterad avbildning.
- **sharedImage** -delade bildgalleriet.
- **VHD** -VHD i ett lagringskonto.

Du kan distribuera en avbildning till båda måltyper i samma konfiguration, läser [exempel](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80).

Eftersom du kan ha fler än ett mål för distribution till Image Builder underhåller tillståndet för varje mål för innehållsdistribution som kan nås genom att fråga den `runOutputName`.  Den `runOutputName` är ett objekt som du kan fråga efter distribution för information om distributionen. Du kan till exempel fråga platsen för den virtuella Hårddisken eller regioner där avbildningsversionen har replikerats till. Det här är en egenskap för varje mål för innehållsdistribution. Den `runOutputName` måste vara unikt för varje mål för innehållsdistribution.
 
### <a name="distribute-managedimage"></a>Distribuera: managedImage

Bildutdata blir en hanterad avbildning-resurs.

```json
"distribute": [
        {
"type":"managedImage",
       "imageId": "<resource ID>",
       "location": "<region>",
       "runOutputName": "<name>",
       "artifactTags": {
            "<name": "<value>",
             "<name>": "<value>"
               }
         }]
```
 
Distribuera egenskaper:
- **typ** – managedImage 
- **imageId** – resurs-ID för mål-avbildningen, förväntat format: /subscriptions/<subscriptionId>/resourceGroups/<destinationResourceGroupName>/providers/Microsoft.Compute/images/<imageName>
- **plats** -platsen för hanterad avbildning.  
- **runOutputName** – unikt namn för att identifiera fördelningen.  
- **artifactTags** -valfritt användardefinierat nyckelvärdet par taggar.
 
 
> [!NOTE]
> Målresursgruppen måste finnas.
> Om du vill att den avbildning som distribuerats till en annan region ökas tidpunkten för distribution. 

### <a name="distribute-sharedimage"></a>Distribuera: sharedImage 
Azure delad galleriet är en ny bildhantering-tjänst som gör att hantera replikering av operativsystemavbildningar region, versionshantering och dela anpassade avbildningar. Azure Image Builder har stöd för distribution i den här tjänsten så att du kan distribuera avbildningar till regioner som stöds av delad Image Galleries. 
 
En delad bildgalleriet består av: 
 
- Galleri - behållare för flera delade bilder. Ett galleri distribueras i en region.
- Bild definitioner - en konceptuell gruppering för avbildningar. 
- Bild-versioner – det här är en Avbildningstyp som används för att distribuera en virtuell dator eller skala uppsättning. Bild-versioner kan replikeras till andra regioner där virtuella datorer måste distribueras.
 
Innan du kan distribuera till galleriet, måste du skapa ett galleri och en bild-definition, finns i [delade bilder](shared-images.md). 

```json
{
     "type": "sharedImage",
     "galleryImageId": “<resource ID>”,
     "runOutputName": "<name>",
     "artifactTags": {
          "<name": "<value>",
           "<name>": "<value>"
             }
     "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]}
``` 

Distribuera egenskaper för delad bildgallerier:

- **typ** -sharedImage  
- **galleryImageId** – ID: T för det delade bildgalleriet. Formatet är: /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageGalleryName>.
- **runOutputName** – unikt namn för att identifiera fördelningen.  
- **artifactTags** -valfritt användardefinierat nyckelvärdet par taggar.
- **replicationRegions** -matris med regioner för replikering. En av regionerna måste vara den region där galleriet har distribuerats.
 
> [!NOTE]
> Du kan använda Azure Image Builder i en annan region i galleriet, men Azure Image Builder-tjänsten behöver du överföra avbildningen mellan datacenter och det tar längre tid. Image Builder kommer automatiskt version avbildning, baserat på en Monoton heltal, du kan inte ange den för närvarande. 

### <a name="distribute-vhd"></a>Distribuera: VHD  
Du kan skickas till en virtuell Hårddisk. Du kan sedan kopiera den virtuella Hårddisken och använda den för att publicera på Azure MarketPlace eller Använd med Azure Stack.  

```json
 { 
     "type": "VHD",
     "runOutputName": "<VHD name>",
     "tags": {
          "<name": "<value>",
           "<name>": "<value>"
             }
 }
```
 
OS-Support: Windows och Linux

Distribuera VHD-parametrar:

- **typ** -VHD.
- **runOutputName** – unikt namn för att identifiera fördelningen.  
- **taggar** -valfritt användardefinierat nyckelvärdet par taggar.
 
Azure Image Builder inte tillåter att användaren anger en lagringsplats för kontot, men du kan fråga efter statusen för den `runOutputs` att få plats.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> När den virtuella Hårddisken har skapats kan du kopiera den till en annan plats, så snart som möjligt. Den virtuella Hårddisken lagras i ett lagringskonto i tillfälliga resursgruppen som skapades när mallen avbildningen skickas till tjänsten Azure Image Builder. Om du tar bort bildmallen förlorar du den virtuella Hårddisken. 
 
## <a name="next-steps"></a>Nästa steg

Det finns exempel .json-filer för olika scenarier i den [Azure Image Builder GitHub](https://github.com/danielsollondon/azvmimagebuilder).
 
 
 
 
 
 
 
 
 
 
