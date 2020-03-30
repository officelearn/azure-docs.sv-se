---
title: 'Snabbstart: Skapa en händelsehubb med konsumentgrupp - Azure Event Hubs'
description: 'Snabbstart: Skapa ett namnområde för händelsehubbar med en händelsehubb och en konsumentgrupp med Azure Resource Manager-mallar'
services: event-hubs
documentationcenter: .net
author: spelluru
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 02/11/2020
ms.author: spelluru
ms.custom: subject-armqs
ms.openlocfilehash: c2221fe5b5ab38afbdde167e5bcbf6b47ed4f861
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79528089"
---
# <a name="quickstart-create-an-event-hub-by-using-an-azure-resource-manager-template"></a>Snabbstart: Skapa en händelsehubb med hjälp av en Azure Resource Manager-mall

Azure Event Hubs är en strömningstjänst för stordata och händelseinmatningstjänst som kan ta emot och bearbeta flera miljoner händelser per sekund. Event Hubs kan bearbeta och lagra händelser, data eller telemetri som producerats av distribuerade program och enheter. Data som skickas till en händelsehubb kan omvandlas och lagras med valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadapter. En detaljerad översikt över Event Hubs finns i [Översikt över Event Hubs](event-hubs-about.md) och [Event Hubs-funktioner](event-hubs-features.md). I den här snabbstarten skapar du en händelsehubb med hjälp av en [Azure Resource Manager-mall](../azure-resource-manager/management/overview.md). Du distribuerar en Azure Resource Manager-mall för att skapa ett namnområde av typen [Event Hubs](event-hubs-what-is-event-hubs.md), med en händelsehubb.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Inga.

## <a name="create-an-event-hub"></a>Skapa en händelsehubb

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure Quickstart-mallar](https://azure.microsoft.com/resources/templates/101-eventhubs-create-namespace-and-eventhub/).

:::code language="json" source="~/quickstart-templates/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json" range="1-61" highlight="32-59":::

De resurser som definieras i mallen omfattar:

- [**Microsoft.EventHub/namnområden**](/azure/templates/microsoft.eventhub/namespaces)
- [**Microsoft.EventHub/namespaces/eventhubs**](/azure/templates/microsoft.eventhub/namespaces/eventhubs)

Mer mallexempel finns i [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/?term=eventhub&pageNumber=1&sort=Popular).

### <a name="deploy-the-template"></a>Distribuera mallen

Så här distribuerar du mallen:

1. Välj **Prova det** från följande kodblock och följ sedan instruktionerna för att logga in på Azure Cloud Shell.

   ```azurepowershell-interactive
   $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
   $resourceGroupName = "${projectName}rg"
   $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-eventhubs-create-namespace-and-eventhub/azuredeploy.json"

   New-AzResourceGroup -Name $resourceGroupName -Location $location
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName

   Write-Host "Press [ENTER] to continue ..."
   ```

   Det tar en stund att skapa en händelsehubb.

1. Välj **Kopiera** för att kopiera PowerShell-skriptet.
1. Högerklicka på skalkonsolen och välj sedan **Klistra in**.

## <a name="verify-the-deployment"></a>Verifiera distributionen

För att verifiera distributionen kan du antingen öppna resursgruppen från [Azure-portalen](https://portal.azure.com)eller använda följande Azure PowerShell-skript.  Om Cloud Shell fortfarande är öppet behöver du inte kopiera/köra den första raden (Läsvärd).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"
$namespaceName = "${projectName}ns"

Get-AzEventHub -ResourceGroupName $resourceGroupName -Namespace $namespaceName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen. Om Cloud Shell fortfarande är öppet behöver du inte kopiera/köra den första raden (Läsvärd).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat ett namnområde för händelsehubbar och en händelsehubb i namnområdet. Stegvisa instruktioner för att skicka händelser till (eller) ta emot händelser från en händelsehubb finns i självstudiekurserna **Skicka och ta emot händelser:**

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](get-started-java-send-v2.md)
- [Python](get-started-python-send-v2.md)
- [Javascript](get-started-java-send-v2.md)
- [Kör](event-hubs-go-get-started-send.md)
- [C (skickar endast)](event-hubs-c-getstarted-send.md)
- [Apache Storm (tar endast emot)](event-hubs-storm-getstarted-receive.md)


[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/templates/template-syntax.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
