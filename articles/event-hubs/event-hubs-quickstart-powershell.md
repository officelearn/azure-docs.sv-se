---
title: Azure Snabbstart – Skapa en händelsehubb med hjälp av PowerShell | Microsoft Docs
description: Den här snabbstarten beskriver hur du skapar en händelsehubb med Azure PowerShell och hur du sedan skickar och tar emot händelser med hjälp av .NET Standard SDK.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/16/2018
ms.author: shvija
ms.openlocfilehash: 9f0a6a910ab9b9589e09b6c8e1e7b16f7e63c5c8
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51258977"
---
# <a name="quickstart-create-an-event-hub-using-azure-powershell"></a>Snabbstart: Skapa en händelsehubb med Azure PowerShell

Azure Event Hubs är en strömningstjänst för stordata och händelseinmatningstjänst som kan ta emot och bearbeta flera miljoner händelser per sekund. Azure Event Hubs kan bearbeta och lagra händelser, data eller telemetri som produceras av distribuerade program och enheter. Data som skickas till en händelsehubb kan omvandlas och lagras med valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadaptrar. En detaljerad översikt över Event Hubs finns i [Översikt över Event Hubs](event-hubs-about.md) och [Event Hubs-funktioner](event-hubs-features.md).

I den här snabbstarten har du skapat en händelsehubb med hjälp av Azure PowerShell.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna följa den här självstudien måste du ha:

- En Azure-prenumeration. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto][] innan du börjar.
- [Visual Studio 2017 Update 3 (version 15.3, 26730.01)](https://www.visualstudio.com/vs) eller senare.
- [SDK för .NET Standard](https://www.microsoft.com/net/download/windows) version 2.0 eller senare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du använder PowerShell lokalt måste du köra den senaste versionen av PowerShell för att kunna slutföra den här snabbstarten. Om du behöver installera eller uppgradera, kan du läsa [Installera och konfigurera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk samling Azure-resurser. Du behöver en resursgrupp för att kunna skapa en händelsehubb. 

I följande exempel skapas en resursgrupp i regionen USA, västra. Ersätt `myResourceGroup` med namnet på resursgruppen som du vill använda:

```azurepowershell-interactive
New-AzureRmResourceGroup –Name myResourceGroup –Location eastus
```

## <a name="create-an-event-hubs-namespace"></a>Skapa ett Event Hubs-namnområde

När resursgruppen har skapats skapar du ett Event Hubs-namnområde i resursgruppen. Ett Event Hubs-namnområde ger ett unikt fullständigt kvalificerat domännamn som du kan skapa din händelsehubb i. Ersätt `namespace_name` med ett unikt namn för ditt namnområde:

```azurepowershell-interactive
New-AzureRmEventHubNamespace -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Location eastus
```

## <a name="create-an-event-hub"></a>Skapa en händelsehubb

Nu när du har skapat ett Event Hubs-namnområde skapar du en händelsehubb i namnområdet:

```azurepowershell-interactive
New-AzureRmEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name
```

Grattis! Du har använt Azure PowerShell för att skapa en Event Hubs-namnrymd och en händelsehubb i den namnrymden. 

## <a name="next-steps"></a>Nästa steg

I den här artikeln skapade du en Event Hubs-namnrymd och använde exempelprogram för att skicka och ta emot meddelanden från din händelsehubb. Stegvisa instruktioner för att skicka händelser till eller ta emot händelser från en händelsehubb finns i följande självstudier: 

- **Skicka händelser till en händelsehubb**: [.NET Standard](event-hubs-dotnet-standard-getstarted-send.md), [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [Node.js](event-hubs-node-get-started-send.md), [Go](event-hubs-go-get-started-send.md), [C](event-hubs-c-getstarted-send.md)
- **Ta emot händelser från en händelsehubb**: [.NET Standard](event-hubs-dotnet-standard-getstarted-receive-eph.md), [.NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md), [Java](event-hubs-java-get-started-receive-eph.md), [Python](event-hubs-python-get-started-receive.md), [Node.js](event-hubs-node-get-started-receive.md), [Go](event-hubs-go-get-started-receive-eph.md), [Apache Storm](event-hubs-storm-getstarted-receive.md)

[Skapa ett kostnadsfritt konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[New-AzureRmResourceGroup]: https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
