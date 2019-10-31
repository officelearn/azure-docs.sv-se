---
title: Skapa en Azure Image Builder-mall (förhands granskning)
description: Lär dig hur du skapar en mall som ska användas med Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 07/31/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 13392644ebe5e163e946deceeec5fcab8f5085cc
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159731"
---
# <a name="preview-create-an-azure-image-builder-template"></a>För hands version: skapa en Azure Image Builder-mall 

Azure Image Builder använder en. JSON-fil för att skicka information till Image Builder-tjänsten. I den här artikeln går vi igenom avsnitten i JSON-filen så att du kan bygga egna. Se exempel på fullständiga. JSON-filer i [Azure Image Builder-GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).

Detta är det grundläggande mallformat:

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
        "buildTimeoutInMinutes": <minutes>, 
        "build": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Typ-och API-version

`type` är resurs typen, som måste vara `"Microsoft.VirtualMachineImages/imageTemplates"`. `apiVersion` ändras med tiden som API-ändringar, men ska vara `"2019-05-01-preview"` för för hands version.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Plats

Platsen är den region där den anpassade avbildningen kommer att skapas. För för hands versionen av Image Builder stöds följande regioner:

- USA, östra
- USA, östra 2
- USA, västra centrala
- USA, västra
- USA, västra 2


```json
    "location": "<region>",
```

## <a name="tags"></a>Taggar

Detta är nyckel/värde-par som du kan ange för den bild som genereras.

## <a name="depends-on-optional"></a>Är beroende av (valfritt)

Det här valfria avsnittet kan användas för att säkerställa att beroenden har slutförts innan du fortsätter. 

```json
    "dependsOn": [],
```

Mer information finns i [definiera resurs beroenden](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson).

## <a name="identity"></a>Identitet
Som standard stöder Image Builder användningen av skript eller kopierar filer från flera platser, till exempel GitHub och Azure Storage. För att kunna använda dessa måste de vara offentligt tillgängliga.

Du kan också använda en Azure User-tilldelade hanterad identitet, som definieras av dig, för att tillåta Image Builder-åtkomst Azure Storage, förutsatt att identiteten har beviljats minst "Storage BLOB data Reader" på Azure Storage-kontot. Det innebär att du inte behöver göra lagrings-blobar externt tillgängliga eller konfigurera SAS-token.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

