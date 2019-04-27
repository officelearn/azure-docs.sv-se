---
title: Köra anpassade skript på virtuella Linux-datorer i Azure | Microsoft Docs
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
ms.date: 08/14/2018
ms.author: danis
ms.openlocfilehash: fe3803b7dc75ab13831a5e42d4b1a96f5aa894e5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60800303"
---
# <a name="use-the-azure-custom-script-extension-version-1-with-linux-virtual-machines"></a>Använda Azure anpassade skript-tillägget Version 1 med Linux-datorer

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]

Anpassade skript-tillägget Version 1 laddar ned och kör skript på virtuella Azure-datorer. Det här tillägget är användbart för konfigurationen efter distribution, Programvaruinstallation eller andra konfigurationshantering/uppgifter. Du kan hämta skript från Azure Storage eller en annan tillgänglig Internetplats eller du kan ange dem till tillägget-körningen.

Det anpassade Skripttillägget integreras med Azure Resource Manager-mallar. Du kan också köra den med hjälp av Azure CLI, PowerShell, Azure-portalen eller Azure Virtual Machines REST API.

Den här artikeln beskriver hur du använder tillägget för anpassat skript från Azure CLI och hur du kör tillägget med hjälp av en Azure Resource Manager-mall. Den här artikeln innehåller också felsökningssteg för Linux-system.

Det finns två Linux anpassade skripttillägg:

* Version 1 - Microsoft.OSTCExtensions.CustomScriptForLinux

* Version 2 – Microsoft.Azure.Extensions.CustomScript

Växla nya och befintliga distributioner för att använda den nya versionen ([Microsoft.Azure.Extensions.CustomScript](custom-script-linux.md)) i stället. Den nya versionen är avsedd att vara en lättillgänglig ersättning. Därför migreringen är lika enkelt som att ändra namn och version, du behöver inte ändra din konfiguration.

### <a name="operating-system"></a>Operativsystem

Linux-distributioner som stöds:

* CentOS 6.5 och högre
* Debian 8 och senare
  * Debian 8.7 levereras inte med Python2 i de senaste avbildningarna, vilket bryter CustomScriptForLinux.
* FreeBSD
* OpenSUSE 13.1 och högre
* Oracle Linux 6.4 och högre
* SUSE Linux Enterprise Server 11 SP3 och högre
* Ubuntu 12.04 och högre

### <a name="script-location"></a>Skriptplats

Du kan använda tillägget för att använda Azure Blob storage-autentiseringsuppgifter för att få åtkomst till Azure Blob storage. Du kan också kan skriptets placering vara någon where, så länge som den virtuella datorn kan vidarebefordra till den slutpunkten, till exempel GitHub, interna filserver osv.

### <a name="internet-connectivity"></a>Internetanslutning

