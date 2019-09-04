---
title: Villkorlig distribution med Azure Resource Manager mallar
description: Beskriver hur du villkorligt distribuerar en resurs i en Azure Resource Manager-mall.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: tomfitz
ms.openlocfilehash: 88f8b6a8dcce0e498a7b81b8741072bcf4cfcad8
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259514"
---
# <a name="conditional-deployment-in-resource-manager-templates"></a>Villkorlig distribution i Resource Manager-mallar

Ibland behöver du eventuellt distribuera en resurs i en mall. Använd- `condition` elementet för att ange om resursen har distribuerats. Värdet för det här elementet matchas till true eller false. När värdet är true skapas resursen. När värdet är false skapas inte resursen. Värdet kan bara användas för hela resursen.

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

En fullständig exempel mall som använder `condition` -elementet finns i [VM med en ny eller befintlig Virtual Network, lagring och offentlig IP-adress](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

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

## <a name="next-steps"></a>Nästa steg

* Rekommendationer om hur du skapar mallar finns i [metod tips för Azure Resource Manager mallar](template-best-practices.md).
* Om du vill skapa flera instanser av en resurs, se [resurs, egenskap eller variabel iteration i Azure Resource Manager mallar](resource-group-create-multiple.md).