---
title: "Anpassade skript körs på virtuella Linux-datorer i Azure | Microsoft Docs"
description: "Automatisera åtgärder för Linux VM-konfigurationen genom att använda tillägget för anpassat skript"
services: virtual-machines-linux
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: nepeters
ms.openlocfilehash: 1dde64aac72c11ccfccf4fdb676279692befaadd
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="using-the-azure-custom-script-extension-with-linux-virtual-machines"></a>Med hjälp av tillägget för anpassat skript för Azure med Linux virtuella datorer
Tillägget för anpassat skript hämtar och kör skript på virtuella Azure-datorer. Det här tillägget är användbart för post distributionskonfiguration, Programvaruinstallation eller någon annan konfiguration / hanteringsaktivitet. Skript kan hämtas från Azure-lagring eller andra tillgängliga Internetplats eller som tillägget körtiden. Tillägget för anpassat skript kan integreras med Azure Resource Manager-mallar och kan också köras med hjälp av Azure CLI, PowerShell, Azure-portalen eller Azure Virtual Machine REST API.

Det här dokumentet beskrivs hur du använder tillägget för anpassat skript från Azure CLI och en Azure Resource Manager-mall och även detaljer felsökningssteg för Linux-datorer.

## <a name="extension-configuration"></a>Tilläggets konfiguration
Tillägget för anpassat skript konfigurationen anger sådant som skriptets placering och kommandot ska köras. Den här konfigurationen kan lagras i konfigurationsfiler, anges på kommandoraden eller i en Azure Resource Manager-mall. Känsliga data kan lagras i en skyddad konfiguration, som krypteras och dekrypteras endast inuti den virtuella datorn. Skyddade konfigurationen är användbar när kommandot körningen innehåller hemligheter, till exempel ett lösenord.

### <a name="public-configuration"></a>Offentliga konfiguration
Schema:

**Obs** -dessa egenskapsnamn är skiftlägeskänsliga. Använda namnen som visas nedan för att undvika distributionsproblem.

* **commandToExecute**: (krävs, string) post punkt att köra skript
* **fileUris**: (valfritt, Strängmatrisen) URL: er för filer som ska hämtas.
* **tidsstämpel** (valfritt, heltal) använda det här fältet bara för att utlösa en kör av skriptet genom att ändra värdet för det här fältet.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>"
}
```

### <a name="protected-configuration"></a>Skyddade konfiguration
Schema:

**Obs** -dessa egenskapsnamn är skiftlägeskänsliga. Använda namnen som visas nedan för att undvika distributionsproblem.

* **commandToExecute**: (valfritt, string) att posten punkt skriptet körs. Använd det här fältet i stället om kommandot innehåller hemligheter, till exempel lösenord.
* **storageAccountName**: (valfritt, string) namnet på lagringskontot. Om du anger autentiseringsuppgifter för lagring, måste alla fileUris vara URL: er för Azure-BLOB.
* **storageAccountKey**: (valfritt, string) åtkomstnyckeln för lagringskontot.

```json
{
  "commandToExecute": "<command-to-execute>",
  "storageAccountName": "<storage-account-name>",
  "storageAccountKey": "<storage-account-key>"
}
```

## <a name="azure-cli"></a>Azure CLI
När du använder Azure CLI för att köra tillägget för anpassat skript, skapa en konfigurationsfil eller filer som innehåller åtminstone uri för filen och köra skriptkommandot.

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

Inställningarna kan du kan också anges i kommandot som en JSON-formaterad sträng. På så sätt kan konfigurationen anges under körning och utan en separat fil.

```azurecli
az vm extension set '
  --resource-group exttest `
  --vm-name exttest `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/neilpeterson/test-extension/master/test.sh"],"commandToExecute": "./test.sh"}'
```

### <a name="azure-cli-examples"></a>Azure CLI-exempel

**Exempel 1** -offentliga konfiguration med skriptfilen.

```json
{
  "fileUris": ["https://raw.githubusercontent.com/neilpeterson/test-extension/master/test.sh"],
  "commandToExecute": "./test.sh"
}
```

Azure CLI-kommando:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

**Exempel 2** -offentliga konfiguration med ingen skriptfilen.

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Azure CLI-kommando:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

**Exempel 3** – en offentlig konfigurationsfil används för att ange skriptfil URI och en skyddad fil används för att ange kommandot som ska köras.

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
Tillägget för Azure anpassat skript kan köras vid tidpunkten för distribution för virtuell dator med en Resource Manager-mall. Gör du genom att lägga till korrekt formaterad JSON i mallen för distribution.

### <a name="resource-manager-examples"></a>Resource Manager-exempel
**Exempel 1** -offentliga konfiguration.

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

**Exempel 2** -körning av kommandot i skyddade konfiguration.

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

Se .net Core musik Store demonstration för en komplett exempel - [musik Store Demo](https://github.com/neilpeterson/nepeters-azure-templates/tree/master/dotnet-core-music-linux-vm-sql-db).

## <a name="troubleshooting"></a>Felsökning
När tillägget för anpassat skript körs skriptet skapas eller hämtas i en katalog som liknar följande exempel. Kommandoutdata sparas i den här katalogen i `stdout` och `stderr` fil.

```bash
/var/lib/waagent/custom-script/download/0/
```

Tillägget för Azure-skript skapar en logg som finns här.

```bash
/var/log/azure/custom-script/handler.log
```

Körningstillståndet för tillägget för anpassat skript kan också hämtas med Azure CLI.

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
Information om andra skript VM-tillägg finns [översikt över Azure skript tillägget för Linux](extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

