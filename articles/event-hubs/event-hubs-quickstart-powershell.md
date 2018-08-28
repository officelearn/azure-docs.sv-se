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
ms.date: 06/26/2018
ms.author: shvija
ms.openlocfilehash: a8a29484af7c95e9cffa1c7048c0f7b8d642c868
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2018
ms.locfileid: "42022876"
---
# <a name="quickstart-create-an-event-hub-using-azure-powershell"></a>Snabbstart: Skapa en händelsehubb med Azure PowerShell

Azure Event Hubs är en mycket skalbar dataströmningsplattform och inmatningstjänst som kan ta emot och bearbeta flera miljoner händelser per sekund. Den här snabbstarten visar hur du skapar en händelsehubb med Azure PowerShell och sedan skickar till och tar emot händelser från en händelsehubb med SDK för .NET Standard.

Du behöver en Azure-prenumeration för att kunna utföra den här snabbstarten. Om du inte har ett konto kan du [skapa ett kostnadsfritt konto][] innan du börjar.

## <a name="prerequisites"></a>Krav

För att kunna följa den här självstudien måste du ha:

- [Visual Studio 2017 Update 3 (version 15.3, 26730.01)](http://www.visualstudio.com/vs) eller senare.
- [SDK för .NET Standard](https://www.microsoft.com/net/download/windows) version 2.0 eller senare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du använder PowerShell lokalt måste du köra den senaste versionen av PowerShell för att kunna slutföra den här snabbstarten. Om du behöver installera eller uppgradera, kan du läsa [Installera och konfigurera Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).

## <a name="provision-resources"></a>Etablera resurser

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk samling Azure-resurser. Du behöver en resursgrupp för att kunna skapa en händelsehubb. 

I följande exempel skapas en resursgrupp i regionen USA, västra. Ersätt `myResourceGroup` med namnet på resursgruppen som du vill använda:

```azurepowershell-interactive
New-AzureRmResourceGroup –Name myResourceGroup –Location eastus
```

### <a name="create-an-event-hubs-namespace"></a>Skapa ett Event Hubs-namnområde

När resursgruppen har skapats skapar du ett Event Hubs-namnområde i resursgruppen. Ett Event Hubs-namnområde ger ett unikt fullständigt kvalificerat domännamn som du kan skapa din händelsehubb i. Ersätt `namespace_name` med ett unikt namn för ditt namnområde:

```azurepowershell-interactive
New-AzureRmEventHubNamespace -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Location eastus
```

### <a name="create-an-event-hub"></a>Skapa en händelsehubb

Nu när du har skapat ett Event Hubs-namnområde skapar du en händelsehubb i namnområdet:

```azurepowershell-interactive
New-AzureRmEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name
```

### <a name="create-a-storage-account-for-event-processor-host"></a>Skapa ett lagringskonto för Event Processor Host

Event Processor Host gör det enklare att ta emot händelser från Event Hubs genom att hantera kontrollpunkter och parallella mottaganden. För att kunna använda kontrollpunkter måste Event Processor Host ha ett lagringskonto. Om du vill skapa ett lagringskonto och få dess nycklar, kör du följande kommandon:

```azurepowershell-interactive
# Create a standard general purpose storage account 
New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name storage_account_name -Location eastus -SkuName Standard_LRS 
e
# Retrieve the storage account key for accessing it
Get-AzureRmStorageAccountKey -ResourceGroupName myResourceGroup -Name storage_account_name
```

### <a name="get-the-connection-string"></a>Hämta anslutningssträngen

En anslutningssträng krävs för att ansluta till din händelsehubb och bearbeta händelser. Du hämtar anslutningssträngen genom att köra:

```azurepowershell-interactive
Get-AzureRmEventHubKey -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Name RootManageSharedAccessKey
```

## <a name="stream-into-event-hubs"></a>Strömma till Event Hubs

Nu kan du börja strömma till Event Hubs. Exemplen kan hämtas eller Git-klonas från [Event Hubs-lagringsplatsen](https://github.com/Azure/azure-event-hubs)

### <a name="ingest-events"></a>Samla in händelser

Om du vill börja strömma händelser laddar du ned [SampleSender](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) från GitHub, eller klonar [Event Hubs GitHub-lagringsplatsen](https://github.com/Azure/azure-event-hubs) genom att köra följande kommando:

```bash
git clone https://github.com/Azure/azure-event-hubs.git
```

Navigera till mappen \azure-event-hubs\samples\DotNet\Microsoft.Azure.EventHubs\SampleSender och läs in filen SampleSender.sln i Visual Studio.

Lägg sedan till Nuget-paketet [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) i projektet.

I filen Program.cs ersätter du följande platshållare med namnet på din händelsehubb och anslutningssträng:

```C#
private const string EhConnectionString = "Event Hubs connection string";
private const string EhEntityPath = "Event Hub name";

```

Skapa och kör exempelprojektet. Du kan se händelserna som matas in i händelsehubben:

![][3]

### <a name="receive-and-process-events"></a>Ta emot och bearbeta händelser

Nu laddar du ned Event Processor Host-exemplet som mottar de meddelanden som du precis skickade. Ladda ned [SampleEphReceiver](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) från GitHub, eller klona [Event Hubs GitHub-lagringsplatsen](https://github.com/Azure/azure-event-hubs) genom att köra följande kommando:

```bash
git clone https://github.com/Azure/azure-event-hubs.git
```

Navigera till mappen \azure-event-hubs\samples\DotNet\Microsoft.Azure.EventHubs\SampleEphReceiver och läs in filen SampleEphReceiver.sln i Visual Studio.

Lägg sedan till Nuget-paketen [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) och [Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) i projektet.

I filen Program.cs ersätter du följande konstanter med motsvarande värden:

```C#
private const string EventHubConnectionString = "Event Hubs connection string";
private const string EventHubName = "Event Hub name";
private const string StorageContainerName = "Storage account container name";
private const string StorageAccountName = "Storage account name";
private const string StorageAccountKey = "Storage account key";
```

Skapa och kör exempelprojektet. Du kan se att händelserna tas emot i exempelprogrammet:

![][4]

I Azure-portalen kan du visa bearbetningshastigheten för händelser för ett visst Event Hubs-namnområde (se bilden):

![][5]

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört den här snabbstarten kan du ta bort resursgruppen samt namnområdet, lagringskontot och händelsehubben som ingår i gruppen. Ersätt `myResourceGroup` med namnet på den resursgrupp som du skapade. 

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln skapade du ett Event Hubs-namnområde och de andra resurserna som krävs för att skicka och ta emot meddelanden från din händelsehubb. Fortsätt med följande självstudie om du vill veta mer:

> [!div class="nextstepaction"]
> [Visualisera dataavvikelser i Event Hubs-dataströmmar](event-hubs-tutorial-visualize-anomalies.md)

[Skapa ett kostnadsfritt konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[New-AzureRmResourceGroup]: https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
