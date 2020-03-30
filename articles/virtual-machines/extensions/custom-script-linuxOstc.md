---
title: Köra anpassade skript på virtuella Linux-datorer i Azure
description: Automatisera konfigurationsuppgifter för Linux VM med hjälp av anpassade skripttillägg v1
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: danis
ms.openlocfilehash: a3eae08510e57227b91deeeb7a7a608a6652cb4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535416"
---
# <a name="use-the-azure-custom-script-extension-version-1-with-linux-virtual-machines"></a>Använda Azure Custom Script Extension Version 1 med virtuella Linux-datorer

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]

Custom Script Extension Version 1 hämtar och kör skript på virtuella Azure-datorer. Det här tillägget är användbart för konfiguration efter distribution, programvaruinstallation eller annan konfigurations-/hanteringsuppgift. Du kan hämta skript från Azure Storage eller en annan tillgänglig internetplats, eller så kan du ange dem till tilläggets körning.

Det anpassade skripttillägget integreras med Azure Resource Manager-mallar. Du kan också köra den med hjälp av Azure CLI, PowerShell, Azure-portalen eller AZURE Virtual Machines REST API.

I den här artikeln beskrivs hur du använder det anpassade skripttillägget från Azure CLI och hur du kör tillägget med hjälp av en Azure Resource Manager-mall. Den här artikeln innehåller också felsökningssteg för Linux-system.

Det finns två Linux Custom Script Extensions:

* Version 1 - Microsoft.OSTCExtensions.CustomScriptForLinux

* Version 2 - Microsoft.Azure.Extensions.CustomScript

Växla nya och befintliga distributioner för att använda den nya versionen ([Microsoft.Azure.Extensions.CustomScript](custom-script-linux.md)) i stället. Den nya versionen är avsedd att vara en lättillgänglig ersättning. Därför är migreringen lika enkel som att ändra namn och version. Du behöver inte ändra tilläggskonfigurationen.

### <a name="operating-system"></a>Operativsystem

Linuxdistributioner som stöds:

* CentOS 6,5 och högre
* Debian 8 och högre
  * Debian 8.7 levereras inte med Python2 i de senaste bilderna, som bryter CustomScriptForLinux.
* FreeBSD
* OpenSUSE 13.1 och högre
* Oracle Linux 6.4 och högre
* SUSE Linux Enterprise Server 11 SP3 och senare
* Ubuntu 12,04 och högre

### <a name="script-location"></a>Skriptplats

Du kan använda tillägget för att använda dina Azure Blob-lagringsautentiseringsuppgifter för att komma åt Azure Blob-lagring. Alternativt kan skriptplatsen vara vilken plats som helst, så länge den virtuella datorn kan dirigera till den slutpunkten, till exempel GitHub, intern filserver etc.

### <a name="internet-connectivity"></a>Internetanslutning

