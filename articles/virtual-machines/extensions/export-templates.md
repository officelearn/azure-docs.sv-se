---
title: Exportera Azure Resource Groups som innehåller VM-tillägg
description: Exportera Resource Manager-mallar som innehåller tillägg för virtuella datorer.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 7f4e2ca6-f1c7-4f59-a2cc-8f63132de279
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/05/2016
ms.author: akjosh
ms.openlocfilehash: 79991dad96742109817d579b951082d1a30e3951
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253915"
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>Exportera resursgrupper som innehåller vm-tillägg

Azure Resource Groups kan exporteras till en ny Resource Manager-mall som sedan kan distribueras om. Exportprocessen tolkar befintliga resurser och skapar en Resource Manager-mall som när distribueras resulterar i en liknande resursgrupp. När du använder alternativet Exportera resursgrupp mot en resursgrupp som innehåller tillägg för virtuella datorer måste flera objekt betraktas som tilläggskompatibilitet och skyddade inställningar.

I det här dokumentet beskrivs hur exportprocessen för resursgruppen fungerar när det gäller tillägg för virtuella datorer, inklusive en lista över tillägg som stöds, och information om hur du hanterar skyddade data.

## <a name="supported-virtual-machine-extensions"></a>Tillägg för virtuella datorer som stöds

Många tillägg för virtuella datorer är tillgängliga. Alla tillägg kan inte exporteras till en Resource Manager-mall med hjälp av funktionen "Automation Script". Om ett tillägg till den virtuella datorn inte stöds måste det placeras manuellt tillbaka i den exporterade mallen.

Följande tillägg kan exporteras med funktionen för automatiseringsskript.

| Anknytning ||||
|---|---|---|---|
| Acronis Backup | Windows-agent för Datadog | OS Patching för Linux | VM Snapshot Linux
| Acronis Backup Linux | Docker-tillägg | Marionett agent |
| Bg Info | DSC-tillägg | Webbplats 24x7 Apm Insight |
| BMC CTM Agent Linux | Dynatrace Linux | Plats 24x7 Linux Server |
| BMC CTM Agent Windows | Dynatrace Windows | Plats 24x7 Windows Server |
| Chef Kund | HPE-säkerhetsprogram försvarare | Trend Micro DSA |
| Anpassat skript | IaaS Antimalware | Trend Micro DSA Linux |
| Anpassat skripttillägg | IaaS Diagnostik | VM-åtkomst för Linux |
| Anpassat skript för Linux | Linux Chef-klient | VM-åtkomst för Linux |
| Datadog Linux-agent | Linux-diagnostik | VM-ögonblicksbild |

## <a name="export-the-resource-group"></a>Exportera resursgruppen

Så här exporterar du en resursgrupp till en återanvändningsbar mall:

1. Logga in på Azure Portal
2. Klicka på Resursgrupper på Hub-menyn
3. Välj målresursgruppen i listan
4. Klicka på Automatiseringsskript i bladet Resursgrupp

![Mallexport](./media/export-templates/template-export.png)

Azure Resource Manager automations-skriptet producerar en Resource Manager-mall, en parameterfil och flera exempeldistributionsskript som PowerShell och Azure CLI. Nu kan den exporterade mallen hämtas med hjälp av hämtningsknappen, läggas till som en ny mall i mallbiblioteket eller distribueras om med hjälp av distributionsknappen.

## <a name="configure-protected-settings"></a>Konfigurera skyddade inställningar

Många Azure-tillägg för virtuella datorer innehåller en konfiguration av skyddade inställningar som krypterar känsliga data som autentiseringsuppgifter och konfigurationssträngar. Skyddade inställningar exporteras inte med automatiseringsskriptet. Om det behövs måste skyddade inställningar återinföras i den exporterade mallen.

### <a name="step-1---remove-template-parameter"></a>Steg 1 - Ta bort mallparameter

När resursgruppen exporteras skapas en enda mallparameter för att ge ett värde till de exporterade skyddade inställningarna. Den här parametern kan tas bort. Om du vill ta bort parametern tittar du igenom parameterlistan och tar bort parametern som liknar det här JSON-exemplet.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>Steg 2 - Få egenskaper för skyddade inställningar

Eftersom varje skyddad inställning har en uppsättning obligatoriska egenskaper måste en lista över dessa egenskaper samlas in. Varje parameter för konfigurationen av skyddade inställningar finns i [Azure Resource Manager-schemat på GitHub](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json). Det här schemat innehåller endast parameteruppsättningarna för tilläggen i översiktsavsnittet i det här dokumentet. 

Sök efter önskat tillägg i schemaarkivet i `IaaSDiagnostics`schemaarkivet. När tilläggsobjektet har hittats `protectedSettings` bör du notera varje parameter. I exemplet med `IaasDiagnostic` tillägget är `storageAccountName`krävparametrarna , `storageAccountKey`och `storageAccountEndPoint`.

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

### <a name="step-3---re-create-the-protected-configuration"></a>Steg 3 - Återskapa den skyddade konfigurationen

Sök efter `protectedSettings` och ersätt det exporterade skyddade inställningsobjektet med ett nytt som innehåller de nödvändiga tilläggsparametrarna och ett värde för var och en i den exporterade skyddade inställningsobjektet med ett nytt som innehåller de nödvändiga tilläggsparametrarna och ett värde för var och en.

I exemplet med `IaasDiagnostic` tillägget skulle den nya skyddade inställningskonfigurationen se ut som följande exempel:

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

Den slutliga tilläggsresursen liknar följande JSON-exempel:

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

Om du använder mallparametrar för att ange egenskapsvärden måste dessa skapas. När du skapar mallparametrar för skyddade `SecureString` inställningsvärden ska du se till att använda parametertypen så att känsliga värden skyddas. Mer information om hur du använder parametrar finns i [Skapa Azure Resource Manager-mallar](../../resource-group-authoring-templates.md).

I exemplet `IaasDiagnostic` med tillägget skapas följande parametrar i avsnittet parametrar i resurshanterarens mall.

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

Nu kan mallen distribueras med valfri malldistributionsmetod.
