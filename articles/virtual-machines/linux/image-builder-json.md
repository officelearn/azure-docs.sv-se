---
title: Skapa en Azure Image Builder-mall (förhandsgranskning)
description: Lär dig hur du skapar en mall som ska användas med Azure Image Builder.
author: danis
ms.author: danis
ms.date: 03/24/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.subservice: imaging
manager: gwallace
ms.openlocfilehash: e1f1bc09406c34836c13deb805fa399ab4751d41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246797"
---
# <a name="preview-create-an-azure-image-builder-template"></a>Förhandsgranska: Skapa en Azure Image Builder-mall 

Azure Image Builder använder en .json-fil för att skicka information till Image Builder-tjänsten. I den här artikeln kommer vi att gå igenom de delar av json-filen, så att du kan bygga din egen. Information om hur du ser exempel på fullständiga .json-filer finns i [Azure Image Builder GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).

Detta är det grundläggande mallformatet:

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



## <a name="type-and-api-version"></a>Typ- och API-version

Resurstypen `type` är som måste `"Microsoft.VirtualMachineImages/imageTemplates"`vara . Den `apiVersion` kommer att ändras med tiden `"2019-05-01-preview"` när API:et ändras, men bör vara förhandsgranskning.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
```

## <a name="location"></a>Location

Platsen är den region där den anpassade avbildningen ska skapas. För förhandsgranskningen av Image Builder stöds följande regioner:

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
Som standard Image Builder kommer att använda en "Standard_D1_v2" bygga VM, kan du åsidosätta detta, till exempel, om du vill anpassa en avbildning för en GPU VM, du behöver en GPU VM storlek. Det här är valfritt.

```json
 {
    "vmSize": "Standard_D1_v2"
 },
```

## <a name="osdisksizegb"></a>osDiskSizeGB

Som standard kommer Image Builder inte ändra storleken på bilden, kommer det att använda storleken från källbilden. Du kan öka storleken på OS Disk (Win och Linux), detta är valfritt, och ett värde på 0 innebär att lämna samma storlek som källavbildningen. 

```json
 {
    "osDiskSizeGB": 100
 },
```

## <a name="vnetconfig"></a>vnetConfig
Om du inte anger några VNET-egenskaper skapar Image Builder sitt eget VNET, Public IP och NSG. Den offentliga IP används för tjänsten för att kommunicera med bygga VM, men om du inte vill ha en offentlig IP eller vill Image Builder att ha tillgång till dina befintliga VNET-resurser, till exempel konfigurationsservrar (DSC, Chef, Puppet, Ansible), filresurser etc. kan du ange ett VNET. Mer information finns i [nätverksdokumentationen](https://github.com/danielsollondon/azvmimagebuilder/blob/master/aibNetworking.md#networking-with-azure-vm-image-builder), det här är valfritt.

```json
    "vnetConfig": {
        "name": "<vnetName>",
        "subnetName": "<subnetName>",
        "resourceGroupName": "<vnetRgName>"
    }
```
## <a name="tags"></a>Taggar

Det här är nyckel-/värdepar som du kan ange för den bild som genereras.

## <a name="depends-on-optional"></a>Beror på (valfritt)

Det här valfria avsnittet kan användas för att säkerställa att beroenden slutförs innan du fortsätter. 

```json
    "dependsOn": [],
```

Mer information finns i [Definiera resursberoenden](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-define-dependencies#dependson).

## <a name="identity"></a>Identitet
Som standard stöder Image Builder att använda skript eller kopierar filer från flera platser, till exempel GitHub och Azure-lagring. För att kunna använda dessa måste de vara allmänt tillgängliga.

Du kan också använda en Azure User-Assigned Managed Identity, definierad av dig, för att tillåta Avbildningsverktyget åtkomst till Azure Storage, så länge identiteten har beviljats ett minimum av "Storage Blob Data Reader" på Azure-lagringskontot. Det innebär att du inte behöver göra lagringsblobar externt tillgängliga eller konfigurera SAS-token.


```json
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<imgBuilderId>": {}
        }
        },
