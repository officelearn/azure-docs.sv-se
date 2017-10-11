---
title: "Skapa en Azure Search-tjänst i portal | Microsoft Docs"
description: "Etablera en Azure Search-tjänst i portalen."
services: search
manager: jhubbard
author: HeidiSteen
documentationcenter: 
ms.assetid: c8c88922-69aa-4099-b817-60f7b54e62df
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: heidist
ms.openlocfilehash: 58f4eab190e40e16ed261c165ffdfc8155eeb434
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="create-an-azure-search-service-in-the-portal"></a>Skapa en Azure Search-tjänst i portalen

Den här artikeln förklarar hur du skapar eller etablera ett Azure Search-tjänsten i portalen. PowerShell instruktioner finns i [hantera Azure Search med PowerShell](search-manage-powershell.md).

## <a name="subscribe-free-or-paid"></a>Prenumerera (ledigt eller betald)

[Öppna ett kostnadsfritt Azure-konto](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) och använda kostnadsfria krediter för att testa betald Azure-tjänster. Efter att krediten är slut, behålla kontot och fortsätta att använda kostnadsfria Azure-tjänster, till exempel Websites. Ditt kreditkort debiteras aldrig såvida du inte uttryckligen ändrar dina inställningar och ber.

Du kan också [aktivera MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). En MSDN-prenumeration ger dig krediter varje månad som du kan använda för Azure-betaltjänster. 

## <a name="find-azure-search"></a>Hitta Azure Search
1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Klicka på plustecknet (”+”) i det övre vänstra hörnet.
3. Välj **webb + mobilt** > **Azure Search**.

![](./media/search-create-service-portal/find-search2.png)

## <a name="name-the-service-and-url-endpoint"></a>Namnet på tjänsten och URL-slutpunkt

Ett tjänstnamn är en del av URL-slutpunkt mot vilken API-anrop har utfärdats. Skriv ditt Tjänstenamn i den **URL** fältet. 

Kraven för tjänsten:
   * 2 och 60 tecken
   * gemena bokstäver, siffror eller bindestreck (”-”)
   * Inga streck (”-”) som den första 2 tecken eller sista tecken
   * streck i följd (”--”)

## <a name="select-a-subscription"></a>Välj en prenumeration
Om du har mer än en prenumeration väljer du en som också har data eller file storage-tjänster. Azure Search kan automatisk identifiering av Azure Table och Blob storage, SQL Database och Azure Cosmos DB för indexering *indexerare*, men endast för tjänster i samma prenumeration.

## <a name="select-a-resource-group"></a>Välj en resursgrupp
En resursgrupp är en samling Azure-tjänster och resurser som används tillsammans. Till exempel om du använder Azure Search ska indexera en SQL-databas, måste sedan båda tjänsterna tillhöra samma resursgrupp.

> [!TIP]
> En resursgrupp också tar du bort tjänster i den. För prototyp projekt använder flera tjänster, underlättar placerar dem i samma resursgrupp Rensa när projektet är över. 

