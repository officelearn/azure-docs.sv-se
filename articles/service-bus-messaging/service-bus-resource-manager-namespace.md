---
title: Skapa namnområde för Service Bus-meddelanden med hjälp av Azure Resource Manager-mall | Microsoft Docs
description: Använd Azure Resource Manager-mall för att skapa ett namnområde för Service Bus-meddelanden
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/23/2019
ms.author: spelluru
ms.openlocfilehash: e925ea91518d8f093cd270e238b7ffd09674e726
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54844100"
---
# <a name="create-a-service-bus-namespace-using-an-azure-resource-manager-template"></a>Skapa ett Service Bus-namnområde med en Azure Resource Manager-mall
I den här snabbstarten skapar du en Azure Resource Manager-mall som skapar ett Service Bus-namnområde av typen **Messaging** med en **Standard** SKU. Artikeln definierar också de parametrar som har angetts för körning av distributionen. Du kan använda den här mallen för dina egna distributioner eller anpassa den så att den uppfyller dina krav. Mer information om att skapa mallar finns i [Redigera Azure Resource Manager-mallar][Authoring Azure Resource Manager templates]. Läs den fullständiga mallen, den [mall för Service Bus-namnområde] [ Service Bus namespace template] på GitHub.

> [!NOTE]
> Följande Azure Resource Manager-mallar är tillgängliga för hämtning och distribution. 
> 
> * [Skapa ett Service Bus-namnområde med kö](service-bus-resource-manager-namespace-queue.md)
> * [Skapa ett Service Bus-namnområde med ämne och en prenumeration](service-bus-resource-manager-namespace-topic.md)
> * [Skapa ett Service Bus-namnområde med kön och auktorisering](service-bus-resource-manager-namespace-auth-rule.md)
> * [Skapa ett Service Bus-namnområde med ämne, prenumeration och regel](service-bus-resource-manager-namespace-topic-with-rule.md)
> 
> Om du vill söka efter de senaste mallarna, Besök den [Azure-Snabbstartsmallar] [ Azure Quickstart Templates] galleriet och söka efter Service Bus.

## <a name="quick-deployment"></a>Snabb distribution
Välj på följande knapp om du vill köra exemplet utan att skriva någon JSON och köra PowerShell/CLI-kommando:

