---
title: "Skapa Azure Service Bus-namnrymd och kö med Azure Resource Manager-mall | Microsoft Docs"
description: "Skapa ett Service Bus-namnområde och en kö med Azure Resource Manager-mall"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: a6bfb5fd-7b98-4588-8aa1-9d5f91b599b6
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 08/07/2017
ms.author: sethm;shvija
ms.openlocfilehash: 4358130a2c8e897a0fdd1f9560f766d6e22db4d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>Skapa ett Service Bus-namnområde och en kö med en Azure Resource Manager-mall

Den här artikeln visar hur du använder en Azure Resource Manager-mall som skapar en Service Bus-namnrymd och en kö inom det här namnområdet. Du kommer lära dig hur du definierar vilka resurser har distribuerats och hur du definierar parametrar som anges när distributionen körs. Du kan använda den här mallen för dina egna distributioner eller anpassa den så att den uppfyller dina krav.

Mer information om hur du skapar mallar finns [redigera Azure Resource Manager-mallar][Authoring Azure Resource Manager templates].

Den fullständiga mallen finns i [mallen för Service Bus-namnområde och kön] [ Service Bus namespace and queue template] på GitHub.

> [!NOTE]
> Följande Azure Resource Manager-mallar är tillgängliga för hämtning och distribution.
> 
> * [Skapa ett namnområde för Service Bus med kön och auktorisering regel](service-bus-resource-manager-namespace-auth-rule.md)
> * [Skapa ett namnområde för Service Bus med ämnet och prenumerationen](service-bus-resource-manager-namespace-topic.md)
> * [Skapa ett namnområde för Service Bus](service-bus-resource-manager-namespace.md)
> * [Skapa ett namnområde för Service Bus med ämne, prenumeration och regel](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Om du vill söka efter de senaste mallarna finns i [Azure-Snabbstartsmallar] [ Azure Quickstart Templates] galleriet och Sök efter ”Service Bus”.
> 
> 

## <a name="what-will-you-deploy"></a>Vad vill du distribuera?

Med den här mallen distribuerar du en Service Bus-namnrymd med en kö.

[Service Bus-köer](service-bus-queues-topics-subscriptions.md#queues) erbjuder First In, First Out (FIFO) meddelandeleverans till en eller flera konkurrerande konsumenter.

Klicka på följande knapp för att köra distributionen automatiskt:

[![Distribuera till Azure](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Parametrar

Med Azure Resource Manager kan du definiera parametrar för värden som du vill ange när mallen distribueras. Mallen innehåller ett avsnitt som heter `Parameters` som innehåller alla parametervärdena. Du bör definiera en parameter för de värden som varierar baserat på projektet som du distribuerar eller baserat på miljön som du distribuerar till. Definiera inte parametrar för värden som alltid inte ändras. Varje parametervärde används i mallen för att definiera de resurser som distribueras.

Mallen definierar följande parametrar.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
Namnet på namnområdet för Service Bus för att skapa.

```json
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName
Namnet på den kö som skapats i Service Bus-namnrymd.

```json
"serviceBusQueueName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion
Service Bus-API-versionen av mallen.

```json
"serviceBusApiVersion": {
"type": "string"
}
```

## <a name="resources-to-deploy"></a>Resurser som ska distribueras
Skapar en standard Service Bus-namnrymd av typen **Messaging**, med en kö.

```json
"resources ": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusQueueName')]",
            "type": "Queues",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusQueueName')]",
            }
        }]
    }]
```

## <a name="commands-to-run-deployment"></a>Kommandon för att köra distributionen
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="next-steps"></a>Nästa steg
Nu när du har skapat och distribuerat resurser med Azure Resource Manager, lär du dig hur du hanterar dessa resurser genom att visa dessa artiklar:

* [Hantera Service Bus med PowerShell](service-bus-manage-with-ps.md)
* [Hantera Service Bus-resurser med Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace and queue template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
