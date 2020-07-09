---
title: Skapa en Azure Image Builder-mall (förhands granskning)
description: Lär dig hur du skapar en mall som ska användas med Azure Image Builder.
author: danielsollondon
ms.author: danis
ms.date: 06/23/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.reviewer: cynthn
ms.openlocfilehash: 975d6842110ffa864a534e09cf35d0d33612d7d5
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135083"
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
     },
    "identity":{},           
    "dependsOn": [], 
    "properties": { 
        "buildTimeoutInMinutes": <minutes>, 
        "vmProfile": 
            {
            "vmSize": "<vmSize>",
            "osDiskSizeGB": <sizeInGB>,
            "vnetConfig": {
                "name": "<vnetName>",
                "subnetName": "<subnetName>",
                "resourceGroupName": "<vnetRgName>"
            },
        "source": {}, 
        "customize": {}, 
        "distribute": {} 
      } 
 } 
```



## <a name="type-and-api-version"></a>Typ-och API-version

`type`Är resurs typen, som måste vara `"Microsoft.VirtualMachineImages/imageTemplates"` . `apiVersion`Kommer att ändras med tiden som API-ändringar, men bör vara `"2019-05-01-preview"` för hands version.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Location

Platsen är den region där den anpassade avbildningen kommer att skapas. För för hands versionen av Image Builder stöds följande regioner:

- USA, östra
- USA, östra 2
- USA, västra centrala
- USA, västra
- USA, västra 2
- Europa, norra
- Europa, västra


```json
    "location": "<region>",
```
## <a name="vmprofile"></a>vmProfile
Som standard använder Image Builder en "Standard_D1_v2"-version av den virtuella datorn. du kan åsidosätta detta, om du till exempel vill anpassa en avbildning för en GPU-VM behöver du en GPU VM-storlek. Det här är valfritt.

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

Som standard kommer bild verktyget inte att ändra bildens storlek, den kommer att använda storleken från käll bilden. Du kan **bara** öka storleken på operativ system disken (Win och Linux), detta är valfritt och värdet 0 innebär att du lämnar samma storlek som käll bilden. Du kan inte minska storleken på OS-disken till mindre än storleken från käll bilden.

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
Om du inte anger några VNET-egenskaper skapas en egen VNET-, offentlig IP-och NSG-avbildnings verktyg. Den offentliga IP-adressen används för tjänsten för att kommunicera med den virtuella datorn, men om du inte vill att en offentlig IP-adress ska ha åtkomst till dina befintliga VNET-resurser, till exempel konfigurations servrar (DSC, chef, Puppet, Ansible), fil resurser osv., kan du ange ett VNET. Mer information finns i dokumentationen för [nätverket](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibNetworking.md#networking-with-azure-vm-image-builder), detta är valfritt.

```json
    "vnetConfig": {
        "name": "<vnetName>",
        "subnetName": "<subnetName>",
        "resourceGroupName": "<vnetRgName>"
    }
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

`source`Avsnittet innehåller information om käll avbildningen som ska användas av Image Builder.

API: t kräver en ' SourceType ' som definierar källan för avbildnings versionen, för närvarande finns det tre typer:
- PlatformImage – anger att käll avbildningen är en Marketplace-avbildning.
- ManagedImage – Använd det här när du startar från en vanlig hanterad avbildning.
- SharedImageVersion – används när du använder en avbildnings version i ett delat avbildnings galleri som källa.

### <a name="iso-source"></a>ISO-källa
Vi har föråldrat den här funktionen från Image Builder, eftersom det nu finns [RHEL att ta med dina egna prenumerations avbildningar](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/byos). granska tids linjerna nedan:
    * 31 mars 2020 – bildmallar med RHEL ISO-källor kommer nu längre att accepteras av resurs leverantören.
    * 30 april 2020 – bildmallar som innehåller RHEL ISO-källor kommer inte att bearbetas längre.