```

Ett fullständigt exempel finns i [Använda en Azure-användartilldelad hanterad identitet för att komma åt filer i Azure Storage](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

Image Builder-stöd för en användartilldelad identitet: • Stöder endast en enda identitet • Stöder inte anpassade domännamn

Mer information finns i [Vad är hanterade identiteter för Azure-resurser?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).
Mer information om hur du distribuerar den här funktionen finns i [Konfigurera hanterade identiteter för Azure-resurser på en Azure VM med Azure CLI](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm#user-assigned-managed-identity).

## <a name="properties-source"></a>Egenskaper: källa

Avsnittet `source` innehåller information om källbilden som ska användas av Image Builder.

API:et kräver en SourceType som definierar källan för avbildningsversionen, för närvarande finns det tre typer:
- PlatformImage - anges källavbildningen är en Marketplace-avbildning.
- ManagedImage - använd detta när du startar från en vanlig hanterad avbildning.
- SharedImageVersion – detta används när du använder en bildversion i ett delat bildgalleri som källa.

### <a name="iso-source"></a>ISO-källa
Vi är inaktuella denna funktion från bildbyggare, eftersom det finns nu [RHEL Ta med din egen prenumeration bilder,](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/byos)läs tidslinjerna nedan:
    * 31 mars 2020 - Bildmallar med RHEL ISO-källor kommer nu längre att accepteras av resursleverantören.
    * 30 april 2020- Bildmallar som innehåller RHEL ISO-källor kommer inte längre att bearbetas.

### <a name="platformimage-source"></a>PlattformBildskälla 
Azure Image Builder stöder Windows Server och klient, och Linux Azure Marketplace avbildningar, se [här](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview#os-support) för hela listan. 

```json
        "source": {
            "type": "PlatformImage",
                "publisher": "Canonical",
                "offer": "UbuntuServer",
                "sku": "18.04-LTS",
                "version": "latest"
        },
```


Egenskaperna här är desamma som används för att skapa virtuella datorer, med hjälp av AZ CLI, kör nedan för att få egenskaper: 
 
```azurecli-interactive
az vm image list -l westus -f UbuntuServer -p Canonical --output table –-all 
```

Du kan använda "senaste" i versionen, utvärderas versionen när avbildningen bygget sker, inte när mallen skickas. Om du använder den här funktionen med målet Delat bildgalleri kan du undvika att skicka in mallen igen och köra bildversionen igen med jämna mellanrum, så att bilderna återskapas från de senaste bilderna.

### <a name="managedimage-source"></a>ManagedImage-källa

Ställer in källavbildningen som en befintlig hanterad avbildning av en generaliserad virtuell hårddisk eller virtuell dator. Källhanterad avbildning måste vara av ett operativsystem som stöds och vara i samma region som din Azure Image Builder-mall. 

```json
        "source": { 
            "type": "ManagedImage", 
                "imageId": "/subscriptions/<subscriptionId>/resourceGroups/{destinationResourceGroupName}/providers/Microsoft.Compute/images/<imageName>"
        }
```

Bör `imageId` vara ResourceId för den hanterade avbildningen. Används `az image list` för att lista tillgängliga bilder.


### <a name="sharedimageversion-source"></a>Källa SharedImageVersion
Ställer in källbilden en befintlig bildversion i ett delat bildgalleri. Avbildningsversionen måste vara av ett operativsystem som stöds och avbildningen måste replikeras till samma region som din Azure Image Builder-mall. 

```json
        "source": { 
            "type": "SharedImageVersion", 
            "imageVersionID": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/p  roviders/Microsoft.Compute/galleries/<sharedImageGalleryName>/images/<imageDefinitionName/versions/<imageVersion>" 
   } 