## <a name="select-a-hosting-location"></a>Välj en värdplats 
Azure Search kan finnas i datacenter runtom i världen som en Azure-tjänst. Observera att [priser kan skilja sig](https://azure.microsoft.com/pricing/details/search/) efter geografi.

## <a name="select-a-pricing-tier-sku"></a>Välj en prisnivå (SKU)
[Azure Search är för närvarande finns flera prisnivåer](https://azure.microsoft.com/pricing/details/search/): ledigt, Basic eller Standard. Varje nivå har sin egen [kapacitet och gränser](search-limits-quotas-capacity.md). Se [Välj en prisnivå nivå eller SKU](search-sku-tier.md) anvisningar.

I den här genomgången, har vi valt standardnivån för vår tjänst.

## <a name="create-your-service"></a>Skapa din tjänst

Kom ihåg att fästa din tjänst på instrumentpanelen för enkel åtkomst när du loggar in.

![](./media/search-create-service-portal/new-service2.png)

## <a name="scale-your-service"></a>Skala din tjänst
Det kan ta några minuter att skapa en tjänst (15 minuter eller mer beroende på nivån). När tjänsten har etablerats kan du skala den för att uppfylla dina behov. Eftersom du har valt standardnivån för din Azure Search-tjänst kan du skala din tjänst i två dimensioner: repliker och partitioner. Hade du valt den grundläggande nivån, du kan bara lägga till repliker. Skalning är inte tillgängligt om du har etablerat tjänsten gratis.

***Partitioner*** Tillåt din tjänst att lagra och söka igenom flera dokument.

***Repliker*** Tillåt din tjänst att hantera en högre belastning av sökfrågor.

> [!Important]
> En tjänst måste ha [2 repliker för skrivskyddade SLA och 3 repliker för läsning och skrivning SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Gå till din sökning service bladet i Azure-portalen.
2. I det vänstra navigeringsfönstret, Välj **inställningar** > **skala**.
3. Använd slidebar om du vill lägga till repliker eller partitioner.

![](./media/search-create-service-portal/settings-scale.png)

> [!Note] 
> Varje nivå har olika [gränser](search-limits-quotas-capacity.md) på det totala antalet Search-enheter som tillåts i en enskild tjänst (repliker * partitioner = Totalt antal Search-enheter).

## <a name="when-to-add-a-second-service"></a>När du ska lägga till en andra tjänst

En stor majoritet av kunder använder en tjänst som etablerats på en nivå som innehåller den [Högerklicka balans mellan resurser](search-sku-tier.md). En tjänst kan vara värd för flera index föremål för den [gränsvärden för nivån som du väljer](search-capacity-planning.md), med varje index isolerade från varandra. I Azure Search dirigeras begäranden endast till ett index som minimerar risken för att hämta oavsiktligt eller avsiktligt data från andra index i samma tjänst.

Även om de flesta kunder använder en tjänst, kan service-redundans vara nödvändigt om operativa krav för inkluderar följande:

+ Katastrofåterställning (data center avbrott). Azure Search ger inte några omedelbara växling vid fel vid ett avbrott. Vägledning och rekommendationer finns [tjänsten administration](search-manage.md).
+ Din undersökning av multitenans modellering har fastställt att ytterligare tjänster är den optimala designen. Mer information finns i [Design för multitenans](search-modeling-multitenant-saas-applications.md).
+ Du kan behöva en instans av Azure Search i flera områden för att minimera svarstiden för ditt program internationell trafik för globalt distribuerade program.

> [!NOTE]
> Du kan särskilja indexering och frågar arbetsbelastningar; i Azure Search Därför skulle du skapa flera tjänster för åtskilda arbetsbelastningar aldrig. Ett index efterfrågas alltid på tjänsten som den skapades (du kan inte skapa ett index i en tjänst och kopiera den till en annan).
>

En andra tjänst krävs inte för hög tillgänglighet. Hög tillgänglighet för frågor uppnås när du använder 2 eller högre repliker i samma tjänst. Replik uppdateringar är sekventiella, vilket innebär att minst en fungerar när en tjänstuppdatering lyfts. Läs mer om drifttid [serviceavtal](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Nästa steg
När du har etablerat en Azure Search-tjänst, är du redo att [definiera ett index](search-what-is-an-index.md) så att du kan ladda upp och söka efter data.

Ange en URL för att komma åt tjänsten från kod eller skript (*Tjänstenamn*. search.windows.net) och en nyckel. Admin nycklar ger fullständig åtkomst; frågan nycklar bevilja läsåtkomst. Se [hur du använder Azure Search i .NET](search-howto-dotnet-sdk.md) att komma igång.

Se [bygga och fråga ditt första index](search-get-started-portal.md) en snabb genomgång av portal-baserade.

