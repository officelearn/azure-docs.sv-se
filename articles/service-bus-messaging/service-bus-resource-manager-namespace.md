---
title: Skapa ett namnområde för Azure Service Bus med hjälp av mall
description: Använda Azure Resource Manager-mall för att skapa ett namnområde för Service Bus Messaging
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
ms.openlocfilehash: 5febdd63ab6f854ca3244f8449f6f715a75e735f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264483"
---
# <a name="create-a-service-bus-namespace-by-using-an-azure-resource-manager-template"></a>Skapa ett tjänstbussnamnområde med hjälp av en Azure Resource Manager-mall

Lär dig hur du distribuerar en Azure Resource Manager-mall för att skapa ett servicebussnamnområde. Du kan använda den här mallen för dina egna distributioner eller anpassa den så att den uppfyller dina krav. Mer information om hur du skapar mallar finns i [Dokumentationen till Azure Resource Manager](/azure/azure-resource-manager/).

Följande mallar är också tillgängliga för att skapa Service Bus-namnområden:

* [Skapa ett servicebussnamnområde med kö](./service-bus-resource-manager-namespace-queue.md)
* [Skapa ett servicebussnamnområde med ämne och prenumeration](./service-bus-resource-manager-namespace-topic.md)
* [Skapa ett servicebussnamnområde med kö- och auktoriseringsregel](./service-bus-resource-manager-namespace-auth-rule.md)
* [Skapa ett servicebussnamnområde med ämne, prenumeration och regel](./service-bus-resource-manager-namespace-topic-with-rule.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-a-service-bus-namespace"></a>Skapa ett tjänstbussnamnområde

I den här snabbstarten använder du en [befintlig Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/azuredeploy.json) från [Azure Quickstart-mallar:](https://azure.microsoft.com/resources/templates/)

[!code-json[create-azure-service-bus-namespace](~/quickstart-templates/101-servicebus-create-namespace/azuredeploy.json)]

Mer mallexempel finns i [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular).

Så här skapar du ett tjänstbussnamnområde genom att distribuera en mall:

1. Välj **Prova det** från följande kodblock och följ sedan instruktionerna för att logga in på Azure Cloud-skalet.

    ```azurepowershell-interactive
    $serviceBusNamespaceName = Read-Host -Prompt "Enter a name for the service bus namespace to be created"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${serviceBusNamespaceName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -serviceBusNamespaceName $serviceBusNamespaceName

    Write-Host "Press [ENTER] to continue ..."
    ```

    Resursgruppsnamnet är namnet på servicebussens namnområde med **rg** till.

2. Välj **Kopiera** för att kopiera PowerShell-skriptet.
3. Högerklicka på skalkonsolen och välj sedan **Klistra in**.

Det tar en stund att skapa en händelsehubb.

## <a name="verify-the-deployment"></a>Verifiera distributionen

Om du vill visa det distribuerade tjänstbussnamnområdet kan du antingen öppna resursgruppen från Azure-portalen eller använda följande Azure PowerShell-skript. Om molnskalet fortfarande är öppet behöver du inte kopiera/köra den första och andra raden i följande skript.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Get-AzServiceBusNamespace -ResourceGroupName $resourceGroupName -Name $serviceBusNamespaceName

Write-Host "Press [ENTER] to continue ..."
```

Azure PowerShell används för att distribuera mallen i den här självstudien. Andra malldistributionsmetoder finns i:

* [Genom att använda Azure-portalen](../azure-resource-manager/templates/deploy-portal.md).
* [Genom att använda Azure CLI](../azure-resource-manager/templates/deploy-cli.md).
* [Genom att använda REST API](../azure-resource-manager/templates/deploy-rest.md).

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen. Om molnskalet fortfarande är öppet behöver du inte kopiera/köra den första och andra raden i följande skript.

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat ett servicebussnamnområde. Se de andra snabbstarterna om du vill lära dig hur du skapar köer, ämnen/prenumerationer och använder dem:

* [Komma igång med servicebussköer](service-bus-dotnet-get-started-with-queues.md)
* [Kom igång med Service Bus-ämnen](service-bus-dotnet-how-to-use-topics-subscriptions.md)
