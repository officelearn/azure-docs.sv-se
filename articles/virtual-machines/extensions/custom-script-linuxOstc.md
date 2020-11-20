---
title: Kör anpassat skript tillägg version 1 (inaktuell) på virtuella Linux-datorer i Azure
description: Automatisera konfigurations uppgifter för virtuella Linux-datorer med hjälp av tillägget för anpassat skript v1
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: danis
ms.openlocfilehash: afce76c8f5330f7d73ff58cc9b9307af2b8832f4
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94962135"
---
# <a name="use-the-azure-custom-script-extension-version-1-with-linux-virtual-machines"></a>Använd Azures anpassade skript tillägg version 1 med virtuella Linux-datorer

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]

Det anpassade skript tillägget version 1 laddar ned och kör skript på virtuella Azure-datorer. Det här tillägget är användbart för konfiguration efter distribution, program varu installation eller andra konfigurations-och hanterings åtgärder. Du kan hämta skript från Azure Storage eller en annan tillgänglig Internet plats, eller så kan du ange dem till tilläggets körnings miljö.

Det anpassade skript tillägget integreras med Azure Resource Manager mallar. Du kan också köra det med hjälp av Azure CLI, PowerShell, Azure Portal eller Azure Virtual Machines-REST API.

Den här artikeln beskriver hur du använder tillägget för anpassat skript från Azure CLI och hur du kör tillägget med hjälp av en Azure Resource Manager mall. Den här artikeln innehåller också fel söknings steg för Linux-system.

Det finns två anpassade skript tillägg för Linux:

* Version 1-Microsoft. OSTCExtensions. CustomScriptForLinux

* Version 2-Microsoft. Azure. Extensions. CustomScript

Växla mellan nya och befintliga distributioner för att använda den nya versionen ([Microsoft. Azure. Extensions. CustomScript](custom-script-linux.md)) i stället. Den nya versionen är avsedd att vara en lättillgänglig ersättning. Därför är migreringen lika enkel som att ändra namn och version. Du behöver inte ändra tilläggskonfigurationen.

### <a name="operating-system"></a>Operativsystem

Linux-distributioner som stöds:

* CentOS 6,5 och högre
* Debian 8 och senare
  * Debian 8,7 levereras inte med Python2 i de senaste bilderna, vilket bryter CustomScriptForLinux.
* FreeBSD
* OpenSUSE 13,1 och högre
* Oracle Linux 6,4 och högre
* SUSE Linux Enterprise Server 11 SP3 och senare
* Ubuntu 12,04 och högre

### <a name="script-location"></a>Skript plats

Du kan använda tillägget för att använda dina Azure Blob Storage-autentiseringsuppgifter för att få åtkomst till Azure Blob Storage. Alternativt kan skript platsen var som helst, så länge den virtuella datorn kan dirigeras till den slut punkten, till exempel GitHub, intern fil Server osv.

### <a name="internet-connectivity"></a>Internet anslutning

