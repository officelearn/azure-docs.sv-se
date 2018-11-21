---
title: Köra anpassade skript på virtuella Linux-datorer i Azure | Microsoft Docs
description: Automatisera åtgärder för Linux VM-konfigurationen genom att använda tillägget för anpassat skript v2
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2018
ms.author: roiyz
ms.openlocfilehash: 87d15f7b351f2b8b6a8c010651d82faa66b28918
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2018
ms.locfileid: "52276473"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>Använda Azure anpassade skript-tillägget Version 2 med Linux-datorer
Anpassat skript-tillägget Version 2 laddar ned och kör skript på virtuella Azure-datorer. Det här tillägget är användbart för konfigurationen efter distribution, Programvaruinstallation eller andra konfigurationshantering/uppgifter. Du kan hämta skript från Azure Storage eller en annan tillgänglig Internetplats eller du kan ange dem till tillägget-körningen. 

Det anpassade Skripttillägget integreras med Azure Resource Manager-mallar. Du kan också köra den med hjälp av Azure CLI, PowerShell, Azure-portalen eller Azure Virtual Machines REST API.

Den här artikeln beskriver hur du använder tillägget för anpassat skript från Azure CLI och hur du kör tillägget med hjälp av en Azure Resource Manager-mall. Den här artikeln innehåller också felsökningssteg för Linux-system.


Det finns två Linux anpassade skripttillägg:
* Version 1 – Microsoft.OSTCExtensions.CustomScriptForLinux
* Version 2 – Microsoft.Azure.Extensions.CustomScript

Växla nya och befintliga distributioner för att använda den nya versionen 2 i stället. Den nya versionen är avsedd att vara en lättillgänglig ersättning. Därför migreringen är lika enkelt som att ändra namn och version, du behöver inte ändra din konfiguration.


### <a name="operating-system"></a>Operativsystem