[![Distribuera till Azure](./media/service-bus-resource-manager-namespace/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-servicebus-create-namespace%2Fazuredeploy.json)

Gå igenom följande avsnitt i den här artikeln för att skapa och distribuera mallen manuellt.

## <a name="prerequisites"></a>Förutsättningar
Du behöver en Azure-prenumeration för att kunna utföra den här snabbstarten. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

Om du vill använda **Azure PowerShell** att distribuera Resource Manager-mallen [installera Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-5.7.0).

Om du vill använda **Azure CLI** att distribuera Resource Manager-mallen [installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-the-resource-manager-template-json"></a>Skapa Resource Manager-mallens JSON 
Skapa en JSON-fil med namnet **MyServiceBusNamespace.json** med följande innehåll: 

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
        "serviceBusSku": {
            "type": "string",
            "allowedValues": [
                "Basic",
                "Standard",
                "Premium"
            ],
            "defaultValue": "Standard",
            "metadata": {
                "description": "The messaging tier for service Bus namespace"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('serviceBusSku')]"
            }
        }
    ]
}
```

Den här mallen skapar en standard Service Bus-namnområdet. JSON-syntax och egenskaper finns i [namnområden](/azure/templates/microsoft.servicebus/namespaces) mallreferensen.

## <a name="create-the-parameters-json"></a>Skapa JSON-parametrar
Den mall du skapade i föregående steg har ett avsnitt som heter `Parameters`. Du kan definiera parametrar för de värden som varierar utifrån det projekt som du distribuerar eller utifrån målmiljön. Den här mallen definierar följande parametrar: **serviceBusNamespaceName**, **serviceBusSku**, och **plats**. Läs mer om SKU: er för Service Bus i [SKU: er för Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) att skapa.

Skapa en JSON-fil med namnet **MyServiceBusNamespace-Parameters.json** med följande innehåll: 

> [!NOTE] 
> Ange ett namn för Service Bus-namnområdet. 


```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "serviceBusNamespaceName": {
      "value": "<Specify a name for the Service Bus namespace>"
    },
    "serviceBusSku": {
      "value": "Standard"
    },
    "location": {
        "value": "East US"
    }
  }
}
```


## <a name="use-azure-powershell-to-deploy-the-template"></a>Använda Azure PowerShell för att distribuera mallen

### <a name="sign-in-to-azure"></a>Logga in på Azure
1. Starta Azure PowerShell

2. Kör följande kommandon för att logga in på Azure:

   ```azurepowershell
   Login-AzureRmAccount
   ```
3. Om du har kan du utfärda följande kommandon för att ange den aktuella prenumerationskontexten:

   ```azurepowershell
   Select-AzureRmSubscription -SubscriptionName "<YourSubscriptionName>" 
   ```

### <a name="deploy-resources"></a>Distribuera resurser
Växla till den mapp där du har sparat JSON-filer för att distribuera resurser med Azure PowerShell och kör följande kommandon:

> [!IMPORTANT]
> Ange ett namn för Azure-resursgrupp som ett värde för $resourceGroupName innan du kör kommandona. 

1. Deklarera en variabel för resursgruppens namn och ange ett värde för den. 

    ```azurepowershell
    $resourceGroupName = "<Specify a name for the Azure resource group>"
    ```
2. Skapa en Azure-resursgrupp.

    ```azurepowershell
    New-AzureRmResourceGroup $resourceGroupName -location 'East US'
    ```
3. Distribuera Resource Manager-mallen. Ange namnen på distributionen själva, resursgrupp, JSON-filen för mallen, JSON-fil för parametrar

    ```azurepowershell
    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName $resourceGroupName -TemplateFile MyServiceBusNamespace.json -TemplateParameterFile MyServiceBusNamespace-Parameters.json
    ```

## <a name="use-azure-cli-to-deploy-the-template"></a>Använd Azure CLI för att distribuera mallen

### <a name="sign-in-to-azure"></a>Logga in på Azure

1. Kör följande kommandon för att logga in på Azure:

    ```azurecli
    az login
    ```
2. Ange den aktuella prenumerationskontexten. Ersätt `MyAzureSub` med namnet på den Azure-prenumeration som du vill använda:

    ```azurecli
    az account set --subscription <Name of your Azure subscription>
    ``` 

### <a name="deploy-resources"></a>Distribuera resurser
Växla till mappen med JSON-filer för att distribuera resurser med Azure CLI, och kör följande kommandon:

> [!IMPORTANT]
> Ange ett namn för Azure-resursgrupp i gruppen az skapa kommando. .

1. Skapa en Azure-resursgrupp. 
    ```azurecli
    az group create --name <YourResourceGroupName> --location eastus
    ```

2. Distribuera Resource Manager-mallen. Ange namn på resursgruppen, distribution, JSON-filen för mallen och JSON-fil för parametrar.

    ```azurecli
    az group deployment create --name <Specify a name for the deployment> --resource-group <YourResourceGroupName> --template-file MyServiceBusNamespace.json --parameters @MyServiceBusNamespace-Parameters.json
    ```

## <a name="next-steps"></a>Nästa steg
I den här artikeln har skapat du ett Service Bus-namnområde. Se de andra snabbstarterna och lär dig att skapa köer, ämnen/prenumerationer och använda dem: 

- [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
- [Kom igång med Service Bus-ämnen](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Service Bus namespace template]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/
[Azure Quickstart Templates]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Service Bus pricing and billing]: service-bus-pricing-billing.md
[Using Azure PowerShell with Azure Resource Manager]: ../azure-resource-manager/powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md
