---
title: Hantera Azure Search med Powershell-skript | Microsoft Docs
description: "Hantera din Azure Search-tjänst med PowerShell-skript. Skapa eller uppdatera en Azure Search-tjänst och hantera Azure Search admin nycklar"
services: search
documentationcenter: 
author: seansaleh
manager: mblythe
editor: 
tags: azure-resource-manager
ms.assetid: 9b3dc1f2-3619-4235-ba1f-d2d6f5c45dd5
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: powershell
ms.date: 08/15/2016
ms.author: seasa
ms.openlocfilehash: aa51c846efef12461ec382274199bc049c42aaa3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="manage-your-azure-search-service-with-powershell"></a>Hantera din Azure Search-tjänst med PowerShell
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> 
> 

Det här avsnittet beskrivs de PowerShell-kommandona för att utföra många av de administrativa uppgifterna för Azure Search-tjänster. Vi kommer att gå igenom hur du skapar en söktjänst, skalning och hantera dess API-nycklar.
Dessa kommandon parallell vilka hanteringsalternativ som finns tillgängliga i den [Azure Search Management REST API](http://msdn.microsoft.com/library/dn832684.aspx).

## <a name="prerequisites"></a>Krav
* Du måste ha Azure PowerShell 1.0 eller senare. Instruktioner finns i [installera och konfigurera Azure PowerShell](/powershell/azure/overview).
* Du måste vara inloggad på Azure-prenumerationen i PowerShell som beskrivs nedan.

Först måste du logga in till Azure med det här kommandot:

    Login-AzureRmAccount

Ange den e-postadressen för kontot och lösenordet i dialogrutan för Microsoft Azure-inloggning.

Du kan också [logga in interaktivt med ett huvudnamn för tjänsten](../azure-resource-manager/resource-group-authenticate-service-principal.md).

Om du har flera Azure-prenumerationer måste du ställa in din Azure-prenumeration. Kör det här kommandot om du vill se en lista över dina befintliga prenumerationer.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Kör följande kommando om du vill ange prenumerationen. I följande exempel prenumerationsnamn är `ContosoSubscription`.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

## <a name="commands-to-help-you-get-started"></a>Kommandon som hjälper dig att komma igång
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
Nu när tjänsten har skapats kan du ta nästa steg: skapa en [index](search-what-is-an-index.md), [fråga ett index](search-query-overview.md), och slutligen skapar och hanterar egna sökprogram som använder Azure Search.

* [Skapa ett Azure Search-index i Azure-portalen](search-create-index-portal.md)
* [Fråga en Azure Search-index med Sök Explorer i Azure-portalen](search-explorer.md)
* [Konfigurera en indexerare för att läsa in data från andra tjänster](search-indexer-overview.md)
* [Hur du använder Azure Search i .NET](search-howto-dotnet-sdk.md)
* [Analysera Azure Search-trafik](search-traffic-analytics.md)

