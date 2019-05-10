---
title: Skapa en Azure Search-tjänst i portalen – Azure Search
description: Etablera en Azure Search-resurs i Azure-portalen. Välj resursgrupper, regioner, samt SKU eller prisnivå.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: quickstart
ms.date: 05/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 9de8fa297c2b3f89f74b17e0dac3eab007367b9b
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65471605"
---
# <a name="create-an-azure-search-service-in-the-portal"></a>Skapa en Azure Search-tjänst i portalen

Azure Search är en fristående-resurs som används för att koppla in en sökfunktion i anpassade appar. Azure Search integreras enkelt med många andra Azure-tjänster, men du kan även använda det separat, med appar på nätverksservrar eller med programvara som körs på andra molnplattformar.

I den här artikeln lär du dig hur du skapar en Azure Search-resurs i [Azure Portal](https://portal.azure.com/).

[![Animerad GIF](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

Föredrar du PowerShell? Använd [tjänstmallen](https://azure.microsoft.com/resources/templates/101-azure-search-create/) för Azure Resource Manager. Om du behöver hjälp att komma igång går du till [Hantera Azure Search med PowerShell](search-manage-powershell.md).

## <a name="subscribe-free-or-paid"></a>Prenumerera (kostnadsfritt eller betalt)

[Öppna ett kostnadsfritt Azure-konto](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) och använd kostnadsfria krediter för att prova betalda Azure-tjänster. Efter att krediterna är slut behåller du kontot och fortsätter använda kostnadsfria Azure-tjänster, till exempel Websites. Ditt kreditkort debiteras aldrig om du inte specifikt ändrar dina inställningar och ber om debitering.

Du kan också [aktivera MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Med en MSDN-prenumeration får du krediter varje månad som du kan använda Azure-betaltjänster. 

## <a name="find-azure-search"></a>Hitta Azure Search

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Klicka på plustecknet (”+ Skapa resurs”) i det övre vänstra hörnet.
3. Använd sökfältet för att hitta ”Azure Search” eller gå till resursen via **Web** > **Azure Search**.

![Navigera till en Azure Search-resurs](./media/search-create-service-portal/find-search3.png "Navigeringssökväg till Azure Search")

## <a name="name-the-service-and-url-endpoint"></a>Namnge tjänsten och URL-slutpunkten

Ett tjänstnamn är en del av URL-slutpunkten mot vilken API-anrop utfärdas: `https://your-service-name.search.windows.net`. Ange tjänstnamnet i fältet **URL**.

Till exempel, om du vill att slutpunkten ska vara `https://my-app-name-01.search.windows.net`, anger du `my-app-name-01`.

Kraven för tjänstnamn:

* Det måste vara unikt inom namnområdet search.windows.net
* 2 och 60 tecken
* Använd gemena bokstäver, siffror och bindestreck (”-”)
* Undvik streck (”-”) i de första 2 tecknen eller som sista enskilt tecken
* Inga streck i följd (”--”) någonstans

## <a name="select-a-subscription"></a>Välj en prenumeration

Om du har mer än en prenumeration väljer du en som även har data- eller fillagringstjänster. Azure Search kan automatiskt identifiera Azure Table- och Blob-lagring, SQL Database och Azure Cosmos DB för indexering via [*indexerare*](search-indexer-overview.md), men endast för tjänster i samma prenumeration.

## <a name="select-a-resource-group"></a>Välj en resursgrupp

En resursgrupp är en samling Azure-tjänster och Azure-resurser som används tillsammans. Om du till exempel använder Azure Search för att indexera en SQL-databas måste båda tjänsterna tillhöra samma resursgrupp.

Om du inte kombinerar resurser i en grupp eller om befintliga resursgrupper är fyllda med resurser som används i orelaterade lösningar, ska du skapa en ny resursgrupp enbart för din Azure Search-resurs.

> [!TIP]
> Om du tar bort en resursgrupp tar du också bort tjänsterna i gruppen. Om du har ett prototypprojekt som använder flera tjänster kan du placera dem i samma resursgrupp. Då är det lättare att rensa upp när projektet är slutfört.

## <a name="select-a-location"></a>Välj en plats

Azure Search är en Azure-tjänst som kan finnas i datacenter över hela världen. [Priserna kan variera](https://azure.microsoft.com/pricing/details/search/) efter geografiskt område.

Om du indexerar data från en annan Azure service (Azure storage, Azure Cosmos DB, Azure SQL Database), skapa Azure Search-tjänsten i samma region för att undvika kostnader för bandbredd. Det finns inga avgifter för utgående data när tjänster som är i samma region.

Om du använder kognitiv sökning AI enrichments kan du skapa din tjänst i samma region som din resurs för Cognitive Services. Samplacering av tjänster är ett krav för AI-funktioner.

## <a name="select-a-pricing-tier-sku"></a>Välj en prisnivå (SKU)

[Azure Search finns för närvarande med flera olika prisnivåer](https://azure.microsoft.com/pricing/details/search/): Kostnadsfri, Basic eller Standard. Nivåerna har olika [kapacitet och begränsningar](search-limits-quotas-capacity.md). Mer information finns i [Välj en prisnivå nivå eller SKU](search-sku-tier.md).

Standard väljs normalt för produktionsarbetsbelastningar men de flesta kunderna börjar med den kostnadsfria tjänsten.

När tjänsten väl har skapats går det inte att ändra prisnivå. Om du senare behöver en högre eller lägre nivå måste du skapa tjänsten på nytt.

## <a name="create-your-service"></a>Skapa din tjänst

Kom ihåg att fästa din tjänst på instrumentpanelen för enkel åtkomst när du loggar in.

![Fäst på instrumentpanelen](./media/search-create-service-portal/new-service3.png "Fäst resursen på instrumentpanelen för snabb åtkomst")

## <a name="get-a-key-and-url-endpoint"></a>Hämta en nyckel och URL-slutpunkt

Med några undantag kräver användning av din nya tjänst att du anger URL-slutpunkten och en API-nyckel för auktorisering. Snabbstarter, självstudier som [Utforska Azure Search REST-API:er (Postman)](search-fiddler.md) och [Så använder du Azure Search från .NET](search-howto-dotnet-sdk.md), exempel och anpassad kod behöver en slutpunkt och nyckel för att köras på just din resurs.

1. På översiktssidan för tjänsten letar du upp och kopierar URL-slutpunkten till höger på sidan.

   ![Översiktssidan för tjänst med URL-slutpunkt](./media/search-create-service-portal/url-endpoint.png "URL-slutpunkt och annan tjänstinformation")

2. I det vänstra navigeringsfönstret väljer du **Nycklar** och kopierar sedan någon av administratörsnycklarna (de är likvärdiga). API-administratörsnycklarna krävs för att skapa, uppdatera och ta bort objekt i tjänsten.

   ![Nyckelsida som visar primär- och sekundärnycklar](./media/search-create-service-portal/admin-api-keys.png "API-administratörsnycklar för auktorisering")

Det behövs ingen slutpunkt eller nyckel för portalbaserade uppgifter. Portalen är redan länkad till din Azure Search-resurs med administratörsrättigheter. För en portalsjälvstudie börjar du med [Självstudie: Importera, indexera och köra frågor i Azure Search](search-get-started-portal.md).

## <a name="scale-your-service"></a>Skala din tjänst

Det kan ta några minuter att skapa en tjänst (15 minuter eller mer beroende på nivå). När tjänsten har etablerats kan du skala den så att den passar dina behov. Eftersom du har valt standardnivån för din Azure Search-tjänst kan du skala tjänsten i två dimensioner: repliker och partitioner. Om du hade valt Basic-nivån hade du bara kunnat lägga till repliker. Skalning är inte tillgängligt om du etablerar tjänsten utan kostnad.

Med ***Partitioner*** kan tjänsten lagra och söka igenom fler dokument.

Med ***Repliker*** kan tjänsten hantera en högre belastning av sökfrågor.

Om du lägger till resurser blir din månatliga faktura större. [Priskalkylatorn](https://azure.microsoft.com/pricing/calculator/) kan hjälpa dig att förstå hur fakturering påverkas när du lägger till resurser. Kom ihåg att du kan ändra resurser baserat på belastning. Du kan till exempel öka resurserna för att skapa den fullständiga initiala indexeringen och sedan minska resurserna till en nivå som är mer lämplig för inkrementell indexering.

> [!Important]
> En tjänst måste ha [2 repliker för skrivskyddad SLA och 3 repliker för läs-/skriv-SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Gå till söktjänstsidan i Azure-portalen.
2. Välj **Inställningar** > **Skala** i det vänstra navigeringsfönstret.
3. Använd reglaget om du vill lägga till resurser av endera typ.

![Lägga till kapacitet](./media/search-create-service-portal/settings-scale.png "Lägga till kapacitet via repliker och partitioner")

> [!Note]
> Varje nivå har olika [begränsningar](search-limits-quotas-capacity.md) för det totala antalet Search-enheter som tillåts i en enskild tjänst (repliker * partitioner = totalt antal Search-enheter).

## <a name="when-to-add-a-second-service"></a>När ska du lägga till en andra tjänst?

De flesta kunder använder bara en tjänst som etablerats på en nivå som har [rätt resursbalans](search-sku-tier.md). En tjänst kan vara värd för flera index i enlighet med [gränsvärdena för den nivå du väljer](search-capacity-planning.md), med indexen isolerade från varandra. I Azure Search kan förfrågningar bara riktas till ett index, vilket minimerar risken för oavsiktlig eller avsiktlig datahämtning från andra index i samma tjänst.

Även om de flesta kunder bara använder en tjänst kan det vara nödvändigt med tjänstredundans om de operativa kraven omfattar följande:

* Haveriberedskap (avbrott i datacenter). Det finns ingen omedelbar redundans i Azure Search vid ett avbrott. Mer information och rekommendationer finns i [Tjänstadministration](search-manage.md).
* Din undersökning av modeller med flera klientorganisationer har fastställt att ytterligare tjänster är den optimala designen. Mer information finns i [Design för flera klientorganisationer](search-modeling-multitenant-saas-applications.md).
* Om du har globalt distribuerade program kan du behöva en instans av Azure Search i flera olika områden för att minimera svarstiden för programmets internationella trafik.

> [!NOTE]
> I Azure Search går det inte att separera indexering och frågearbetsbelastningar. Därför skapar du aldrig flera tjänster för separerade arbetsbelastningar. Index tillfrågas alltid om vilken tjänst den skapades i (du kan inte skapa ett index i en tjänst och kopiera den till en annan).

Det behövs ingen andra tjänst för hög tillgänglighet. Hög tillgänglighet för frågor uppnås när du använder minst 2 repliker i samma tjänst. Replikuppdateringar är sekventiella, vilket innebär att minst en fungerar när en ny tjänstuppdatering görs. Mer information om drifttid finns i [Serviceavtal](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Nästa steg

Du kan fortsätta i portalen för att skapa ditt första index när du har etablerat en Azure Search-tjänst.

> [!div class="nextstepaction"]
> [Självstudie: Importera data, indexera och köra frågor i portalen](search-get-started-portal.md)
