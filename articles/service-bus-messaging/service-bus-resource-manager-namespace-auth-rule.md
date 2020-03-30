---
title: Skapa en servicebussauktoriseringsregel med hjälp av en Azure-mall
description: Skapa en servicebussauktoriseringsregel för namnområde och kö med Azure Resource Manager-mall
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 7f1443a0-5fa8-4d90-8637-1a977ef0b1f0
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: 1bfb2d2d946a85c1d051315fb29a5a63f7a00871
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384933"
---
# <a name="create-a-service-bus-authorization-rule-for-namespace-and-queue-using-an-azure-resource-manager-template"></a>Skapa en servicebussauktoriseringsregel för namnområde och kö med hjälp av en Azure Resource Manager-mall

Den här artikeln visar hur du använder en Azure Resource Manager-mall som skapar en [auktoriseringsregel](service-bus-authentication-and-authorization.md#shared-access-signature) för ett tjänstbussnamnområde och en kö. Artikeln förklarar hur du anger vilka resurser som ska distribueras och hur du definierar parametrar som anges när distributionen körs. Du kan använda den här mallen för dina egna distributioner eller anpassa den så att den uppfyller dina krav.

Mer information om hur du skapar mallar finns i [Redigera Azure Resource Manager-mallar][Authoring Azure Resource Manager templates].

Den fullständiga mallen finns i [mallen Service Bus auktoriseringsregel][Service Bus auth rule template] på GitHub.

> [!NOTE]
> Följande Azure Resource Manager-mallar är tillgängliga för hämtning och distribution.
> 
> * [Skapa ett namnområde för Service Bus](service-bus-resource-manager-namespace.md)
> * [Skapa ett servicebussnamnområde med kö](service-bus-resource-manager-namespace-queue.md)
> * [Skapa ett servicebussnamnområde med ämne och prenumeration](service-bus-resource-manager-namespace-topic.md)
> * [Skapa ett servicebussnamnområde med ämne, prenumeration och regel](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Om du vill söka efter de senaste mallarna besöker du galleriet [Azure Quickstart Templates][Azure Quickstart Templates] och söker efter **Service Bus**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-will-you-deploy"></a>Vad vill du distribuera?

Med den här mallen distribuerar du en servicebussauktoriseringsregel för ett namnområde och entiteten meddelanden (i det här fallet en kö).

Den här mallen använder [SAS (Shared Access Signature)](service-bus-sas.md) för autentisering. SAS gör det möjligt för program att autentisera till Service Bus med hjälp av en åtkomstnyckel som konfigurerats på namnområdet eller på meddelandeentiteten (kön eller ämnet) som specifika rättigheter är associerade med. Du kan sedan använda den här nyckeln för att generera en SAS-token som klienter i sin tur kan använda för att autentisera till Service Bus.

Klicka på följande knapp för att köra distributionen automatiskt:

[![Distribuera till Azure](./media/service-bus-resource-manager-namespace-auth-rule/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F301-servicebus-create-authrule-namespace-and-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Parametrar

Med Azure Resource Manager kan du definiera parametrar för värden som du vill ange när mallen distribueras. Mallen innehåller ett `Parameters` avsnitt som heter som innehåller alla parametervärden. Du bör definiera en parameter för de värden som varierar beroende på det projekt som du distribuerar eller baserat på den miljö som du distribuerar till. Definiera inte parametrar för värden som alltid förblir desamma. Varje parametervärde används i mallen för att definiera de resurser som distribueras.

Mallen definierar följande parametrar.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

Namnet på namnområdet Service Bus som ska skapas.

```json
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="namespaceauthorizationrulename"></a>namnområdeAuktoriseringRuleName

Namnet på auktoriseringsregeln för namnområdet.

```json
"namespaceAuthorizationRuleName ": {
"type": "string"
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName

Namnet på kön i servicebussnamnområdet.

```json
"serviceBusQueueName": {
"type": "string"
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

Skapar ett standardnamnområde för Service Bus av typen **Meddelanden**och en servicebussauktoriseringsregel för namnområde och entitet.

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

För JSON-syntax och egenskaper finns [namnområden](/azure/templates/microsoft.servicebus/namespaces), [köer](/azure/templates/microsoft.servicebus/namespaces/queues)och [AuthorizationRules](/azure/templates/microsoft.servicebus/namespaces/authorizationrules).

## <a name="commands-to-run-deployment"></a>Kommandon för att köra distributionen

[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```powershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat och distribuerat resurser med Hjälp av Azure Resource Manager kan du läsa om hur du hanterar dessa resurser genom att visa följande artiklar:

* [Hantera Service Bus med PowerShell](service-bus-powershell-how-to-provision.md)
* [Hantera servicebussresurser med Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/releases)
* [Service Bus, autentisering och auktorisering](service-bus-authentication-and-authorization.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
[Service Bus auth rule template]: https://github.com/Azure/azure-quickstart-templates/blob/master/301-servicebus-create-authrule-namespace-and-queue/
