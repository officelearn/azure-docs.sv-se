---
title: Exportera Azure-resurs grupper som innehåller VM-tillägg | Microsoft Docs
description: Exportera Resource Manager-mallar som innehåller tillägg för virtuella datorer.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 7f4e2ca6-f1c7-4f59-a2cc-8f63132de279
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: roiyz
ms.openlocfilehash: 6ac3a19d12b99c61dd0607b07b4659114f52400e
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70084622"
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>Exportera resurs grupper som innehåller VM-tillägg

Azures resurs grupper kan exporteras till en ny Resource Manager-mall som sedan kan distribueras om. Export processen tolkar befintliga resurser och skapar en Resource Manager-mall som när de distribuerade resultaten i en liknande resurs grupp. När du använder export alternativet resurs grupp mot en resurs grupp som innehåller tillägg för virtuella datorer, måste flera objekt beaktas, till exempel tilläggets kompatibilitet och skyddade inställningar.

Det här dokumentet beskriver hur export processen för resurs grupper fungerar för tillägg till virtuella datorer, inklusive en lista över tillägg som stöds och information om hur du hanterar skyddade data.

## <a name="supported-virtual-machine-extensions"></a>Tillägg för virtuella datorer som stöds

Många tillägg för virtuella datorer är tillgängliga. Det går inte att exportera alla tillägg till en Resource Manager-mall med hjälp av funktionen "Automation-skript". Om ett tillägg för virtuell dator inte stöds måste det placeras manuellt i den exporterade mallen.

Följande tillägg kan exporteras med Automation-skript funktionen.

| Anknytning ||||
|---|---|---|---|
| Acronis-säkerhetskopiering | Datadog Windows-agent | OS-uppdatering för Linux | Linux för VM-ögonblicksbild
| Acronis Backup Linux | Docker-tillägg | Puppet-agent |
| BG-information | DSC-tillägg | Insikter om webbplats dygnet runt |
| BMC CTM-agent Linux | DynaTrace Linux | Plats 24 Linux-Server |
| BMC CTM agent Windows | DynaTrace Windows | Plats dygnet runt Windows Server |
| Chefs klient | HPE Security Application Defender | Trend Micro DSA |
| Anpassat skript | IaaS mot skadlig kod | Trend Micro DSA Linux |
| Anpassat skripttillägg | IaaS-diagnostik | VM-åtkomst för Linux |
| Anpassat skript för Linux | Linux chef-klient | VM-åtkomst för Linux |
| Datadog Linux-Agent | Linux-diagnostik | VM-ögonblicksbild |

## <a name="export-the-resource-group"></a>Exportera resurs gruppen

Utför följande steg för att exportera en resurs grupp till en återanvändbar mall:

1. Logga in på Azure Portal
2. Klicka på resurs grupper på menyn hubb
3. Välj mål resurs grupp i listan
4. I bladet resurs grupp klickar du på Automation-skript

![Exportera mall](./media/export-templates/template-export.png)

Skriptet Azure Resource Manager automations skapar en Resource Manager-mall, en parameter fil och flera exempel på distributions skript som PowerShell och Azure CLI. Den exporterade mallen kan nu hämtas med hjälp av knappen Ladda ned, som läggs till som en ny mall i biblioteket eller omdistribueras med hjälp av knappen distribuera.

## <a name="configure-protected-settings"></a>Konfigurera skyddade inställningar

Många Azure Virtual Machine-tillägg innehåller en konfiguration för skyddade inställningar som krypterar känsliga data, till exempel autentiseringsuppgifter och konfigurations strängar. Skyddade inställningar exporteras inte med Automation-skriptet. Om det behövs måste de skyddade inställningarna läggas till i den exporterade mallen igen.

### <a name="step-1---remove-template-parameter"></a>Steg 1 – ta bort mallparameter

När resurs gruppen har exporter ATS skapas en enskild mallparameter för att tillhandahålla ett värde för de exporterade skyddade inställningarna. Den här parametern kan tas bort. Ta bort parametern genom att titta igenom parameter listan och ta bort den parameter som liknar det här JSON-exemplet.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>Steg 2 – Hämta egenskaper för skyddat alternativ

Eftersom varje skyddad inställning har en uppsättning obligatoriska egenskaper måste en lista över dessa egenskaper samlas in. Varje parameter i konfigurationen för skyddade inställningar finns i [Azure Resource Manager schema på GitHub](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json). Det här schemat innehåller bara parameter uppsättningar för de tillägg som anges i översikts avsnittet i det här dokumentet. 

I lagrings platsen för schemat söker du efter det önskade tillägget, i det `IaaSDiagnostics`här exemplet. När objektet Extensions `protectedSettings` har hittats noterar du varje parameter. I exemplet på `IaasDiagnostic` tillägget är `storageAccountName`Kräv parametrar, `storageAccountKey`och `storageAccountEndPoint`.

```json
"protectedSettings": {
    "type": "object",
    "properties": {
        "storageAccountName": {
            "type": "string"
        },
        "storageAccountKey": {
            "type": "string"
        },
        "storageAccountEndPoint": {
            "type": "string"
        }
    },
    "required": [
        "storageAccountName",
        "storageAccountKey",
        "storageAccountEndPoint"
    ]
}
```

### <a name="step-3---re-create-the-protected-configuration"></a>Steg 3 – återskapa den skyddade konfigurationen

I den exporterade mallen söker du `protectedSettings` efter och ersätter objektet exporterat skyddat objekt med en ny som innehåller nödvändiga tilläggs parametrar och ett värde för var och en.

I exemplet på `IaasDiagnostic` tillägget skulle den nya skyddade inställnings konfigurationen se ut som i följande exempel:

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

Den sista tilläggs resursen ser ut ungefär som följande JSON-exempel:

```json
{
    "name": "Microsoft.Insights.VMDiagnosticsSettings",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "[variables('apiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "tags": {
        "displayName": "AzureDiagnostics"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Diagnostics",
        "type": "IaaSDiagnostics",
        "typeHandlerVersion": "1.5",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
        },
        "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]",
            "storageAccountEndPoint": "https://core.windows.net"
        }
    }
}
```

Om du använder mallparametrar för att ange egenskaps värden måste du skapa dem. När du skapar mallparametrar för skyddade inställnings värden ska du se till att `SecureString` använda parameter typen så att känsliga värden skyddas. Mer information om hur du använder parametrar finns i [redigera Azure Resource Manager mallar](../../resource-group-authoring-templates.md).

I exemplet för `IaasDiagnostic` tillägget skapas följande parametrar i avsnittet parametrar i Resource Manager-mallen.

```json
"storageAccountName": {
    "defaultValue": null,
    "type": "SecureString"
},
"storageAccountKey": {
    "defaultValue": null,
    "type": "SecureString"
}
```

I det här läget kan mallen distribueras med hjälp av en metod för distribution av mallar.
