---
title: Köra anpassade skript på virtuella Linux-datorer i Azure
description: Automatisera konfigurationsuppgifter för Linux VM med hjälp av anpassade skripttillägg v2
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2018
ms.author: mimckitt
ms.openlocfilehash: 7afba24d6334991a694d43b2258244ec425884d5
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985515"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>Använda version 2 av Azures tillägg för anpassat skript med virtuella Linux-datorer
Custom Script Extension Version 2 hämtar och kör skript på virtuella Azure-datorer. Det här tillägget är användbart för konfiguration efter distribution, programvaruinstallation eller annan konfigurations-/hanteringsuppgift. Du kan hämta skript från Azure Storage eller en annan tillgänglig internetplats, eller så kan du ange dem till tilläggets körning. 

Det anpassade skripttillägget integreras med Azure Resource Manager-mallar. Du kan också köra den med hjälp av Azure CLI, PowerShell eller AZURE Virtual Machines REST API.

I den här artikeln beskrivs hur du använder det anpassade skripttillägget från Azure CLI och hur du kör tillägget med hjälp av en Azure Resource Manager-mall. Den här artikeln innehåller också felsökningssteg för Linux-system.


Det finns två Linux Custom Script Extensions:
* Version 1 - Microsoft.OSTCExtensions.CustomScriptForLinux
* Version 2 - Microsoft.Azure.Extensions.CustomScript

Byt nya och befintliga distributioner för att använda den nya versionen 2 i stället. Den nya versionen är avsedd att vara en lättillgänglig ersättning. Därför är migreringen lika enkel som att ändra namn och version. Du behöver inte ändra tilläggskonfigurationen.


### <a name="operating-system"></a>Operativsystem

