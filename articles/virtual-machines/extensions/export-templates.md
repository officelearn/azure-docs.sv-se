---
title: Exportera Azure-resursgrupper som innehåller VM-tillägg | Microsoft Docs
description: Exportera Resource Manager-mallar som inkluderar tillägg för virtuell dator.
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7f4e2ca6-f1c7-4f59-a2cc-8f63132de279
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: danis
ms.openlocfilehash: 3c54b77f52dfc7acf10dc26d4c00e9c14a296774
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>Exportera resursgrupper som innehåller VM-tillägg

Azure resursgrupper kan exporteras till en ny Resource Manager-mall som sedan kan distribueras på nytt. Exportprocessen tolkar befintliga resurser och skapar en Resource Manager-mall som när den distribuerats resulterar i en liknande resursgrupp. När du använder resursgruppen exportalternativ mot en resursgrupp som innehåller tillägg för virtuell dator, flera artiklar måste beaktas, till exempel tillägg kompatibilitet och skyddade inställningarna.

Det här dokumentet beskriver hur resursgruppen exportprocessen om tillägg för virtuell dator, inklusive en lista över tillägg som stöds och information om hantering av skyddade data.

## <a name="supported-virtual-machine-extensions"></a>Tillägg för virtuella datorer

Det finns många tillägg för virtuell dator. Inte alla tillägg kan exporteras till en Resource Manager-mall med hjälp av funktionen ”Automatiseringsskriptet”. Om ett tillägg för virtuell dator inte stöds, måste det placeras manuellt tillbaka i den exporterade mallen.

Följande tillägg kan exporteras med funktionen för automation-skript.

| Anknytning ||||
|---|---|---|---|
| Acronis säkerhetskopiering | Datadog Windows-agenten | OS-korrigering för Linux | VM-ögonblicksbild Linux
| Acronis säkerhetskopiering Linux | Docker-tillägg | Puppet-Agent |
| BG Info | DSC-tillägg | Plats 24 x 7 Apm Insight |
| BMC CTM Agent Linux | Dynatrace Linux | Linux platsservern 24 x 7 |
| BMC CTM Agent Windows | Dynatrace Windows | Windows platsservern 24 x 7 |
| Chef klienten | HPE säkerhet programmet Defender | Trend Micro DSA |
| Anpassat skript | IaaS program mot skadlig kod | Trend Micro DSA Linux |
| Anpassat skripttillägg | IaaS-diagnostik | VM-åtkomst för Linux |
| Anpassat skript för Linux | Klienten för Linux-Chef | VM-åtkomst för Linux |
| Datadog Linux-Agent | Linux-diagnostik | VM-ögonblicksbild |

## <a name="export-the-resource-group"></a>Exportera resursgruppen.

Om du vill exportera en resursgrupp till en återanvändbara mall, gör du följande:

1. Logga in på Azure Portal
2. På Navmenyn klickar du på resursgrupper
3. Välj målresursgruppen i listan
4. I resursgrupp-bladet klickar du på Automatiseringsskriptet

![Exportera mall](./media/export-templates/template-export.png)

Azure Resource Manager automatiseringar skript skapar en Resource Manager-mall, en fil med parametrar och flera exempel på distribution av skript, till exempel PowerShell och Azure CLI. Den exporterade mallen kan nu hämtas med hjälp av knappen ladda ned läggs till som en ny mall mall-biblioteket eller omdistribueras med knappen distribuera.

## <a name="configure-protected-settings"></a>Konfigurera inställningar för skyddade

Många tillägg för virtuella Azure-datorn innehåller en skyddad Inställningskonfiguration som krypterar känsliga data, till exempel autentiseringsuppgifter och konfigurationssträngar. Skyddade inställningarna exporteras inte med automation-skript. Om nödvändigt, skyddade inställningar måste infogas igen till den exporterade utan mall.

### <a name="step-1---remove-template-parameter"></a>Steg 1 – ta bort mallparameter

När resursgruppen exporteras, skapas en enda mallparameter om du vill ange ett värde till de exporterade skyddade inställningarna. Den här parametern kan tas bort. Ta bort parametern, titta igenom listan och ta bort parametern som ser ut ungefär så här JSON.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>Steg 2 – Get skyddade egenskaper

En lista över de här egenskaperna måste samlas in eftersom varje skyddad inställning har en uppsättning egenskaper som krävs. Varje parameter för den skydda Inställningskonfiguration finns i den [Azure Resource Manager schemat på GitHub](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json). Det här schemat innehåller endast parameteruppsättningar för de tillägg som anges i översiktsavsnittet översikt i det här dokumentet. 

Från inom schemadatabasen, Sök efter önskade tillägget i det här exemplet `IaaSDiagnostics`. När tillägg `protectedSettings` objekt har hittats, anteckna varje parameter. I exemplet med den `IaasDiagnostic` tillägg, den kräver parametrar är `storageAccountName`, `storageAccountKey`, och `storageAccountEndPoint`.

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

### <a name="step-3---re-create-the-protected-configuration"></a>Steg 3 – återskapa konfigurationen av skyddade

I den exporterade mallen söka efter `protectedSettings` och Ersätt den exporterade skyddade inställningsobjekt med en ny med nödvändiga Tilläggsparametrarna och ett värde för var och en.

I exemplet med den `IaasDiagnostic` tillägg, den nya skydda inställningen konfigurationen ser ut som följande exempel:

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

Resursen Final = extension ser ut ungefär så här JSON:

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

Om du använder mallparametrar för att ange egenskapsvärden, måste dessa skapas. När du skapar mallparametrar för skyddade inställningsvärden, se till att använda den `SecureString` parametertypen så att känslig värden är skyddade. Mer information om hur du använder parametrar finns [redigera Azure Resource Manager-mallar](../../resource-group-authoring-templates.md).

I exemplet med den `IaasDiagnostic` tillägg, följande parametrar skulle skapas i avsnittet parametrar i Resource Manager-mallen.

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

Mallen kan nu distribueras med valfri metod för distribution av mallen.
