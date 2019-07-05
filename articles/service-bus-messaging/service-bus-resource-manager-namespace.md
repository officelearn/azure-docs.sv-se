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
ms.date: 06/21/2019
ms.author: spelluru
ms.openlocfilehash: 4162775153a48dc8ea28e06f7c99f9927b9c602a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444759"
---
# <a name="create-a-service-bus-namespace-by-using-an-azure-resource-manager-template"></a>Skapa ett Service Bus-namnområde med en Azure Resource Manager-mall

Lär dig hur du distribuerar en Azure Resource Manager-mall för att skapa ett Service Bus-namnområde. Du kan använda den här mallen för dina egna distributioner eller anpassa den så att den uppfyller dina krav. Mer information om hur du skapar mallar finns i [dokumentation om Azure Resource Manager](/azure/azure-resource-manager/).

Följande mallar kan också användas för att skapa Service Bus-namnområden:

* [Skapa ett Service Bus-namnområde med kö](./service-bus-resource-manager-namespace-queue.md)
* [Skapa ett Service Bus-namnområde med ämne och en prenumeration](./service-bus-resource-manager-namespace-topic.md)
* [Skapa ett Service Bus-namnområde med kön och auktorisering](./service-bus-resource-manager-namespace-auth-rule.md)
* [Skapa ett Service Bus-namnområde med ämne, prenumeration och regel](./service-bus-resource-manager-namespace-topic-with-rule.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-a-service-bus-namespace"></a>Skapa ett namnområde för service bus

I den här snabbstarten använder du en [befintlig Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/azuredeploy.json) från [Azure-Snabbstartsmallar](https://azure.microsoft.com/resources/templates/):

[!code-json[create-azure-service-bus-namespace](~/quickstart-templates/101-servicebus-create-namespace/azuredeploy.json)]

Du hittar mer mallexempel [Azure-Snabbstartsmallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular).

Skapa en service bus-namnområde genom att distribuera en mall:

1. Välj **prova** från följande kodblock och följ sedan anvisningarna för att logga in på Azure Cloud shell.

    ```azurepowershell-interactive
    $serviceBusNamespaceName = Read-Host -Prompt "Enter a name for the service bus namespace to be created"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${serviceBusNamespaceName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -serviceBusNamespaceName $serviceBusNamespaceName

    Write-Host "Press [ENTER] to continue ..."
    ```

    Resursgruppens namn är den service bus-namnrymden med **rg** sist.

2. Välj **Kopiera** för att kopiera PowerShell-skriptet.
3. Högerklicka på shell-konsolen och välj sedan **klistra in**.

Det tar en stund att skapa en händelsehubb.

## <a name="verify-the-deployment"></a>Verifiera distributionen

Om du vill se distribuerade service bus-namnområdet, kan du antingen öppna resursgruppen från Azure-portalen eller använda följande Azure PowerShell-skript. Om cloudshell är fortfarande öppen, behöver du inte att kopiera/köra de första och andra raderna i skriptet nedan.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Get-AzServiceBusNamespace -ResourceGroupName $resourceGroupName -Name $serviceBusNamespaceName

Write-Host "Press [ENTER] to continue ..."
```

Azure PowerShell används för att distribuera mallen i den här självstudien. Andra metoder för distribution av mallen, finns här:

* [Med hjälp av Azure portal](../azure-resource-manager/resource-group-template-deploy-portal.md).
* [Med hjälp av Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md).
* [Med hjälp av REST API](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen. Om cloudshell är fortfarande öppen, behöver du inte att kopiera/köra de första och andra raderna i skriptet nedan.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har skapat du ett Service Bus-namnområde. Se de andra snabbstarterna och lär dig att skapa köer, ämnen/prenumerationer och använda dem:

* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Kom igång med Service Bus-ämnen](service-bus-dotnet-how-to-use-topics-subscriptions.md)
