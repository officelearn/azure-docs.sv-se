---
title: Skapa en händelsehubb med konsumentgrupp - Azure Event Hubs | Microsoft Docs
description: Skapa ett namnområde för Event Hubs med en händelsehubb och en konsumentgrupp med hjälp av Azure Resource Manager-mallar
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/16/2018
ms.author: shvija
ms.openlocfilehash: 8664b431239f7b288deccedeadff0806ab600bcd
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56232521"
---
# <a name="quickstart-create-an-event-hub-using-azure-resource-manager-template"></a>Snabbstart: Skapa en event hub med Azure Resource Manager-mall
Azure Event Hubs är en strömningstjänst för stordata och händelseinmatningstjänst som kan ta emot och bearbeta flera miljoner händelser per sekund. Azure Event Hubs kan bearbeta och lagra händelser, data eller telemetri som produceras av distribuerade program och enheter. Data som skickas till en händelsehubb kan omvandlas och lagras med valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadaptrar. En detaljerad översikt över Event Hubs finns i [Översikt över Event Hubs](event-hubs-about.md) och [Event Hubs-funktioner](event-hubs-features.md).

I den här snabbstarten skapar du en händelsehubb med en Azure Resource Manager-mall. Du använder en Azure Resource Manager-mall för att skapa ett namnområde av typen [Händelsehubbar](event-hubs-what-is-event-hubs.md), med en händelsehubb och en konsumentgrupp. Artikeln visar hur du definierar vilka resurser distribueras och hur du definierar parametrar som anges när distributionen körs. Du kan använda den här mallen för dina egna distributioner eller anpassa den så att den uppfyller dina krav. Information om hur du skapar mallar finns i [redigera Azure Resource Manager-mallar][Authoring Azure Resource Manager templates]. Den JSON-syntax och de egenskaper som ska användas i en mall finns i avsnittet om [Microsoft.EventGrid-resurstyper](/azure/templates/microsoft.eventhub/allversions).

> [!NOTE]
> Läs den fullständiga mallen, den [Event hub och konsument mall] [ Event Hub and consumer group template] på GitHub. Den här mallen skapas en konsumentgrupp förutom ett händelsehubbnamnområde och en händelsehubb. Om du vill söka efter de senaste mallarna kan du gå till galleriet [Azure-snabbstartsmallar][Azure Quickstart Templates] och söka efter Event Hubs.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Du behöver en Azure-prenumeration för att kunna utföra den här snabbstarten. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

Om du vill använda **Azure PowerShell** att distribuera Resource Manager-mallen [installera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Om du vill använda **Azure CLI** att distribuera Resource Manager-mallen [installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-the-resource-manager-template-json"></a>Skapa Resource Manager-mallens JSON
Skapa en JSON-fil med namnet MyEventHub.json med följande innehåll och spara den till en mapp (till exempel: C:\EventHubsQuickstarts\ResourceManagerTemplate).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "eventhub-namespace-name": {
            "type": "String"
        },
        "eventhub_name": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.EventHub/namespaces",
            "sku": {
                "name": "Standard",
                "tier": "Standard",
                "capacity": 1
            },
            "name": "[parameters('eventhub-namespace-name')]",
            "apiVersion": "2017-04-01",
            "location": "East US",
            "tags": {},
            "scale": null,
            "properties": {
                "isAutoInflateEnabled": false,
                "maximumThroughputUnits": 0
            },
            "dependsOn": []
        },
        {
            "type": "Microsoft.EventHub/namespaces/eventhubs",
            "name": "[concat(parameters('eventhub-namespace-name'), '/', parameters('eventhub_name'))]",
            "apiVersion": "2017-04-01",
            "location": "East US",
            "scale": null,
            "properties": {
                "messageRetentionInDays": 7,
                "partitionCount": 1,
                "status": "Active"
            },
            "dependsOn": [
                "[resourceId('Microsoft.EventHub/namespaces', parameters('eventhub-namespace-name'))]"
            ]
        }
    ]
}
```

## <a name="create-the-parameters-json"></a>Skapa JSON-parametrar
Skapa en JSON-fil med namnet MyEventHub-Parameters.json som innehåller parametrar för Azure Resource Manager-mallen. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
        "eventhub-namespace-name": {
            "value": "<specify a name for the event hub namespace>"
        },
        "eventhub_name": {
            "value": "<Specify a name for the event hub in the namespace>"
        }
  }
}
```



## <a name="use-azure-powershell-to-deploy-the-template"></a>Använda Azure PowerShell för att distribuera mallen

### <a name="sign-in-to-azure"></a>Logga in på Azure
1. Starta Azure PowerShell

2. Kör följande kommandon för att logga in på Azure:

   ```azurepowershell
   Login-AzAccount
   ```
3. Om du har kan du utfärda följande kommandon för att ange den aktuella prenumerationskontexten:

   ```azurepowershell
   Select-AzSubscription -SubscriptionName "<YourSubscriptionName>" 
   ```

### <a name="provision-resources"></a>Etablera resurser
Om du vill distribuera/etablera resurser med Azure PowerShell, växla till mappen C:\EventHubsQuickStart\ARM\, kör följande kommandon:

> [!IMPORTANT]
> Ange ett namn för Azure-resursgrupp som ett värde för $resourceGroupName innan du kör kommandona. 

```azurepowershell
$resourceGroupName = "<Specify a name for the Azure resource group>"

# Create an Azure resource group
New-AzResourceGroup $resourceGroupName -location 'East US'

# Deploy the Resource Manager template. Specify the names of deployment itself, resource group, JSON file for the template, JSON file for parameters
New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName $resourceGroupName -TemplateFile MyEventHub.json -TemplateParameterFile MyEventHub-Parameters.json
```

## <a name="use-azure-cli-to-deploy-the-template"></a>Använd Azure CLI för att distribuera mallen

## <a name="sign-in-to-azure"></a>Logga in på Azure

Följande steg krävs inte om du kör kommandon i Cloud Shell. Om du kör CLI lokalt måste du utföra följande steg för att logga in i Azure och ange din aktuella prenumeration:

Kör följande kommandon för att logga in på Azure:

```azurecli
az login
```

Ange den aktuella prenumerationskontexten. Ersätt `MyAzureSub` med namnet på den Azure-prenumeration som du vill använda:

```azurecli
az account set --subscription <Name of your Azure subscription>
``` 

### <a name="provision-resources"></a>Etablera resurser
Växla till mappen C:\EventHubsQuickStart\ARM\ för att distribuera resurser med Azure CLI, och kör följande kommandon:

> [!IMPORTANT]
> Ange ett namn för Azure-resursgrupp i gruppen az skapa kommando. .

```azurecli
# Create an Azure resource group
az group create --name <YourResourceGroupName> --location eastus

# # Deploy the Resource Manager template. Specify the names of resource group, deployment, JSON file for the template, JSON file for parameters
az group deployment create --name <Specify a name for the deployment> --resource-group <YourResourceGroupName> --template-file MyEventHub.json --parameters @MyEventHub-Parameters.json
```

Grattis! Du har använt Azure Resource Manager-mallen för att skapa ett namnområde för Event Hubs och en händelsehubb i namnområdet.

## <a name="next-steps"></a>Nästa steg

I den här artikeln skapade du en Event Hubs-namnrymd och använde exempelprogram för att skicka och ta emot meddelanden från din händelsehubb. Stegvisa instruktioner för att skicka händelser till eller ta emot händelser från en händelsehubb finns i följande självstudier: 

- **Skicka händelser till en händelsehubb**: [.NET Core](event-hubs-dotnet-standard-getstarted-send.md), [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [Node.js](event-hubs-node-get-started-send.md), [Go](event-hubs-go-get-started-send.md), [C](event-hubs-c-getstarted-send.md)
- **Ta emot händelser från en händelsehubb**: [.NET Core](event-hubs-dotnet-standard-getstarted-receive-eph.md), [.NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md), [Java](event-hubs-java-get-started-receive-eph.md), [Python](event-hubs-python-get-started-receive.md), [Node.js](event-hubs-node-get-started-receive.md), [Go](event-hubs-go-get-started-receive-eph.md), [Apache Storm](event-hubs-storm-getstarted-receive.md)

[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
