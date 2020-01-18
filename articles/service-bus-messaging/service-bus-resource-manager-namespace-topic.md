---
title: Avsnittet Skapa Azure Service Bus namn område med hjälp av en mall
description: 'Snabb start: skapa ett Service Bus-namnområde med ämne och prenumeration med Azure Resource Manager-mall'
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: d3d55200-5c60-4b5f-822d-59974cafff0e
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 98ca0f95f5b2aab9651b97442f1f38651767f1df
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264500"
---
# <a name="quickstart-create-a-service-bus-namespace-with-topic-and-subscription-using-an-azure-resource-manager-template"></a>Snabb start: skapa ett Service Bus-namnområde med ämnet och prenumerationen med hjälp av en Azure Resource Manager mall

Den här artikeln visar hur du använder en Azure Resource Manager-mall som skapar ett Service Bus-namnområde och ett ämne och en prenumeration inom det namn området. Artikeln förklarar hur du anger vilka resurser som distribueras och hur du definierar parametrar som anges när distributionen körs. Du kan använda den här mallen för dina egna distributioner eller anpassa den så att den uppfyller dina krav

Mer information om hur du skapar mallar finns i [redigera Azure Resource Manager mallar][Authoring Azure Resource Manager templates].

En fullständig mall finns i [avsnittet Service Bus namnrymd med ämne och prenumerations][Service Bus namespace with topic and subscription] mall.

> [!NOTE]
> Följande Azure Resource Manager mallar är tillgängliga för hämtning och distribution.
> 
> * [Skapa ett Service Bus-namnområde](service-bus-resource-manager-namespace.md)
> * [Skapa ett Service Bus-namnområde med kö](service-bus-resource-manager-namespace-queue.md)
> * [Skapa ett Service Bus-namnområde med kö och auktoriseringsregel](service-bus-resource-manager-namespace-auth-rule.md)
> * [Skapa ett Service Bus-namnområde med ämne, prenumeration och regel](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Om du vill söka efter de senaste mallarna går du till galleriet för [Azure snabb starts mallar][Azure Quickstart Templates] och söker efter **Service Bus**.
> 
> 

## <a name="what-do-you-deploy"></a>Vad distribuerar du?

Med den här mallen distribuerar du ett Service Bus-namnområde med ämne och prenumeration.

[Service Bus ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) ger en en-till-många-form av kommunikation i ett mönster för *publicering/prenumeration* .

Klicka på följande knapp för att köra distributionen automatiskt:

[![Distribuera till Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-and-subscription%2Fazuredeploy.json)

## <a name="parameters"></a>Parametrar

Med Azure Resource Manager kan du definiera parametrar för värden som du vill ange när mallen distribueras. Mallen innehåller ett avsnitt som heter `Parameters` som innehåller alla parameter värden. Definiera en parameter för de värden som varierar beroende på vilket projekt du distribuerar eller baserat på den miljö som du distribuerar till. Definiera inte parametrar för värden som aldrig ändras. Varje parametervärde används i mallen för att definiera de resurser som distribueras.

Mallen definierar följande parametrar:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
Namnet på Service Bus namn området som ska skapas.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName
Namnet på ämnet som skapats i Service Bus namn området.

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName
Namnet på prenumerationen som skapades i Service Bus namn området.

```json
"serviceBusSubscriptionName": {
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
Skapar ett standard Service Bus-namnområde av typ **meddelanden**, med ämnet och prenumerationen.

```json
"resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "Standard",
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]",
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {}
            }]
        }]
    }]
```

Information om JSON-syntax och egenskaper finns i [namn områden](/azure/templates/microsoft.servicebus/namespaces), [ämnen](/azure/templates/microsoft.servicebus/namespaces/topics)och [prenumerationer](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions).

## <a name="commands-to-run-deployment"></a>Kommandon för att köra distributionen
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```powershell
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI
```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## <a name="next-steps"></a>Nästa steg
Nu när du har skapat och distribuerat resurser med Azure Resource Manager kan du läsa mer om hur du hanterar resurserna genom att läsa följande artiklar:

* [Hantera Service Bus med PowerShell](service-bus-manage-with-ps.md)
* [Hantera Service Bus-resurser med Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Service Bus namespace with topic and subscription]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-and-subscription/
