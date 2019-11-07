---
title: 'Snabb start: Skapa Azure Service Bus namnrymd och kö med Azure Resource Manager-mall'
description: 'Snabb start: skapa en Service Bus namnrymd och en kö med Azure Resource Manager mall'
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: a6bfb5fd-7b98-4588-8aa1-9d5f91b599b6
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: 40aeff225cfa6d499ebdfae6736e0684b2a27244
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718793"
---
# <a name="quickstart-create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>Snabb start: skapa en Service Bus namnrymd och en kö med hjälp av en Azure Resource Manager-mall

Den här artikeln visar hur du använder en Azure Resource Manager-mall som skapar ett Service Bus-namnområde och en kö inom denna namnrymd. Artikeln förklarar hur du anger vilka resurser som distribueras och hur du definierar parametrar som anges när distributionen körs. Du kan använda den här mallen för dina egna distributioner eller anpassa den så att den uppfyller dina krav.

Mer information om hur du skapar mallar finns i [redigera Azure Resource Manager mallar][Authoring Azure Resource Manager templates].

Den fullständiga mallen finns i [Service Bus namnrymd och Queue-mall][Service Bus namespace and queue template] på GitHub.

> [!NOTE]
> Följande Azure Resource Manager mallar är tillgängliga för hämtning och distribution.
> 
> * [Skapa ett Service Bus-namnområde med kö och auktoriseringsregel](service-bus-resource-manager-namespace-auth-rule.md)
> * [Skapa ett Service Bus-namnområde med ämne och prenumeration](service-bus-resource-manager-namespace-topic.md)
> * [Skapa ett Service Bus-namnområde](service-bus-resource-manager-namespace.md)
> * [Skapa ett Service Bus-namnområde med ämne, prenumeration och regel](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Om du vill söka efter de senaste mallarna går du till galleriet för [Azure snabb starts mallar][Azure Quickstart Templates] och söker efter **Service Bus**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-will-you-deploy"></a>Vad vill du distribuera?

Med den här mallen distribuerar du en Service Bus namnrymd med en kö.

[Service Bus köer](service-bus-queues-topics-subscriptions.md#queues) ger en eller flera konkurrerande konsumenter först in, först ut-meddelande (FIFO).

Klicka på följande knapp för att köra distributionen automatiskt:

[![Distribuera till Azure](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Parametrar

Med Azure Resource Manager kan du definiera parametrar för värden som du vill ange när mallen distribueras. Mallen innehåller ett avsnitt som heter `Parameters` som innehåller alla parameter värden. Du bör definiera en parameter för de värden som varierar beroende på vilket projekt du distribuerar eller baserat på den miljö som du distribuerar till. Definiera inte parametrar för värden som alltid ska vara desamma. Varje parametervärde används i mallen för att definiera de resurser som distribueras.

Mallen definierar följande parametrar.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
Namnet på Service Bus namn området som ska skapas.

```json
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName
Namnet på kön som skapades i Service Bus namn området.

```json
"serviceBusQueueName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion
Mallens Service Bus-API-version.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       }
```

## <a name="resources-to-deploy"></a>Resurser som ska distribueras
Skapar ett standard Service Bus-namnområde av typ **meddelanden**, med en kö.

```json
{
    "resources": [{
        "apiVersion": "2017-04-01",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
            "name": "Standard"
        },
        "properties": {},
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusQueueName')]",
            "type": "Queues",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusQueueName')]"
            }
        }]
    }]
}
```

Information om JSON-syntax och egenskaper finns i [namnrymder](/azure/templates/microsoft.servicebus/namespaces) och [köer](/azure/templates/microsoft.servicebus/namespaces/queues).

## <a name="commands-to-run-deployment"></a>Kommandon för att köra distributionen
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="next-steps"></a>Nästa steg
Se följande avsnitt som visar hur du skapar en auktoriseringsregel för namn området/kön: [skapa en Service Bus auktoriseringsregel för namnrymd och kö med en Azure Resource Manager-mall](service-bus-resource-manager-namespace-auth-rule.md)

Lär dig hur du hanterar dessa resurser genom att läsa följande artiklar:

* [Hantera Service Bus med PowerShell](service-bus-manage-with-ps.md)
* [Hantera Service Bus-resurser med Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