Ett fullständigt exempel finns i [använda en Azure User-tilldelade hanterad identitet för att komma åt filer i Azure Storage](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

Image Builder-stöd för en användardefinierad identitet: • stöder endast en enda identitet • stöder inte anpassade domän namn

Mer information finns i [Vad är hanterade identiteter för Azure-resurser?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
Mer information om hur du distribuerar den här funktionen finns i [Konfigurera hanterade identiteter för Azure-resurser på en virtuell Azure-dator med Azure CLI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

## <a name="properties-source"></a>Egenskaper: källa

Avsnittet `source` innehåller information om käll avbildningen som ska användas av Image Builder.

API: t kräver en ' SourceType ' som definierar källan för avbildnings versionen, för närvarande finns det tre typer:
- ISO – Använd detta när källan är en RHEL ISO.
- PlatformImage – anger att käll avbildningen är en Marketplace-avbildning.
- ManagedImage – Använd det här när du startar från en vanlig hanterad avbildning.
- SharedImageVersion – används när du använder en avbildnings version i ett delat avbildnings galleri som källa.

### <a name="iso-source"></a>ISO-källa

Azure Image Builder har endast stöd för användning av publicerade Red Hat Enterprise Linux 7. x-ISO, för för hands version. Image Builder stöder:
- RHEL 7,3 
- RHEL 7,4 
- RHEL 7.5 
 
```json
"source": {
       "type": "ISO",
       "sourceURI": "<sourceURI from the download center>",
       "sha256Checksum": "<checksum associated with ISO>"
}
```

Om du vill hämta `sourceURI` och `sha256Checksum` värden går du till `https://access.redhat.com/downloads` och väljer sedan produkt **Red Hat Enterprise Linux**och en version som stöds. 

I listan över **installations program och avbildningar för Red Hat Enterprise Linux server**måste du kopiera länken för Red Hat Enterprise Linux 7. x binär DVD och kontroll summan.

> [!NOTE]
> Åtkomst-token för länkarna uppdateras med jämna mellanrum, så varje gång du vill skicka en mall måste du kontrol lera om uppdaterings adressen för RH har ändrats.
 
### <a name="platformimage-source"></a>PlatformImage-källa 
Azure Image Builder har stöd för följande Azure Marketplace-avbildningar:
* Ubuntu 18.04
* Ubuntu 16.04
* RHEL 7,6
* CentOS 7,6
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


Egenskaperna här är samma som används för att skapa virtuella datorer med hjälp av AZ CLI, och kör följande för att hämta egenskaperna: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

> [!NOTE]
> Versionen får inte vara "senaste", du måste använda kommandot ovan för att hämta ett versions nummer. 

### <a name="managedimage-source"></a>ManagedImage-källa

Anger käll avbildningen som en befintlig hanterad avbildning av en generaliserad virtuell hård disk eller virtuell dator. Den käll hanterade avbildningen måste vara av ett operativ system som stöds och vara i samma region som din Azure Image Builder-mall. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

`imageId` ska vara den hanterade avbildningens ResourceId. Använd `az image list` för att visa en lista över tillgängliga avbildningar.


### <a name="sharedimageversion-source"></a>SharedImageVersion-källa
Anger käll avbildningen av en befintlig avbildnings version i ett galleri för delade avbildningar. Avbildnings versionen måste vara av ett operativ system som stöds och avbildningen måste replikeras till samma region som din Azure Image Builder-mall. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

`imageVersionId` ska vara en ResourceId för avbildnings versionen. Använd [AZ sig-avbildning – versions lista](/cli/azure/sig/image-version#az-sig-image-version-list) för att lista avbildnings versioner.

## <a name="properties-buildtimeoutinminutes"></a>Egenskaper: buildTimeoutInMinutes

Som standard körs Image Builder i 240 minuter. Efter det kommer timeout och stoppas, oavsett om avbildningen är slutförd eller inte. Om tids gränsen har nåtts visas ett fel som liknar detta:

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

Om du inte anger något värde för buildTimeoutInMinutes, eller om du anger värdet 0, används standardvärdet. Du kan öka eller minska värdet, upp till maximalt 960mins (16hrs). För Windows rekommenderar vi inte att du anger det här under 60 minuter. Om du upptäcker att du når tids gränsen granskar du [loggarna](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-image-build-logs)för att se om anpassnings steget väntar på något som indata från användaren. 

Om du upptäcker att du behöver mer tid för att anpassningarna ska slutföras, ställer du in den på det du tycker att du behöver, med lite extra kostnad. Men Ställ inte in den för hög eftersom du kanske måste vänta tills den är tids gräns innan du ser ett fel. 


## <a name="properties-customize"></a>Egenskaper: anpassa

Avbildnings verktyget stöder flera "anpassningar". Anpassningar är funktioner som används för att anpassa din avbildning, till exempel köra skript eller starta om servrar. 

När du använder `customize`: 
- Du kan använda flera anpassningar, men de måste ha en unik `name`.
- Anpassningar körs i den ordning som anges i mallen.
- Om en anpassning Miss lyckas, kommer hela anpassnings komponenten att Miss lyckas och rapportera ett fel.
- Det rekommenderas starkt att du testar skriptet noggrant innan du använder det i en mall. Det blir enklare att felsöka skriptet på din egen virtuella dator.
- Lägg inte till känsliga data i skripten. 
- Skript platserna måste vara offentligt tillgängliga, såvida du inte använder [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

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

 
Avsnittet Anpassa är en matris. Azure Image Builder kommer att köras via anpassningarna i nummerordning. Eventuella misslyckanden i en anpassnings process kommer inte att kunna skapas. 
 
 
### <a name="shell-customizer"></a>Shell-anpassning

Shell-anpassningen stöder körning av gränssnitts skript, de måste vara offentligt tillgängliga för IB för att få åtkomst till dem.

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

OS-support: Linux 
 
Anpassa egenskaper:

- **typ** – gränssnitt 
- **namn** – namn för att spåra anpassningen 
- **scriptUri** -URI till filens plats 
- **infogad** matris av Shell-kommandon, avgränsade med kommatecken.
 
> [!NOTE]
> När du kör Shell Customization med RHEL ISO-källa måste du se till att ditt första anpassnings gränssnitt hanterar registrering med en Red Hat-rättighets server innan eventuella anpassningar sker. När anpassningen är klar ska skriptet avregistreras med rättighets servern.

### <a name="windows-restart-customizer"></a>Starta om anpassning av Windows 
Med alternativet starta om anpassning kan du starta om en virtuell Windows-dator och vänta tills den är online igen, så att du kan installera program vara som kräver en omstart.  

```json 
     "customize": [ 
            "type{ ": "WindowsRestart", 
            "restartCommand": "shutdown /r /f /t 0 /c", 
            "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > buildArtifacts/azureImageBuilderRestart.txt",
            "restartTimeout": "5m"
         }],
```

OS-support: Windows
 
Anpassa egenskaper:
- **Typ**: WindowsRestart
- **restartCommand** -kommando för att köra omstarten (valfritt). Standardvärdet är `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** – kommando för att kontrol lera om omstart lyckades (valfritt). 
- **restartTimeout** – timeout för omstart har angetts som en sträng med storlek och enhet. Till exempel `5m` (5 minuter) eller `2h` (2 timmar). Standardvärdet är: ' 5 m '


### <a name="powershell-customizer"></a>PowerShell-anpassning 
Shell-anpassningen stöder körning av PowerShell-skript och infogat kommando, skripten måste vara offentligt tillgängliga för IB för att få åtkomst till dem.

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
- **scriptUri** -URI till platsen för PowerShell-skriptfilen. 
- **infogade** – infogade kommandon som ska köras, avgränsade med kommatecken.
- **valid_exit_codes** – valfria, giltiga koder som kan returneras från skriptet/inline-kommandot. Detta undviker att det rapporteras ett skript/inline-kommando.

### <a name="file-customizer"></a>Fil anpassning

Med fil anpassnings verktyget kan Image Builder Ladda ned en fil från en GitHub eller Azure Storage. Om du har en pipeline för avbildnings utveckling som förlitar sig på bygg artefakter kan du sedan konfigurera fil anpassningen så att den laddas ned från build-resursen och flytta artefakterna till avbildningen.  

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

Fil anpassnings egenskaper:

- **sourceUri** – en tillgänglig lagrings slut punkt kan vara GitHub eller Azure Storage. Du kan bara ladda ned en fil, inte en hel katalog. Om du behöver hämta en katalog använder du en komprimerad fil och expanderar den sedan med Shell-eller PowerShell-anpassningarna. 
- **destination** – detta är den fullständiga sökvägen till målet och fil namnet. Alla refererade sökvägar och under kataloger måste finnas, Använd Shell-eller PowerShell-anpassningarna för att ställa in dem på förhand. Du kan använda skript anpassningarna för att skapa sökvägen. 

Detta stöds av Windows-kataloger och Linux-sökvägar, men det finns vissa skillnader: 
- Linux OS – det enda Sök vägs avbildnings verktyget kan skriva till är/tmp.
- Windows – ingen Sök vägs begränsning, men sökvägen måste finnas.
 
 
Om det uppstår ett fel vid försök att hämta filen, eller om den placeras i en angiven katalog, kommer anpassnings steget inte att fungera, och detta görs i anpassnings loggen.

> [!NOTE]
> Fil anpassningen är bara lämplig för små fil hämtningar, < 20 MB. För större fil hämtningar används ett skript eller ett infogat kommando, koden används för att ladda ned filer, till exempel Linux `wget` eller `curl`Windows `Invoke-WebRequest`.

Filer i fil anpassningen kan laddas ned från Azure Storage med [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

### <a name="generalize"></a>Generalisera 
Som standard kommer Azure Image Builder också att köra "avetablering"-kod i slutet av varje bild anpassnings fas till "generalize"-avbildningen. Att generalisera är en process där avbildningen konfigureras så att den kan återanvändas för att skapa flera virtuella datorer. För virtuella Windows-datorer använder Azure Image Builder Sysprep. För Linux kör Azure Image Builder "waagent-deetablering". 

Kommandona Image Builder-användare att generalisera är kanske inte lämpliga för varje situation, så Azure Image Builder gör det möjligt att anpassa kommandot om det behövs. 

Om du migrerar befintlig anpassning och använder olika Sysprep/waagent-kommandon kan du använda de allmänna kommandona för Image Builder och om det inte går att skapa en virtuell dator använder du dina egna Sysprep-eller waagent-kommandon.

Om Azure Image Builder skapar en anpassad Windows-avbildning och du skapar en virtuell dator från den, kontrollerar du att den virtuella datorn Miss lyckas eller inte slutförs utan problem, du måste granska Windows Server Sysprep-dokumentationen eller utlösa en supportbegäran med Support teamet för Windows Server Sysprep för kund tjänster, som kan felsöka och meddela om rätt Sysprep-användning.


#### <a name="default-sysprep-command"></a>Standard kommando för Sysprep
```powershell
echo '>>> Waiting for GA to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
echo '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force} & $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit
while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 5  } else { break } }
```
#### <a name="default-linux-deprovision-command"></a>Standard kommando för att avetablera Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Åsidosätta kommandona
Om du vill åsidosätta kommandona använder du PowerShell-eller Shell-skript provisioor för att skapa kommando filerna med det exakta fil namnet och lägga dem i rätt kataloger:

* Windows: c:\DeprovisioningScript.ps1
* Linux:/tmp/DeprovisioningScript.sh

Image Builder läser dessa kommandon, de skrivs ut till AIB-loggarna, anpassning. log. Se [fel sökning](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs) för insamling av loggar.
 
## <a name="properties-distribute"></a>Egenskaper: Distribuera

Azure Image Builder stöder tre distributions mål: 

- **managedImage** -hanterad avbildning.
- **sharedImage** -delat avbildnings Galleri.
- **VHD** -VHD i ett lagrings konto.

Du kan distribuera en avbildning till båda mål typerna i samma konfiguration, se [exempel](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80).

Eftersom du kan ha fler än ett mål att distribuera till, har Image Builder ett tillstånd för varje distributions mål som kan nås genom att fråga `runOutputName`.  `runOutputName` är ett objekt som du kan skicka frågor till efter distribution för information om distributionen. Du kan till exempel fråga platsen för den virtuella hård disken eller regioner där avbildnings versionen replikeras till. Detta är en egenskap för varje distributions mål. `runOutputName` måste vara unikt för varje distributions mål.
 
### <a name="distribute-managedimage"></a>Distribuera: managedImage

Avbildningens utdata är en hanterad avbildnings resurs.

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
- **imageId** – resurs-ID för mål avbildningen, förväntat format:/Subscriptions/\<subscriptionId >/ResourceGroups/\<destinationResourceGroupName >/providers/Microsoft.Compute/images/\<imageName >
- **plats** – plats för den hanterade avbildningen.  
- **runOutputName** – unikt namn för identifiering av distributionen.  
- **artifactTags** – valfri användardefinierad nyckel värde par taggar.
 
 
> [!NOTE]
> Mål resurs gruppen måste finnas.
> Om du vill att avbildningen ska distribueras till en annan region ökar distributions tiden. 

### <a name="distribute-sharedimage"></a>Distribuera: sharedImage 
Azures delade avbildnings galleri är en ny avbildnings hanterings tjänst som gör det möjligt att hantera replikering av avbildnings regioner, versions hantering och delning av anpassade avbildningar. Azure Image Builder stöder distribution med den här tjänsten, så du kan distribuera avbildningar till regioner som stöds av delade bild gallerier. 
 
Ett delat avbildnings Galleri består av: 
 
- Galleri-behållare för flera delade avbildningar. Ett galleri har distribuerats i en region.
- Bild definitioner – en konceptuell gruppering för bilder. 
- Avbildnings versioner – det här är en avbildnings typ som används för att distribuera en virtuell dator eller skalnings uppsättning. Avbildnings versioner kan replikeras till andra regioner där de virtuella datorerna måste distribueras.
 
Innan du kan distribuera till avbildnings galleriet måste du skapa ett galleri och en bild definition, se [delade avbildningar](shared-images.md). 

```json
{
    "type": "sharedImage",
    "galleryImageId": "<resource ID>",
    "runOutputName": "<name>",
    "artifactTags": {
        "<name>": "<value>",
        "<name>": "<value>"
    },
    "replicationRegions": [
        "<region where the gallery is deployed>",
        "<region>"
    ]
}
``` 

Distribuera egenskaper för delade avbildnings gallerier:

- **typ** -sharedImage  
- **galleryImageId** – ID för det delade avbildnings galleriet. Formatet är:/Subscriptions/\<subscriptionId >/resourceGroups/\<resourceGroupName >/providers/Microsoft.Compute/galleries/\<sharedImageGalleryName >/images/\<imageGalleryName >.
- **runOutputName** – unikt namn för identifiering av distributionen.  
- **artifactTags** – valfri användardefinierad nyckel värde par taggar.
- **replicationRegions** -matris för replikering. En av regionerna måste vara den region där galleriet har distribuerats.
 
> [!NOTE]
> Du kan använda Azure Image Builder i en annan region i galleriet, men tjänsten Azure Image Builder måste överföra avbildningen mellan data centret och det tar längre tid. Bildverktyget skapar automatiskt en version av bilden, baserat på ett enkel färgs heltal, men du kan inte ange den för närvarande. 

### <a name="distribute-vhd"></a>Distribuera: virtuell hård disk  
Du kan skriva utdata till en virtuell hård disk. Du kan sedan kopiera den virtuella hård disken och använda den för att publicera på Azure MarketPlace eller använda med Azure Stack.  

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
 
OS-support: Windows och Linux

Distribuera VHD-parametrar:

- **typ** -VHD.
- **runOutputName** – unikt namn för identifiering av distributionen.  
- **taggar** – valfritt användardefinierat nyckel värde par.
 
Azure Image Builder tillåter inte användaren att ange en lagrings konto plats, men du kan fråga status för `runOutputs` för att hämta platsen.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> När den virtuella hård disken har skapats kopierar du den till en annan plats så snart som möjligt. Den virtuella hård disken lagras i ett lagrings konto i den tillfälliga resurs grupp som skapas när avbildnings mal len skickas till Azure Image Builder-tjänsten. Om du tar bort avbildnings mal len förlorar du den virtuella hård disken. 
 
## <a name="next-steps"></a>Nästa steg

Det finns exempel på JSON-filer för olika scenarier i [Azure Image Builder-GitHub](https://github.com/danielsollondon/azvmimagebuilder).
 
 
 
 
 
 
 
 
 
 
