---
title: Hantera Azure Search med Powershell-skript | Microsoft Docs
description: Hantera Azure Search-tjänsten med PowerShell-skript. Skapa eller uppdatera en Azure Search-tjänst och hantera Azure Search admin-nycklar
author: HeidiSteen
manager: cgronlun
tags: azure-resource-manager
services: search
ms.service: search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 08/15/2016
ms.author: heidist
ms.openlocfilehash: 1d0024af3bbf9edfe8c43032878a8b61e35cea9c
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/13/2018
ms.locfileid: "39000998"
---
# <a name="manage-your-azure-search-service-with-powershell"></a>Hantera Azure Search-tjänsten med PowerShell
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> 
> 

Det här avsnittet beskriver PowerShell-kommandon för att utföra många av hanteringsaktiviteter för Azure Search-tjänsterna. Vi visar hur du skapar en söktjänst, skalning och hantera dess API-nycklar.
Dessa kommandon parallell hanteringsalternativ som är tillgängliga i den [Azure Search Management REST API](https://docs.microsoft.com/rest/api/searchmanagement).

## <a name="prerequisites"></a>Förutsättningar
* Du måste ha Azure PowerShell 1.0 eller senare. Anvisningar finns i [installera och konfigurera Azure PowerShell](/powershell/azure/overview).
* Du måste vara inloggad på Azure-prenumerationen i PowerShell enligt beskrivningen nedan.

Först måste du logga in på Azure med det här kommandot:

    Connect-AzureRmAccount

Ange e-postadressen till ditt Azure-konto och lösenordet i dialogrutan för Microsoft Azure-inloggning.

Du kan också [logga in interaktivt med ett huvudnamn för tjänsten](../azure-resource-manager/resource-group-authenticate-service-principal.md).

Om du har flera Azure-prenumerationer kan behöva du ange dina Azure-prenumeration. Kör det här kommandot om du vill se en lista över dina aktuella prenumerationer.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Kör följande kommando för att ange prenumerationen. I följande exempel prenumerationens namn är `ContosoSubscription`.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

## <a name="commands-to-help-you-get-started"></a>Kommandon för att hjälpa dig att komma igång
    $serviceName = "your-service-name-lowercase-with-dashes"
    $sku = "free" # or "basic" or "standard" for paid services
    $location = "West US"
    # You can get a list of potential locations with
    # (Get-AzureRmResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Search'}).Locations
    $resourceGroupName = "YourResourceGroup" 
    # If you don't already have this resource group, you can create it with 
    # New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Register the ARM provider idempotently. This must be done once per subscription
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Search"

    # Create a new search service
    # This command will return once the service is fully created
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateUri "https://gallery.azure.com/artifact/20151001/Microsoft.Search.1.0.9/DeploymentTemplates/searchServiceDefaultTemplate.json" `
        -NameFromTemplate $serviceName `
        -Sku $sku `
        -Location $location `
        -PartitionCount 1 `
        -ReplicaCount 1

    # Get information about your new service and store it in $resource
    $resource = Get-AzureRmResource `
        -ResourceType "Microsoft.Search/searchServices" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19

    # View your resource
    $resource

    # Get the primary admin API key
    $primaryKey = (Invoke-AzureRmResourceAction `
        -Action listAdminKeys `
        -ResourceId $resource.ResourceId `
        -ApiVersion 2015-08-19).PrimaryKey

    # Regenerate the secondary admin API Key
    $secondaryKey = (Invoke-AzureRmResourceAction `
        -ResourceType "Microsoft.Search/searchServices/regenerateAdminKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action secondary).SecondaryKey

    # Create a query key for read only access to your indexes
    $queryKeyDescription = "query-key-created-from-powershell"
    $queryKey = (Invoke-AzureRmResourceAction `
        -ResourceType "Microsoft.Search/searchServices/createQueryKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action $queryKeyDescription).Key

    # View your query key
    $queryKey

    # Delete query key
    Remove-AzureRmResource `
        -ResourceType "Microsoft.Search/searchServices/deleteQueryKey/$($queryKey)" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19

    # Scale your service up
    # Note that this will only work if you made a non "free" service
    # This command will not return until the operation is finished
    # It can take 15 minutes or more to provision the additional resources
    $resource.Properties.ReplicaCount = 2
    $resource | Set-AzureRmResource

    # Delete your service
    # Deleting your service will delete all indexes and data in the service
    $resource | Remove-AzureRmResource

## <a name="next-steps"></a>Nästa steg
Nu när din tjänst har skapats kan du ta nästa steg: skapa en [index](search-what-is-an-index.md), [fråga ett index](search-query-overview.md), och slutligen skapa och hantera din egen sökprogram som använder Azure Search.

* [Skapa ett Azure Search-index i Azure portal](search-create-index-portal.md)
* [Fråga ett Azure Search-index med Sökutforskaren i Azure portal](search-explorer.md)
* [Konfigurera en indexerare för att läsa in data från andra tjänster](search-indexer-overview.md)
* [Hur du använder Azure Search i .NET](search-howto-dotnet-sdk.md)
* [Analysera din Azure Search-trafik](search-traffic-analytics.md)

