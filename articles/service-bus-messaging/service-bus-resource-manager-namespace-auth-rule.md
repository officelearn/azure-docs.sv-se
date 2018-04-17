---
title: Skapa en regel för auktorisering av Service Bus med hjälp av Azure Resource Manager-mall | Microsoft Docs
description: Skapa en regel för auktorisering av Service Bus för namnområdet och kön med Azure Resource Manager-mall
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 7f1443a0-5fa8-4d90-8637-1a977ef0b1f0
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 04/11/2018
ms.author: sethm
ms.openlocfilehash: e29045f43f5b80ffc66d7b10bac39f3d8d03ae3c
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="create-a-service-bus-authorization-rule-for-namespace-and-queue-using-an-azure-resource-manager-template"></a>Skapa en regel för auktorisering av Service Bus för namnområdet och kö med en Azure Resource Manager-mall

Den här artikeln visar hur du använder en Azure Resource Manager-mall som skapar en [auktoriseringsregeln](service-bus-authentication-and-authorization.md#shared-access-signature-authentication) för en Service Bus-namnrymd och kön. Artikeln beskriver hur för att ange vilka resurser har distribuerats och hur du definierar parametrar som anges när distributionen körs. Du kan använda den här mallen för dina egna distributioner eller anpassa den så att den uppfyller dina krav.

Mer information om hur du skapar mallar finns [redigera Azure Resource Manager-mallar][Authoring Azure Resource Manager templates].

Den fullständiga mallen finns i [regelmall för Service Bus-auktorisering] [ Service Bus auth rule template] på GitHub.

> [!NOTE]
> Följande Azure Resource Manager-mallar är tillgängliga för hämtning och distribution.
> 
> * [Skapa ett namnområde för Service Bus](service-bus-resource-manager-namespace.md)
> * [Skapa ett namnområde för Service Bus med kön](service-bus-resource-manager-namespace-queue.md)
> * [Skapa ett namnområde för Service Bus med ämnet och prenumerationen](service-bus-resource-manager-namespace-topic.md)
> * [Skapa ett namnområde för Service Bus med ämne, prenumeration och regel](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Om du vill söka efter de senaste mallarna finns i [Azure-Snabbstartsmallar] [ Azure Quickstart Templates] galleriet och Sök efter **Service Bus**.
> 
> 

## <a name="what-will-you-deploy"></a>Vad vill du distribuera?

Med den här mallen kan du distribuera en Service Bus auktoriseringsregel för ett namnområde och meddelandeentitet (i det här fallet en kö).

Den här mallen använder [delade signatur åtkomst (SAS)](service-bus-sas.md) för autentisering. SAS gör det möjligt för program att autentisera till Service Bus med hjälp av en åtkomstnyckel som konfigurerats på namnområdet eller meddelandeentitet (kö eller ett ämne) som specifika rättigheter associeras. Du kan sedan använda denna nyckel för att generera en SAS-token som klienter i sin tur kan använda för att autentisera till Service Bus.

Klicka på följande knapp för att köra distributionen automatiskt:

[![Distribuera till Azure](./media/service-bus-resource-manager-namespace-auth-rule/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F301-servicebus-create-authrule-namespace-and-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Parametrar

Med Azure Resource Manager kan du definiera parametrar för värden som du vill ange när mallen distribueras. Mallen innehåller ett avsnitt som heter `Parameters` som innehåller alla parametervärdena. Du bör definiera en parameter för de värden som varierar baserat på projektet som du distribuerar eller baserat på miljön som du distribuerar till. Definiera inte parametrar för värden som alltid inte ändras. Varje parametervärde används i mallen för att definiera de resurser som distribueras.

Mallen definierar följande parametrar.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
Namnet på namnområdet för Service Bus för att skapa.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="namespaceauthorizationrulename"></a>namespaceAuthorizationRuleName
Namnet på regeln för namnområdet.

```json
"namespaceAuthorizationRuleName ": {
"type": "string"
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName
Namnet på kön i Service Bus-namnrymd.

```json
"serviceBusQueueName": {
"type": "string"
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
Skapar en standard Service Bus-namnrymd av typen **Messaging**, och en Service Bus auktoriseringsregel för namnområdet och entiteten.

```json
"resources": [
        {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[variables('location')]",
            "kind": "Messaging",
            "sku": {
                "name": "Standard",
            },
            "resources": [
                {
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusQueueName')]",
                    "type": "Queues",
                    "dependsOn": [
                        "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('serviceBusQueueName')]"
                    },
                    "resources": [
                        {
                            "apiVersion": "[variables('sbVersion')]",
                            "name": "[parameters('queueAuthorizationRuleName')]",
                            "type": "authorizationRules",
                            "dependsOn": [
                                "[parameters('serviceBusQueueName')]"
                            ],
                            "properties": {
                                "Rights": ["Listen"]
                            }
                        }
                    ]
                }
            ]
        }, {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[variables('namespaceAuthRuleName')]",
            "type": "Microsoft.ServiceBus/namespaces/authorizationRules",
            "dependsOn": ["[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"],
            "location": "[resourceGroup().location]",
            "properties": {
                "Rights": ["Send"]
            }
        }
    ]
```

## <a name="commands-to-run-deployment"></a>Kommandon för att köra distributionen
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI
```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="next-steps"></a>Nästa steg
Nu när du har skapat och distribuerat resurser med Azure Resource Manager, lär du dig hur du hanterar dessa resurser genom att visa dessa artiklar:

* [Hantera Service Bus med PowerShell](service-bus-powershell-how-to-provision.md)
* [Hantera Service Bus-resurser med Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)
* [Service Bus-autentisering och auktorisering](service-bus-authentication-and-authorization.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Service Bus auth rule template]: https://github.com/Azure/azure-quickstart-templates/blob/master/301-servicebus-create-authrule-namespace-and-queue/
