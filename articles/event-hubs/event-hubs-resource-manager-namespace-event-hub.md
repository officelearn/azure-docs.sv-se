---
title: Skapa en händelsehubb med konsumentgrupp - Azure Event Hubs | Microsoft Docs
description: Skapa ett namnområde för Event Hubs med en händelsehubb och en konsumentgrupp med hjälp av Azure Resource Manager-mallar
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/14/2019
ms.author: shvija
ms.openlocfilehash: 007e016672f8548956b37b961805183a504d6bf0
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154081"
---
# <a name="quickstart-create-an-event-hub-by-using-an-azure-resource-manager-template"></a>Snabbstart: Skapa en händelsehubb med hjälp av en Azure Resource Manager-mall

Azure Event Hubs är en strömningstjänst för stordata och händelseinmatningstjänst som kan ta emot och bearbeta flera miljoner händelser per sekund. Azure Event Hubs kan bearbeta och lagra händelser, data eller telemetri som produceras av distribuerade program och enheter. Data som skickas till en händelsehubb kan omvandlas och lagras med valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadaptrar. En detaljerad översikt över Event Hubs finns i [Översikt över Event Hubs](event-hubs-about.md) och [Event Hubs-funktioner](event-hubs-features.md).

I den här snabbstarten skapar du en händelsehubb med hjälp av en [Azure Resource Manager-mall](../azure-resource-manager/resource-group-overview.md). Du distribuerar en Azure Resource Manager-mall för att skapa ett namnområde av typen [Händelsehubbar](event-hubs-what-is-event-hubs.md), med en händelsehubb. Artikeln visar hur du definierar vilka resurser distribueras och hur du definierar parametrar som anges när distributionen körs. Du kan använda den här mallen för dina egna distributioner eller anpassa den så att den uppfyller dina krav. Information om hur du skapar mallar finns i [redigera Azure Resource Manager-mallar][Authoring Azure Resource Manager templates]. Den JSON-syntax och de egenskaper som ska användas i en mall finns i avsnittet om [Microsoft.EventGrid-resurstyper](/azure/templates/microsoft.eventhub/allversions).

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="create-an-event-hub"></a>Skapa en händelsehubb

I den här snabbstarten använder du en [befintlig Resource Manager-mall](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json). Du hittar mer mallexempel [Azure-Snabbstartsmallar](https://azure.microsoft.com/resources/templates/?term=eventhub&pageNumber=1&sort=Popular).

För att distribuera mallen:

1. Välj **prova** från följande kodblock och följ sedan anvisningarna för att logga in på Azure Cloud shell.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName

   Write-Host "Press [ENTER] to continue ..."
   ```

   Det tar en stund att skapa en händelsehubb.

1. Välj **Kopiera** för att kopiera PowerShell-skriptet.
1. Högerklicka på shell-konsolen och välj sedan **klistra in**.

## <a name="verify-the-deployment"></a>Verifiera distributionen

Om du vill verifiera distributionen, kan du öppna resursgruppen från den [Azure-portalen](https://portal.azure.com), eller använda följande Azure PowerShell-skript.  Om cloudshell är fortfarande öppen, behöver du inte att kopiera/körs den första raden (Read-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"
$namespaceName = "${projectName}ns"

Get-AzEventHub -ResourceGroupName $resourceGroupName -Namespace $namespaceName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen. Om cloudshell är fortfarande öppen, behöver du inte att kopiera/körs den första raden (Read-Host).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har skapat du ett namnområde för Event Hubs och en händelsehubb i namnområdet. Stegvisa instruktioner för att skicka händelser till (eller) ta emot händelser från en event hub, se den **skicka och ta emot händelser** Självstudier:

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [Node.js](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (skicka)](event-hubs-c-getstarted-send.md)
- [Apache Storm (endast reecive)](event-hubs-storm-getstarted-receive.md)


[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