Om du behöver hämta ett skript externt, till exempel GitHub eller Azure Storage kan sedan ytterligare Brandväggsnätverk säkerhetsgrupp-portar måste du öppna. Till exempel om skriptet finns i Azure Storage, du kan tillåta åtkomst till med hjälp av Azure NSG-Tjänsttaggar för [Storage](../../virtual-network/security-overview.md#service-tags).

Om skriptet finns på en lokal server så kan du fortfarande behöver ytterligare brandvägg/Network Security Group portar måste du öppna.

### <a name="tips-and-tricks"></a>Tips

* Den högsta felfrekvensen för det här tillägget beror på syntaxfel i skriptet. Kontrollera att skriptet körs utan fel, och implementera ytterligare loggning i skriptet så att det blir enklare att identifiera var felet inträffade.
* Skriv skript som är idempotenta, så att inga systemändringar görs om de oavsiktligt körs mer än en gång.
* Se till att skript som inte kräver indata från användaren när de körs.
* Det är 90 minuter som tillåts för skriptet att köra, något längre resulterar i en misslyckad tillhandahållande av tillägget.
* Placera inte omstarter i skriptet och detta kan orsaka problem med andra tillägg installeras efter omstarten tillägget kommer inte att fortsätta efter omstarten. 
* Om du har ett skript som utlöser en omstart installerar program, kör skript osv. Du bör schemalägga omstarten med ett Cron-jobb eller med verktyg som DSC eller Chef, Puppet-tillägg.
* Tillägget körs bara ett skript en gång, om du vill köra ett skript på varje start kan du använda [cloud-init bild](../linux/using-cloud-init.md) och använda en [skript Per Start](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) modulen. Du kan också använda skriptet för att skapa en Systemd service-enhet.
* Om du vill schemalägga när ett skript som körs ska du använda tillägget för att skapa ett Cron-jobb.
* När skriptet körs visas tillägget med övergångsstatus på Azure-portalen eller i CLI. Om du vill mer frekventa statusuppdateringar ett skript som körs, behöver du skapa en egen lösning.
* Anpassat skripttillägg internt stöder inte proxy-servrar, men du kan använda ett filöverföringsverktyg som stöder proxyservrar i skriptet som *Curl*.
* Tänk på inte är standard directory-platser som dina skript eller kommandon kan förlita sig på, har logik för att hantera det.

## <a name="extension-schema"></a>Tilläggsschema

Tillägget för anpassat skript konfigurationen anger till exempel skriptplats och kommandot som ska köras. Du kan lagra den här konfigurationen i konfigurationsfiler, ange det på kommandoraden eller anger den i en Azure Resource Manager-mall. 

Du kan lagra känsliga data i en skyddad konfiguration, som krypteras och dekrypteras bara på den virtuella datorn. Konfigurationen av skyddade är användbart när körningskommandot innefattar hemligheter som lösenord.

De här objekten ska behandlas som känsliga data och anges i den skyddade Konfigurationsinställningen för tillägg. Azure VM-tillägget skyddade inställningsdata krypteras och dekrypteras bara på den virtuella måldatorn.

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
| apiVersion | 2015-06-15 | date |
| utgivare | Microsoft.OSTCExtensions | string |
| typ | CustomScriptForLinux | string |
| typeHandlerVersion | 1.5 | int |
| fileUris (t.ex.) | https://github.com/MyProject/Archive/MyPythonScript.py | matris |
| commandToExecute (t.ex.) | python MyPythonScript.py \<my-param1\> | string |
| enableInternalDNSCheck | true | boolesk |
| storageAccountName (t.ex.) | examplestorageacct | string |
| storageAccountKey (t.ex.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |

### <a name="property-value-details"></a>Information om egenskapen

* `fileUris`: (valfritt, Strängmatrisen) listan uri av skript
* `enableInternalDNSCheck`: (valfritt, bool) standard är sant, inställd på False för att inaktivera DNS-kontroll.
* `commandToExecute`: (valfritt, string) entrypoint skriptet att köra
* `storageAccountName`: (valfritt, string) namnet på storage-konto
* `storageAccountKey`: (valfritt, string) åtkomstnyckeln för lagringskontot

Följande värden kan anges i antingen offentliga eller skyddade inställningarna måste du ha inte dessa värden under set i inställningarna för både offentliga och skyddade.

* `commandToExecute`

Med offentliga inställningar som kan vara användbart för felsökning, men det rekommenderas starkt att du använder skyddade inställningarna.

Inställningar för offentliga skickas i klartext till den virtuella datorn där skriptet körs.  Skyddade inställningarna är krypterat med en nyckel som bara du känner till Azure och den virtuella datorn. Inställningarna sparas till den virtuella datorn som de skickades, dvs. Om inställningarna krypterades sparas de krypterade på den virtuella datorn. Certifikatet som används för att dekryptera krypterade värden lagras på den virtuella datorn, och används för att dekryptera inställningar (vid behov) vid körning.

## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. JSON-schemat som beskrivs i föregående avsnitt kan användas i en Azure Resource Manager-mall för att köra tillägget för anpassat skript under en malldistribution för Azure Resource Manager.

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
>Dessa egenskapsnamn är skiftlägeskänsliga. Undvik distributionsproblem genom att använda namnen som visas här.

## <a name="azure-cli"></a>Azure CLI

Skapa en konfigurationsfil eller filer när du använder Azure CLI för att köra tillägget för anpassat skript. Du måste ha ”commandToExecute” som ett minimum.

```azurecli
az vm extension set -n VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.5 \
  --vm-name MyVm --resource-group MyResourceGroup \
  --protected-settings '{"commandToExecute": "echo hello"}'
```

Alternativt kan du ange inställningarna i kommandot som en JSON-formaterad sträng. På så sätt kan konfigurationen anges vid körning och utan en separat konfigurationsfil.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Azure CLI-exempel

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
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
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
az vm extension set
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Felsökning

När tillägget för anpassat skript körs skriptet skapas eller hämtas i en katalog som liknar följande exempel. Utdata från kommandot sparas även i den här katalogen i `stdout` och `stderr` filer.

```bash
/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-<version>/download/1
```

Felsöka, först kontrollera loggen för Linux-agenten, kontrollera att tillägget har körts, kontrollera:

```bash
/var/log/waagent.log
```

Du bör se ut för tillägget körningen, det ser ut ungefär som:

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

Några saker att tänka på:

1. Aktivera är när kommandot börjar köras.
1. Download som relaterar till hämtning av paketets CustomScript-tillägget från Azure, inte skriptfiler anges i fileUris.
1. Du kan också se vilken loggfil som skriver ut till `/var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log`

Nästa steg är att gå en kontroll av loggfilen, vilket format:

```bash
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Du bör se ut för enskilda körningen, det ser ut ungefär som:

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
Name                  ProvisioningState    Publisher                   Version  AutoUpgradeMinorVersion
--------------------  -------------------  ------------------------  ---------  -------------------------
CustomScriptForLinux  Succeeded            Microsoft.OSTCExtensions        1.5  True
```

## <a name="next-steps"></a>Nästa steg

Kod, aktuella problem och versioner finns i [CustomScript-tillägget lagringsplatsen](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript).
