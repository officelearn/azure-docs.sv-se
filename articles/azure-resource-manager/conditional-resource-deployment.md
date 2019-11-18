---
title: Villkorlig distribution med mallar
description: Beskriver hur du villkorligt distribuerar en resurs i en Azure Resource Manager-mall.
ms.topic: conceptual
ms.date: 09/03/2019
ms.openlocfilehash: e3d78f875e39628194ac30310314e59e2ef19fdb
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147181"
---
# <a name="conditional-deployment-in-resource-manager-templates"></a>Villkorlig distribution i Resource Manager-mallar

Ibland behöver du eventuellt distribuera en resurs i en mall. Använd `condition`-elementet för att ange om resursen har distribuerats. Värdet för det här elementet matchas till true eller false. När värdet är true skapas resursen. När värdet är false skapas inte resursen. Värdet kan bara användas för hela resursen.

## <a name="new-or-existing-resource"></a>Ny eller befintlig resurs

Du kan använda villkorlig distribution för att skapa en ny resurs eller använda en befintlig. I följande exempel visas hur du använder villkor för att distribuera ett nytt lagrings konto eller använda ett befintligt lagrings konto.

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[parameters('location')]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

När parametern **newOrExisting** har angetts till **New**utvärderas villkoret som sant. Lagrings kontot har distribuerats. Men när **newOrExisting** är inställt på **befintlig**, utvärderas villkoret till falskt och lagrings kontot distribueras inte.

En fullständig exempel mall som använder `condition`-elementet finns i [VM med en ny eller befintlig Virtual Network, lagring och offentlig IP-adress](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

## <a name="allow-condition"></a>Tillåt villkor

Du kan skicka ett parameter värde som anger om ett villkor är tillåtet. I följande exempel distribueras en SQL-Server och du kan även tillåta Azure IP-adresser.

```json
{
    "type": "Microsoft.Sql/servers",
    "name": "[parameters('serverName')]",
    "apiVersion": "2015-05-01-preview",
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
            "name": "AllowAllWindowsAzureIps",
            "apiVersion": "2015-05-01-preview",
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

Den fullständiga mallen finns i [logisk Azure SQL-Server](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server).

## <a name="runtime-functions"></a>Körnings funktioner

Om du använder en [referens](resource-group-template-functions-resource.md#reference) -eller [list](resource-group-template-functions-resource.md#list) funktion med en resurs som är villkorligt distribuerad utvärderas funktionen även om resursen inte har distribuerats. Du får ett fel meddelande om funktionen hänvisar till en resurs som inte finns.

Använd funktionen [IF](resource-group-template-functions-logical.md#if) för att se till att funktionen bara utvärderas för villkor när resursen distribueras. Se [funktionen IF](resource-group-template-functions-logical.md#if) för en exempel mall som använder IF och Reference med en villkorligt distribuerad resurs.

## <a name="condition-with-complete-mode"></a>Villkor med slutfört läge

Om du distribuerar en mall med [slutfört läge](deployment-modes.md) och en resurs inte distribueras eftersom villkoret utvärderas till false beror resultatet på vilken REST API version som du använder för att distribuera mallen. Om du använder en tidigare version än 2019-05-10 **tas inte resursen bort**. Med 2019-05-10 eller senare **tas resursen bort**. De senaste versionerna av Azure PowerShell och Azure CLI tar bort resursen när villkoret är falskt.

## <a name="next-steps"></a>Nästa steg

* Rekommendationer om hur du skapar mallar finns i [metod tips för Azure Resource Manager mallar](template-best-practices.md).
* Om du vill skapa flera instanser av en resurs, se [resurs, egenskap eller variabel iteration i Azure Resource Manager mallar](resource-group-create-multiple.md).