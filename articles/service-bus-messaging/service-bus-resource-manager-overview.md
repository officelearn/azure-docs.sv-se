---
title: Skapa Azure Service Bus-resurser med hjälp av Resource Manager-mallar | Microsoft Docs
description: Använd Azure Resource Manager-mallar för att automatisera skapandet av Service Bus-resurser
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 24f6a207-0fa4-49cf-8a58-963f9e2fd655
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 04/11/2018
ms.author: sethm
ms.openlocfilehash: c8d84de608ccf3d9a9293c20c07c10a00b73da68
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
ms.locfileid: "31598293"
---
# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Skapa Service Bus-resurser med hjälp av Azure Resource Manager-mallar

Den här artikeln beskriver hur du skapar och distribuerar Service Bus-resurser med hjälp av Azure Resource Manager-mallar, PowerShell och Service Bus-resursprovidern.

Azure Resource Manager-mallar hjälpa dig att definiera vilka resurser för att distribuera en lösning och ange parametrar och variabler som gör att du kan ange värden för olika miljöer. Mallen skrivs i JSON och består av uttryck som du kan använda för att skapa värden för din distribution. Detaljerad information om hur du skriver Azure Resource Manager-mallar och en beskrivning av mallen formatet finns [struktur och syntaxen för Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md).

> [!NOTE]
> Exemplen i den här artikeln visar hur du använder Azure Resource Manager för att skapa ett namnområde för Service Bus och meddelandeentitet (kö). Andra mall-exempel finns i [Azure Quickstart mallgalleriet] [ Azure Quickstart Templates gallery] och Sök efter **Service Bus**.
>
>

## <a name="service-bus-resource-manager-templates"></a>Service Bus Resource Manager-mallar

Dessa Service Bus Azure Resource Manager-mallar är tillgängliga för hämtning och distribution. Klicka på länken nedan för mer information om var och en, med länkar till mallar på GitHub:

* [Skapa ett namnområde för Service Bus](service-bus-resource-manager-namespace.md)
* [Skapa ett namnområde för Service Bus med kön](service-bus-resource-manager-namespace-queue.md)
* [Skapa ett namnområde för Service Bus med ämnet och prenumerationen](service-bus-resource-manager-namespace-topic.md)
* [Skapa ett namnområde för Service Bus med kön och auktorisering regel](service-bus-resource-manager-namespace-auth-rule.md)
* [Skapa ett namnområde för Service Bus med ämne, prenumeration och regel](service-bus-resource-manager-namespace-topic-with-rule.md)

## <a name="deploy-with-powershell"></a>Distribuera med PowerShell

Följande procedur beskriver hur du använder PowerShell för att distribuera en Azure Resource Manager-mall som skapar en standardnivån Service Bus-namnrymd och en kö inom det här namnområdet. Det här exemplet är baserad på den [skapa ett namnområde för Service Bus med kön](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue) mall. Ungefärlig arbetsflödet är följande:

1. Installera PowerShell.
2. Skapa mallen och (valfritt) en parameterfil.
3. Logga in på ditt Azure-konto i PowerShell.
4. Skapa en ny resursgrupp om det inte finns.
5. Testa distributionen.
6. Om du vill ange ett distribution-läge.
7. Distribuera mallen.

Fullständig information om hur du distribuerar Azure Resource Manager-mallar finns [distribuera resurser med Azure Resource Manager-mallar][Deploy resources with Azure Resource Manager templates].

### <a name="install-powershell"></a>Installera PowerShell

Installera Azure PowerShell genom att följa anvisningarna i [komma igång med Azure PowerShell](/powershell/azure/get-started-azureps).

### <a name="create-a-template"></a>Skapa en mall

