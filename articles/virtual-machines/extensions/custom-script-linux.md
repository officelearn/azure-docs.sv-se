---
title: Anpassade skript körs på virtuella Linux-datorer i Azure | Microsoft Docs
description: Automatisera åtgärder för Linux VM-konfigurationen genom att använda tillägget för anpassat skript v2
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
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
ms.author: danis
ms.openlocfilehash: 89b3f1184254964a32073c63de3fe69d8a51e292
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652965"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>Använda Azure anpassade skript tillägget Version 2 med Linux virtuella datorer
Anpassade skript tillägget Version 2 laddar ned och kör skript på virtuella Azure-datorer. Det här tillägget är användbart för efter distributionen konfiguration, installation av programvara eller andra uppgifter konfiguration och hantering. Du kan hämta skript från Azure Storage eller en annan tillgänglig Internetplats eller kan du ge dem till tillägget körningsmiljön. 

Tillägget för anpassat skript integreras med Azure Resource Manager-mallar. Du kan också köra det med hjälp av Azure CLI, PowerShell, Azure portal eller REST API för Azure-virtuella datorer.

Den här artikeln beskrivs hur du använder tillägget för anpassat skript från Azure CLI och hur du kör tillägget med hjälp av en Azure Resource Manager-mall. Den här artikeln innehåller även felsökningssteg för Linux-system.


Det finns två Linux anpassade Skriptfilnamnstillägg:
* Version 1 - Microsoft.OSTCExtensions.CustomScriptForLinux
* Version 2 - Microsoft.Azure.Extensions.CustomScript

Växla nya och befintliga distributioner om du vill använda den nya versionen 2 i stället. Den nya versionen är avsedd att ersätta gör att installera. Därför migreringen är lika enkelt som att ändra namn och version, du behöver inte ändra konfigurationen av tillägget.


### <a name="operating-system"></a>Operativsystem

