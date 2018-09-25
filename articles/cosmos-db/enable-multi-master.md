---
title: Aktivera multimaster för Azure Cosmos DB-konton
description: Den här artikeln beskriver hur du aktiverar flera huvudservrar support när du skapar ett Azure Cosmos DB-konto med Azure portal, PowerShell, CLI eller en Azure Resource Manager-mall.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 71579e28f389d91498ef5f37c5d43dc9d5140234
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963227"
---
# <a name="enable-multi-master-for-azure-cosmos-db-accounts"></a>Aktivera multimaster för Azure Cosmos DB-konton

Stöd för flera huvudservrar aktiveras när du skapar ett Azure Cosmos DB-konto. Ett Azure Cosmos DB-konto kan skapas med hjälp av Azure portal, PowerShell, CLI eller en Azure Resource Manager-mall.

> [!IMPORTANT]
> För närvarande kan stöd för flera huvudservrar aktiveras för endast nya Azure Cosmos DB-konton. Befintliga Azure Cosmos DB-konton kan inte använda funktionen. Vi arbetar för att ge stöd för befintliga konton och meddelar det här stödet när den är tillgänglig.

När du har skapat ett Azure Cosmos DB-konto med flera huvudservrar stöd kan du skapa databaser, behållare, ladda upp dokument och tilldela konflikt upplösning principer. Konfliktlösning i multimaster och exempel på kod, se [flera huvudservrar kodexempel](multi-master-conflict-resolution.md#code-samples) artikeln.

## <a name="enable-multi-master-using-azure-portal"></a>Aktivera multimaster med hjälp av Azure portal

1. Logga in på [Azure-portalen](https://portal.azure.com/)

2. Klicka på Välj **skapa en resurs > databaser > Azure Cosmos DB**.

3. I den **nytt konto** fönstret anger du följande inställningar för ett nytt Azure Cosmos DB-konto:

   |**Inställning**  |**Föreslaget värde** |**Beskrivning**|
   |---------|---------|---------|
   |Prenumeration   | {Din prenumeration}  |Välj den Azure-prenumerationen för Azure Cosmos DB-kontot.  |
   |Resursgrupp  |   {Namn på resursgruppen}    |  Välj en befintlig resursgrupp eller välj **Skapa ny**, ange sedan ett nytt Resursgruppsnamn för ditt konto. |
   |Kontonamn | {namnet på ditt konto}   |  Ange ett unikt namn som identifierar Azure Cosmos DB-kontot.        |
   |API  |   Alla   |  Välj en API-typen.   |
   |Plats  | Välj en region   | Välj den geografiska plats som ska vara värd för ditt Azure Cosmos DB-konto. Du kan välja en region, eftersom det här kontot kommer att finnas i flera regioner.  |
   |Aktivera geo-redundans   |  Aktivera  |  Välj det här alternativet om du vill aktivera flera master väljas nedan.   |
   |Aktivera flera original | Aktivera  | Välj det här alternativet om du vill aktivera multi-hanteraren för det här kontot. |


## <a name="using-multi-master-in-sdks"></a>Med hjälp av multimaster i SDK: er

Med ett multimaster aktiverat-konto kan i dina program, du dra nytta av flera mastering genom att utnyttja ConnectionPolicy enligt nedan.

```csharp
ConnectionPolicy policy = new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct,
   ConnectionProtocol = Protocol.Tcp,
   UseMultipleWriteLocations = true,
};
policy.PreferredLocations.Add("West US");
policy.PreferredLocations.Add("North Europe");
policy.PreferredLocations.Add("Southeast Asia");
```

## <a name="enable-multi-master-using-powershell"></a>Aktivera flera huvudservrar med hjälp av PowerShell

Du kan också skapa flera huvudservrar aktiverade Cosmos DB-konto genom att ange den `enableMultipleWriteLocations` parametern ”true”. Öppna ett PowerShell-fönster för att skapa ett Azure Cosmos DB-konto med Multi-Master aktiverat, och kör följande skript:

```azurepowershell-interactive
$locations = @(@{"locationName"="East US"; "failoverPriority"=0},
             @{"locationName"="West US"; "failoverPriority"=1})

$iprangefilter = "<ip-range-filter>"

$consistencyPolicy = @{"defaultConsistencyLevel"="Session";
                       "maxIntervalInSeconds"= "10";
                       "maxStalenessPrefix"="200"}

$CosmosDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$locations;
                        "consistencyPolicy"=$consistencyPolicy;
                        "ipRangeFilter"=$iprangefilter;
                        "enableMultipleWriteLocations"="true"}

New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

## <a name="enable-multi-master-using-cli"></a>Aktivera flera huvudservrar med CLI

Du kan aktivera flera master genom att aktivera flera-write-platser för parametern ”true”. Öppna Azure CLI för att skapa ett Azure Cosmos DB-konto med Multi-Master aktiverad, eller cloud shell och kör följande kommando:

```azurecli-interactive
az cosmosdb create \
   –-name "myCosmosDbAccount" \
   --resource-group "myResourceGroup" \
   --default-consistency-level "Session" \
   --enable-automatic-failover "true" \
   --locations "EastUS=0" "WestUS=1" \
   --enable-multiple-write-locations true \
```

## <a name="enable-multi-master-using-resource-manager-template"></a>Aktivera flera huvudservrar med hjälp av Resource Manager-mall

Följande JSON-kod är ett exempel Resource Manager-mall som du kan använda för att distribuera ett Azure Cosmos DB-konto. Läs om formatet för Resource Manager-mall och syntaxen i [Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) dokumentation. Parametern viktiga att känna till i den här mallen är ”enableMultipleWriteLocations”: true.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "locationName": {
            "type": "String"
        },
        "defaultExperience": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {
                "defaultExperience": "[parameters('defaultExperience')]"
            },
            "properties": {
                "databaseAccountOfferType": "Standard",
                "locations": [
                    {
                        "id": "[concat(parameters('name'), '-', parameters('location'))]",
                        "failoverPriority": 0,
                        "locationName": "[parameters('locationName')]"
                    }
                ],
                "isVirtualNetworkFilterEnabled": false,
                "enableMultipleWriteLocations": true,
                "virtualNetworkRules": [],
                "dependsOn": []
            }
        }
    ]
}
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att aktivera flera huvudservrar stöd för Azure Cosmos DB-konton. Nu ska titta på följande resurser:

* [med hjälp av multimaster med öppen källkod NoSQL-databaser](multi-master-oss-nosql.md)

* [Förstå konfliktlösning i Azure Cosmos DB](multi-master-conflict-resolution.md)