Om du behöver hämta ett skript externt, till exempel GitHub eller Azure Storage, måste du öppna ytterligare brand Väggs-och nätverks säkerhets grupps portar. Om ditt skript till exempel finns i Azure Storage kan du tillåta åtkomst med hjälp av Azure NSG service-taggar för [lagring](../../virtual-network/network-security-groups-overview.md#service-tags).

Om ditt skript finns på en lokal server kanske du fortfarande behöver fler brand Väggs-och nätverks säkerhets grupps portar som måste öppnas.

### <a name="tips-and-tricks"></a>Tips

* Den högsta felfrekvensen för det här tillägget beror på syntaxfel i skriptet. Kontrollera att skriptet körs utan fel, och implementera ytterligare loggning i skriptet så att det blir enklare att identifiera var felet inträffade.
* Skriv skript som är idempotenta, så att inga systemändringar görs om de oavsiktligt körs mer än en gång.
* Kontrol lera att skripten inte kräver användarindata när de körs.
* Det finns 90 minuter som tillåts för att skriptet ska kunna köras. allt längre leder till en misslyckad etablering av tillägget.
* Starta inte om omstarter inuti skriptet. Detta leder till problem med andra tillägg som installeras och efter omstarten kommer tillägget inte att fortsätta efter omstarten. 
* Om du har ett skript som gör en omstart installerar du program och kör skript osv. Du bör schemalägga en omstart med hjälp av ett cron-jobb eller använda verktyg som DSC eller chef, Puppet-tillägg.
* Tillägget kör bara ett skript en gång, om du vill köra ett skript vid varje start, kan du använda [Cloud-Init-avbildning](../linux/using-cloud-init.md)  och använda [skript per](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) startmodul. Du kan också använda skriptet för att skapa en system tjänst enhet.
* Om du vill schemalägga när ett skript ska köras ska du använda tillägget för att skapa ett cron-jobb.
* När skriptet körs visas tillägget med övergångsstatus på Azure-portalen eller i CLI. Om du vill ha mer frekventa status uppdateringar för ett skript som körs måste du skapa en egen lösning.
* Anpassat skript tillägg stöder inte proxyservrar, men du kan använda ett fil överförings verktyg som stöder proxyservrar i skriptet, till exempel *vändning*.
* Var medveten om icke-standardkataloger som skripten eller kommandona kan vara beroende av, har logik för att hantera detta.

## <a name="extension-schema"></a>Tilläggsschema

Konfigurationen för det anpassade skript tillägget anger saker som skript plats och kommandot som ska köras. Du kan lagra konfigurationen i konfigurationsfiler, ange den på kommando raden eller ange den i en Azure Resource Manager mall. 

Du kan lagra känsliga data i en skyddad konfiguration, som krypteras och bara dekrypteras i den virtuella datorn. Den skyddade konfigurationen är användbar när körnings kommandot innehåller hemligheter som lösen ord.

De här objekten ska behandlas som känsliga data och anges i konfigurationerna för tilläggen för skyddad inställning. Skyddade inställnings data för Azure VM-tillägg krypteras och endast dekrypteras på den virtuella mål datorn.

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

### <a name="property-values"></a>Egenskaps värden

| Namn | Värde/exempel | Datatyp |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | datum |
| utgivare | Microsoft. OSTCExtensions | sträng |
| typ | CustomScriptForLinux | sträng |
| typeHandlerVersion | 1.5 | int |
| fileUris (t. ex.) | `https://github.com/MyProject/Archive/MyPythonScript.py` | matris |
| commandToExecute (t. ex.) | python-MyPythonScript.py \<my-param1\> | sträng |
| enableInternalDNSCheck | true | boolean |
| storageAccountName (t. ex.) | examplestorageacct | sträng |
| storageAccountKey (t. ex.) | TmJK/1N3AbAZ3q/+ hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg = = | sträng |

### <a name="property-value-details"></a>Information om egenskaps värde

* `fileUris`: (valfritt, sträng mat ris) URI-listan för skripten
* `enableInternalDNSCheck`: (valfritt, bool) standard är true, ange till false för att inaktivera DNS-kontroll.
* `commandToExecute`: (valfritt, sträng) EntryPoint-skriptet som ska köras
* `storageAccountName`: (valfritt, sträng) namnet på lagrings kontot
* `storageAccountKey`: (valfritt, sträng) åtkomst nyckeln för lagrings kontot

Du kan ange följande värden i offentliga eller skyddade inställningar: du behöver inte ange följande värden i både offentliga och skyddade inställningar.

* `commandToExecute`

Användning av offentliga inställningar kan vara användbart för fel sökning, men vi rekommenderar starkt att du använder skyddade inställningar.

Offentliga inställningar skickas i klartext till den virtuella dator där skriptet ska köras.  Skyddade inställningar krypteras med en nyckel som endast är känd för Azure och den virtuella datorn. Inställningarna sparas på den virtuella datorn när de skickades, dvs. om inställningarna krypterades sparas de som krypterade på den virtuella datorn. Certifikatet som används för att dekryptera de krypterade värdena lagras på den virtuella datorn och används för att dekryptera inställningar (vid behov) vid körning.

## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager mallar. Det JSON-schema som beskrivs i föregående avsnitt kan användas i en Azure Resource Manager mall för att köra det anpassade skript tillägget under en Azure Resource Manager mall-distribution.

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
>Dessa egenskaps namn är Skift läges känsliga. Använd de namn som visas här för att undvika distributions problem.

## <a name="azure-cli"></a>Azure CLI

När du använder Azure CLI för att köra det anpassade skript tillägget skapar du en konfigurations fil eller filer. Du måste minst ha "commandToExecute".

```azurecli
az vm extension set -n VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.5 \
  --vm-name MyVm --resource-group MyResourceGroup \
  --protected-settings '{"commandToExecute": "echo hello"}'
```

Alternativt kan du ange inställningarna i kommandot som en JSON-formaterad sträng. Detta gör att konfigurationen kan anges under körningen och utan en separat konfigurations fil.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Azure CLI-exempel

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
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
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
az vm extension set
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Felsökning

När det anpassade skript tillägget körs, skapas eller laddas skriptet ned till en katalog som liknar följande exempel. Kommandots utdata sparas också i den här katalogen i `stdout` och `stderr` filer.

```bash
/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-<version>/download/1
```

Du kan felsöka genom att först kontrol lera loggen för Linux-agenten, se till att tillägget har körts, se:

```bash
/var/log/waagent.log
```

Du bör söka efter tilläggs körningen, det ser ut ungefär så här:

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

Några saker att tänka på:

1. Aktivera är när kommandot börjar köras.
1. Hämtningen är relaterad till nedladdningen av CustomScript-tilläggs paketet från Azure, inte skriptfilerna som anges i fileUris.
1. Du kan också se vilken loggfil som den skrivs till `/var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log`

Nästa steg är att gå igenom logg filen, detta är formatet:

```bash
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Du bör söka efter den enskilda körningen. den ser ut ungefär så här:

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
* Inställningarna som överförs till tillägget
* Tillägget laddar ned filen och resultatet av det.
* Kommandot som körs och resultatet.

Du kan också hämta körnings statusen för det anpassade skript tillägget med hjälp av Azure CLI:

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

Om du vill se kod, aktuella problem och versioner, se [CustomScript Extension lagrings platsen](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript).