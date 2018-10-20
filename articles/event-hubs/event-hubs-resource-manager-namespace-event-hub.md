---
title: Skapa en Azure Event Hubs-namnområde och konsument-grupp med en mall | Microsoft Docs
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
ms.openlocfilehash: 48d41ef4df986f959dfabe04e07552e287fdfcd0
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457110"
---
# <a name="create-an-event-hubs-namespace-with-event-hub-and-consumer-group-using-an-azure-resource-manager-template"></a>Skapa ett namnområde för Event Hubs med event hub och konsument grupp med en Azure Resource Manager-mall
Azure Event Hubs är en strömningstjänst för stordata och händelseinmatningstjänst som kan ta emot och bearbeta flera miljoner händelser per sekund. Azure Event Hubs kan bearbeta och lagra händelser, data eller telemetri som produceras av distribuerade program och enheter. Data som skickas till en händelsehubb kan omvandlas och lagras med valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadaptrar. Detaljerad översikt över Event Hubs finns i [översikt av Händelsehubbar](event-hubs-about.md) och [Event Hubs-funktioner](event-hubs-features.md).

I den här snabbstarten skapar du en händelsehubb med en Azure Resource Manager-mall. Du använder en Azure Resource Manager-mall för att skapa ett namnområde av typen [Händelsehubbar](event-hubs-what-is-event-hubs.md), med en händelsehubb och en konsumentgrupp. Artikeln visar hur du definierar vilka resurser distribueras och hur du definierar parametrar som anges när distributionen körs. Du kan använda den här mallen för dina egna distributioner eller anpassa den så att den uppfyller dina krav. Information om hur du skapar mallar finns i [redigera Azure Resource Manager-mallar][Authoring Azure Resource Manager templates].

Läs den fullständiga mallen, den [Event hub och konsument mall] [ Event Hub and consumer group template] på GitHub.

> [!NOTE]
> Om du vill söka efter de senaste mallarna kan du gå till galleriet [Azure-snabbstartsmallar][Azure Quickstart Templates] och söka efter Event Hubs.

## <a name="prerequisites"></a>Förutsättningar
Du behöver en Azure-prenumeration för att kunna utföra den här snabbstarten. Om du inte har någon, [skapa ett kostnadsfritt konto] [] innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du använder **Azure PowerShell** för att distribuera Resource Manager-mallen lokalt, måste du köra den senaste versionen av PowerShell för att slutföra den här snabbstarten. Om du behöver installera eller uppgradera, kan du läsa [Installera och konfigurera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).

Om du väljer att installera och använda **Azure CLI** för att distribuera Resource Manager-mallen lokalt, den här självstudien krävs att du kör Azure CLI version 2.0.4 eller senare. Kör `az --version` för att kontrollera vilken version du har. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="what-will-you-deploy"></a>Vad vill du distribuera?

Med den här mallen kan du distribuera ett namnområde för Event Hubs med en händelsehubb och en konsumentgrupp.

Välj på följande knapp för att köra distributionen automatiskt:

[![Distribuera till Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## <a name="define-parameters"></a>Definiera parametrar
Med Azure Resource Manager kan du definiera parametrar för värden som du vill ange när mallen distribueras. Mallen innehåller ett avsnitt som heter `Parameters` och som innehåller alla parametervärden. Du bör definiera en parameter för värden som varierar utifrån det projekt som du distribuerar eller utifrån den miljö som du distribuerar. Definiera inte parametrar för värden som aldrig ändras. Varje parametervärde i mallen definierar de resurser som distribueras.

Mallen definierar följande parametrar:

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

Namnet på namnområdet för Event Hubs som ska skapas.

```json
"eventHubNamespaceName": {
"type": "string"
}
```

### <a name="eventhubname"></a>eventHubName

Namnet på händelsehubben som skapats i namnområdet för Event Hubs.

```json
"eventHubName": {
"type": "string"
}
```

### <a name="eventhubconsumergroupname"></a>eventHubConsumerGroupName

Namnet på konsumentgrupp som skapats för event hub.

```json
"eventHubConsumerGroupName": {
"type": "string"
}
```

### <a name="apiversion"></a>apiVersion

API-versionen av mallen.

```json
"apiVersion": {
"type": "string"
}
```

## <a name="define-resources-to-deploy"></a>Definiera resurser som ska distribueras

Skapar ett namnområde av typen **EventHubs**, med en händelsehubb och en konsumentgrupp:

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('namespaceName')]",
         "type":"Microsoft.EventHub/namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]"
               },
               "resources":[  
                  {  
                     "apiVersion":"[variables('ehVersion')]",
                     "name":"[parameters('consumerGroupName')]",
                     "type":"ConsumerGroups",
                     "dependsOn":[  
                        "[parameters('eventHubName')]"
                     ],
                     "properties":{  

                     }
                  }
               ]
            }
         ]
      }
   ],
```

## <a name="azure-powershell"></a>Azure PowerShell
Om du vill distribuera resurser med Azure PowerShell kör du följande kommando:

```azurepowershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json
```

## <a name="azure-cli"></a>Azure CLI
Om du vill distribuera resurser med Azure PowerShell kör du följande kommando:

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

Grattis! Du har använt Azure Resource Manager-mallen för att skapa ett namnområde för Event Hubs och en händelsehubb i namnområdet.

## <a name="next-steps"></a>Nästa steg

I den här artikeln får du skapade Event Hubs-namnområdet och används exempelprogram för att skicka och ta emot händelser från event hub. Stegvisa instruktioner för att skicka händelser till (eller) ta emot händelser från en event hub, finns i följande Självstudier: 

- **Skicka händelser till en händelsehubb**: [.NET Standard](event-hubs-dotnet-standard-getstarted-send.md), [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [Node.js ](event-hubs-node-get-started-send.md), [Gå](event-hubs-go-get-started-send.md), [C](event-hubs-c-getstarted-send.md)
- **Ta emot händelser från en event hub**: [.NET Standard](event-hubs-dotnet-standard-getstarted-receive-eph.md), [.NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md), [Java](event-hubs-java-get-started-receive-eph.md), [Python](event-hubs-python-get-started-receive.md), [ Node.js](event-hubs-node-get-started-receive.md), [Gå](event-hubs-go-get-started-receive-eph.md), [Apache Storm](event-hubs-storm-getstarted-receive.md)

[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
