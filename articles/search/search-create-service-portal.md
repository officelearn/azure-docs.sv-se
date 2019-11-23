---
title: 'Quickstart: Create a search service in the portal'
titleSuffix: Azure Cognitive Search
description: In this portal quickstart, learn how to set up an Azure Cognitive Search resource in the Azure portal. Välj resursgrupper, regioner, samt SKU eller prisnivå.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: 79554429ddc9516eb2fbe9aa7b0e75c78fb17662
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406677"
---
# <a name="quickstart-create-an-azure-cognitive-search-service-in-the-portal"></a>Quickstart: Create an Azure Cognitive Search service in the portal

Azure Cognitive Search is a standalone resource used to plug in a search experience in custom apps. Although Azure Cognitive Search integrates easily with other Azure services, you can also use it as a standalone component, or integrate it with apps on network servers, or with software running on other cloud platforms.

In this article, learn how to create a resource in the [Azure portal](https://portal.azure.com/).

[![Animerad GIF](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

Föredrar du PowerShell? Använd [tjänstmallen](https://azure.microsoft.com/resources/templates/101-azure-search-create/) för Azure Resource Manager. For help with getting started, see [Manage Azure Cognitive Search with PowerShell](search-manage-powershell.md).

## <a name="subscribe-free-or-paid"></a>Prenumerera (kostnadsfritt eller betalt)

[Öppna ett kostnadsfritt Azure-konto](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) och använd kostnadsfria krediter för att prova betalda Azure-tjänster. Efter att krediterna är slut behåller du kontot och fortsätter använda kostnadsfria Azure-tjänster, till exempel Websites. Ditt kreditkort debiteras aldrig om du inte specifikt ändrar dina inställningar och ber om debitering.

Du kan också [aktivera MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Med en MSDN-prenumeration får du krediter varje månad som du kan använda Azure-betaltjänster. 

## <a name="find-azure-cognitive-search"></a>Find Azure Cognitive Search

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på plustecknet (”+ Skapa resurs”) i det övre vänstra hörnet.
3. Use the search bar to find "Azure Cognitive Search" or navigate to the resource through **Web** > **Azure Cognitive Search**.

![Create a resource in the portal](./media/search-create-service-portal/find-search3.png "Skapa en resurs i portalen")

## <a name="choose-a-subscription"></a>Choose a subscription

Setting the subscription ID and resource group is your first step. Om du har mer än en prenumeration väljer du en som även har data- eller fillagringstjänster. Azure Cognitive Search can autodetect Azure Table and Blob storage, SQL Database, and Azure Cosmos DB for indexing via [*indexers*](search-indexer-overview.md), but only for services under the same subscription.

## <a name="set-a-resource-group"></a>Set a resource group

A resource group is required and is useful for managing resources all-up, including costs. A resource group can consist of one service, or multiple services used together. For example, if you are using Azure Cognitive Search to index an Azure Cosmos DB database, you could make both services part of the same resource group for management purposes. 

If you aren't combining resources into a single group, or if existing resource groups are filled with resources used in unrelated solutions, create a new resource group just for your Azure Cognitive Search resource. 

![Create a new resource group](./media/search-create-service-portal/new-resource-group.png "Skapa en ny resursgrupp")

Over time, you can track current and projected costs all-up (as shown in the screenshot) or scroll down to view charges for individual resources. The following screenshot shows the kind of cost information you can eventually expect to see when you combine multiple resources into one group.

![Manage costs at the resource group level](./media/search-create-service-portal/resource-group-cost-management.png "Manage costs at the resource group level")

> [!TIP]
> Resource groups simplify cleanup because deleting a group also deletes the services within it. Om du har ett prototypprojekt som använder flera tjänster kan du placera dem i samma resursgrupp. Då är det lättare att rensa upp när projektet är slutfört.

## <a name="name-the-service"></a>Name the service

In Instance Details, provide a service name in the **URL** field. The name is part of the URL endpoint against which API calls are issued: `https://your-service-name.search.windows.net`. Till exempel, om du vill att slutpunkten ska vara `https://myservice.search.windows.net`, anger du `myservice`.

Kraven för tjänstnamn:

* Det måste vara unikt inom namnområdet search.windows.net
* 2 och 60 tecken
* Använd gemena bokstäver, siffror och bindestreck (”-”)
* Undvik streck (”-”) i de första 2 tecknen eller som sista enskilt tecken
* Inga streck i följd (”--”) någonstans

> [!TIP]
> If you think you'll be using multiple services, we recommend including the region (or location) in the service name as a naming convention. Services within the same region can exchange data at no charge, so if Azure Cognitive Search is in West US, and you have other services also in West US, a name like `mysearchservice-westus` can save you a trip to the properties page when deciding how to combine or attach resources.

## <a name="choose-a-location"></a>Choose a location

As an Azure service, Azure Cognitive Search can be hosted in datacenters around the world. The list of supported regions can be found in the [pricing page](https://azure.microsoft.com/pricing/details/search/). 

You can minimize or avoid bandwidth charges by choosing the same location for multiple services. For example, if you are indexing data provided by another Azure service (Azure storage, Azure Cosmos DB, Azure SQL Database), creating your Azure Cognitive Search service in the same region avoids bandwidth charges (there are no charges for outbound data when services are in the same region).

Additionally, if you are using AI enrichment, create your service in the same region as Cognitive Services. *Co-location of Azure Cognitive Search and Cognitive Services in the same region is a requirement for AI enrichment*.

> [!Note]
> Central India is currently unavailable for new services. For services already in Central India, you can scale up with no restrictions, and your service is fully supported in that region. The restriction on this region is temporary and limited to new services only. We will remove this note when the restriction no longer applies.

## <a name="choose-a-pricing-tier-sku"></a>Choose a pricing tier (SKU)

[Azure Cognitive Search is currently offered in multiple pricing tiers](https://azure.microsoft.com/pricing/details/search/): Free, Basic, or Standard. Nivåerna har olika [kapacitet och begränsningar](search-limits-quotas-capacity.md). Mer information finns i [Välj en prisnivå nivå eller SKU](search-sku-tier.md).

Basic and Standard are the most common choices for production workloads, but most customers start with the Free service. Key differences among tiers is partition size and speed, and limits on the number of objects you can create.

Remember that a pricing tier cannot be changed once the service is created. Om du senare behöver en högre eller lägre nivå måste du skapa tjänsten på nytt.

## <a name="create-your-service"></a>Skapa din tjänst

After you've provided the necessary inputs, go ahead and create the service. 

![Review and create the service](./media/search-create-service-portal/new-service3.png "Review and create the service")

Your service is deployed within minutes, which you can monitor through Azure notifications. Consider pinning the service to your dashboard for easy access in the future.

![Monitor and pin the service](./media/search-create-service-portal/monitor-notifications.png "Monitor and pin the service")

## <a name="get-a-key-and-url-endpoint"></a>Hämta en nyckel och URL-slutpunkt

Unless you are using the portal, programmatic access to your new service requires that you provide the URL endpoint and an authentication api-key.

1. On the **Overview** page, locate and copy the URL endpoint on the right side of the page.

2. On the **Keys** page, copy either one of the admin keys (they are equivalent). API-administratörsnycklarna krävs för att skapa, uppdatera och ta bort objekt i tjänsten. In contrast, query keys provide read-access to index content.

   ![Service overview page with URL endpoint](./media/search-create-service-portal/get-url-key.png "URL endpoint and other service details")

Det behövs ingen slutpunkt eller nyckel för portalbaserade uppgifter. The portal is already linked to your Azure Cognitive Search resource with admin rights. For a portal walkthrough, start with [Quickstart: Create an Azure Cognitive Search index in the portal](search-get-started-portal.md).

## <a name="scale-your-service"></a>Skala din tjänst

När tjänsten har etablerats kan du skala den så att den passar dina behov. If you chose the Standard tier for your Azure Cognitive Search service, you can scale your service in two dimensions: replicas and partitions. Om du hade valt Basic-nivån hade du bara kunnat lägga till repliker. Skalning är inte tillgängligt om du etablerar tjänsten utan kostnad.

Med ***Partitioner*** kan tjänsten lagra och söka igenom fler dokument.

Med ***Repliker*** kan tjänsten hantera en högre belastning av sökfrågor.

Om du lägger till resurser blir din månatliga faktura större. [Priskalkylatorn](https://azure.microsoft.com/pricing/calculator/) kan hjälpa dig att förstå hur fakturering påverkas när du lägger till resurser. Kom ihåg att du kan ändra resurser baserat på belastning. Du kan till exempel öka resurserna för att skapa den fullständiga initiala indexeringen och sedan minska resurserna till en nivå som är mer lämplig för inkrementell indexering.

> [!Important]
> En tjänst måste ha [2 repliker för skrivskyddad SLA och 3 repliker för läs-/skriv-SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Gå till söktjänstsidan i Azure-portalen.
2. Välj **Inställningar** > **Skala** i det vänstra navigeringsfönstret.
3. Använd reglaget om du vill lägga till resurser av endera typ.

![Add capacity](./media/search-create-service-portal/settings-scale.png "Add capacity through replicas and partitions")

> [!Note]
> Per-partition storage and speed increases at higher tiers. For more information, see [capacity and limits](search-limits-quotas-capacity.md).

## <a name="when-to-add-a-second-service"></a>När ska du lägga till en andra tjänst?

De flesta kunder använder bara en tjänst som etablerats på en nivå som har [rätt resursbalans](search-sku-tier.md). En tjänst kan vara värd för flera index i enlighet med [gränsvärdena för den nivå du väljer](search-capacity-planning.md), med indexen isolerade från varandra. In Azure Cognitive Search, requests can only be directed to one index, minimizing the chance of accidental or intentional data retrieval from other indexes in the same service.

Även om de flesta kunder bara använder en tjänst kan det vara nödvändigt med tjänstredundans om de operativa kraven omfattar följande:

* Haveriberedskap (avbrott i datacenter). Azure Cognitive Search does not provide instant failover in the event of an outage. Mer information och rekommendationer finns i [Tjänstadministration](search-manage.md).
* Din undersökning av modeller med flera klientorganisationer har fastställt att ytterligare tjänster är den optimala designen. Mer information finns i [Design för flera klientorganisationer](search-modeling-multitenant-saas-applications.md).
* For globally deployed applications, you might require an instance of Azure Cognitive Search in multiple regions to minimize latency of your application’s international traffic.

> [!NOTE]
> In Azure Cognitive Search, you cannot segregate indexing and querying operations; thus, you would never create multiple services for segregated workloads. Index tillfrågas alltid om vilken tjänst den skapades i (du kan inte skapa ett index i en tjänst och kopiera den till en annan).

Det behövs ingen andra tjänst för hög tillgänglighet. Hög tillgänglighet för frågor uppnås när du använder minst 2 repliker i samma tjänst. Replica updates are sequential, which means at least one is operational when a service update is rolled out. For more information about uptime, see [Service Level Agreements](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Nästa steg

After provisioning a service, you can continue in the portal to create your first index.

> [!div class="nextstepaction"]
> [Quickstart: Create an Azure Cognitive Search index in the portal](search-get-started-portal.md)