Klona lagringsplatsen eller kopiera den [201-servicebus-skapa-queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) mall från GitHub:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Service Bus namespace"
      }
    },
    "serviceBusQueueName": {
      "type": "string",
      "metadata": {
        "description": "Name of the Queue"
      }
    }
  },
  "variables": {
    "defaultSASKeyName": "RootManageSharedAccessKey",
    "authRuleResourceId": "[resourceId('Microsoft.ServiceBus/namespaces/authorizationRules', parameters('serviceBusNamespaceName'), variables('defaultSASKeyName'))]",
    "sbVersion": "2017-04-01"
  },
  "resources": [
    {
      "apiVersion": "2017-04-01",
      "name": "[parameters('serviceBusNamespaceName')]",
      "type": "Microsoft.ServiceBus/Namespaces",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {},
      "resources": [
        {
          "apiVersion": "2017-04-01",
          "name": "[parameters('serviceBusQueueName')]",
          "type": "Queues",
          "dependsOn": [
            "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
          ],
          "properties": {
            "lockDuration": "PT5M",
            "maxSizeInMegabytes": "1024",
            "requiresDuplicateDetection": "false",
            "requiresSession": "false",
            "defaultMessageTimeToLive": "P10675199DT2H48M5.4775807S",
            "deadLetteringOnMessageExpiration": "false",
            "duplicateDetectionHistoryTimeWindow": "PT10M",
            "maxDeliveryCount": "10",
            "autoDeleteOnIdle": "P10675199DT2H48M5.4775807S",
            "enablePartitioning": "false",
            "enableExpress": "false"
          }
        }
      ]
    }
  ],
  "outputs": {
    "NamespaceConnectionString": {
      "type": "string",
      "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryConnectionString]"
    },
    "SharedAccessPolicyPrimaryKey": {
      "type": "string",
      "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryKey]"
    }
  }
}
```

### <a name="create-a-parameters-file-optional"></a>Skapa en fil med parametrar (valfritt)

Om du vill använda en fil för valfria parametrar, kopiera den [201-servicebus-skapa-queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json) fil. Ersätt värdet för `serviceBusNamespaceName` med namnet på Service Bus-namnområde som du vill skapa i den här distributionen och Ersätt värdet för `serviceBusQueueName` med namnet på den kö som du vill skapa.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "value": "<myNamespaceName>"
        },
        "serviceBusQueueName": {
            "value": "<myQueueName>"
        },
        "serviceBusApiVersion": {
            "value": "2017-04-01"
        }
    }
}
```

Mer information finns i [parametrar](../azure-resource-manager/resource-group-template-deploy.md#parameter-files) artikel.

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Logga in på Azure och ange Azure-prenumeration

Kör följande kommando från en PowerShell-kommandotolk:

```powershell
Connect-AzureRmAccount
```

Du uppmanas att logga in på ditt Azure-konto. Kör följande kommando för att visa tillgängliga prenumerationer efter inloggningen:

```powershell
Get-AzureRMSubscription
```

Det här kommandot returnerar en lista över tillgängliga Azure-prenumerationer. Välj en prenumeration för den aktuella sessionen genom att köra följande kommando. Ersätt `<YourSubscriptionId>` med GUID för Azure-prenumerationen du vill använda:

```powershell
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Ange resursgruppen.

Om du inte har en befintlig resurs, skapa en ny resursgrupp med det ** New-AzureRmResourceGroup ** kommando. Ange namnet på den resursgrupp och plats som du vill använda. Exempel:

```powershell
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

Om detta lyckas visas en sammanfattning av den nya resursgruppen.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Testa distributionen

Verifiera distributionen genom att köra den `Test-AzureRmResourceGroupDeployment` cmdlet. När du testar distributionen, ange parametrar precis som vid körning av distributionen.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>Skapa en distribution

Om du vill skapa den nya distributionen, kör den `New-AzureRmResourceGroupDeployment` cmdlet, och ange nödvändiga parametrar när du tillfrågas. Parametrarna inkluderar ett namn för din distribution, namnet på din resursgrupp och sökväg eller URL till mallfilen. Om den **läge** parametern anges standardvärdet **stegvis** används. Mer information finns i [inkrementell och fullständig distributioner](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments).

Följande kommando efterfrågar tre parametrar i PowerShell-fönstret:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Om du vill ange en fil med parametrar i stället använder du följande kommando:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Du kan också använda infogade parametrar när du kör cmdlet för distribution. Kommandot är följande:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Att köra en [fullständig](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) distribution, ange den **läge** parameter till **Slutför**:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="verify-the-deployment"></a>Kontrollera distributionen
Om resurserna som har distribuerats visas en sammanfattning av distributionen i PowerShell-fönstret:

```powershell
DeploymentName    : MyDemoDeployment
ResourceGroupName : MyDemoRG
ProvisioningState : Succeeded
Timestamp         : 4/19/2017 10:38:30 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    serviceBusNamespaceName  String             <namespaceName>
                    serviceBusQueueName  String                 <queueName>
                    serviceBusApiVersion  String                2017-04-01

```

## <a name="next-steps"></a>Nästa steg
Nu har du sett grundläggande arbetsflöde och kommandon för att distribuera en Azure Resource Manager-mall. Mer detaljerad information finns i följande länkar:

* [Översikt över Azure Resource Manager][Azure Resource Manager overview]
* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell][Deploy resources with Azure Resource Manager templates]
* [Skapa Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md)

[Azure Resource Manager overview]: ../azure-resource-manager/resource-group-overview.md
[Deploy resources with Azure Resource Manager templates]: ../azure-resource-manager/resource-group-template-deploy.md
[Azure Quickstart Templates gallery]: https://azure.microsoft.com/documentation/templates/?term=service+bus
