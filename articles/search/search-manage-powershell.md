---
title: PowerShell-skript med Az.Search-modul
titleSuffix: Azure Cognitive Search
description: Skapa och konfigurera en Azure Cognitive Search-tjänst med PowerShell. Du kan skala en tjänst uppåt eller nedåt, hantera api-nycklar för administratörer och frågor och fråga efter systeminformation.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 711071e08a52a0075512bc8b3ffe14707238cdfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77209304"
---
# <a name="manage-your-azure-cognitive-search-service-with-powershell"></a>Hantera din Azure Cognitive Search-tjänst med PowerShell
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [Powershell](search-manage-powershell.md)
> * [REST API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Du kan köra PowerShell-cmdlets och skript på Windows, Linux eller i [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) för att skapa och konfigurera Azure Cognitive Search. **Az.Search-modulen** utökar Azure [PowerShell](https://docs.microsoft.com/powershell/) med full paritet till [REST-API:erna för sökhantering](https://docs.microsoft.com/rest/api/searchmanagement) och möjligheten att utföra följande uppgifter:

> [!div class="checklist"]
> * [Lista söktjänster i en prenumeration](#list-search-services)
> * [Information om returservice](#get-search-service-information)
> * [Skapa eller ta bort en tjänst](#create-or-delete-a-service)
> * [Återskapa API-nycklar för administratörer](#regenerate-admin-keys)
> * [Skapa eller ta bort fråge-api-nycklar](#create-or-delete-query-keys)
> * [Skala upp eller ned med repliker och partitioner](#scale-replicas-and-partitions)

Ibland ställs frågor om uppgifter *som inte* finns med i listan ovan. För närvarande kan du inte använda modulen **Az.Search** eller REST API för hantering för att ändra ett servernamn, en region eller en nivå. Dedikerade resurser allokeras när en tjänst skapas. Därför kräver en ändring av den underliggande maskinvaran (plats- eller nodtyp) en ny tjänst. På samma sätt finns det inga verktyg eller API:er för att överföra innehåll, till exempel ett index, från en tjänst till en annan.

Inom en tjänst är skapandet och hanteringen av innehåll via [REST API för söktjänst](https://docs.microsoft.com/rest/api/searchservice/) eller [.NET SDK](https://docs.microsoft.com/dotnet/api/?term=microsoft.azure.search). Det finns inga dedikerade PowerShell-kommandon för innehåll, men du kan skriva PowerShell-skript som anropar REST- eller .NET-API:er för att skapa och läsa in index.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>Kontrollera versioner och lastmoduler

Exemplen i den här artikeln är interaktiva och kräver förhöjda behörigheter. Azure PowerShell **(Az-modulen)** måste installeras. Mer information finns i [Installera Azure PowerShell](/powershell/azure/overview).

### <a name="powershell-version-check-51-or-later"></a>PowerShell-versionskontroll (5.1 eller senare)

Lokala PowerShell måste vara 5.1 eller senare, på alla operativsystem som stöds.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Läs in Azure PowerShell

Om du är osäker på om **Az** är installerat kör du följande kommando som ett verifieringssteg. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

Vissa system laddar inte moduler automatiskt. Om du får ett felmeddelande på föregående kommando kan du prova att läsa in modulen, och om det misslyckas går du tillbaka till installationsanvisningarna för att se om du missade ett steg.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Ansluta till Azure med en inloggningstoken för webbläsare

Du kan använda dina inloggningsuppgifter för portalen för att ansluta till en prenumeration i PowerShell. Alternativt kan du [autentisera icke-interaktivt med en tjänst huvudnamn](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

```azurepowershell-interactive
Connect-AzAccount
```

Om du har flera Azure-prenumerationer anger du din Azure-prenumeration. Om du vill visa en lista över dina aktuella prenumerationer kör du det här kommandot.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Om du vill ange prenumerationen kör du följande kommando. I följande exempel är `ContosoSubscription`prenumerationsnamnet .

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Lista tjänster i en prenumeration

Följande kommandon kommer från [**Az.Resources**](https://docs.microsoft.com/powershell/module/az.resources/?view=azps-1.4.0#resources)och returnerar information om befintliga resurser och tjänster som redan har etablerats i prenumerationen. Om du inte vet hur många söktjänster som redan har skapats returnerar dessa kommandon den informationen och sparar en resa till portalen.

Det första kommandot returnerar alla söktjänster.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

Returnera information om en viss resurs i listan över tjänster.

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

Resultaten bör se ut ungefär som följande utdata.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alpha-numeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>Importera Az.Search

Kommandon från [**Az.Search**](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search) är inte tillgängliga förrän du läser in modulen.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>Lista alla Az.Search-kommandon

Som ett verifieringssteg returnerar du en lista över kommandon som finns i modulen.

```azurepowershell-interactive
Get-Command -Module Az.Search
```

Resultaten bör se ut ungefär som följande utdata.

```
CommandType     Name                                Version    Source
-----------     ----                                -------    ------
Cmdlet          Get-AzSearchAdminKeyPair            0.7.1      Az.Search
Cmdlet          Get-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          Get-AzSearchService                 0.7.1      Az.Search
Cmdlet          New-AzSearchAdminKey                0.7.1      Az.Search
Cmdlet          New-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          New-AzSearchService                 0.7.1      Az.Search
Cmdlet          Remove-AzSearchQueryKey             0.7.1      Az.Search
Cmdlet          Remove-AzSearchService              0.7.1      Az.Search
Cmdlet          Set-AzSearchService                 0.7.1      Az.Search
```

## <a name="get-search-service-information"></a>Hämta söktjänstinformation

När **Az.Search** har importerats och du känner till resursgruppen som innehåller söktjänsten kör du [Get-AzSearchService](https://docs.microsoft.com/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0) för att returnera tjänstdefinitionen, inklusive namn, region, nivå och replik- och partitionsantal.

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

Resultaten bör se ut ungefär som följande utdata.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : West US
Sku               : Standard
ReplicaCount      : 1
PartitionCount    : 1
HostingMode       : Default
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-or-delete-a-service"></a>Skapa eller ta bort en tjänst

[**New-AzSearchService**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) används för att [skapa en ny söktjänst](search-create-service-portal.md).

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName "demo-westus" -Name "my-demo-searchapp" -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3
``` 
Resultaten bör se ut ungefär som följande utdata.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Id                : /subscriptions/<alphanumeric-subscription-ID>/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 3
PartitionCount    : 3
HostingMode       : Default
Tags
```     

## <a name="regenerate-admin-keys"></a>Återskapa adminnycklar

[**New-AzSearchAdminKey**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) används för att rulla över admin [API nycklar](search-security-api-keys.md). Två administratörsnycklar skapas med varje tjänst för autentiserat åtkomst. Nycklar krävs på varje begäran. Båda admin nycklar är funktionellt likvärdiga, vilket ger full skrivåtkomst till en söktjänst med möjlighet att hämta information, eller skapa och ta bort alla objekt. Det finns två nycklar så att du kan använda den ena när du ersätter den andra. 

Du kan bara återskapa en i taget, `primary` angiven som antingen eller `secondary` nyckel. För oavbruten tjänst, kom ihåg att uppdatera all klientkod för att använda en sekundär nyckel medan du rullar över primärnyckeln. Undvik att byta nycklar när verksamheten är under flygning.

Som du kan förvänta dig, om du återskapa nycklar utan att uppdatera klientkod, begäranden med den gamla nyckeln kommer att misslyckas. Om du återskapar alla nya nycklar låss du inte permanent ut från tjänsten och du kan fortfarande komma åt tjänsten via portalen. När du har återskapat primära och sekundära nycklar kan du uppdatera klientkoden för att använda de nya nycklarna och åtgärderna återupptas i enlighet med detta.

Värden för API-nycklarna genereras av tjänsten. Du kan inte ange en anpassad nyckel för Azure Cognitive Search att använda. På samma sätt finns det inget användardefinierat namn för api-nycklar för administratörer. Referenser till nyckeln är fasta strängar, antingen `primary` eller `secondary`. 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

Resultaten bör se ut ungefär som följande utdata. Båda nycklarna returneras även om du bara ändrar en i taget.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>Skapa eller ta bort frågenycklar

[**New-AzSearchQueryKey**](https://docs.microsoft.com/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0) används för att skapa [fråge-API-nycklar](search-security-api-keys.md) för skrivskyddad åtkomst från klientappar till ett Azure Cognitive Search-index. Frågenycklar används för att autentisera till ett visst index i syfte att hämta sökresultat. Frågenycklar ger inte skrivskyddad åtkomst till andra objekt på tjänsten, till exempel ett index, en datakälla eller en indexerare.

Du kan inte ange en nyckel för Azure Cognitive Search att använda. API-nycklar genereras av tjänsten.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>Skala repliker och partitioner

[**Set-AzSearchService**](https://docs.microsoft.com/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0) används för att [öka eller minska repliker och partitioner](search-capacity-planning.md) för att justera fakturerbara resurser i tjänsten. Öka repliker eller partitioner lägger till din faktura, som har både fasta och rörliga avgifter. Om du har ett tillfälligt behov av ytterligare processorkraft kan du öka repliker och partitioner för att hantera arbetsbelastningen. Övervakningsområdet på sidan Översiktsportal har paneler på frågefördröjning, frågor per sekund och begränsning, vilket anger om aktuell kapacitet är tillräcklig.

Det kan ta ett tag att lägga till eller ta bort resourcing. Kapacitetsjusteringar sker i bakgrunden, vilket gör att befintliga arbetsbelastningar kan fortsätta. Ytterligare kapacitet används för inkommande begäranden så snart den är klar, utan ytterligare konfiguration krävs. 

Att ta bort kapaciteten kan vara störande. Stoppa alla indexerings- och indexerarejobb innan du minskar kapaciteten rekommenderas för att undvika ignorerade begäranden. Om det inte är möjligt kan du överväga att minska kapaciteten stegvis, en replik och partition i taget tills de nya målnivåerna har uppnåtts.

När du skickar kommandot, finns det inget sätt att avsluta det halvvägs igenom. Du måste vänta tills kommandot är klart innan du reviderar antalet.

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

Resultaten bör se ut ungefär som följande utdata.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 6
PartitionCount    : 6
HostingMode       : Default
Id                : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="next-steps"></a>Nästa steg

Skapa ett [index](search-what-is-an-index.md), [fråga ett index](search-query-overview.md) med hjälp av portalen, REST API:er eller .NET SDK.

* [Skapa ett Azure Cognitive Search-index i Azure-portalen](search-create-index-portal.md)
* [Ställ in en indexerare för att läsa in data från andra tjänster](search-indexer-overview.md)
* [Fråga ett Azure Cognitive Search-index med hjälp av Sök explorer i Azure-portalen](search-explorer.md)
* [Så här använder du Azure Cognitive Search i .NET](search-howto-dotnet-sdk.md)