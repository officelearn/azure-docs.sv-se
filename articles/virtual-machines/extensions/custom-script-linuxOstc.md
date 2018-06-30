---
title: Anpassade skript körs på virtuella Linux-datorer i Azure | Microsoft Docs
description: Automatisera åtgärder för Linux VM-konfigurationen genom att använda tillägget för anpassat skript v1
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2018
ms.author: danis
ms.openlocfilehash: 526021ca238be7bc934e639c34d3e49879279a6a
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37127660"
---
# <a name="use-the-azure-custom-script-extension-version-1-with-linux-virtual-machines"></a>Använda Azure anpassade skript tillägget Version 1 med Linux virtuella datorer
Anpassade skript tillägget Version 1 hämtar och kör skript på virtuella Azure-datorer. Det här tillägget är användbart för efter distributionen konfiguration, installation av programvara eller andra uppgifter konfiguration och hantering. Du kan hämta skript från Azure Storage eller en annan tillgänglig Internetplats eller kan du ge dem till tillägget körningsmiljön. 

Tillägget för anpassat skript integreras med Azure Resource Manager-mallar. Du kan också köra det med hjälp av Azure CLI, PowerShell, Azure portal eller REST API för Azure-virtuella datorer.

Den här artikeln beskrivs hur du använder tillägget för anpassat skript från Azure CLI och hur du kör tillägget med hjälp av en Azure Resource Manager-mall. Den här artikeln innehåller även felsökningssteg för Linux-system.


Det finns två Linux anpassade Skriptfilnamnstillägg:
* Version 1 - Microsoft.OSTCExtensions.CustomScriptForLinux
* Version 2 - Microsoft.Azure.Extensions.CustomScript

Växla nya och befintliga distributioner om du vill använda den nya versionen ([Microsoft.Azure.Extensions.CustomScript](https://docs.microsoft.com/en-us/azure/virtual-machines/extensions/custom-script-linux)) i stället. Den nya versionen är avsedd att ersätta gör att installera. Därför migreringen är lika enkelt som att ändra namn och version, du behöver inte ändra konfigurationen av tillägget.

 

### <a name="operating-system"></a>Operativsystem
Linux-distributioner som stöds:

- CentOS 6.5 och högre
- Debian 8 och senare
    - Debian 8.7 levereras inte med Python2 i de senaste bilderna som bryter CustomScriptForLinux.
- FreeBSD
- OpenSUSE 13.1 och högre
- Oracle Linux 6.4 och högre
- SUSE Linux Enterprise Server 11 SP3 och högre
- Ubuntu 12.04 och högre

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
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "<url>"
      ],
      "enableInternalDNSCheck": true
    },
    "protectedSettings": {
      "storageAccountName": "<storage-account-name>",
      "storageAccountKey": "<storage-account-key>",
      "commandToExecute": "<command>"
    }
  }
}
```

### <a name="property-values"></a>Egenskapsvärden

| Namn | Värdet / exempel | Datatyp | 
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | datum |
| Publisher | Microsoft.OSTCExtensions | sträng |
| typ | CustomScriptForLinux | sträng |
| typeHandlerVersion | 1.5 | int |
| fileUris (t.ex.) | https://github.com/MyProject/Archive/MyPythonScript.py | matris |
| commandToExecute (t.ex.) | Python MyPythonScript.py < Mina param1 > | sträng |
| enableInternalDNSCheck | true | boolesk |
| storageAccountName (t.ex.) | examplestorageacct | sträng |
| storageAccountKey (t.ex.) | TmJK/1N3AbAZ3q / + hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg == | sträng |

### <a name="property-value-details"></a>Värdet för egenskapsinformationen
* `fileUris`: (valfritt, Strängmatrisen) listan uri skript
* `enableInternalDNSCheck`: (valfritt, bool) standard är sant, inställd på False för att inaktivera DNS-kontroll.
* `commandToExecute`: (valfritt, string) entrypoint skriptet ska köras
* `storageAccountName`: (valfritt, string) namnet på lagringskontot
* `storageAccountKey`: (valfritt, string) åtkomstnyckeln för lagringskontot

Följande värden kan anges i inställningarna för offentliga eller skyddade, du måste inte har dessa värden under set i både offentliga och skyddade inställningarna.
* `commandToExecute`

Med inställningar för offentliga är användbart för felsökning, men rekommenderas starkt att du använder skyddade inställningarna.

Inställningar för offentliga skickas i klartext till den virtuella datorn där skriptet körs.  Inställningar för skyddade krypteras med en nyckel som känner till Azure och den virtuella datorn. Inställningarna sparas till den virtuella datorn som de skickades, dvs. Om inställningarna krypterades sparas de krypterade på den virtuella datorn. Certifikatet som används för att dekryptera de krypterade värdena lagras på den virtuella datorn, och används för att dekryptera inställningar (om det behövs) vid körning.


## <a name="template-deployment"></a>Malldistribution
Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. JSON-schema som beskrivs i föregående avsnitt kan användas i en Azure Resource Manager-mall för att köra tillägget för anpassat skript under en Azure Resource Manager för malldistribution. 


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
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": ["http://MyAccount.blob.core.windows.net/vhds/MyShellScript.sh"]
    },
    "protectedSettings": {
      "storageAccountName": "MyAccount",
      "storageAccountKey": "<storage-account-key>",
      "commandToExecute": "sh MyShellScript.sh"
    }
  }
}
```