### <a name="platformimage-source"></a>PlatformImage-källa 
Azure Image Builder stöder Windows Server och klient, och Linux Azure Marketplace-avbildningar finns [här](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview#os-support) för den fullständiga listan. 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


Egenskaperna här är samma som används för att skapa virtuella datorer med hjälp av AZ CLI, och kör följande för att hämta egenskaperna: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

Du kan använda "senaste" i versionen om versionen utvärderas när avbildningen skapas, inte när mallen skickas. Om du använder den här funktionen med det delade avbildnings galleriet kan du undvika att skicka mallen igen och köra avbildningen igen med intervall, så att dina avbildningar återskapas från de senaste bilderna.

### <a name="managedimage-source"></a>ManagedImage-källa

Anger käll avbildningen som en befintlig hanterad avbildning av en generaliserad virtuell hård disk eller virtuell dator. Den käll hanterade avbildningen måste vara av ett operativ system som stöds och vara i samma region som din Azure Image Builder-mall. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

`imageId`Ska vara den hanterade avbildningens ResourceID. Använd `az image list` för att visa en lista över tillgängliga avbildningar.


### <a name="sharedimageversion-source"></a>SharedImageVersion-källa
Anger käll avbildningen av en befintlig avbildnings version i ett galleri för delade avbildningar. Avbildnings versionen måste vara av ett operativ system som stöds och avbildningen måste replikeras till samma region som din Azure Image Builder-mall. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

`imageVersionId`Ska vara avbildnings versionens ResourceID. Använd [AZ sig-avbildning – versions lista](/cli/azure/sig/image-version#az-sig-image-version-list) för att lista avbildnings versioner.

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

När du använder `customize` : 
- Du kan använda flera anpassningar, men de måste ha ett unikt `name` .
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
                "scriptUri": "<path to script>",
                "sha256Checksum": "<sha256 checksum>"
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
            "scriptUri": "<link to script>",
            "sha256Checksum": "<sha256 checksum>"       
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
- **sha256Checksum** -värdet för filens SHA256-kontrollsumma, du genererar det här lokalt och sedan kontrol leras kontroll summor och Image Builder.
    * Skapa sha256Checksum med hjälp av en terminal på Mac/Linux-körning:`sha256sum <fileName>`


För kommandon som ska köras med superuser-privilegier måste de föregås av `sudo` .

> [!NOTE]
> När du kör Shell Customization med RHEL ISO-källa måste du se till att ditt första anpassnings gränssnitt hanterar registrering med en Red Hat-rättighets server innan eventuella anpassningar sker. När anpassningen är klar ska skriptet avregistreras med rättighets servern.

### <a name="windows-restart-customizer"></a>Starta om anpassning av Windows 
Med alternativet starta om anpassning kan du starta om en virtuell Windows-dator och vänta tills den är online igen, så att du kan installera program vara som kräver en omstart.  

```json 
     "customize": [ 

            {
                "type": "WindowsRestart",
                "restartCommand": "shutdown /r /f /t 0", 
                "restartCheckCommand": "echo Azure-Image-Builder-Restarted-the-VM  > c:\\buildArtifacts\\azureImageBuilderRestart.txt",
                "restartTimeout": "5m"
            }
  
        ],
```

OS-support: Windows
 
Anpassa egenskaper:
- **Typ**: WindowsRestart
- **restartCommand** -kommando för att köra omstarten (valfritt). Standardvärdet är `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** – kommando för att kontrol lera om omstart lyckades (valfritt). 
- **restartTimeout** – timeout för omstart har angetts som en sträng med storlek och enhet. Till exempel `5m` (5 minuter) eller `2h` (2 timmar). Standardvärdet är: ' 5 m '

### <a name="linux-restart"></a>Linux-omstart  
Det finns ingen Linux restart-anpassning, men om du installerar driv rutiner eller komponenter som kräver en omstart, kan du installera dem och anropa en omstart med hjälp av Shell-anpassningen, det finns en 20min SSH-tidsgräns till den virtuella dator som skapas.

### <a name="powershell-customizer"></a>PowerShell-anpassning 
Shell-anpassningen stöder körning av PowerShell-skript och infogat kommando, skripten måste vara offentligt tillgängliga för IB för att få åtkomst till dem.

```json 
     "customize": [
        { 
             "type": "PowerShell",
             "name":   "<name>",  
             "scriptUri": "<path to script>",
             "runElevated": "<true false>",
             "sha256Checksum": "<sha256 checksum>" 
        },  
        { 
             "type": "PowerShell", 
             "name": "<name>", 
             "inline": "<PowerShell syntax to run>", 
             "validExitCodes": "<exit code>",
             "runElevated": "<true or false>" 
         } 
    ], 
```

OS-support: Windows och Linux

Anpassa egenskaper:

- **typ** – PowerShell.
- **scriptUri** -URI till platsen för PowerShell-skriptfilen. 
- **infogade** – infogade kommandon som ska köras, avgränsade med kommatecken.
- **validExitCodes** – valfria, giltiga koder som kan returneras från skriptet/inline-kommandot. Detta undviker att det rapporteras ett skript/inline-kommando.
- **runElevated** – valfritt, booleskt, stöd för att köra kommandon och skript med förhöjd behörighet.
- **sha256Checksum** -värdet för filens SHA256-kontrollsumma, du genererar det här lokalt och sedan kontrol leras kontroll summor och Image Builder.
    * Skapa sha256Checksum med hjälp av PowerShell på Windows [Get-hash](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-6)


### <a name="file-customizer"></a>Fil anpassning

Med fil anpassnings verktyget kan Image Builder Ladda ned en fil från en GitHub eller Azure Storage. Om du har en pipeline för avbildnings utveckling som förlitar sig på bygg artefakter kan du sedan konfigurera fil anpassningen så att den laddas ned från build-resursen och flytta artefakterna till avbildningen.  

```json
     "customize": [ 
         { 
            "type": "File", 
             "name": "<name>", 
             "sourceUri": "<source location>",
             "destination": "<destination>",
             "sha256Checksum": "<sha256 checksum>"
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
> Fil anpassningen är bara lämplig för små fil hämtningar, < 20 MB. För större fil hämtningar används ett skript eller ett infogat kommando, koden används för att ladda ned filer, till exempel Linux `wget` eller `curl` Windows, `Invoke-WebRequest` .

Filer i fil anpassningen kan laddas ned från Azure Storage med [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

### <a name="windows-update-customizer"></a>Windows Update anpassning
Den här anpassningen bygger på [community Windows Update-etableringen](https://packer.io/docs/provisioners/community-supported.html) för Packer, som är ett projekt med öppen källkod som underhålls av communityn för programpaket. Microsoft testar och validerar etableringen med tjänsten Image Builder och har stöd för att undersöka problem med den, och det kan också fungera att lösa problem, men projektet med öppen källkod stöds inte av Microsoft. Detaljerad dokumentation om och hjälp med Windows Update etablerings tjänsten finns i projektets lagrings plats.

```json
     "customize": [
            {
                "type": "WindowsUpdate",
                "searchCriteria": "IsInstalled=0",
                "filters": [
                    "exclude:$_.Title -like '*Preview*'",
                    "include:$true"
                            ],
                "updateLimit": 20
            }
               ], 
OS support: Windows
```

Anpassa egenskaper:
- **typ** – windowsupdate.
- **searchCriteria** – valfritt, definierar vilken typ av uppdateringar som installeras (rekommenderas, viktigt osv.), BrowseOnly = 0 och IsInstalled = 0 (rekommenderas) är standardvärdet.
- **filter** – valfritt, gör att du kan ange ett filter för att ta med eller undanta uppdateringar.
- **updateLimit** – valfritt, definierar hur många uppdateringar som kan installeras, standard 1000.
 
 

### <a name="generalize"></a>Generalisera 
Som standard kommer Azure Image Builder också att köra "avetablering"-kod i slutet av varje bild anpassnings fas till "generalize"-avbildningen. Att generalisera är en process där avbildningen konfigureras så att den kan återanvändas för att skapa flera virtuella datorer. För virtuella Windows-datorer använder Azure Image Builder Sysprep. För Linux kör Azure Image Builder "waagent-deetablering". 

Kommandona Image Builder-användare att generalisera är kanske inte lämpliga för varje situation, så Azure Image Builder gör det möjligt att anpassa kommandot om det behövs. 

Om du migrerar befintlig anpassning och använder olika Sysprep/waagent-kommandon kan du använda de allmänna kommandona för Image Builder och om det inte går att skapa en virtuell dator använder du dina egna Sysprep-eller waagent-kommandon.

Om Azure Image Builder skapar en anpassad Windows-avbildning och du skapar en virtuell dator från den, kontrollerar du att den virtuella datorn Miss lyckas eller inte slutförs utan problem, du måste gå igenom Windows Server Sysprep-dokumentationen eller utlösa en supportbegäran med support teamet för Windows Server Sysprep, som kan felsöka och Avisera om korrekt Sysprep-användning.


#### <a name="default-sysprep-command"></a>Standard kommando för Sysprep
```powershell
Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
  Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
}
& $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
while($true) {
  $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
  Write-Output $imageState
  if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
  Start-Sleep -s 5
}
Write-Output '>>> Sysprep complete ...'
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

Eftersom du kan ha fler än ett mål att distribuera till, har Image Builder ett tillstånd för varje distributions mål som kan nås genom att fråga `runOutputName` .  `runOutputName`Är ett objekt som du kan skicka frågor till efter distribution för information om distributionen. Du kan till exempel fråga platsen för den virtuella hård disken eller regioner där avbildnings versionen har repliker ATS till, eller SIG-avbildnings version som skapats. Detta är en egenskap för varje distributions mål. `runOutputName`Måste vara unik för varje distributions mål. Här är ett exempel som frågar en distribution av delade avbildnings Galleri:

```bash
subscriptionID=<subcriptionID>
imageResourceGroup=<resourceGroup of image template>
runOutputName=<runOutputName>

az resource show \
        --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/$runOutputName"  \
        --api-version=2019-05-01-preview
```

Resultat:
```json
{
  "id": "/subscriptions/xxxxxx/resourcegroups/rheltest/providers/Microsoft.VirtualMachineImages/imageTemplates/ImageTemplateLinuxRHEL77/runOutputs/rhel77",
  "identity": null,
  "kind": null,
  "location": null,
  "managedBy": null,
  "name": "rhel77",
  "plan": null,
  "properties": {
    "artifactId": "/subscriptions/xxxxxx/resourceGroups/aibDevOpsImg/providers/Microsoft.Compute/galleries/devOpsSIG/images/rhel/versions/0.24105.52755",
    "provisioningState": "Succeeded"
  },
  "resourceGroup": "rheltest",
  "sku": null,
  "tags": null,
  "type": "Microsoft.VirtualMachineImages/imageTemplates/runOutputs"
}
```

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
- **imageId** – resurs-ID för mål avbildningen, förväntat format:/Subscriptions/ \<subscriptionId> /resourceGroups/ \<destinationResourceGroupName> /providers/Microsoft.Compute/images/\<imageName>
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
- **galleryImageId** – ID för det delade avbildnings galleriet. Formatet är:/Subscriptions/ \<subscriptionId> /ResourceGroups/ \<resourceGroupName> /providers/Microsoft.Compute/Galleries/ \<sharedImageGalleryName> /images/ \<imageGalleryName> .
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
 
Azure Image Builder tillåter inte användaren att ange en lagrings konto plats, men du kan fråga efter status för `runOutputs` att hämta platsen.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> När den virtuella hård disken har skapats kopierar du den till en annan plats så snart som möjligt. Den virtuella hård disken lagras i ett lagrings konto i den tillfälliga resurs grupp som skapas när avbildnings mal len skickas till Azure Image Builder-tjänsten. Om du tar bort avbildnings mal len förlorar du den virtuella hård disken. 
 
## <a name="next-steps"></a>Nästa steg

Det finns exempel på JSON-filer för olika scenarier i [Azure Image Builder-GitHub](https://github.com/danielsollondon/azvmimagebuilder).
 