```

Det `imageVersionId` bör vara ResourceId för avbildningsversionen. Använd [az sig bild-version lista](/cli/azure/sig/image-version#az-sig-image-version-list) för att lista bildversioner.

## <a name="properties-buildtimeoutinminutes"></a>Egenskaper: buildTimeoutInMinutes

Som standard körs Image Builder i 240 minuter. Därefter kommer det att timeout och stoppa, oavsett om bilden bygga är klar. Om timeouten är träffad ser du ett fel som liknar detta:

```text
[ERROR] Failed while waiting for packerizer: Timeout waiting for microservice to
[ERROR] complete: 'context deadline exceeded'
```

Om du inte anger ett buildTimeoutInMinutes-värde eller anger det till 0, används standardvärdet. Du kan öka eller minska värdet, upp till maximalt 960mins (16 timmar). För Windows rekommenderar vi inte att du ställer in detta under 60 minuter. Om du upptäcker att du träffar timeout, granska [loggarna,](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-image-build-logs)för att se om anpassningssteget väntar på något som användarindata. 

Om du upptäcker att du behöver mer tid för anpassningar att slutföra, ställa in detta till vad du tror att du behöver, med lite overhead. Men ställ inte in den för högt eftersom du kanske måste vänta på att timeout innan du ser ett fel. 


## <a name="properties-customize"></a>Egenskaper: anpassa

Image Builder stöder flera "anpassare". Anpassare är funktioner som används för att anpassa avbildningen, till exempel att köra skript eller starta om servrar. 

När `customize`du använder: 
- Du kan använda flera anpassare, men `name`de måste ha en unik .
- Anpassare körs i den ordning som anges i mallen.
- Om en anpassare misslyckas misslyckas hela anpassningskomponenten och rapporterar tillbaka ett fel.
- Det rekommenderas starkt att du testar skriptet noggrant innan du använder det i en mall. Det blir enklare att felsöka skriptet på den egna virtuella datorn.
- Placera inte känsliga data i skripten. 
- Skriptplatserna måste vara allmänt tillgängliga, såvida du inte använder [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

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

 
Avsnittet Anpassa är en matris. Azure Image Builder körs genom anpassarna i sekventiell ordning. Alla fel i någon anpassare kommer att misslyckas byggprocessen. 
 
 
### <a name="shell-customizer"></a>Shell-anpassare

Skalanpassaren stöder skalskript som körs, dessa måste vara allmänt tillgängliga för IB att komma åt dem.

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

OS-stöd: Linux 
 
Anpassa egenskaper:

- **typ** – Skal 
- **namn** - namn för att spåra anpassningen 
- **scriptUri** - URI till platsen för filen 
- **inline** - matris med skalkommandon, avgränsade med kommatecken.
- **sha256Checksum** - Värdet av sha256 kontrollsumman för filen, du genererar detta lokalt och sedan Image Builder kommer kontrollsumma och validera.
    * Så här genererar du sha256Checksum med hjälp av en terminal på Mac/Linux:To generate the sha256Checksum, using a terminal on Mac/Linux run:`sha256sum <fileName>`


För att kommandon ska kunna köras med superanvändarbehörighet måste de föregås av `sudo`.

> [!NOTE]
> När du kör skalanpassaren med RHEL ISO-källa måste du se till att dina första anpassningsskal hanterar registrering med en Red Hat-berättigandeserver innan någon anpassning sker. När anpassningen är klar ska skriptet avregistrera sig med rättighetsservern.

### <a name="windows-restart-customizer"></a>Anpassaren för windows-omstart 
Omstart anpassaren kan du starta om en Windows VM och vänta på att det kommer tillbaka online, detta gör att du kan installera programvara som kräver en omstart.  

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

OS-stöd: Windows
 
Anpassa egenskaper:
- **Typ:** WindowsRestart
- **restartCommand** - Kommando för att köra omstarten (valfritt). Standardvärdet är `'shutdown /r /f /t 0 /c \"packer restart\"'`.
- **restartCheckCommand** – Kommando för att kontrollera om omstarten lyckades (valfritt). 
- **restartTimeout** - Starta om tidsgränsen som anges som en sträng med magnitud och enhet. Till exempel `5m` (5 minuter) eller `2h` (2 timmar). Standard är: "5m"

### <a name="linux-restart"></a>Linux omstart  
Det finns ingen Linux Restart-anpassare, men om du installerar drivrutiner, eller komponenter som kräver en omstart, kan du installera dem och anropa en omstart med Shell-anpassaren, det finns en 20min SSH-timeout till den virtuella bygga virtuella datorn.

### <a name="powershell-customizer"></a>PowerShell-anpassare 
Skalanpassaren stöder körning av PowerShell-skript och infogat kommando, skripten måste vara allmänt tillgängliga för IB för att komma åt dem.

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

OS-stöd: Windows och Linux

Anpassa egenskaper:

- **typ** – PowerShell.
- **scriptUri** - URI till platsen för PowerShell-skriptfilen. 
- **infogade** – Infoga kommandon som ska köras, avgränsade med kommatecken.
- **validExitCodes** – Valfria, giltiga koder som kan returneras från kommandot script/inline, undviker detta rapporterade fel på skriptet/infogade kommandot.
- **runElevated** – Valfritt, booleskt, stöd för att köra kommandon och skript med förhöjda behörigheter.
- **sha256Checksum** - Värdet av sha256 kontrollsumman för filen, du genererar detta lokalt och sedan Image Builder kommer kontrollsumma och validera.
    * Så här genererar du sha256Checksum med hjälp av ett PowerShell på Windows [Get-Hash](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/get-filehash?view=powershell-6)


### <a name="file-customizer"></a>Anpassa filer

Med filanpassaren kan avbildningsverktyget hämta en fil från en GitHub- eller Azure-lagring. Om du har en pipeline för bildversion som är beroende av att skapa artefakter kan du sedan ställa in filanpassaren så att den hämtas från byggresursen och flytta artefakterna till avbildningen.  

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

OS-stöd: Linux och Windows 

Egenskaper för filanpassningsanpassare:

- **sourceUri** - en tillgänglig lagringsslutpunkt, kan detta vara GitHub eller Azure-lagring. Du kan bara hämta en fil, inte en hel katalog. Om du behöver hämta en katalog använder du en komprimerad fil och komprimerar den sedan med shell- eller PowerShell-anpassarna. 
- **destination** – det här är den fullständiga målsökvägen och filnamnet. Alla refererade sökvägar och underkataloger måste finnas, använd Shell- eller PowerShell-anpassarna för att ställa in dessa i förväg. Du kan använda skriptanpassare för att skapa sökvägen. 

Detta stöds av Windows-kataloger och Linux-sökvägar, men det finns vissa skillnader: 
- Linux OS - den enda vägen Bildbyggare kan skriva till är / tmp.
- Windows – Ingen sökvägsbegränsning, men sökvägen måste finnas.
 
 
Om det finns ett fel när du försöker hämta filen eller placera den i en angiven katalog misslyckas anpassningssteget och det kommer att finnas i customization.log.

> [!NOTE]
> Filanpassaren är endast lämplig för små filnedladdningar, < 20MB. För större filhämtningar använder ett skript eller infogat kommando, använd `wget` `curl`koden för `Invoke-WebRequest`att ladda ner filer, till exempel Linux eller , Windows, .

Filer i filanpassaren kan hämtas från Azure Storage med [MSI](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage).

### <a name="windows-update-customizer"></a>Anpassa windows update
Den här anpassaren bygger på [communityn Windows Update Provisioner](https://packer.io/docs/provisioners/community-supported.html) för Packer, som är ett projekt med öppen källkod som underhålls av Packer-communityn. Microsoft testar och validerar etableringstjänsten med Image Builder-tjänsten och kommer att stödja att undersöka problem med den och arbeta för att lösa problem, men open source-projektet stöds inte officiellt av Microsoft. Detaljerad dokumentation och hjälp med Windows Update Provisioner finns i projektarkivet.
 
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
Stöd för operativsystemet: Windows

Anpassa egenskaper:
- **typ** – WindowsUpdate.
- **searchCriteria** - Valfritt, definierar vilken typ av uppdateringar som är installerade (Rekommenderas, Viktigt etc.), BrowseOnly =0 och IsInstalled=0 (Rekommenderas) är standard.
- **filter** – Valfritt, kan du ange ett filter som ska inkludera eller utesluta uppdateringar.
- **updateLimit** – Valfritt, definierar hur många uppdateringar som kan installeras, standard 1000.
 
 

### <a name="generalize"></a>Generalisera 
Som standard kommer Azure Image Builder också att köra "avetablering" kod i slutet av varje avbildning anpassningsfas, för att "generalisera" avbildningen. Generalisering är en process där avbildningen är inställd så att den kan återanvändas för att skapa flera virtuella datorer. För virtuella Windows-datorer använder Azure Image Builder Sysprep. För Linux kör Azure Image Builder 'waagent -deetavision'. 

Kommandona Image Builder-användare som generaliserar kanske inte är lämpliga för alla situationer, så Azure Image Builder låter dig anpassa det här kommandot om det behövs. 

Om du migrerar befintlig anpassning och använder olika Sysprep/waagent-kommandon kan du använda de allmänna kommandona Image Builder och om skapandet av den virtuella datorn misslyckas använder du dina egna Sysprep- eller waagent-kommandon.

Om Azure Image Builder skapar en anpassad Windows-avbildning och du skapar en virtuell dator från den, och sedan upptäcker att skapandet av den virtuella datorn misslyckas eller inte slutförs måste du granska Dokumentationen till Windows Server Sysprep eller ta upp en supportbegäran med Supportteamet för Windows Server Sysprep-kundtjänst, som kan felsöka och ge råd om rätt Sysprep-användning.


#### <a name="default-sysprep-command"></a>Standardkommandot Sysprep
```powershell
echo '>>> Waiting for GA to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running') { Start-Sleep -s 5 }
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
echo '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml ){ rm $Env:SystemRoot\\windows\\system32\\Sysprep\\unattend.xml -Force} & $Env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit
while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 5  } else { break } }
```
#### <a name="default-linux-deprovision-command"></a>Standardkommando för Linux-avetablen

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

#### <a name="overriding-the-commands"></a>Åsidosätta kommandona
Om du vill åsidosätta kommandona använder du Skriptetableringar för PowerShell eller Shell för att skapa kommandofilerna med det exakta filnamnet och placera dem i rätt kataloger:

* Windows: c:\AvetableingScript.ps1
* Linux: /tmp/DeprovisioningScript.sh

Image Builder kommer att läsa dessa kommandon, dessa skrivs ut till AIB loggar, "customization.log". Se [felsökning om](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#collecting-and-reviewing-aib-logs) hur du samlar in loggar.
 
## <a name="properties-distribute"></a>Egenskaper: distribuera

Azure Image Builder stöder tre distributionsmål: 

- **managedImage** - hanterad avbildning.
- **sharedImage** - Delat bildgalleri.
- **VHD** - VHD i ett lagringskonto.

Du kan distribuera en avbildning till båda måltyperna i samma konfiguration, se [exempel](https://github.com/danielsollondon/azvmimagebuilder/blob/7f3d8c01eb3bf960d8b6df20ecd5c244988d13b6/armTemplates/azplatform_image_deploy_sigmdi.json#L80).

Eftersom du kan ha mer än ett mål att distribuera till, upprätthåller Image Builder ett `runOutputName`tillstånd för varje distributionsmål som kan nås genom att fråga .  Det `runOutputName` är ett objekt som du kan fråga efter distribution för information om den distributionen. Du kan till exempel fråga platsen för den virtuella hårddisken eller regioner där avbildningsversionen replikerades till eller FRÅN-avbildningsversionen skapas. Detta är en egenskap för varje distributionsmål. Måste `runOutputName` vara unikt för varje distributionsmål. Här är ett exempel, detta är att fråga en delad bild galleri distribution:

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

Bildutdata blir en hanterad bildresurs.

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
- **imageId** – Resurs-ID för målavbildningen,\<förväntat format: /subscriptions/subscriptionId\<>/resourceGroups/destinationResourceGroupName>/providers/Microsoft.Compute/images/imageName\<>
- **plats** - platsen för den hanterade bilden.  
- **runOutputName** – unikt namn för att identifiera distributionen.  
- **artifactTags** - Valfria användarspecificerade nyckelvärdepartaggar.
 
 
> [!NOTE]
> Målresursgruppen måste finnas.
> Om du vill att avbildningen ska distribueras till en annan region ökar distributionstiden . 

### <a name="distribute-sharedimage"></a>Distribuera: sharedImage 
Azure Shared Image Gallery är en ny imagehanteringstjänst som gör det möjligt att hantera avbildningsregionreplikering, versionshantering och delning av anpassade avbildningar. Azure Image Builder stöder distribution med den här tjänsten, så att du kan distribuera avbildningar till regioner som stöds av delade avbildningsgallerier. 
 
Ett delat bildgalleri består av: 
 
- Galleri - Behållare för flera delade bilder. Ett galleri distribueras i en region.
- Bilddefinitioner - en konceptuell gruppering för bilder. 
- Avbildningsversioner - det här är en avbildningstyp som används för att distribuera en virtuell dator eller skalningsuppsättning. Avbildningsversioner kan replikeras till andra regioner där virtuella datorer måste distribueras.
 
Innan du kan distribuera till bildgalleriet måste du skapa ett galleri och en bilddefinition, se [Delade bilder](shared-images.md). 

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

Distribuera egenskaper för delade bildgallerier:

- **typ** - sharedImage  
- **galleryImageId** – ID för det delade bildgalleriet. Formatet\<är: /subscriptions/subscriptionId>/resourceGroups/\<resourceGroupName>/providers/Microsoft.Compute/galleries/\<sharedImageGalleryName>/images/\<imageGalleryName>.
- **runOutputName** – unikt namn för att identifiera distributionen.  
- **artifactTags** - Valfria användarspecificerade nyckelvärdepartaggar.
- **replicationRegioner** - Matris med regioner för replikering. En av regionerna måste vara den region där galleriet distribueras.
 
> [!NOTE]
> Du kan använda Azure Image Builder i en annan region till galleriet, men Azure Image Builder-tjänsten måste överföra avbildningen mellan datacenter och det tar längre tid. Image Builder kommer automatiskt att versionsa bilden, baserat på en monotonisk heltal, kan du inte ange det för närvarande. 

### <a name="distribute-vhd"></a>Distribuera: VHD  
Du kan skriva ut till en virtuell hårddisk. Du kan sedan kopiera den virtuella hårddisken och använda den för att publicera till Azure MarketPlace eller använda med Azure Stack.  

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
 
OS-stöd: Windows och Linux

Distribuera VHD-parametrar:

- **typ** - VIRTUELLD.
- **runOutputName** – unikt namn för att identifiera distributionen.  
- **taggar** - Valfria användarspecificerade nyckelvärdepartaggar.
 
Azure Image Builder tillåter inte att användaren anger en lagringskontoplats, `runOutputs` men du kan fråga status för platsen.  

```azurecli-interactive
az resource show \
   --ids "/subscriptions/$subscriptionId/resourcegroups/<imageResourceGroup>/providers/Microsoft.VirtualMachineImages/imageTemplates/<imageTemplateName>/runOutputs/<runOutputName>"  | grep artifactUri 
```

> [!NOTE]
> När den virtuella hårddisken har skapats kopierar du den till en annan plats så snart som möjligt. Den virtuella hårddisken lagras i ett lagringskonto i den tillfälliga resursgruppen som skapas när avbildningsmallen skickas till Azure Image Builder-tjänsten. Om du tar bort bildmallen förlorar du den virtuella hårddisken. 
 
## <a name="next-steps"></a>Nästa steg

Det finns exempel på Json-filer för olika scenarier i [Azure Image Builder GitHub](https://github.com/danielsollondon/azvmimagebuilder).
 
