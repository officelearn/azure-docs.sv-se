---
title: Skapa Azure Service Bus-resurser med hjälp av mallar
description: Använda Azure Resource Manager-mallar för att automatisera skapandet av Service Bus-resurser
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: 24f6a207-0fa4-49cf-8a58-963f9e2fd655
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 09/11/2018
ms.author: spelluru
ms.openlocfilehash: 9bc784ee57b9bde393408cbefa9a197aebc59b08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264466"
---
# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Skapa servicebussresurser med Azure Resource Manager-mallar

I den här artikeln beskrivs hur du skapar och distribuerar Service Bus-resurser med Hjälp av Azure Resource Manager-mallar, PowerShell och Service Bus-resursprovidern.

Azure Resource Manager-mallar hjälper dig att definiera de resurser som ska distribueras för en lösning och ange parametrar och variabler som gör att du kan ange värden för olika miljöer. Mallen är skriven i JSON och består av uttryck som du kan använda för att konstruera värden för distributionen. Detaljerad information om hur du skriver Azure Resource Manager-mallar och en diskussion om mallformatet finns i [strukturen och syntaxen för Azure Resource Manager-mallar](../azure-resource-manager/templates/template-syntax.md).

> [!NOTE]
> Exemplen i den här artikeln visar hur du använder Azure Resource Manager för att skapa ett servicebussnamnområde och meddelandeentitet (kö). Andra mallexempel finns i [galleriet Azure Quickstart Templates][Azure Quickstart Templates gallery] och söker efter **Service Bus**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="service-bus-resource-manager-templates"></a>Mallar för Service Bus Resource Manager

Dessa Service Bus Azure Resource Manager-mallar är tillgängliga för hämtning och distribution. Klicka på följande länkar för mer information om var och en, med länkar till mallarna på GitHub:

* [Skapa ett namnområde för Service Bus](service-bus-resource-manager-namespace.md)
* [Skapa ett servicebussnamnområde med kö](service-bus-resource-manager-namespace-queue.md)
* [Skapa ett servicebussnamnområde med ämne och prenumeration](service-bus-resource-manager-namespace-topic.md)
* [Skapa ett servicebussnamnområde med kö- och auktoriseringsregel](service-bus-resource-manager-namespace-auth-rule.md)
* [Skapa ett servicebussnamnområde med ämne, prenumeration och regel](service-bus-resource-manager-namespace-topic-with-rule.md)

## <a name="deploy-with-powershell"></a>Distribuera med PowerShell

I följande procedur beskrivs hur du använder PowerShell för att distribuera en Azure Resource Manager-mall som skapar ett namnområde för servicebuss på standardnivå och en kö inom det namnområdet. Det här exemplet baseras på [namnområdet Skapa en servicebuss med kömall.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue) Det ungefärliga arbetsflödet är följande:

1. Installera PowerShell.
2. Skapa mallen och (eventuellt) en parameterfil.
3. Logga in på ditt Azure-konto i PowerShell.
4. Skapa en ny resursgrupp om det inte finns någon.
5. Testa distributionen.
6. Om du vill kan du ställa in distributionsläget.
7. Distribuera mallen.

Fullständig information om hur du distribuerar Azure Resource Manager-mallar finns i [Distribuera resurser med Azure Resource Manager-mallar][Deploy resources with Azure Resource Manager templates].

### <a name="install-powershell"></a>Installera PowerShell

Installera Azure PowerShell genom att följa instruktionerna i [Komma igång med Azure PowerShell](/powershell/azure/get-started-azureps).

### <a name="create-a-template"></a>Skapa en mall

Klona databasen eller kopiera [201-servicebus-create-queue-mallen](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) från GitHub:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
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

### <a name="create-a-parameters-file-optional"></a>Skapa en parameterfil (valfritt)

Om du vill använda en valfri parameterfil kopierar du filen [201-servicebus-create-queue.](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json) Ersätt värdet `serviceBusNamespaceName` för med namnet på det servicebussnamnområde som du vill skapa `serviceBusQueueName` i den här distributionen och ersätt värdet för med namnet på den kö som du vill skapa.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
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

Mer information finns i artikeln [Parametrar.](../azure-resource-manager/templates/parameter-files.md)

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Logga in på Azure och ange Azure-prenumerationen

Kör följande kommando från en PowerShell-prompt:

```powershell
Connect-AzAccount
```

Du uppmanas att logga in på ditt Azure-konto. När du har loggat in kör du följande kommando för att visa dina tillgängliga prenumerationer:

```powershell
Get-AzSubscription
```

Det här kommandot returnerar en lista över tillgängliga Azure-prenumerationer. Välj en prenumeration för den aktuella sessionen genom att köra följande kommando. Ersätt `<YourSubscriptionId>` med GUID för den Azure-prenumeration som du vill använda:

```powershell
Set-AzContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Ange resursgruppen

Om du inte har en befintlig resursgrupp skapar du en ny resursgrupp med kommandot **New-AzResourceGroup.** Ange namnet på den resursgrupp och plats som du vill använda. Ett exempel:

```powershell
New-AzResourceGroup -Name MyDemoRG -Location "West US"
```

Om det lyckas visas en sammanfattning av den nya resursgruppen.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Testa distributionen

Verifiera distributionen genom `Test-AzResourceGroupDeployment` att köra cmdleten. När du testar distributionen anger du parametrar precis som när du kör distributionen.

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>Skapa distributionen

Om du vill skapa `New-AzResourceGroupDeployment` den nya distributionen kör du cmdleten och anger nödvändiga parametrar när du uppmanas att göra det. Parametrarna innehåller ett namn för distributionen, namnet på resursgruppen och sökvägen eller URL:en till mallfilen. Om parametern **Läge** inte har angetts används standardvärdet **för Inkrementellt.** Mer information finns i [Inkrementella och fullständiga distributioner](../azure-resource-manager/templates/deployment-modes.md).

Följande kommandotolkar dig för de tre parametrarna i PowerShell-fönstret:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Om du vill ange en parameterfil i stället använder du följande kommando:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Du kan också använda infogade parametrar när du kör distributions-cmdlet. Kommandot är följande:

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Om du vill köra en [fullständig](../azure-resource-manager/templates/deployment-modes.md) distribution ställer du in parametern **Läge** till **Slutför:**

```powershell
New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="verify-the-deployment"></a>Verifiera distributionen
Om resurserna har distribuerats visas en sammanfattning av distributionen i PowerShell-fönstret:

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
Du har nu sett det grundläggande arbetsflödet och kommandona för distribution av en Azure Resource Manager-mall. Mer detaljerad information finns i följande länkar:

* [Översikt över Azure Resource Manager][Azure Resource Manager overview]
* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell][Deploy resources with Azure Resource Manager templates]
* [Skapa Azure Resource Manager-mallar](../azure-resource-manager/templates/template-syntax.md)
* [Resurstyper för Microsoft.ServiceBus](/azure/templates/microsoft.servicebus/allversions)

[Azure Resource Manager overview]: ../azure-resource-manager/management/overview.md
[Deploy resources with Azure Resource Manager templates]: ../azure-resource-manager/templates/deploy-powershell.md
[Azure Quickstart Templates gallery]: https://azure.microsoft.com/documentation/templates/?term=service+bus
