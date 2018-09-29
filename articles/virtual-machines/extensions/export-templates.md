---
title: Exportera Azure-resursgrupper som innehåller VM-tillägg | Microsoft Docs
description: Exportera Resource Manager-mallar som innehåller tillägg för virtuell dator.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
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
ms.author: roiyz
ms.openlocfilehash: f56cfeeede393dbdb9632ea4120d3a81e89f3f7c
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451977"
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>Exportera resursgrupper som innehåller VM-tillägg

Azure-resursgrupper kan exporteras till en ny Resource Manager-mall som sedan kan distribueras på nytt. Exportprocessen tolkar befintliga resurser och skapar en Resource Manager-mall som när den distribuerats resulterar i en liknande resursgrupp. När du använder resursgruppen Exportalternativet mot en resursgrupp som innehåller tillägg för virtuell dator, flera objekt måste beaktas, till exempel tillägget kompatibilitet och inställningarna för replikeringsskyddade.

Den här dokumentet beskriver hur resursgrupp exportprocessen om virtual machine-tillägg, inklusive en lista över tillägg som stöds och information om hantering av skyddade data.

## <a name="supported-virtual-machine-extensions"></a>Tillägg för virtuell dator som stöds

Det finns många tillägg för virtuell dator. Inte alla tillägg kan exporteras till en Resource Manager-mall med hjälp av funktionen ”Automationsskript”. Om tillägg för virtuell dator inte stöds, måste den placeras manuellt tillbaka till den exporterade mallen.

Följande tillägg kan exporteras med funktionen för automation-skript.

| Anknytning ||||
|---|---|---|---|
| Acronis säkerhetskopiering | Datadog Windows-agenten | OS-korrigering för Linux | Linux för VM-ögonblicksbild
| Acronis säkerhetskopiering Linux | Docker-tillägg | Puppet-agenten |
| BG Info | DSC-tillägg | Plats 24 x 7 Apm Insight |
| BMC CTM agenten Linux | Dynatrace Linux | Linux platsservern 24 x 7 |
| BMC CTM agenten Windows | Dynatrace Windows | Windows platsservern 24 x 7 |
| Chef-klienten | HPE Security program Defender | Trend Micro DSA |
| Anpassat skript | IaaS program mot skadlig kod | Trend Micro DSA Linux |
| Anpassat skripttillägg | IaaS-diagnostik | Åtkomst till virtuell dator för Linux |
| Anpassat skript för Linux | Klienten för Linux-Chef | Åtkomst till virtuell dator för Linux |
| Datadog Linux-agenten | Linux-diagnostik | VM-ögonblicksbild |

## <a name="export-the-resource-group"></a>Exportera resursgruppens namn

Om du vill exportera en resursgrupp i en återanvändningsbara mallar, gör du följande:

1. Logga in på Azure Portal
2. På Navmenyn klickar du på resursgrupper
3. Välj målresursgruppen i listan
4. Resursgrupp-bladet klickar du på Automationsskript

![Mallexporten](./media/export-templates/template-export.png)

Azure Resource Manager automatiseringar skriptet skapar en Resource Manager-mall, en fil med parametrar och flera exempel på distributionsskript, till exempel PowerShell och Azure CLI. Nu kan kan den exporterade mallen hämtas med hjälp av knappen Hämta läggs till som en ny mall i biblioteket för mallen eller omdistribueras med knappen distribuera.

## <a name="configure-protected-settings"></a>Konfigurera inställningar för skyddade

Många Azure virtual machine-tillägg är en konfiguration för skyddade inställningarna som krypterar känsliga data, till exempel autentiseringsuppgifter och av konfigurationssträngar. Skyddade inställningarna exporteras inte med automationsskript. Om det behövs, skyddade inställningarna måste infogas igen i den exporterade mallbaserade.

### <a name="step-1---remove-template-parameter"></a>Steg 1 – ta bort mallparameter

När resursgruppen exporteras, skapas en enda mall-parameter för att tillhandahålla ett värde till de exporterade skyddade inställningarna. Den här parametern kan tas bort. Ta bort parametern, titta igenom listan över och ta bort parametern som liknar det här JSON-exemplet.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>Steg 2 – Get skyddade egenskaper

Eftersom varje skyddad inställning har en uppsättning nödvändiga egenskaper, måste en lista över de här egenskaperna samlas in. Varje parameter för den skyddade Inställningskonfiguration finns i den [Azure Resource Manager-schemat på GitHub](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json). Det här schemat innehåller endast parameteruppsättningar för de tillägg som anges i översiktsavsnittet i det här dokumentet. 

Från inom lagringsplatsen schema söka efter önskad tillägget i det här exemplet `IaaSDiagnostics`. När tillägg `protectedSettings` objekt har hittats, anteckna varje parameter. I exemplet med den `IaasDiagnostic` tillägg, den kräver parametrar är `storageAccountName`, `storageAccountKey`, och `storageAccountEndPoint`.

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

I den exporterade mallen, söka efter `protectedSettings` och Ersätt den exporterade skyddade inställningsobjekt med en ny med nödvändiga Tilläggsparametrarna och ett värde för var och en.

I exemplet med den `IaasDiagnostic` tillägg, den nya skyddade Konfigurationsinställningen skulle se ut som i följande exempel:

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

Den slutliga tillägg resursen liknar följande JSON-exempel:

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

Dessa måste skapas om du använder mallparametrar ange egenskapsvärden. När du skapar mallparametrar för den skyddade inställningsvärden, se till att använda den `SecureString` parametertypen så att känsliga värden är skyddade. Mer information om hur du använder parametrar finns i [redigera Azure Resource Manager-mallar](../../resource-group-authoring-templates.md).

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

Då kan mallen distribueras med valfri metod för distribution av mallen.
