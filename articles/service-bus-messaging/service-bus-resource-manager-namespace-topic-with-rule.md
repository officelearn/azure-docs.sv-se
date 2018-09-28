---
title: Skapa Azure Service Bus ämnesprenumeration och regel med hjälp av Azure Resource Manager-mall | Microsoft Docs
description: Skapa ett Service Bus-namnområde med ämne, prenumeration och regel med hjälp av Azure Resource Manager-mall
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
ms.date: 09/11/2018
ms.author: spelluru
ms.openlocfilehash: 7774d67d02b2b0f0080cbff2ffc3606b99450f26
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2018
ms.locfileid: "47404969"
---
# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Skapa ett Service Bus-namnområde med ämne, prenumeration och regel med en Azure Resource Manager-mall

Den här artikeln visar hur du använder en Azure Resource Manager-mall som skapar ett Service Bus-namnområde med ämne, prenumeration och regel (filter). Den här artikeln beskriver hur du ange vilka resurser distribueras och hur du definierar parametrar som anges när distributionen körs. Du kan använda den här mallen för dina egna distributioner eller anpassa den så att den uppfyller dina krav

Mer information om att skapa mallar finns i [Redigera Azure Resource Manager-mallar][Authoring Azure Resource Manager templates].

Läs mer om praxis och mönster på Azure-resurser namngivningskonventioner, [rekommenderade namnkonventioner för Azure-resurser][Recommended naming conventions for Azure resources].

Läs den fullständiga mallen, den [Service Bus-namnområde med ämne, prenumeration och regel] [ Service Bus namespace with topic, subscription, and rule] mall.

> [!NOTE]
> Följande Azure Resource Manager-mallar är tillgängliga för hämtning och distribution.
> 
> * [Skapa ett Service Bus-namnområde med kön och auktorisering](service-bus-resource-manager-namespace-auth-rule.md)
> * [Skapa ett Service Bus-namnområde med kö](service-bus-resource-manager-namespace-queue.md)
> * [Skapa ett Service Bus-namnområde](service-bus-resource-manager-namespace.md)
> * [Skapa ett Service Bus-namnområde med ämne och en prenumeration](service-bus-resource-manager-namespace-topic.md)
> 
> Om du vill söka efter de senaste mallarna, Besök den [Azure-Snabbstartsmallar] [ Azure Quickstart Templates] galleriet och söka efter Service Bus.
> 
> 

## <a name="what-do-you-deploy"></a>Vad du distribuera?

Med den här mallen kan du distribuera ett Service Bus-namnområde med ämne, prenumeration och regel (filter).

[Service Bus-ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) tillhandahålla en en-till-många-kommunikation i en *Publicera/prenumerera på* mönster. När du använder ämnen och prenumerationer, kommunicerar komponenter i ett distribuerat program inte direkt med varandra, utbyter de istället meddelanden via ämne som fungerar som en mellanhand. En prenumeration till ett ämne liknar en virtuell kö som tar emot kopior av meddelanden som skickades till ämnet. Ett filter på prenumerationen kan du ange vilka meddelanden som skickas till ett ämne bör visas inom en viss ämnesprenumeration.

## <a name="what-are-rules-filters"></a>Vad är regler (filter)?

Meddelanden som har specifika egenskaper måste bearbetas på olika sätt i många scenarier. Om du vill aktivera den här anpassade bearbetning, kan du konfigurera prenumerationer för att hitta meddelanden som har specifika egenskaper och utför ändringar i dessa egenskaper. Du kan bara kopiera en delmängd av de här meddelandena till virtuella prenumerationskön även om Service Bus-prenumerationer visas alla meddelanden som skickas till ämnet. Det åstadkoms med hjälp av prenumerationsfilter. Mer information om regler (filter) finns [regler och åtgärder](service-bus-queues-topics-subscriptions.md#rules-and-actions).

Klicka på följande knapp för att köra distributionen automatiskt:

[![Distribuera till Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>Parametrar

Med Azure Resource Manager kan definiera parametrar för värden som du vill ange när mallen distribueras. Mallen innehåller ett avsnitt som heter `Parameters` och som innehåller alla parametervärden. Definiera en parameter för de värden som varierar utifrån det projekt som du distribuerar eller utifrån den miljö som du distribuerar till. Definiera inte parametrar för värden som aldrig ändras. Varje parametervärde används i mallen för att definiera de resurser som distribueras.

Mallen definierar följande parametrar:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
Namnet på Service Bus-namnområdet för att skapa.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName
Namnet på det ämne som skapats i Service Bus-namnområdet.

```json
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName
Namnet på den prenumeration som skapats i Service Bus-namnområdet.

```json
"serviceBusSubscriptionName": {
"type": "string"
}
```
### <a name="servicebusrulename"></a>serviceBusRuleName
Namnet på rule(filter) som skapats i Service Bus-namnområdet.

```json
   "serviceBusRuleName": {
   "type": "string",
  }
```
### <a name="servicebusapiversion"></a>serviceBusApiVersion
Service Bus-API-versionen av mallen.

```json
"serviceBusApiVersion": { 
       "type": "string", 
       "defaultValue": "2017-04-01", 
       "metadata": { 
           "description": "Service Bus ApiVersion used by the template" 
       }
```
## <a name="resources-to-deploy"></a>Resurser som ska distribueras
Skapar en standard Service Bus-namnområde av typen **Messaging**med ämnet och prenumerationen och regler.

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

## <a name="commands-to-run-deployment"></a>Kommandon för att köra distributionen
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```powershell
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI
```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="next-steps"></a>Nästa steg
Nu när du har skapat och distribuerat resurser med hjälp av Azure Resource Manager, lär du dig hur du hanterar dessa resurser genom att visa de här artiklarna:

* [Hantera Azure Service Bus](service-bus-management-libraries.md)
* [Hantera Service Bus med PowerShell](service-bus-manage-with-ps.md)
* [Hantera Service Bus-resurser med Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Recommended naming conventions for Azure resources]: ../guidance/guidance-naming-conventions.md
[Service Bus namespace with topic, subscription, and rule]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md