Tillägget för anpassat skript för Linux ska köras på tillägget tillägg stöds OS, mer information finns i det här [artikel](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Skriptets placering

Du kan använda tillägget för att använda Azure Blob storage-autentiseringsuppgifter för att komma åt Azure Blob storage. Du kan också kan skriptets placering vara någon where, förutsatt att den virtuella datorn kan vidarebefordra till den slutpunkten som GitHub, interna filserver osv.

### <a name="internet-connectivity"></a>Internet-anslutning
Om du behöver hämta ett skript externt, till exempel GitHub eller Azure Storage sedan ytterligare Brandväggsnätverk säkerhetsgruppen portar måste öppnas. Till exempel om skriptet finns i Azure Storage, du kan tillåta åtkomst med hjälp av Azure NSG Service taggar för [lagring](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview#service-tags).

Om skriptet finns på en lokal server, så du kan behöva ytterligare brandvägg/Network Security Group portar måste öppnas.

### <a name="tips-and-tricks"></a>Tips
* Det högsta antalet misslyckade för det här tillägget är på grund av syntaxfel i skriptet test skriptet körs utan fel, och också för ytterligare loggning i skript för att göra det lättare att hitta där det misslyckades.
* Skriva skript som är idempotent, så om de får köras igen mer än en gång av misstag, inte får den systemändringar.
* Se till att skripten inte kräver indata från användaren när de körs.
* Det finns 90 minuter som tillåts att köra skript, något längre resulterar i en misslyckad tillhandahållande av tillägget.
* Placera inte omstarter i skriptet och detta medför problem med andra filnamnstillägg som installeras efter omstart, tillägget kan inte fortsätta efter omstart. 
* Om du har ett skript som gör att en omstart kan installera program och köra skript osv. Du bör schemalägga omstart använder ett Cron-jobb, eller använda verktyg som DSC eller Chef, Puppet-tillägg.
* Tillägget körs bara ett skript som en gång, om du vill köra ett skript på varje start kan du använda [moln init avbildningen](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/using-cloud-init) och använda en [skript Per Start](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) modul. Alternativt kan du använda skriptet för att skapa en Systemd service-enhet.
* Om du vill schemalägga när ett skript, använder du tillägget för att skapa ett Cron-jobb. 
* När skriptet körs, visas bara en 'transitioning' tillståndets status från Azure-portalen eller CLI. Om du vill oftare statusuppdateringar ett skript som körs, behöver du skapa din egen lösning.
* Tillägget för anpassat skript internt stöder inte proxy-servrar, men du kan använda ett verktyg för dataöverföring som stöder proxyservrar i skriptet som *Curl*. 
* Tänk på att icke directory standardplatser som ditt skript eller kommandon kan förlita sig på har logik för att hantera detta.



## <a name="extension-schema"></a>Tilläggsschema

Tillägget för anpassat skript konfigurationen anger sådant som skriptets placering och kommandot ska köras. Du kan lagra den här konfigurationen i konfigurationsfiler, ange på kommandoraden eller ange i en Azure Resource Manager-mall. 

Du kan lagra känsliga data i en skyddad konfiguration, som krypteras och dekrypteras endast inuti den virtuella datorn. Skyddade konfigurationen är användbar när kommandot körningen innehåller hemligheter, till exempel ett lösenord.

De här objekten ska behandlas som känsliga data och angetts i konfigurationen för skyddade inställningen tillägg. Azure för VM-tillägget skyddade inställningsdata krypteras och dekrypteras endast på den virtuella måldatorn.

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
      "timestamp":"123456789",          
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
| Publisher | Microsoft.Compute.Extensions | sträng |
| typ | CustomScript | sträng |
| typeHandlerVersion | 2.0 | int |
| fileUris (t.ex.) | https://github.com/MyProject/Archive/MyPythonScript.py | matris |
| commandToExecute (t.ex.) | Python MyPythonScript.py < Mina param1 > | sträng |
| Skriptet | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo = | sträng |
| skipDos2Unix (t.ex.) | false | boolesk |
| tidsstämpeln (t.ex.) | 123456789 | 32-bitars heltal |
| storageAccountName (t.ex.) | examplestorageacct | sträng |
| storageAccountKey (t.ex.) | TmJK/1N3AbAZ3q / + hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg == | sträng |

### <a name="property-value-details"></a>Värdet för egenskapsinformationen
* `skipDos2Unix`: (valfritt, boolean) hoppa över dos2unix konvertering av skriptbaserade webbadresser eller skript.
* `timestamp` (valfritt, 32-bitars heltal) Använd det här fältet bara för att utlösa ett nytt kör av skriptet genom att ändra värdet för det här fältet.  Valfritt heltal är godtagbara. Det får endast vara annorlunda än det tidigare värdet.
 * `commandToExecute`: (**krävs** om skriptet inte har angetts sträng) att posten punkt skriptet körs. Använd det här fältet i stället om kommandot innehåller hemligheter, till exempel lösenord.
* `script`: (**krävs** om commandToExecute inte har angetts sträng) en base64-kodat (och eventuellt gzip'ed) skript som körs av/bin/del.
* `fileUris`: (valfritt, Strängmatrisen) URL: er för filer som ska hämtas.
* `storageAccountName`: (valfritt, string) namnet på lagringskontot. Om du anger autentiseringsuppgifter för lagring, alla `fileUris` måste vara URL: er för Azure-BLOB.
* `storageAccountKey`: (valfritt, string) åtkomstnyckeln för lagringskontot


Följande värden kan anges i inställningarna för offentliga eller skyddade, tillägget avvisar konfiguration där värdena nedan anges i inställningarna för både offentliga och skyddade.
* `commandToExecute`
* `script`
* `fileUris`

Med inställningar för offentliga är användbart för felsökning, men rekommenderas starkt att du använder skyddade inställningarna.

Inställningar för offentliga skickas i klartext till den virtuella datorn där skriptet körs.  Inställningar för skyddade krypteras med en nyckel som känner till Azure och den virtuella datorn. Inställningarna sparas till den virtuella datorn som de skickades, dvs. Om inställningarna krypterades sparas de krypterade på den virtuella datorn. Certifikatet som används för att dekryptera de krypterade värdena lagras på den virtuella datorn, och används för att dekryptera inställningar (om det behövs) vid körning.

#### <a name="property-skipdos2unix"></a>Egenskapen: skipDos2Unix

Standardvärdet är FALSKT, vilket innebär att dos2unix konvertering **är** utförs.

Den tidigare versionen av CustomScript Microsoft.OSTCExtensions.CustomScriptForLinux, skulle automatiskt konvertera DOS-filer till UNIX-filer genom att översätta `\r\n` till `\n`. Denna översättning fortfarande finns och är aktiverad som standard. Den här konverteringen tillämpas på alla filer som hämtas från fileUris eller inställningen skript baserat på något av följande villkor.

* Om tillägget är en av `.sh`, `.txt`, `.py`, eller `.pl` kommer att konverteras. Inställningen skript kommer alltid att matcha det här villkoret eftersom det antas vara ett skript som körs med /bin/sh och sparas som script.sh på den virtuella datorn.
* Om filen som börjar med `#!`.

Dos2unix konvertering kan du hoppa över genom att skipDos2Unix till true.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>"
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>Egenskapen: skript

CustomScript stöder körning av anpassade skript. Skriptinställningar för för att kombinera commandToExecute och fileUris i en och samma inställning. I stället för att konfigurera en fil för att ladda ned från Azure storage eller GitHub gist koda du helt enkelt skriptet som en inställning. Skriptet kan användas för att ersatta commandToExecute och fileUris.

Skriptet **måste** vara base64-kodad.  Skriptet kan **alternativt** vara gzip'ed. Inställningen för skript kan användas i inställningarna för offentliga eller skyddade. Den maximala storleken på data i parametern skript är 256 KB. Om skriptet överskrider den här storleken inte kommer att utföras.

Till exempel får du följande skript som sparas i filen /script.sh/.

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

Inställningens för CustomScript-skriptet skulle konstrueras med utdata från kommandot.

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

Skriptet kan också ha gzip'ed att ytterligare minska storleken (i de flesta fall). (CustomScript identifieras användningen av gzip-komprimering.)

```sh
cat script | gzip -9 | base64 -w 0
```

CustomScript använder följande algoritm för att köra ett skript.

 1. Assert skriptets värdets längd inte överstiga 256 KB.
 1. Base64 avkoda skriptets värdet
 1. _Försök_ till gunzip i base64 avkoda värdet
 1. att skriva värdet avkodade (och alternativt okomprimerade) till disk (/ var/lib/waagent/custom-script/#/script.sh)
 1. Kör skriptet med _ / bin/v - c /var/lib/waagent/custom-script/#/script.sh.


## <a name="template-deployment"></a>Malldistribution
Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. JSON-schema som beskrivs i föregående avsnitt kan användas i en Azure Resource Manager-mall för att köra tillägget för anpassat skript under en Azure Resource Manager för malldistribution. En exempelmall som innehåller tillägget för anpassat skript hittar du här, [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).


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
      "fileUris": ["https://github.com/MyProject/Archive/MyPythonScript.py"
      ]  
    }
  }
}
```

>[!NOTE]
>Dessa egenskapsnamn är skiftlägeskänsliga. Du undviker problem med distribution genom att använda namnen som visas här.

## <a name="azure-cli"></a>Azure CLI
Skapa en konfigurationsfil eller filer när du använder Azure CLI för att köra tillägget för anpassat skript. Som minimum måste du ha 'commandToExecute'.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

Alternativt kan ange du inställningarna i kommandot som en JSON-formaterad sträng. På så sätt kan konfigurationen anges under körning och utan en separat fil.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Azure CLI-exempel

#### <a name="public-configuration-with-script-file"></a>Offentliga konfiguration med skriptfilen

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

#### <a name="public-configuration-with-no-script-file"></a>Offentliga konfiguration med ingen skriptfilen

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

Du kan använda en offentlig konfigurationsfil för att ange skriptfil URI. Du kan använda en skyddad fil för att ange kommandot som ska köras.

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
När tillägget för anpassat skript körs skriptet skapas eller hämtas i en katalog som liknar följande exempel. Kommandoutdata sparas i den här katalogen i `stdout` och `stderr` filer.

```bash
/var/lib/waagent/custom-script/download/0/
```

Felsöka, först Läs i loggen för Linux-Agent, kontrollera att tillägget har körts, kontrollera:

```bash
/var/log/waagent.log 
```

Du bör titta efter tillägg körningen, det ser ut ungefär så:
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
Vissa punkter att Observera:
1. Aktivera är när kommandot börjar köras.
2. Hämta avser hämtning av CustomScript-tilläggspaket från Azure, inte skriptfiler som anges i fileUris.


Tillägget för Azure-skript resulterar i en logg som finns här:

```bash
/var/log/azure/custom-script/handler.log
```

Du bör titta efter induvidual körningen, det ser ut ungefär så:
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
* Aktivera kommandot start är den här loggen
* Inställningarna som överförs till tillägget
* Tillägget hämta fil- och resultatet av den.
* Det kommando som körs och resultatet.

Du kan också hämta Körningstillståndet för tillägget för anpassat skript med hjälp av Azure CLI:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

Det ser ut som följande utdata:

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
Kod, aktuella problem och versioner finns [anpassad-skript-tillägg-linux lagringsplatsen](https://github.com/Azure/custom-script-extension-linux).