>[!NOTE]
>Dessa egenskapsnamn är skiftlägeskänsliga. Du undviker problem med distribution genom att använda namnen som visas här.

## <a name="azure-cli"></a>Azure CLI
Skapa en konfigurationsfil eller filer när du använder Azure CLI för att köra tillägget för anpassat skript. Som minimum måste du ha 'commandToExecute'.

```azurecli
az vm extension set -n VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.5 \
  --vm-name MyVm --resource-group MyResourceGroup \
  --protected-settings '{"commandToExecute": "echo hello"}'
```

Alternativt kan ange du inställningarna i kommandot som en JSON-formaterad sträng. På så sätt kan konfigurationen anges under körning och utan en separat fil.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Azure CLI-exempel

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
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
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
az vm extension set 
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Felsökning
När tillägget för anpassat skript körs skriptet skapas eller hämtas i en katalog som liknar följande exempel. Kommandoutdata sparas i den här katalogen i `stdout` och `stderr` filer. 

```bash
/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-<version>/download/1
```

Felsöka, först Läs i loggen för Linux-Agent, kontrollera att tillägget har körts, kontrollera:

```bash
/var/log/waagent.log 
```

Du bör titta efter tillägg körningen, det ser ut ungefär så:
```text
2018/04/26 15:29:44.835067 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Target handler state: enabled
2018/04/26 15:29:44.867625 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] [Enable] current handler state is: notinstalled
2018/04/26 15:29:44.959605 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Download, message=Download succeeded, duration=59
2018/04/26 15:29:44.993269 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Initialize extension directory
2018/04/26 15:29:45.022972 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Update settings file: 0.settings
2018/04/26 15:29:45.051763 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Install extension [customscript.py -install]
2018/04/26 15:29:45 CustomScriptForLinux started to handle.
2018/04/26 15:29:45 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] cwd is /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2
2018/04/26 15:29:45 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Change log file to /var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log
2018/04/26 15:29:46.088212 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Install, message=Launch command succeeded: customscript.py -install, duration=1005
2018/04/26 15:29:46.133367 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Enable extension [customscript.py -enable]
2018/04/26 15:29:46 CustomScriptForLinux started to handle.
..
2018/04/26 15:29:47.178163 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Enable, message=Launch command succeeded: customscript.py -enable, duration=1012
```
Vissa punkter att Observera:
1. Aktivera är när kommandot börjar köras.
2. Hämta avser hämtning av CustomScript-tilläggspaket från Azure, inte skriptfiler som anges i fileUris.
3. Du kan också se vilka loggfilen den skriver till '/var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log'.

Nästa steg är att gå en kontroll av loggfilen, vilket format som:
```bash
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Du bör titta efter induvidual körningen, det ser ut ungefär så:
```text
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Enable,transitioning,0,Launching the script...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] sequence number is 0
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] setting file path is/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/config/0.settings
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] JSON config: {"runtimeSettings": [{"handlerSettings": {"protectedSettings": "MIIB0AYJKoZIhvcNAQcDoIIBwTCCAb0CAQAxggF+hnEXRtFKTTuKiFC8gTfHKupUSs7qI0zFYRya", "publicSettings": {"fileUris": ["https://dannytesting.blob.core.windows.net/demo/myBash.sh"]}, "protectedSettingsCertThumbprint": "4385AB21617C2452FF6998C0A37F71A0A01C8368"}}]}
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Config decoded correctly.
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Will try to download files, number of retries = 10, wait SECONDS between retrievals = 20s
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Downloading,transitioning,0,Downloading files...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] No azure storage account and key specified in protected settings. Downloading scripts from external links...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Converting /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/download/0/myBash.sh from DOS to Unix formats: Done
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Removing BOM of /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/download/0/myBash.sh: Done
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Succeeded to download files, retry count = 0
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Internal DNS is ready, retry count = 0
2018/04/26 15:29:47 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Command is finished.
2018/04/26 15:29:47 ---stdout---
2018/04/26 15:29:47 
2018/04/26 15:29:47 ---errout---
2018/04/26 15:29:47 
2018/04/26 15:29:47 
2018/04/26 15:29:47 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Daemon,success,0,Command is finished.
2018/04/26 15:29:47 ---stdout---
2018/04/26 15:29:47 
2018/04/26 15:29:47 ---errout---
2018/04/26 15:29:47 
2018/04/26 15:29:47 
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
Name                  ProvisioningState    Publisher                   Version  AutoUpgradeMinorVersion
--------------------  -------------------  ------------------------  ---------  -------------------------
CustomScriptForLinux  Succeeded            Microsoft.OSTCExtensions        1.5  True
```

## <a name="next-steps"></a>Nästa steg
Kod, aktuella problem och versioner finns [CustomScript-tillägg lagringsplatsen](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript).

