---
title: Villkorsstyrd distribution med mallar
description: Beskriver hur du distribuerar en resurs villkorligt i en Azure Resource Manager-mall.
ms.topic: conceptual
ms.date: 12/03/2019
ms.openlocfilehash: f170710118c0e3de6f3643b6216ed55b83b5c7df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153428"
---
# <a name="conditional-deployment-in-arm-templates"></a>Villkorlig distribution i ARM-mallar

Ibland måste du distribuera en resurs i en ARM-mall (Azure Resource Manager). Använd `condition` elementet för att ange om resursen ska distribueras. Värdet för det här elementet löss till sant eller falskt. När värdet är sant skapas resursen. När värdet är falskt skapas inte resursen. Värdet kan bara tillämpas på hela resursen.

## <a name="new-or-existing-resource"></a>Ny eller befintlig resurs

Du kan använda villkorlig distribution för att skapa en ny resurs eller använda en befintlig. I följande exempel visas hur du använder villkoret för att distribuera ett nytt lagringskonto eller använda ett befintligt lagringskonto.

```json
{
  "condition": "[equals(parameters('newOrExisting'),'new')]",
  "type": "Microsoft.Storage/storageAccounts",
  "apiVersion": "2017-06-01",
  "name": "[variables('storageAccountName')]",
  "location": "[parameters('location')]",
  "sku": {
    "name": "[variables('storageAccountType')]"
  },
  "kind": "Storage",
  "properties": {}
}
```

När parametern **newOrExisting** är inställd på **ny**utvärderas villkoret till true. Lagringskontot distribueras. Men när **newOrExisting** är inställt på **befintlig**utvärderas villkoret till false och lagringskontot distribueras inte.

En komplett exempelmall som `condition` använder elementet finns [i virtuell dator med en ny eller befintlig virtuell nätverks-, lagrings- och offentlig IP.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions)

## <a name="allow-condition"></a>Tillåt tillstånd

Du kan skicka in ett parametervärde som anger om ett villkor är tillåtet. I följande exempel distribueras en SQL-server och tillåts eventuellt Azure IPs.

```json
{
  "type": "Microsoft.Sql/servers",
  "apiVersion": "2015-05-01-preview",
  "name": "[parameters('serverName')]",
  "location": "[parameters('location')]",
  "properties": {
    "administratorLogin": "[parameters('administratorLogin')]",
    "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
    "version": "12.0"
  },
  "resources": [
    {
      "condition": "[parameters('allowAzureIPs')]",
      "type": "firewallRules",
      "apiVersion": "2015-05-01-preview",
      "name": "AllowAllWindowsAzureIps",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Sql/servers/', parameters('serverName'))]"
      ],
      "properties": {
        "endIpAddress": "0.0.0.0",
        "startIpAddress": "0.0.0.0"
      }
    }
  ]
}
```

Den fullständiga mallen finns i [Den logiska Azure SQL-servern](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server).

## <a name="runtime-functions"></a>Körningsfunktioner

Om du använder en [referens-](template-functions-resource.md#reference) eller [listfunktion](template-functions-resource.md#list) med en resurs som är villkorligt distribuerad utvärderas funktionen även om resursen inte distribueras. Du får ett felmeddelande om funktionen refererar till en resurs som inte finns.

Använd [funktionen om](template-functions-logical.md#if) för att kontrollera att funktionen endast utvärderas för villkor när resursen distribueras. Se [om-funktionen](template-functions-logical.md#if) för en exempelmall som använder om och refererar till en villkorligt distribuerad resurs.

Du anger en [resurs som beroende av](define-resource-dependency.md) en villkorsresurs precis som andra resurser. När en villkorlig resurs inte distribueras tar Azure Resource Manager automatiskt bort den från de nödvändiga beroenden.

## <a name="condition-with-complete-mode"></a>Skick med fullständigt läge

Om du distribuerar en mall med [fullständigt läge](deployment-modes.md) och en resurs inte distribueras eftersom villkoret utvärderas till false, beror resultatet på vilken REST API-version du använder för att distribuera mallen. Om du använder en version tidigare än 2019-05-10 **tas resursen inte bort**. Med 2019-05-10 eller senare tas resursen **bort**. De senaste versionerna av Azure PowerShell och Azure CLI tar bort resursen när villkoret är falskt.

## <a name="next-steps"></a>Nästa steg

* Rekommendationer om hur du skapar mallar finns i [metodtips för ARM-mall .](template-best-practices.md)
* Information om hur du skapar flera instanser av en resurs finns [i Resursiteration i ARM-mallar](copy-resources.md).