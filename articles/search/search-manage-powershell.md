---
title: PowerShell-skript med hjälp av Az.Search modul – Azure Search
description: Skapa och konfigurera en Azure Search-tjänst med PowerShell. Du kan skala en tjänst upp eller ned, hantera admin och api-frågenycklarna och information om frågan.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: heidist
ms.openlocfilehash: 8f07468ccff4431e1afdf66aedc72599ddc0c25b
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58620605"
---
# <a name="manage-your-azure-search-service-with-powershell"></a>Hantera Azure Search-tjänsten med PowerShell
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [REST-API](https://docs.microsoft.com/rest/api/searchmanagement/)
> * [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Du kan köra PowerShell-cmdlets och skript på Windows, Linux, eller i [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) att skapa och konfigurera Azure Search. Den **Az.Search** modulen utökar Azure PowerShell] med fullständig paritet till den [Azure Search Management REST API: er](https://docs.microsoft.com/rest/api/searchmanagement). Med Azure PowerShell och **Az.Search**, du kan utföra följande uppgifter:

> [!div class="checklist"]
> * [Visa en lista över söktjänster i din prenumeration](#list-search-services)
> * [Få information om en specifik söktjänst](#get-search-service-information)
> * [Skapa eller ta bort en tjänst](#create-or-delete-a-service)
> * [Återskapa admin API-nycklar](#regenerate-admin-keys)
> * [Skapa eller ta bort api-frågenycklarna](#create-or-delete-query-keys)
> * [Skala en tjänst genom att öka eller minska repliker och partitioner](#scale-replicas-and-partitions)

PowerShell kan inte användas för att ändra namn, region eller nivå för din tjänst. Dedikerade resurser allokeras när en tjänst har skapats. Ändra den underliggande maskinvaran (plats eller nod-typ) kräver en ny tjänst. Det finns inga verktyg eller API: er för att överföra innehåll från en tjänst till en annan. Alla innehållshantering är via [REST](https://docs.microsoft.com/rest/api/searchservice/) eller [.NET](https://docs.microsoft.com/dotnet/api/?term=microsoft.azure.search) API: er, och du vill flytta index, måste du återskapa och läsa in dem på en ny tjänst. 

Det finns inga dedikerade PowerShell-kommandon för innehållshantering, kan du skriva PowerShell-skript som anropar REST- eller .NET för att skapa och läsa in index. Den **Az.Search** modulen ensamt tillhandahåller inte de här åtgärderna.

Andra uppgifter som inte stöds via PowerShell eller andra API: er (endast portal) är:
+ [Bifoga en resurs för cognitive services](cognitive-search-attach-cognitive-services.md) för [AI-utökad indexering](cognitive-search-concept-intro.md). En cognitive service är kopplad till en kompetens, inte en prenumeration eller en tjänst.
+ [Tillägg övervakningslösningar](search-monitor-usage.md#add-on-monitoring-solutions) eller [söktrafikanalys](search-traffic-analytics.md) används för att övervaka Azure Search.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>Kontrollera versioner och läsa in moduler

Exemplen i den här artikeln är interaktiva och kräver förhöjd behörighet. Azure PowerShell (den **Az** module) måste vara installerad. Mer information finns i [installera Azure PowerShell](/powershell/azure/overview).

### <a name="powershell-version-check-51-or-later"></a>Kontroll av PowerShell-version (5.1 eller senare)

Lokala PowerShell måste vara 5.1 eller senare på alla operativsystem som stöds.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Load Azure PowerShell

Om du inte vet om **Az** har installerats kör du följande kommando som ett verifieringssteg. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

Vissa system gör inte automatiskt belastningen moduler. Om det uppstår ett fel på det föregående kommandot försök att läsa in modulen och om det misslyckas så gå tillbaka till instruktioner för installation för att se om du missat något steg.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Ansluta till Azure med en webbläsare inloggning token

Du kan använda din portal inloggningsuppgifter för att ansluta till en prenumeration i PowerShell. Du kan också [autentisera icke-interaktivt med ett huvudnamn för tjänsten](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

```azurepowershell-interactive
Connect-AzAccount
```

Om du har flera Azure-prenumerationer, ange din Azure-prenumeration. Kör det här kommandot om du vill se en lista över dina aktuella prenumerationer.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Kör följande kommando för att ange prenumerationen. I följande exempel prenumerationens namn är `ContosoSubscription`.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-all-azure-search-services-in-your-subscription"></a>Lista över alla Azure Search-tjänsterna i din prenumeration

Följande kommandon är från [ **Az.Resources**](https://docs.microsoft.com/powershell/module/az.resources/?view=azps-1.4.0#resources), returnerar information om befintliga resurser och tjänster som redan har etablerat i din prenumeration. Om du inte vet hur många söktjänster redan har skapats, returnera dessa kommandon den information som gör att du sparar en resa till portalen.

Det första kommandot returnerar alla söktjänster.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

Returnera information om en specifik resurs i listan över tjänster.

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

Resultatet bör likna följande utdata.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alpha-numeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>Importera Az.Search

Kommandon från [ **Az.Search** ](https://docs.microsoft.com/powershell/module/az.search/?view=azps-1.4.0#search) är inte tillgängliga förrän du läsa in modulen.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>Lista alla Az.Search kommandon

Returnera en lista över kommandon som finns i modulen som ett verifieringssteg.

```azurepowershell-interactive
Get-Command -Module Az.Search
```

Resultatet bör likna följande utdata.

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

Efter **Az.Search** har importerats och du vet att resursgruppen som innehåller din söktjänst kör [Get-AzSearchService](https://docs.microsoft.com/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0) att returnera service-definitionen, inklusive namn, region, nivå och repliken och partition räknas.

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

Resultatet bör likna följande utdata.

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

[**Ny AzSearchService** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) används för att [skapa en ny söktjänst](search-create-service-portal.md).

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName "demo-westus" -Name "my-demo-searchapp" -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3
``` 
Resultatet bör likna följande utdata.

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

## <a name="regenerate-admin-keys"></a>Återskapa administratörsnycklar

[**Ny AzSearchAdminKey** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) används för att rulla över admin [API-nycklar](search-security-api-keys.md). Två administratörsnycklar skapas med varje tjänst för autentiserad åtkomst. Nycklar krävs på varje begäran. Båda administratörsnycklar har samma funktioner, bevilja fullständig åtkomst till en söktjänst med möjlighet att hämta information, eller skapa och ta bort alla objekt. Det finns två nycklar så att du kan använda en när den andra. 

Du kan bara återskapa en i taget, anges som antingen den `primary` eller `secondary` nyckel. Kom ihåg att uppdatera alla klientkod för att använda en sekundär när över den primära nyckeln för att undvika avbrott i tjänsten. Undvik att ändra nycklarna medan åtgärder som rör sig.

Som förväntat, om du återskapar nycklarna utan att uppdatera klientkod, misslyckas begäranden som använder den gamla nyckeln. Återskapande av alla nya nycklar inte permanent låsa dig ute från din tjänst och du kan fortfarande komma åt tjänsten via portalen. När du har återskapat primära och sekundära nycklarna kan du uppdatera klientkod för att använda de nya nycklarna och åtgärder kommer att återupptas därefter.

Värden för API-nycklar genereras av tjänsten. Du kan inte ange en anpassad nyckel för Azure Search att använda. Det finns på samma sätt kan inga användardefinierat namn för admin API-nycklar. Referenser till nyckeln är fasta strängar, antingen `primary` eller `secondary`. 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

Resultatet bör likna följande utdata. Båda nycklarna returneras även om du bara ändra en i taget.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>Skapa eller ta bort frågenycklar

[**Ny AzSearchQueryKey** ](https://docs.microsoft.com/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0) används för att skapa fråga [API-nycklar](search-security-api-keys.md) för skrivskyddad åtkomst från klientprogram till ett Azure Search-index. Frågenycklar används för att autentisera till ett specifikt index för att hämta sökresultaten. Frågenycklar bevilja inte skrivskyddad åtkomst till andra artiklar på tjänst, till exempel ett index, datakälla eller indexerare.

Du kan inte ange en nyckel för Azure Search för att använda. API-nycklar genereras av tjänsten.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>Skala repliker och partitioner

[**Set-AzSearchService** ](https://docs.microsoft.com/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0) används för att [öka eller minska repliker och partitioner](search-capacity-planning.md) justera fakturerbara resurser i din tjänst. Öka repliker eller partitioner lägger till i din faktura, som har både fasta och variabla avgifter. Om du har ett tillfälligt behov av mer processorkraft, kan du öka repliker och partitioner för att hantera arbetsbelastningen. Området för övervakning på sidan Översikt portal innehåller paneler på svarstid, frågor per sekund, och begränsning, som anger om aktuella kapaciteten är tillräckliga.

Det kan ta en stund att lägga till eller ta bort resurser. Justeringar av kapacitet sker i bakgrunden, så att befintliga arbetsbelastningar att fortsätta. Ytterligare kapacitet används för inkommande begäranden när den är klar med någon ytterligare konfiguration krävs. 

Tar bort kapacitet kan vara störande. Stoppa alla jobb innan minskar kapaciteten som indexering och indexerare rekommenderas för att undvika förlorade begäranden. Om det inte är möjligt kan du minska kapaciteten stegvis, en replik och partition i taget tills din nya mål-nivåer har uppnåtts.

När du skickar in kommandot, går det inte att avsluta mitt via. Du måste vänta tills kommandot har slutförts innan uppdatering av antalen.

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

Resultatet bör likna följande utdata.

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

Skapa en [index](search-what-is-an-index.md), [fråga ett index](search-query-overview.md) med portalen, REST API: er eller .NET SDK.

* [Skapa ett Azure Search-index i Azure portal](search-create-index-portal.md)
* [Konfigurera en indexerare för att läsa in data från andra tjänster](search-indexer-overview.md)
* [Fråga ett Azure Search-index med Sökutforskaren i Azure portal](search-explorer.md)
* [Hur du använder Azure Search i .NET](search-howto-dotnet-sdk.md)