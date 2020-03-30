---
title: Skapa prenumeration och regel för Service Bus-ämne med Azure-mall
description: Skapa ett servicebussnamnområde med ämne, prenumeration och regel med Azure Resource Manager-mall
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 9e0aaf58-0214-4bca-bd00-d29c08f9b1bc
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/27/2019
ms.author: spelluru
ms.openlocfilehash: 6cbaf447dfcf06ae11f2282d7d847978297af8b8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384899"
---
# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Skapa ett servicebussnamnområde med ämne, prenumeration och regel med hjälp av en Azure Resource Manager-mall

Den här artikeln visar hur du använder en Azure Resource Manager-mall som skapar ett servicebussnamnområde med ett ämne, en prenumeration och en regel (filter). Artikeln förklarar hur du anger vilka resurser som ska distribueras och hur du definierar parametrar som anges när distributionen körs. Du kan använda den här mallen för dina egna distributioner eller anpassa den så att den uppfyller dina krav

Mer information om att skapa mallar finns i [Redigera Azure Resource Manager-mallar][Authoring Azure Resource Manager templates].

Mer information om praxis och mönster för namngivningskonventioner för Azure-resurser finns i [Rekommenderade namngivningskonventioner för Azure-resurser][Recommended naming conventions for Azure resources].

Den fullständiga mallen finns i [namnområdet Service Bus med ämne, prenumeration och regelmall.][Service Bus namespace with topic, subscription, and rule]

> [!NOTE]
> Följande Azure Resource Manager-mallar är tillgängliga för hämtning och distribution.
> 
> * [Skapa ett servicebussnamnområde med kö- och auktoriseringsregel](service-bus-resource-manager-namespace-auth-rule.md)
> * [Skapa ett servicebussnamnområde med kö](service-bus-resource-manager-namespace-queue.md)
> * [Skapa ett namnområde för Service Bus](service-bus-resource-manager-namespace.md)
> * [Skapa ett servicebussnamnområde med ämne och prenumeration](service-bus-resource-manager-namespace-topic.md)
> 
> Om du vill söka efter de senaste mallarna besöker du [galleriet Azure Quickstart Templates][Azure Quickstart Templates] och söker efter Service Bus.

## <a name="what-do-you-deploy"></a>Vad distribuerar ni?

Med den här mallen distribuerar du ett servicebussnamnområde med ämne, prenumeration och regel (filter).

[Service Bus ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) ger en en-till-många form av kommunikation, i en *publicera / prenumerera* mönster. När du använder ämnen och prenumerationer kommunicerar inte komponenter i ett distribuerat program direkt med varandra, utan de utbyter meddelanden via ett ämne som fungerar som en mellanhand. En prenumeration på ett ämne liknar en virtuell kö som tar emot kopior av meddelanden som har skickats till ämnet. Med ett filter för prenumeration kan du ange vilka meddelanden som ska skickas till ett ämne som ska visas i en viss ämnesprenumeration.

## <a name="what-are-rules-filters"></a>Vad är regler (filter)?

I många fall måste meddelanden som har specifika egenskaper bearbetas på olika sätt. Om du vill aktivera den här anpassade bearbetningen kan du konfigurera prenumerationer för att hitta meddelanden som har specifika egenskaper och sedan utföra ändringar av dessa egenskaper. Även om Service Bus-prenumerationer ser alla meddelanden som skickas till ämnet kan du bara kopiera en delmängd av dessa meddelanden till den virtuella prenumerationskön. Det åstadkoms med hjälp av prenumerationsfilter. Mer information om regler (filter) finns i [Regler och åtgärder](service-bus-queues-topics-subscriptions.md#rules-and-actions).

Klicka på följande knapp för att köra distributionen automatiskt:

[![Distribuera till Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>Parametrar

Med Azure Resource Manager definierar du parametrar för värden som du vill ange när mallen distribueras. Mallen innehåller ett avsnitt som heter `Parameters` och som innehåller alla parametervärden. Definiera en parameter för de värden som varierar beroende på det projekt som du distribuerar eller baserat på den miljö som du distribuerar till. Definiera inte parametrar för värden som aldrig ändras. Varje parametervärde används i mallen för att definiera de resurser som distribueras.

Mallen definierar följande parametrar:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

Namnet på namnområdet Service Bus som ska skapas.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName

Namnet på det ämne som skapats i servicebussnamnområdet.

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName

Namnet på prenumerationen som skapats i servicebussnamnområdet.

```json
"serviceBusSubscriptionName": {
"type": "string"
}
```

### <a name="servicebusrulename"></a>serviceBusRuleName

Namnet på regeln(filtret) som skapats i servicebussnamnområdet.

```json
   "serviceBusRuleName": {
   "type": "string",
  }
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion

Service Bus API-versionen av mallen.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       }
```

## <a name="resources-to-deploy"></a>Resurser som ska distribueras

Skapar ett standardnamnområde för Service Bus av typen **Meddelanden**, med ämne och prenumeration och regler.

```json
 "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
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
                "path": "[parameters('serviceBusTopicName')]"
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {},
                "resources": [{
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusRuleName')]",
                    "type": "Rules",
                    "dependsOn": [
                        "[parameters('serviceBusSubscriptionName')]"
                    ],
                    "properties": {
                        "filterType": "SqlFilter",
                        "sqlFilter": {
                            "sqlExpression": "StoreName = 'Store1'",
                            "requiresPreprocessing": "false"
                        },
                        "action": {
                            "sqlExpression": "set FilterTag = 'true'"
                        }
                    }
                }]
            }]
        }]
    }]
```

För JSON-syntax och egenskaper finns [i namnområden,](/azure/templates/microsoft.servicebus/namespaces) [ämnen,](/azure/templates/microsoft.servicebus/namespaces/topics) [prenumerationer](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions)och [regler](/azure/templates/microsoft.servicebus/namespaces/topics/subscriptions/rules).

## <a name="commands-to-run-deployment"></a>Kommandon för att köra distributionen

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell-interactive
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du hanterar dessa resurser genom att visa följande artiklar:

* [Hantera Azure Service Bus](service-bus-management-libraries.md)
* [Hantera Service Bus med PowerShell](service-bus-manage-with-ps.md)
* [Hantera servicebussresurser med Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Recommended naming conventions for Azure resources]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging
[Service Bus namespace with topic, subscription, and rule]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
