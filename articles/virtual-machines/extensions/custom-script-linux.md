---
title: Köra anpassade skript på virtuella Linux-datorer i Azure
description: Automatisera konfigurations uppgifter för virtuella Linux-datorer med hjälp av det anpassade skript tillägget v2
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
ms.openlocfilehash: 2190bfd1a260d7b866fedc1f7c699faef2431a93
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78380987"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>Använd Azures anpassade skript tillägg version 2 med virtuella Linux-datorer
Det anpassade skript tillägget version 2 laddar ned och kör skript på virtuella Azure-datorer. Det här tillägget är användbart för konfiguration efter distribution, program varu installation eller andra konfigurations-och hanterings åtgärder. Du kan hämta skript från Azure Storage eller en annan tillgänglig Internet plats, eller så kan du ange dem till tilläggets körnings miljö. 

Det anpassade skript tillägget integreras med Azure Resource Manager mallar. Du kan också köra det med hjälp av Azure CLI, PowerShell eller Azure Virtual Machines REST API.

Den här artikeln beskriver hur du använder tillägget för anpassat skript från Azure CLI och hur du kör tillägget med hjälp av en Azure Resource Manager mall. Den här artikeln innehåller också fel söknings steg för Linux-system.


Det finns två anpassade skript tillägg för Linux:
* Version 1-Microsoft. OSTCExtensions. CustomScriptForLinux
* Version 2-Microsoft. Azure. Extensions. CustomScript

Växla mellan nya och befintliga distributioner för att använda den nya version 2 i stället. Den nya versionen är avsedd att vara en lättillgänglig ersättning. Därför är migreringen lika enkel som att ändra namn och version. Du behöver inte ändra tilläggskonfigurationen.


### <a name="operating-system"></a>Operativsystem