Om du behöver hämta ett skript externt, till exempel GitHub eller Azure Storage, måste ytterligare brandväggs-/nätverkssäkerhetsgruppportar öppnas. Om skriptet till exempel finns i Azure Storage kan du tillåta åtkomst med Azure NSG-tjänsttaggar för [lagring](../../virtual-network/security-overview.md#service-tags).

Om skriptet finns på en lokal server kan du fortfarande behöva ytterligare brandväggs-/nätverkssäkerhetsgruppsportar måste öppnas.

### <a name="tips-and-tricks"></a>Tips

* Den högsta felfrekvensen för det här tillägget beror på syntaxfel i skriptet. Kontrollera att skriptet körs utan fel, och implementera ytterligare loggning i skriptet så att det blir enklare att identifiera var felet inträffade.
* Skriv skript som är idempotenta, så att inga systemändringar görs om de oavsiktligt körs mer än en gång.
* Se till att skripten inte kräver indata från användaren när de körs.
* Det är 90 minuter tillåtet för skriptet att köras, något längre kommer att resultera i en misslyckad bestämmelse i tillägget.
* Lägg inte omstarter inuti skriptet, kommer detta att orsaka problem med andra tillägg som installeras, och efter omstart, kommer tillägget inte att fortsätta efter omstarten. 
* Om du har ett skript som kommer att orsaka en omstart, sedan installera program och köra skript etc. Du bör schemalägga omstarten med ett Cron-jobb eller använda verktyg som DSC eller Chef, Puppet-tillägg.
* Tillägget kommer bara att köra ett skript en gång, om du vill köra ett skript på varje start, kan du använda [cloud-init avbildning](../linux/using-cloud-init.md) och använda en [Skript per start](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) modul. Du kan också använda skriptet för att skapa en systemd-serviceenhet.
* Om du vill schemalägga när ett skript ska köras bör du använda tillägget för att skapa ett Cron-jobb.
* När skriptet körs visas tillägget med övergångsstatus på Azure-portalen eller i CLI. Om du vill ha fler frekventa statusuppdateringar för ett skript som körs måste du skapa en egen lösning.
* Custom Script-tillägget stöder inte proxyservrar, men du kan använda ett filöverföringsverktyg som stöder proxyservrar i skriptet, till exempel *Curl*.
* Tänk på icke-standardkatalogplatser som skripten eller kommandona kan förlita sig på har logik för att hantera detta.

## <a name="extension-schema"></a>Tilläggsschema

Konfigurationen för anpassat skripttillägg anger saker som skriptplats och kommandot som ska köras. Du kan lagra den här konfigurationen i konfigurationsfiler, ange den på kommandoraden eller ange den i en Azure Resource Manager-mall. 

Du kan lagra känsliga data i en skyddad konfiguration, som krypteras och endast dekrypteras inuti den virtuella datorn. Den skyddade konfigurationen är användbar när körningskommandot innehåller hemligheter som ett lösenord.

Dessa objekt ska behandlas som känsliga data och anges i den skyddade inställningskonfigurationen för tillägg. Azure VM-tilläggsskyddade inställningsdata krypteras och dekrypteras endast på den virtuella måldatorn.

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

| Namn | Värde / Exempel | Datatyp |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| utgivare | Microsoft.OSTCExtensions Microsoft.OSTCExtensions Microsoft.OSTCExtensions Microsoft. | sträng |
| typ | CustomScriptForLinux | sträng |
| typHandlerVersion | 1.5 | int |
| fileUris (t.ex. ) | https://github.com/MyProject/Archive/MyPythonScript.py | matris |
| commandToExecute (t.ex.) | python MyPythonScript.py \<min-param1\> | sträng |
| enableInternalDNSCheck | true | boolean |
| storageAccountName (t.ex.) | exempelstoracket | sträng |
| lagringAccountKey (t.ex.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | sträng |

### <a name="property-value-details"></a>Information om egenskapsvärde

* `fileUris`: (valfritt, strängmatris) uri-listan för skripten
* `enableInternalDNSCheck`: (valfritt, bool) standard är Sant, inställd på Falskt för att inaktivera DNS-kontroll.
* `commandToExecute`: (valfritt, sträng) inmatningspunktsskriptet som ska köras
* `storageAccountName`: (valfritt, sträng) namnet på lagringskontot
* `storageAccountKey`: (valfritt, sträng) åtkomstnyckeln för lagringskontot

Följande värden kan anges i offentliga eller skyddade inställningar, du får inte ha dessa värden nedan inställda i både offentliga och skyddade inställningar.

* `commandToExecute`

Använda offentliga inställningar kanske användbart för felsökning, men det rekommenderas starkt att du använder skyddade inställningar.

Offentliga inställningar skickas i klartext till den virtuella datorn där skriptet ska köras.  Skyddade inställningar krypteras med en nyckel som bara är känd för Azure och den virtuella datorn. Inställningarna sparas på den virtuella datorn när de skickades, dvs om inställningarna krypterades de sparas krypterade på den virtuella datorn. Certifikatet som används för att dekryptera de krypterade värdena lagras på den virtuella datorn och används för att dekryptera inställningar (om det behövs) vid körning.

## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. JSON-schemat som beskrivs i föregående avsnitt kan användas i en Azure Resource Manager-mall för att köra det anpassade skripttillägget under en Azure Resource Manager-malldistribution.

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
>Dessa egenskapsnamn är skiftlägeskänsliga. Använd namnen som visas här för att undvika distributionsproblem.

## <a name="azure-cli"></a>Azure CLI

När du använder Azure CLI för att köra tillägget Anpassat skript skapar du en konfigurationsfil eller filer. Du måste ha åtminstone "commandToExecute".

```azurecli
az vm extension set -n VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.5 \
  --vm-name MyVm --resource-group MyResourceGroup \
  --protected-settings '{"commandToExecute": "echo hello"}'
```

Du kan också ange inställningarna i kommandot som en JSON-formaterad sträng. Detta gör att konfigurationen kan anges under körningen och utan en separat konfigurationsfil.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Azure CLI-exempel

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
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
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
az vm extension set
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Felsökning

När tillägget Anpassat skript körs skapas eller hämtas skriptet till en katalog som liknar följande exempel. Kommandoutdata sparas också i `stdout` `stderr` den här katalogen och filer.

```bash
/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-<version>/download/1
```

Om du vill felsöka kontrollerar du först Linux Agent Log, kontrollerar att tillägget kördes, kontrollerar:

```bash
/var/log/waagent.log
```

Du bör leta efter förlängningen utförande, kommer det att se ut ungefär som:

```output
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

Några punkter att notera:

1. Aktivera är när kommandot börjar köras.
1. Hämtningen avser nedladdning av CustomScript-tilläggspaketet från Azure, inte skriptfilerna som anges i fileUris.
1. Du kan också se vilken loggfil den skriver ut till`/var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log`

Nästa steg är att gå en kontroll loggfilen, detta är formatet:

```bash
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Du bör leta efter den enskilda utförandet, kommer det att se ut ungefär som:

```output
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

* Kommandot Aktivera startar är den här loggen
* Inställningarna som skickas till tillägget
* Tillägget nedladdningsfilen och resultatet av det.
* Kommandot körs och resultatet.

Du kan också hämta körningstillståndet för det anpassade skripttillägget med hjälp av Azure CLI:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

Utdata ser ut som följande text:

```output
Name                  ProvisioningState    Publisher                   Version  AutoUpgradeMinorVersion
--------------------  -------------------  ------------------------  ---------  -------------------------
CustomScriptForLinux  Succeeded            Microsoft.OSTCExtensions        1.5  True
```

## <a name="next-steps"></a>Nästa steg

Mer om du vill visa koden, aktuella problem och versioner finns i [CustomScript Extension repo](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript).