Tillägget för anpassat skript för Linux ska köras på tillägget tillägget stöds OS, mer information finns i det här [artikeln](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Skriptplats

Du kan använda tillägget för att använda Azure Blob storage-autentiseringsuppgifter för att få åtkomst till Azure Blob storage. Du kan också kan skriptets placering vara någon where, så länge som den virtuella datorn kan vidarebefordra till den slutpunkten, till exempel GitHub, interna filserver osv.

### <a name="internet-connectivity"></a>Internetanslutning
Om du behöver hämta ett skript externt, till exempel GitHub eller Azure Storage kan sedan ytterligare Brandväggsnätverk säkerhetsgrupp-portar måste du öppna. Till exempel om skriptet finns i Azure Storage, du kan tillåta åtkomst till med hjälp av Azure NSG-Tjänsttaggar för [Storage](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Om skriptet finns på en lokal server så kan du fortfarande behöver ytterligare brandvägg/Network Security Group portar måste du öppna.

### <a name="tips-and-tricks"></a>Tips
* Högsta Felfrekvensen för det här tillägget är på grund av ett syntaxfel i skriptet test som skriptet körs utan fel, och även placera i ytterligare loggning till skriptet att göra det enklare att hitta där det misslyckades.
* Skriva skript som är idempotenta, så om de få kör igen mer än en gång av misstag kan den inte orsakar ändringarna.
* Se till att skript som inte kräver indata från användaren när de körs.
* Det är 90 minuter som tillåts för skriptet att köra, något längre resulterar i en misslyckad tillhandahållande av tillägget.
* Placera inte omstarter i skriptet och detta kan orsaka problem med andra tillägg installeras efter omstarten tillägget kommer inte att fortsätta efter omstarten. 
* Om du har ett skript som gör att en omstart kan installera program och kör skript osv. Du bör schemalägga omstarten med ett Cron-jobb eller med verktyg som DSC eller Chef, Puppet-tillägg.
* Tillägget körs bara ett skript en gång, om du vill köra ett skript på varje start kan du använda [cloud-init bild](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init) och använda en [skript Per Start](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) modulen. Du kan också använda skriptet för att skapa en Systemd service-enhet.
* Om du vill schemalägga när ett skript som körs ska du använda tillägget för att skapa ett Cron-jobb. 
* När skriptet körs, visas bara statusen ”transitioning' tillägg från Azure-portalen eller CLI. Om du vill mer frekventa statusuppdateringar ett skript som körs, behöver du skapa en egen lösning.
* Anpassat skripttillägg internt stöder inte proxy-servrar, men du kan använda ett filöverföringsverktyg som stöder proxyservrar i skriptet som *Curl*. 
* Tänk på inte är standard directory-platser som dina skript eller kommandon kan förlita sig på, har logik för att hantera det.



## <a name="extension-schema"></a>Tilläggsschema

Tillägget för anpassat skript konfigurationen anger till exempel skriptplats och kommandot som ska köras. Du kan lagra den här konfigurationen i konfigurationsfiler, ange det på kommandoraden eller anger den i en Azure Resource Manager-mall. 

Du kan lagra känsliga data i en skyddad konfiguration, som krypteras och dekrypteras bara på den virtuella datorn. Konfigurationen av skyddade är användbart när körningskommandot innefattar hemligheter som lösenord.

De här objekten ska behandlas som känsliga data och anges i den skyddade Konfigurationsinställningen för tillägg. Azure VM-tillägget skyddade inställningsdata krypteras och dekrypteras bara på den virtuella måldatorn.

```json
{
  "name": "config-app",
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
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
       "fileUris": ["https://.."]  
    }
  }
}
```

### <a name="property-values"></a>Egenskapsvärden

| Namn | Värdet / exempel | Datatyp | 
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | datum |
| utgivare | Microsoft.Compute.Extensions | sträng |
| typ | CustomScript | sträng |
| typeHandlerVersion | 2.0 | int |
| fileUris (t.ex.) | https://github.com/MyProject/Archive/MyPythonScript.py | matris |
| commandToExecute (t.ex.) | Python MyPythonScript.py < Mina param1 > | sträng |
| skriptet | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo = | sträng |
| skipDos2Unix (t.ex.) | false | boolesk |
| tidsstämpel (t.ex.) | 123456789 | 32-bitars heltal |
| storageAccountName (t.ex.) | examplestorageacct | sträng |
| storageAccountKey (t.ex.) | TmJK/1N3AbAZ3q / + hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg == | sträng |

### <a name="property-value-details"></a>Information om egenskapen
* `skipDos2Unix`: (valfritt, booleskt) hoppa över dos2unix konvertering av skriptbaserade fil-URL: er eller skript.
* `timestamp` (valfritt, 32-bitars heltal) Använd det här fältet bara för att utlösa en omkörning av skriptet genom att ändra värdet för det här fältet.  Alla heltalsvärde är godtagbara. Det får endast vara skiljer sig från det tidigare värdet.
 * `commandToExecute`: (**krävs** om skriptet inte har angetts sträng) startpunktsskriptet att köra. Använd det här fältet i stället om kommandot innehåller hemligheter som lösenord.
* `script`: (**krävs** om commandToExecute inte har angetts sträng) en base64-kodat (och eventuellt gzip'ed) skript som körs av/bin/sh.
* `fileUris`: (valfritt, Strängmatrisen) URL: er för filer som ska hämtas.
* `storageAccountName`: (valfritt, string) namnet på lagringskontot. Om du anger autentiseringsuppgifter för lagringskontot, alla `fileUris` måste vara URL: er för Azure-BLOB.
* `storageAccountKey`: (valfritt, string) åtkomstnyckeln för lagringskontot


Följande värden kan anges i antingen offentliga eller skyddade inställningar, tillägget kommer att avslå alla konfigurationer där värdena nedan anges i inställningarna för både offentliga och skyddade.
* `commandToExecute`
* `script`
* `fileUris`

Med offentliga inställningar som kan vara användbart för felsökning, men det rekommenderas starkt att du använder skyddade inställningarna.

Inställningar för offentliga skickas i klartext till den virtuella datorn där skriptet körs.  Skyddade inställningarna är krypterat med en nyckel som bara du känner till Azure och den virtuella datorn. Inställningarna sparas till den virtuella datorn som de skickades, dvs. Om inställningarna krypterades sparas de krypterade på den virtuella datorn. Certifikatet som används för att dekryptera krypterade värden lagras på den virtuella datorn, och används för att dekryptera inställningar (vid behov) vid körning.

#### <a name="property-skipdos2unix"></a>Egenskapen: skipDos2Unix

Standardvärdet är FALSKT, vilket innebär att dos2unix konvertering **är** körs.

Den tidigare versionen av CustomScript Microsoft.OSTCExtensions.CustomScriptForLinux, skulle automatiskt konvertera DOS-filer till UNIX-filer genom att översätta `\r\n` till `\n`. Den här translation fortfarande finns och är aktiverad som standard. Den här konverteringen tillämpas på alla filer som hämtas från fileUris eller skriptinställningen baserat på något av följande villkor.

* Om tillägget är en av `.sh`, `.txt`, `.py`, eller `.pl` kommer att konverteras. Skriptinställningen kommer alltid att matcha det här villkoret eftersom det antas vara ett skript som körs med /bin/sh och sparas som script.sh på den virtuella datorn.
* Om filen som börjar med `#!`.

Dos2unix konverteringen kan du hoppa över genom att ange skipDos2Unix till true.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>",
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>Egenskapen: skript

CustomScript stöder användardefinierade skriptkörningen. Skriptinställningar att kombinera commandToExecute och fileUris till en och samma inställning. Du kan helt enkelt koda skriptet som en inställning i stället för att konfigurera en fil för nedladdning från Azure storage eller GitHub gist. Skriptet kan användas för att ersatta commandToExecute och fileUris.

Skriptet **måste** vara base64-kodad.  Skriptet kan **eventuellt** vara gzip'ed. Skriptinställningen kan användas i inställningarna för offentliga eller skyddade. Den maximala storleken för parametern skriptet data är 256 KB. Om skriptet överskrider den här storleken inte kommer att köra.

Till exempel får följande skript sparas i filen /script.sh/.

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

Rätt skriptinställningen med CustomScript skulle skapas genom att utföra följande kommandots utdata.

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

Skriptet kan du kan också vara gzip'ed att ytterligare minska storleken (i de flesta fall). (CustomScript upptäcker automatiskt användningen av gzip-komprimering.)

```sh
cat script | gzip -9 | base64 -w 0
```

CustomScript använder följande algoritm för att köra ett skript.

 1. Assert längden på skriptets värdet inte överstiga 256 KB.
 1. Base64 avkoda skriptets värdet
 1. _Försök_ till gunzip base64 avkodas värde
 1. skriva avkodade (och eventuellt okomprimerade) värdet till disk (/ var/lib/waagent/custom-script/#/script.sh)
 1. Kör skriptet med _ / bin/sh – c /var/lib/waagent/custom-script/#/script.sh.


## <a name="template-deployment"></a>Malldistribution
Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. JSON-schemat som beskrivs i föregående avsnitt kan användas i en Azure Resource Manager-mall för att köra tillägget för anpassat skript under en malldistribution för Azure Resource Manager. En exempelmall som innehåller det anpassade Skripttillägget finns här, [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).


```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
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
>Dessa egenskapsnamn är skiftlägeskänsliga. Undvik distributionsproblem genom att använda namnen som visas här.

## <a name="azure-cli"></a>Azure CLI
Skapa en konfigurationsfil eller filer när du använder Azure CLI för att köra tillägget för anpassat skript. Du måste ha ”commandToExecute” som ett minimum.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

Alternativt kan du ange inställningarna i kommandot som en JSON-formaterad sträng. På så sätt kan konfigurationen anges vid körning och utan en separat konfigurationsfil.

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

#### <a name="public-configuration-with-no-script-file"></a>Offentlig konfiguration med inga skriptfil

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

Du kan använda en offentlig konfigurationsfil för att ange skriptfil URI. Du kan använda en skyddad fil för att ange kommandot för att köras.

Offentliga konfigurationsfil:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

Skyddade konfigurationsfil:  

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
När tillägget för anpassat skript körs skriptet skapas eller hämtas i en katalog som liknar följande exempel. Utdata från kommandot sparas även i den här katalogen i `stdout` och `stderr` filer.

```bash
/var/lib/waagent/custom-script/download/0/
```

Felsöka, först kontrollera loggen för Linux-agenten, kontrollera att tillägget har körts, kontrollera:

```bash
/var/log/waagent.log 
```

Du bör se ut för tillägget körningen, det ser ut ungefär som:
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
2. Download som relaterar till hämtning av paketets CustomScript-tillägget från Azure, inte skriptfiler anges i fileUris.


Azure-Skripttillägget resulterar i en logg som du hittar här:

```bash
/var/log/azure/custom-script/handler.log
```

Du bör se ut för induvidual körningen, det ser ut ungefär som:
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
* Aktivera kommando startar är den här loggfilen
* Inställningarna som överförs till tillägget
* Tillägget hämtar fil- och resultatet av den.
* Det kommando som körs och resultatet.

Du kan också hämta Körningstillståndet för tillägget för anpassat skript med hjälp av Azure CLI:

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
Kod, aktuella problem och versioner finns i [anpassad-skript-tillägg-linux lagringsplatsen](https://github.com/Azure/custom-script-extension-linux).