Custom Script Extension för Linux kommer att köras på tillägget stöds förlängning OS: s, för mer information, se den här [artikeln](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

### <a name="script-location"></a>Skriptplats

Du kan använda tillägget för att använda dina Azure Blob-lagringsautentiseringsuppgifter för att komma åt Azure Blob-lagring. Alternativt kan skriptplatsen vara vilken plats som helst, så länge den virtuella datorn kan dirigera till den slutpunkten, till exempel GitHub, intern filserver etc.

### <a name="internet-connectivity"></a>Internetanslutning
Om du behöver hämta ett skript externt, till exempel GitHub eller Azure Storage, måste ytterligare brandväggs-/nätverkssäkerhetsgruppportar öppnas. Om skriptet till exempel finns i Azure Storage kan du tillåta åtkomst med Azure NSG-tjänsttaggar för [lagring](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Om skriptet finns på en lokal server kan du fortfarande behöva ytterligare brandväggs-/nätverkssäkerhetsgruppsportar måste öppnas.

### <a name="tips-and-tricks"></a>Tips
* Den högsta felfrekvensen för det här tillägget beror på syntaxfel i skriptet. Kontrollera att skriptet körs utan fel, och implementera ytterligare loggning i skriptet så att det blir enklare att identifiera var felet inträffade.
* Skriv skript som är idempotenta, så att inga systemändringar görs om de oavsiktligt körs mer än en gång.
* Se till att skripten inte kräver indata från användaren när de körs.
* Det är 90 minuter tillåtet för skriptet att köras, något längre kommer att resultera i en misslyckad bestämmelse i tillägget.
* Lägg inte omstarter inuti skriptet, kommer detta att orsaka problem med andra tillägg som installeras, och efter omstart, kommer tillägget inte att fortsätta efter omstarten. 
* Om du har ett skript som kommer att orsaka en omstart, sedan installera program och köra skript etc. Du bör schemalägga omstarten med ett Cron-jobb eller använda verktyg som DSC eller Chef, Puppet-tillägg.
* Tillägget kommer bara att köra ett skript en gång, om du vill köra ett skript på varje start, kan du använda [cloud-init avbildning](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init) och använda en [Skript per start](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) modul. Du kan också använda skriptet för att skapa en SystemD-serviceenhet.
* Om du vill schemalägga när ett skript ska köras bör du använda tillägget för att skapa ett Cron-jobb. 
* När skriptet körs visas tillägget med övergångsstatus på Azure-portalen eller i CLI. Om du vill ha fler frekventa statusuppdateringar för ett skript som körs måste du skapa en egen lösning.
* Custom Script-tillägget stöder inte proxyservrar, men du kan använda ett filöverföringsverktyg som stöder proxyservrar i skriptet, till exempel *Curl*. 
* Tänk på icke-standardkatalogplatser som skripten eller kommandona kan förlita sig på har logik för att hantera detta.
*  När du distribuerar anpassade skript till vmss-produktionsinstanser föreslås distribution via json-mall och lagra skriptlagringskontot där du har kontroll över SAS-token. 


## <a name="extension-schema"></a>Tilläggsschema

Konfigurationen för anpassat skripttillägg anger saker som skriptplats och kommandot som ska köras. Du kan lagra den här konfigurationen i konfigurationsfiler, ange den på kommandoraden eller ange den i en Azure Resource Manager-mall. 

Du kan lagra känsliga data i en skyddad konfiguration, som krypteras och endast dekrypteras inuti den virtuella datorn. Den skyddade konfigurationen är användbar när körningskommandot innehåller hemligheter som ett lösenord.

Dessa objekt ska behandlas som känsliga data och anges i den skyddade inställningskonfigurationen för tillägg. Azure VM-tilläggsskyddade inställningsdata krypteras och dekrypteras endast på den virtuella måldatorn.

```json
{
  "name": "config-app",
  "type": "Extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2019-03-01",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "skipDos2Unix":false,
      "timestamp":123456789          
    },
    "protectedSettings": {
       "commandToExecute": "<command-to-execute>",
       "script": "<base64-script-to-execute>",
       "storageAccountName": "<storage-account-name>",
       "storageAccountKey": "<storage-account-key>",
       "fileUris": ["https://.."],
        "managedIdentity" : "<managed-identity-identifier>"
    }
  }
}
```

>[!NOTE]
> egenskapen managedIdentity **får inte** användas tillsammans med egenskaperna storageAccountName eller storageAccountKey

### <a name="property-values"></a>Egenskapsvärden

| Namn | Värde / Exempel | Datatyp | 
| ---- | ---- | ---- |
| apiVersion | 2019-03-01 | date |
| utgivare | Microsoft.Compute.Extensions | sträng |
| typ | CustomScript (Anpassade program) | sträng |
| typHandlerVersion | 2.1 | int |
| fileUris (t.ex. ) | https://github.com/MyProject/Archive/MyPythonScript.py | matris |
| commandToExecute (t.ex.) | python MyPythonScript.py \<my-param1> | sträng |
| -skriptet | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo= | sträng |
| skipDos2Unix (t.ex.) | false | boolean |
| tidsstämpel (t.ex.) | 123456789 | 32-bitars heltal |
| storageAccountName (t.ex.) | exempelstoracket | sträng |
| lagringAccountKey (t.ex.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | sträng |
| hanteradidentitet (t.ex. | { } eller { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" } eller { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" } | json-objekt |

### <a name="property-value-details"></a>Information om egenskapsvärde
* `apiVersion`: Det senaste apiVersion kan hittas med Hjälp av [Resource Explorer](https://resources.azure.com/) eller från Azure CLI med följande kommando`az provider list -o json`
* `skipDos2Unix`: (valfritt, booleskt) hoppa över dos2unix-konvertering av skriptbaserade filadresser eller skript.
* `timestamp`(valfritt, 32-bitars heltal) använd det här fältet endast för att utlösa en omkörning av skriptet genom att ändra värdet för det här fältet.  Alla heltalsvärde är godtagbara. Den får bara vara annorlunda än det tidigare värdet.
* `commandToExecute`:**(krävs** om skriptet inte har angetts, sträng) startpunkten skriptet för att köra. Använd det här fältet i stället om kommandot innehåller hemligheter som lösenord.
* `script`:**(krävs** om kommandotToExecute inte har angetts, sträng)ett base64-kodat (och eventuellt gzip'ed) skript som körs av /bin/sh.
* `fileUris`: (valfritt, strängmatris) url:erna för filer som ska hämtas.
* `storageAccountName`: (valfritt, sträng) namnet på lagringskontot. Om du anger lagringsautentiseringsuppgifter måste alla `fileUris` vara URL:er för Azure Blobbar.
* `storageAccountKey`: (valfritt, sträng) åtkomstnyckeln för lagringskontot
* `managedIdentity`: (valfritt, json-objekt) den [hanterade identiteten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) för nedladdning av filer
  * `clientId`: (valfritt, sträng) klient-ID för den hanterade identiteten
  * `objectId`: (valfritt, sträng) objekt-ID för den hanterade identiteten


Följande värden kan anges i offentliga eller skyddade inställningar, tillägget avvisar alla konfigurationer där värdena nedan anges i både offentliga och skyddade inställningar.
* `commandToExecute`
* `script`
* `fileUris`

Använda offentliga inställningar kanske användbart för felsökning, men det rekommenderas starkt att du använder skyddade inställningar.

Offentliga inställningar skickas i klartext till den virtuella datorn där skriptet ska köras.  Skyddade inställningar krypteras med en nyckel som bara är känd för Azure och den virtuella datorn. Inställningarna sparas på den virtuella datorn när de skickades, dvs om inställningarna krypterades de sparas krypterade på den virtuella datorn. Certifikatet som används för att dekryptera de krypterade värdena lagras på den virtuella datorn och används för att dekryptera inställningar (om det behövs) vid körning.

#### <a name="property-skipdos2unix"></a>Egenskap: skipDos2Unix

Standardvärdet är falskt, vilket innebär att **is** dos2unix-konvertering körs.

Den tidigare versionen av CustomScript, Microsoft.OSTCExtensions.CustomScriptForLinux, konverterar automatiskt DOS-filer `\r\n` `\n`till UNIX-filer genom att översätta till . Den här översättningen finns fortfarande och är aktiverad som standard. Den här konverteringen tillämpas på alla filer som hämtas från fileUris eller skriptinställningen baserat på något av följande villkor.

* Om tillägget är `.sh`ett `.txt` `.py`av `.pl` , , , eller konverteras det. Skriptinställningen matchar alltid det här villkoret eftersom det antas vara ett skript som körs med /bin/sh och sparas som script.sh på den virtuella datorn.
* Om filen börjar `#!`med .

Dos2unix-konverteringen kan hoppas över genom att ange skipDos2Unix till true.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>",
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>Egenskap: skript

CustomScript stöder körning av ett användardefinierat skript. Skriptinställningarna för att kombinera commandToExecute och fileUris till en enda inställning. I stället för att behöva konfigurera en fil för nedladdning från Azure storage eller GitHub-gist kan du helt enkelt koda skriptet som en inställning. Skript kan användas för att ersätta commandToExecute och fileUris.

Skriptet **måste** vara base64-kodat.  Skriptet kan **eventuellt** gzip'ed. Skriptinställningen kan användas i offentliga eller skyddade inställningar. Den maximala storleken på skriptparameterns data är 256 KB. Om skriptet överskrider den här storleken kommer det inte att köras.

Med tanke på följande skript som sparats i filen /script.sh/.

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

Rätt CustomScript-skriptinställning skulle konstrueras genom att ta utdata från följande kommando.

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

Skriptet kan eventuellt gzip'ed att ytterligare minska storleken (i de flesta fall). (CustomScript identifierar automatiskt användningen av gzip-komprimering.)

```sh
cat script | gzip -9 | base64 -w 0
```

CustomScript använder följande algoritm för att köra ett skript.

 1. bekräfta längden på skriptets värde inte överstiger 256 KB.
 1. base64 avkoda skriptets värde
 1. _försök_ att gunzip base64 avkodade värde
 1. skriva det avkodade (och eventuellt expanderade) värdet till disk (/var/lib/waagent/custom-script/#/script.sh)
 1. köra skriptet med _/bin/sh -c /var/lib/waagent/custom-script/#/script.sh.

####  <a name="property-managedidentity"></a>Fastighetsförmedling: hanteradIdentity
> [!NOTE]
> Den här egenskapen **får** endast anges i skyddade inställningar.

CustomScript (version 2.1 och framåt) stöder [hanterad identitet](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) för nedladdning av filer från webbadresser som anges i inställningen "fileUris". Det gör att CustomScript kan komma åt privata azure storage-blobbar eller behållare utan att användaren behöver skicka hemligheter som SAS-token eller lagringskontonycklar.

Om du vill använda den här funktionen måste användaren lägga till en [systemtilldelad](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-system-assigned-identity) eller [användartilldelad](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-user-assigned-identity) identitet till den virtuella datorn eller VMSS där CustomScript förväntas köras och [bevilja den hanterade identitetsåtkomsten till Azure Storage-behållaren eller bloben](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage#grant-access).

Om du vill använda den systemtilldelade identiteten på mål-VM/VMSS anger du fältet "managedidentity" till ett tomt json-objekt. 

> Exempel:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : {}
> }
> ```

Om du vill använda den användartilldelade identiteten på mål-VM/VMSS konfigurerar du fältet "managedidentity" med klient-ID eller objekt-ID för den hanterade identiteten.

> Exempel:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> egenskapen managedIdentity **får inte** användas tillsammans med egenskaperna storageAccountName eller storageAccountKey

## <a name="template-deployment"></a>Malldistribution
Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. JSON-schemat som beskrivs i föregående avsnitt kan användas i en Azure Resource Manager-mall för att köra det anpassade skripttillägget under en Azure Resource Manager-malldistribution. En exempelmall som innehåller tillägget Anpassat skript finns här, [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).


```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2019-03-01",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
      },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <param2>",
      "fileUris": ["https://github.com/MyProject/Archive/hello.sh"
      ]  
    }
  }
}
```

>[!NOTE]
>Dessa egenskapsnamn är skiftlägeskänsliga. Använd namnen som visas här för att undvika distributionsproblem.

## <a name="azure-cli"></a>Azure CLI
När du använder Azure CLI för att köra tillägget Anpassat skript skapar du en konfigurationsfil eller filer. Du måste ha åtminstone "commandToExecute".

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

Du kan också ange inställningarna i kommandot som en JSON-formaterad sträng. Detta gör att konfigurationen kan anges under körningen och utan en separat konfigurationsfil.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Azure CLI-exempel

#### <a name="public-configuration-with-script-file"></a>Offentlig konfiguration med skriptfil

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Azure CLI-kommando:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

#### <a name="public-configuration-with-no-script-file"></a>Offentlig konfiguration utan skriptfil

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Azure CLI-kommando:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Offentliga och skyddade konfigurationsfiler

Du använder en offentlig konfigurationsfil för att ange skriptfilen URI. Du använder en skyddad konfigurationsfil för att ange vilket kommando som ska köras.

Offentlig konfigurationsfil:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

Skyddad konfigurationsfil:  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

Azure CLI-kommando:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \ 
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Felsökning
När tillägget Anpassat skript körs skapas eller hämtas skriptet till en katalog som liknar följande exempel. Kommandoutdata sparas också i `stdout` `stderr` den här katalogen och filer.

```bash
/var/lib/waagent/custom-script/download/0/
```

Om du vill felsöka kontrollerar du först Linux Agent Log, kontrollerar att tillägget kördes, kontrollerar:

```bash
/var/log/waagent.log 
```

Du bör leta efter förlängningen utförande, kommer det att se ut ungefär som:

```output
2018/04/26 17:47:22.110231 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] [Enable] current handler state is: notinstalled
2018/04/26 17:47:22.306407 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Download, message=Download succeeded, duration=167
2018/04/26 17:47:22.339958 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Initialize extension directory
2018/04/26 17:47:22.368293 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Update settings file: 0.settings
2018/04/26 17:47:22.394482 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Install extension [bin/custom-script-shim install]
2018/04/26 17:47:23.432774 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Install, message=Launch command succeeded: bin/custom-script-shim install, duration=1007
2018/04/26 17:47:23.476151 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Enable extension [bin/custom-script-shim enable]
2018/04/26 17:47:24.516444 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Enable, message=Launch command succeeded: bin/custom-sc
```

Några punkter att notera:
1. Aktivera är när kommandot börjar köras.
2. Hämtningen avser nedladdning av CustomScript-tilläggspaketet från Azure, inte skriptfilerna som anges i fileUris.


Azure Script Extension skapar en logg som du hittar här:

```bash
/var/log/azure/custom-script/handler.log
```

Du bör leta efter den enskilda utförandet, kommer det att se ut ungefär som:

```output
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=start
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=pre-check
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="comparing seqnum" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="seqnum saved" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="reading configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="read configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating json schema"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="json schema valid"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsing configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsed configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating configuration logically"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validated configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="creating output directory" path=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="created output directory"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 files=1
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download start"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download complete" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing protected commandToExecute" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executed command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=enabled
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=end
```

Här kan du se:
* Kommandot Aktivera startar är den här loggen
* Inställningarna som skickas till tillägget
* Tillägget nedladdningsfilen och resultatet av det.
* Kommandot körs och resultatet.

Du kan också hämta körningstillståndet för det anpassade `commandToExecute` skripttillägget, inklusive de faktiska argument som skickas som med hjälp av Azure CLI:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

Utdata ser ut som följande text:

```output
[
  {
    "autoUpgradeMinorVersion": true,
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionid/resourceGroups/rgname/providers/Microsoft.Compute/virtualMachines/vmname/extensions/customscript",
    "resourceGroup": "rgname",
    "settings": {
      "commandToExecute": "sh script.sh > ",
      "fileUris": [
        "https://catalogartifact.azureedge.net/publicartifacts/scripts/script.sh",
        "https://catalogartifact.azureedge.net/publicartifacts/scripts/script.sh"
      ]
    },
    "tags": null,
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "typeHandlerVersion": "2.0",
    "virtualMachineExtensionType": "CustomScript"
  },
  {
    "autoUpgradeMinorVersion": true,
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionid/resourceGroups/rgname/providers/Microsoft.Compute/virtualMachines/vmname/extensions/OmsAgentForLinux",
    "instanceView": null,
    "location": "eastus",
    "name": "OmsAgentForLinux",
    "protectedSettings": null,
    "provisioningState": "Succeeded",
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "resourceGroup": "rgname",
    "settings": {
      "workspaceId": "workspaceid"
    },
    "tags": null,
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "typeHandlerVersion": "1.0",
    "virtualMachineExtensionType": "OmsAgentForLinux"
  }
]
```

## <a name="next-steps"></a>Nästa steg
Mer om du vill se koden, aktuella problem och versioner finns i [custom-script-extension-linux repo](https://github.com/Azure/custom-script-extension-linux).