Det anpassade skript tillägget för Linux kommer att köras på tillägget tillägg som stöds, mer information finns i den här [artikeln](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

### <a name="script-location"></a>Skript plats

Du kan använda tillägget för att använda dina Azure Blob Storage-autentiseringsuppgifter för att få åtkomst till Azure Blob Storage. Alternativt kan skript platsen var som helst, så länge den virtuella datorn kan dirigeras till den slut punkten, till exempel GitHub, intern fil Server osv.

### <a name="internet-connectivity"></a>Internet anslutning
Om du behöver hämta ett skript externt, till exempel GitHub eller Azure Storage, måste du öppna ytterligare brand Väggs-och nätverks säkerhets grupps portar. Om ditt skript till exempel finns i Azure Storage kan du tillåta åtkomst med hjälp av Azure NSG service-taggar för [lagring](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Om ditt skript finns på en lokal server kanske du fortfarande behöver fler brand Väggs-och nätverks säkerhets grupps portar som måste öppnas.

### <a name="tips-and-tricks"></a>Tips och knep
* Den högsta felfrekvensen för det här tillägget beror på syntaxfel i skriptet. Kontrollera att skriptet körs utan fel, och implementera ytterligare loggning i skriptet så att det blir enklare att identifiera var felet inträffade.
* Skriv skript som är idempotenta, så att inga systemändringar görs om de oavsiktligt körs mer än en gång.
* Kontrol lera att skripten inte kräver användarindata när de körs.
* Det finns 90 minuter som tillåts för att skriptet ska kunna köras. allt längre leder till en misslyckad etablering av tillägget.
* Starta inte om omstarter inuti skriptet. Detta leder till problem med andra tillägg som installeras och efter omstarten kommer tillägget inte att fortsätta efter omstarten. 
* Om du har ett skript som gör en omstart installerar du program och kör skript osv. Du bör schemalägga en omstart med hjälp av ett cron-jobb eller använda verktyg som DSC eller chef, Puppet-tillägg.
* Tillägget kör bara ett skript en gång, om du vill köra ett skript vid varje start, kan du använda [Cloud-Init-avbildning](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init) och använda [skript per](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) startmodul. Du kan också använda skriptet för att skapa en system tjänst enhet.
* Om du vill schemalägga när ett skript ska köras ska du använda tillägget för att skapa ett cron-jobb. 
* När skriptet körs visas tillägget med övergångsstatus på Azure-portalen eller i CLI. Om du vill ha mer frekventa status uppdateringar för ett skript som körs måste du skapa en egen lösning.
* Anpassat skript tillägg stöder inte proxyservrar, men du kan använda ett fil överförings verktyg som stöder proxyservrar i skriptet, till exempel *vändning*. 
* Var medveten om icke-standardkataloger som skripten eller kommandona kan vara beroende av, har logik för att hantera detta.
*  När du distribuerar anpassat skript till produktion VMSS-instanser rekommenderar vi att du distribuerar via JSON-mallen och lagrar ditt skript lagrings konto där du har kontroll över SAS-token. 


## <a name="extension-schema"></a>Tilläggsschema

Konfigurationen för det anpassade skript tillägget anger saker som skript plats och kommandot som ska köras. Du kan lagra konfigurationen i konfigurationsfiler, ange den på kommando raden eller ange den i en Azure Resource Manager mall. 

Du kan lagra känsliga data i en skyddad konfiguration, som krypteras och bara dekrypteras i den virtuella datorn. Den skyddade konfigurationen är användbar när körnings kommandot innehåller hemligheter som lösen ord.

De här objekten ska behandlas som känsliga data och anges i konfigurationerna för tilläggen för skyddad inställning. Azure VM-tillägget skyddade inställningsdata krypteras och dekrypteras bara på den virtuella måldatorn.

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
> Egenskapen managedIdentity **får inte** användas tillsammans med storageAccountName-eller storageAccountKey-egenskaper

### <a name="property-values"></a>Egenskapsvärden

| Namn | Värdet / exempel | Datatyp | 
| ---- | ---- | ---- |
| apiVersion | 2019-03-01 | date |
| publisher | Microsoft. Compute. Extensions | sträng |
| typ | CustomScript | sträng |
| typeHandlerVersion | 2.1 | int |
| fileUris (t. ex.) | https://github.com/MyProject/Archive/MyPythonScript.py | matris |
| commandToExecute (t. ex.) | python-MyPythonScript.py \<param1 > | sträng |
| -skriptet | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo= | sträng |
| skipDos2Unix (t. ex.) | false | boolean |
| timestamp (t.ex.) | 123456789 | 32-bitars heltal |
| storageAccountName (t. ex.) | examplestorageacct | sträng |
| storageAccountKey (t. ex.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | sträng |
| managedIdentity (t. ex.) | {} eller {"clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232"} eller {"objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b"} | JSON-objekt |

### <a name="property-value-details"></a>Information om egenskaps värde
* `apiVersion`: den mest aktuella API version kan hittas med [Resursläsaren](https://resources.azure.com/) eller från Azure CLI med hjälp av följande kommando `az provider list -o json`
* `skipDos2Unix`: (valfritt, boolesk) hoppa över dos2unix-konvertering av skriptbaserade fil-URL: er eller skript.
* `timestamp` (valfritt, 32-bitars heltal) Använd endast det här fältet för att utlösa en ny körning av skriptet genom att ändra värdet för det här fältet.  Alla heltals värden är acceptabla. Det får bara vara ett annat än det tidigare värdet.
  * `commandToExecute`: (**krävs** om skript inte har angetts, sträng) Start punkt skriptet som ska köras. Använd det här fältet i stället om kommandot innehåller hemligheter som lösen ord.
* `script`: (**krävs** om commandToExecute inte har angetts, sträng) ett base64-kodat (och eventuellt gzip'ed) skript som körs av/bin/sh.
* `fileUris`: (valfritt, sträng mat ris) URL: er för fil (er) som ska hämtas.
* `storageAccountName`: (valfritt, sträng) namnet på lagrings kontot. Om du anger autentiseringsuppgifter för lagring måste alla `fileUris` vara URL: er för Azure-blobar.
* `storageAccountKey`: (valfri, sträng) åtkomst nyckeln för lagrings kontot
* `managedIdentity`: (valfritt, JSON-objekt) den [hanterade identiteten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) för nedladdning av fil (er)
  * `clientId`: (valfritt, sträng) klient-ID: t för den hanterade identiteten
  * `objectId`: (valfritt, sträng) objekt-ID för den hanterade identiteten


Följande värden kan anges i offentliga eller skyddade inställningar. tillägget kommer att neka alla konfigurationer där värdena nedan anges i både offentliga och skyddade inställningar.
* `commandToExecute`
* `script`
* `fileUris`

Användning av offentliga inställningar kan vara användbart för fel sökning, men vi rekommenderar starkt att du använder skyddade inställningar.

Offentliga inställningar skickas i klartext till den virtuella dator där skriptet ska köras.  Skyddade inställningar krypteras med en nyckel som endast är känd för Azure och den virtuella datorn. Inställningarna sparas på den virtuella datorn när de skickades, dvs. om inställningarna krypterades sparas de som krypterade på den virtuella datorn. Certifikatet som används för att dekryptera de krypterade värdena lagras på den virtuella datorn och används för att dekryptera inställningar (vid behov) vid körning.

#### <a name="property-skipdos2unix"></a>Egenskap: skipDos2Unix

Standardvärdet är false, vilket innebär att dos2unix **-konverteringen körs.**

Den tidigare versionen av CustomScript, Microsoft. OSTCExtensions. CustomScriptForLinux, konverterar automatiskt DOS-filer till UNIX-filer genom att översätta `\r\n` till `\n`. Den här översättningen finns fortfarande och är på som standard. Den här konverteringen tillämpas på alla filer som laddats ned från fileUris eller skript inställningen baserat på något av följande kriterier.

* Om tillägget är ett av `.sh`, `.txt`, `.py`eller `.pl` kommer det att konverteras. Skript inställningen matchar alltid det här kriteriet eftersom det antas vara ett skript som körs med/bin/sh och som sparas som script.sh på den virtuella datorn.
* Om filen börjar med `#!`.

Dos2unix-omvandlingen kan hoppas över genom att ange skipDos2Unix till true.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>",
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>Egenskap: skript

CustomScript stöder körning av ett användardefinierat skript. Skript inställningarna för att kombinera commandToExecute och fileUris i en enda inställning. I stället för att behöva konfigurera en fil för nedladdning från Azure Storage eller GitHub, kan du bara koda skriptet som en inställning. Skriptet kan användas för att ersätta commandToExecute och fileUris.

Skriptet **måste** vara Base64-kodat.  Skriptet kan **också** vara gzip'ed. Skript inställningen kan användas i offentliga eller skyddade inställningar. Den maximala storleken för skript parameterns data är 256 KB. Om skriptet överskrider den här storleken kommer det inte att köras.

Till exempel kan följande skript sparas i filen/script.sh/.

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

Rätt CustomScript-skript inställning skulle konstrueras genom att ta utdata från följande kommando.

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

Skriptet kan också vara gzip'ed för att ytterligare minska storleken (i de flesta fall). (CustomScript automatiskt identifierar användningen av gzip-komprimering.)

```sh
cat script | gzip -9 | base64 -w 0
```

CustomScript använder följande algoritm för att köra ett skript.

 1. kontroll av att längden på skriptets värde inte överskrider 256 KB.
 1. Base64 avkoda skriptets värde
 1. _försök_ att gunzip det Base64-avkodade värdet
 1. Skriv avkodat (och eventuellt expanderat) värde till disk (/var/lib/waagent/Custom-script/#/script.sh)
 1. Kör skriptet med _/bin/sh-c/var/lib/waagent/Custom-script/#/script.sh.

####  <a name="property-managedidentity"></a>Egenskap: managedIdentity

CustomScript (version 2,1 och senare) stöder [hanterad identitet](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) för hämtning av filer från URL: er som finns i inställningen "fileUris". Det ger CustomScript åtkomst till Azure Storage privata blobbar eller behållare utan att användaren måste skicka hemligheter som SAS-token eller lagrings konto nycklar.

Om du vill använda den här funktionen måste användaren lägga till en [tilldelad](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#adding-a-system-assigned-identity) eller [användardefinierad](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#adding-a-user-assigned-identity) identitet till den virtuella datorn eller VMSS där CustomScript förväntas köras, och [ge hanterad identitets åtkomst till Azure Storage containern eller blobben](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage#grant-access).

Om du vill använda den systemtilldelade identiteten på den virtuella mål datorn/VMSS anger du fältet managedidentity till ett tomt JSON-objekt. 

> Exempel:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : {}
> }
> ```

Om du vill använda den användardefinierade identiteten på den virtuella mål datorn/VMSS konfigurerar du fältet managedidentity med klient-ID: t eller objekt-ID: t för den hanterade identiteten.

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
> Egenskapen managedIdentity **får inte** användas tillsammans med storageAccountName-eller storageAccountKey-egenskaper

## <a name="template-deployment"></a>Malldistribution
Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. Det JSON-schema som beskrivs i föregående avsnitt kan användas i en Azure Resource Manager mall för att köra det anpassade skript tillägget under en Azure Resource Manager mall-distribution. En exempel mall som innehåller tillägget för anpassat skript hittar du här, [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).


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
>Dessa egenskaps namn är Skift läges känsliga. Använd de namn som visas här för att undvika distributions problem.

## <a name="azure-cli"></a>Azure CLI
När du använder Azure CLI för att köra det anpassade skript tillägget skapar du en konfigurations fil eller filer. Du måste minst ha "commandToExecute".

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

Alternativt kan du ange inställningarna i kommandot som en JSON-formaterad sträng. Detta gör att konfigurationen kan anges under körningen och utan en separat konfigurations fil.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Azure CLI-exempel

#### <a name="public-configuration-with-script-file"></a>Offentlig konfiguration med skript fil

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

#### <a name="public-configuration-with-no-script-file"></a>Offentlig konfiguration utan skript fil

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

Du kan använda en offentlig konfigurations fil för att ange skript filens URI. Du använder en skyddad konfigurations fil för att ange kommandot som ska köras.

Offentlig konfigurations fil:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

Skyddad konfigurations fil:  

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
När det anpassade skript tillägget körs, skapas eller laddas skriptet ned till en katalog som liknar följande exempel. Kommandots utdata sparas också i den här katalogen i `stdout` och `stderr` filer.

```bash
/var/lib/waagent/custom-script/download/0/
```

Du kan felsöka genom att först kontrol lera loggen för Linux-agenten, se till att tillägget har körts, se:

```bash
/var/log/waagent.log 
```

Du bör söka efter tilläggs körningen, det ser ut ungefär så här:
```text
2018/04/26 17:47:22.110231 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] [Enable] current handler state is: notinstalled
2018/04/26 17:47:22.306407 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Download, message=Download succeeded, duration=167
2018/04/26 17:47:22.339958 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Initialize extension directory
2018/04/26 17:47:22.368293 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Update settings file: 0.settings
2018/04/26 17:47:22.394482 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Install extension [bin/custom-script-shim install]
2018/04/26 17:47:23.432774 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Install, message=Launch command succeeded: bin/custom-script-shim install, duration=1007
2018/04/26 17:47:23.476151 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Enable extension [bin/custom-script-shim enable]
2018/04/26 17:47:24.516444 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Enable, message=Launch command succeeded: bin/custom-sc
```
Några saker att tänka på:
1. Aktivera är när kommandot börjar köras.
2. Hämtningen är relaterad till nedladdningen av CustomScript-tilläggs paketet från Azure, inte skriptfilerna som anges i fileUris.


Azure-skriptets tillägg skapar en logg, som du hittar här:

```bash
/var/log/azure/custom-script/handler.log
```

Du bör söka efter den enskilda körningen. den ser ut ungefär så här:
```text
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
* Inställningarna som överförs till tillägget
* Tillägget laddar ned filen och resultatet av det.
* Kommandot som körs och resultatet.

Du kan också hämta körnings statusen för det anpassade skript tillägget med hjälp av Azure CLI:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

Utdata ser ut som följande text:

```azurecli
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>Nästa steg
Om du vill se kod, aktuella problem och versioner, se [lagrings platsen för anpassade skript-tillägg – Linux](https://github.com/Azure/custom-script-extension-linux).

