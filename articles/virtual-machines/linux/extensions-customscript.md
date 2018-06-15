---
title: Anpassade skript körs på virtuella Linux-datorer i Azure | Microsoft Docs
description: Automatisera åtgärder för Linux VM-konfigurationen genom att använda tillägget för anpassat skript
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
ms.date: 04/26/2017
ms.author: danis
ms.openlocfilehash: 88f337df14b7e2647a76cce7ef91ec6f8950bbc6
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
ms.locfileid: "30906835"
---
# <a name="use-the-azure-custom-script-extension-with-linux-virtual-machines"></a>Använda tillägget för anpassat skript på Azure med Linux virtuella datorer
Tillägget för anpassat skript hämtar och kör skript på virtuella Azure-datorer. Det här tillägget är användbart för efter distributionen konfiguration, installation av programvara eller andra uppgifter konfiguration och hantering. Du kan hämta skript från Azure Storage eller en annan tillgänglig Internetplats eller kan du ge dem till tillägget körningsmiljön. 

Tillägget för anpassat skript integreras med Azure Resource Manager-mallar. Du kan också köra det med hjälp av Azure CLI, PowerShell, Azure portal eller REST API för Azure-virtuella datorer.

Den här artikeln beskrivs hur du använder tillägget för anpassat skript från Azure CLI och hur du kör tillägget med hjälp av en Azure Resource Manager-mall. Den här artikeln innehåller även felsökningssteg för Linux-system.

## <a name="extension-configuration"></a>Tilläggets konfiguration
Tillägget för anpassat skript konfigurationen anger sådant som skriptets placering och kommandot ska köras. Du kan lagra den här konfigurationen i konfigurationsfiler, ange på kommandoraden eller ange i en Azure Resource Manager-mall. 

Du kan lagra känsliga data i en skyddad konfiguration, som krypteras och dekrypteras endast inuti den virtuella datorn. Skyddade konfigurationen är användbar när kommandot körningen innehåller hemligheter, till exempel ett lösenord.

### <a name="public-configuration"></a>Offentliga konfiguration
Schemat för den offentliga konfigurationen är som följer.

>[!NOTE]
>Dessa egenskapsnamn är skiftlägeskänsliga. Du undviker problem med distribution genom att använda namnen som visas här.

* **commandToExecute** (krävs, string): post punkt skriptet ska köras.
* **fileUris** (valfritt, Strängmatrisen): på URL: er för filer som ska hämtas.
* **tidsstämpel** (valfritt, heltal): tidsstämpeln för skriptet. Ändra värdet för det här fältet bara om du vill ska utlösa en kör av skriptet.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>"
}
```

### <a name="protected-configuration"></a>Skyddade konfiguration
Schemat för den skydda konfigurationen är som följer.

>[!NOTE]
>Dessa egenskapsnamn är skiftlägeskänsliga. Du undviker problem med distribution genom att använda namnen som visas här.

* **commandToExecute** (valfritt, string): post punkt skriptet ska köras. Använd det här fältet om ditt kommando innehåller hemligheter, till exempel lösenord.
* **storageAccountName** (valfritt, string): namnet på lagringskontot. Om du anger autentiseringsuppgifter för lagring, måste alla filen URI: er vara URL: er för Azure BLOB.
* **storageAccountKey** (valfritt, string): åtkomstnyckeln för lagringskontot.

```json
{
  "commandToExecute": "<command-to-execute>",
  "storageAccountName": "<storage-account-name>",
  "storageAccountKey": "<storage-account-key>"
}
```

## <a name="azure-cli"></a>Azure CLI
Skapa en konfigurationsfil eller filer när du använder Azure CLI för att köra tillägget för anpassat skript. Åtminstone innehålla konfigurationsfiler filen URI och köra skriptkommandot.

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

Alternativt kan ange du inställningarna i kommandot som en JSON-formaterad sträng. På så sätt kan konfigurationen anges under körning och utan en separat fil.

```azurecli
az vm extension set '
  --resource-group exttest `
  --vm-name exttest `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
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
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

#### <a name="public-configuration-with-no-script-file"></a>Offentliga konfiguration med ingen skriptfilen

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Azure CLI-kommando:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Offentliga och skyddade konfigurationsfiler

Du kan använda en offentlig konfigurationsfil för att ange skriptfil URI. Du kan använda en skyddad fil för att ange kommandot som ska köras.

Offentliga konfigurationsfil:

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"]
}
```

Skyddade konfigurationsfil:  

```json
{
  "commandToExecute": "./hello.sh <password>"
}
```

Azure CLI-kommando:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json --protected-settings ./protected-config.json
```

## <a name="resource-manager-template"></a>Resource Manager-mall
Du kan köra tillägget för anpassat skript på Azure vid tidpunkten för distribution av virtuell dator med hjälp av en Resource Manager-mall. Gör du genom att lägga till korrekt formaterad JSON i mallen för distribution.

### <a name="resource-manager-examples"></a>Resource Manager-exempel

#### <a name="public-configuration"></a>Offentliga konfiguration

```json
{
    "name": "scriptextensiondemo",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "2015-06-15",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('scriptextensiondemoName'))]"
    ],
    "tags": {
        "displayName": "scriptextensiondemo"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
      "settings": {
        "fileUris": [
          "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
        ],
        "commandToExecute": "sh hello.sh"
      }
    }
}
```

#### <a name="execution-command-in-protected-configuration"></a>Körning av kommandot i skyddade konfiguration

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
      "fileUris": [
        "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
      ]              
    },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <password>"
    }
  }
}
```

En komplett exempel finns i [.NET musik Store demo](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

## <a name="troubleshooting"></a>Felsökning
När tillägget för anpassat skript körs skriptet skapas eller hämtas i en katalog som liknar följande exempel. Kommandoutdata sparas i den här katalogen i `stdout` och `stderr` filer.

```bash
/var/lib/waagent/custom-script/download/0/
```

Tillägget för Azure-skript resulterar i en logg som finns här:

```bash
/var/log/azure/custom-script/handler.log
```

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
Information om andra skript VM-tillägg finns [översikt över Azure-skript tillägget för Linux](extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

