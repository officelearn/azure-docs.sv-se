---
title: 'Snabb start: skapa en Azure Kognitiv sökning-tjänst i portalen'
titleSuffix: Azure Cognitive Search
description: Etablera en AzureCognitive Sök resurs i Azure Portal. Välj resursgrupper, regioner, samt SKU eller prisnivå.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: 5acfb60395f39d17e640e389ae2a28e220ccfae6
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73884282"
---
# <a name="quickstart-create-an-azure-cognitive-search-service-in-the-portal"></a>Snabb start: skapa en Azure Kognitiv sökning-tjänst i portalen

Azure Kognitiv sökning är en fristående resurs som används för att koppla in en Sök funktion i anpassade appar. Även om Azure Kognitiv sökning enkelt integreras med andra Azure-tjänster, kan du också använda den som en fristående komponent eller integrera den med appar på nätverks servrar eller med program vara som körs på andra moln plattformar.

I den här artikeln får du lära dig hur du skapar en resurs i [Azure Portal](https://portal.azure.com/).

[![Animerad GIF](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

Föredrar du PowerShell? Använd [tjänstmallen](https://azure.microsoft.com/resources/templates/101-azure-search-create/) för Azure Resource Manager. Hjälp med att komma igång finns i [Hantera Azure kognitiv sökning med PowerShell](search-manage-powershell.md).

## <a name="subscribe-free-or-paid"></a>Prenumerera (kostnadsfritt eller betalt)

[Öppna ett kostnadsfritt Azure-konto](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) och använd kostnadsfria krediter för att prova betalda Azure-tjänster. Efter att krediterna är slut behåller du kontot och fortsätter använda kostnadsfria Azure-tjänster, till exempel Websites. Ditt kreditkort debiteras aldrig om du inte specifikt ändrar dina inställningar och ber om debitering.

Du kan också [aktivera MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Med en MSDN-prenumeration får du krediter varje månad som du kan använda Azure-betaltjänster. 

## <a name="find-azure-cognitive-search"></a>Hitta Azure-Kognitiv sökning

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Klicka på plustecknet (”+ Skapa resurs”) i det övre vänstra hörnet.
3. Använd Sök fältet för att hitta "Azure Kognitiv sökning" eller navigera till resursen via **webb** > Azure- **kognitiv sökning**.

![Skapa en resurs i portalen](./media/search-create-service-portal/find-search3.png "Skapa en resurs i portalen")

## <a name="choose-a-subscription"></a>Välj en prenumeration

Det första steget är att ange prenumerations-ID och resurs grupp. Om du har mer än en prenumeration väljer du en som även har data- eller fillagringstjänster. Azure Kognitiv sökning kan automatiskt identifiera Azure Table-och Blob Storage, SQL Database och Azure Cosmos DB för indexering via [*indexerare*](search-indexer-overview.md), men endast för tjänster under samma prenumeration.

## <a name="set-a-resource-group"></a>Ange en resurs grupp

En resurs grupp krävs och är användbar för att hantera alla resurser, inklusive kostnader. En resurs grupp kan bestå av en tjänst eller flera tjänster som används tillsammans. Om du till exempel använder Azure Kognitiv sökning för att indexera en Azure Cosmos DB databas kan du göra båda tjänsterna i samma resurs grupp i hanterings syfte. 

Om du inte kombinerar resurser till en enda grupp, eller om befintliga resurs grupper är fyllda med resurser som används i orelaterade lösningar, skapar du en ny resurs grupp för din Azure Kognitiv sökning-resurs. 

![Skapa en ny resurs grupp](./media/search-create-service-portal/new-resource-group.png "Skapa en ny resursgrupp")

Med tiden kan du spåra aktuella och projekterade kostnader alla (se skärm bilden) eller rulla nedåt för att Visa avgifter för enskilda resurser. Följande skärm bild visar vilken typ av kostnads information som du kan förväntar dig att se när du kombinerar flera resurser i en grupp.

![Hantera kostnader på resurs grupps nivå](./media/search-create-service-portal/resource-group-cost-management.png "Hantera kostnader på resurs grupps nivå")

> [!TIP]
> Resurs grupper fören klar rensningen eftersom borttagning av en grupp också tar bort tjänsterna i den. Om du har ett prototypprojekt som använder flera tjänster kan du placera dem i samma resursgrupp. Då är det lättare att rensa upp när projektet är slutfört.

## <a name="name-the-service"></a>Namnge tjänsten

I instans information anger du ett tjänst namn i **URL** -fältet. Namnet är en del av URL-slutpunkten mot vilken API-anrop utfärdas: `https://your-service-name.search.windows.net`. Till exempel, om du vill att slutpunkten ska vara `https://myservice.search.windows.net`, anger du `myservice`.

Kraven för tjänstnamn:

* Det måste vara unikt inom namnområdet search.windows.net
* 2 och 60 tecken
* Använd gemena bokstäver, siffror och bindestreck (”-”)
* Undvik streck (”-”) i de första 2 tecknen eller som sista enskilt tecken
* Inga streck i följd (”--”) någonstans

> [!TIP]
> Om du tror att du kommer att använda flera tjänster rekommenderar vi att du inkluderar regionen (eller platsen) i tjänst namnet som en namngivnings konvention. Tjänster inom samma region kan utbyta data utan kostnad, så om Azure Kognitiv sökning är i västra USA och du har andra tjänster även i västra USA, kan ett namn som `mysearchservice-westus` Spara en resa till sidan Egenskaper när du bestämmer dig för att kombinera eller bifoga resurser.

## <a name="choose-a-location"></a>Välj en plats

Som Azure-tjänst kan Azure Kognitiv sökning ligga i Data Center över hela världen. Du hittar en lista över regioner som stöds på [sidan med priser](https://azure.microsoft.com/pricing/details/search/). 

Du kan minimera eller undvika bandbredds avgifter genom att välja samma plats för flera tjänster. Om du till exempel indexerar data som tillhandahålls av en annan Azure-tjänst (Azure Storage, Azure Cosmos DB Azure SQL Database) och skapar din Azure Kognitiv sökning-tjänst i samma region, undviks bandbredds avgifter (inga avgifter för utgående data när tjänsterna finns i samma region).

Om du använder AI-anrikning skapar du dessutom din tjänst i samma region som Cognitive Services. *Samplacering av Azure kognitiv sökning och Cognitive Services i samma region är ett krav för AI-berikning*.

> [!Note]
> Centrala Indien är för närvarande inte tillgängligt för nya tjänster. För tjänster som redan finns i Central Indien kan du skala upp utan begränsningar och tjänsten stöds fullt ut i den regionen. Begränsningen i den här regionen är temporär och begränsad till endast nya tjänster. Vi tar bort den här anteckningen när begränsningen inte längre gäller.

## <a name="choose-a-pricing-tier-sku"></a>Välj en pris nivå (SKU)

[Azure kognitiv sökning erbjuds för närvarande på flera pris nivåer](https://azure.microsoft.com/pricing/details/search/): kostnads fri, Basic eller standard. Nivåerna har olika [kapacitet och begränsningar](search-limits-quotas-capacity.md). Mer information finns i [Välj en prisnivå nivå eller SKU](search-sku-tier.md).

Basic och standard är de vanligaste alternativen för produktions arbets belastningar, men de flesta kunder börjar med den kostnads fria tjänsten. Viktiga skillnader mellan nivåer är partitionens storlek och hastighet och begränsningar för antalet objekt som du kan skapa.

Kom ihåg att det inte går att ändra pris nivån när tjänsten har skapats. Om du senare behöver en högre eller lägre nivå måste du skapa tjänsten på nytt.

## <a name="create-your-service"></a>Skapa din tjänst

När du har angett nödvändiga indata kan du gå vidare och skapa tjänsten. 

![Granska och skapa tjänsten](./media/search-create-service-portal/new-service3.png "Granska och skapa tjänsten")

Tjänsten distribueras inom några minuter, som du kan övervaka via Azure-meddelanden. Överväg att fästa tjänsten på instrument panelen för enkel åtkomst i framtiden.

![Övervaka och fäst tjänsten](./media/search-create-service-portal/monitor-notifications.png "Övervaka och fäst tjänsten")

## <a name="get-a-key-and-url-endpoint"></a>Hämta en nyckel och URL-slutpunkt

Om du inte använder portalen kräver program mässig åtkomst till din nya tjänst att du anger URL-slutpunkten och en API-nyckel för autentisering.

1. På sidan **Översikt** letar du upp och kopierar URL-slutpunkten på höger sida av sidan.

2. På sidan **nycklar** kopierar du antingen en av administratörs nycklarna (de är likvärdiga). API-administratörsnycklarna krävs för att skapa, uppdatera och ta bort objekt i tjänsten. Som kontrast ger frågeinställningar Läs åtkomst till index innehåll.

   ![Översikts sida för tjänsten med URL-slutpunkt](./media/search-create-service-portal/get-url-key.png "URL-slutpunkt och annan tjänst information")

Det behövs ingen slutpunkt eller nyckel för portalbaserade uppgifter. Portalen är redan länkad till din Azure Kognitiv sökning-resurs med administratörs behörighet. För en portal genom gång börjar du med [snabb start: skapa ett Azure kognitiv sökning-index i portalen](search-get-started-portal.md).

## <a name="scale-your-service"></a>Skala din tjänst

När tjänsten har etablerats kan du skala den så att den passar dina behov. Om du väljer standard nivån för din Azure Kognitiv sökning-tjänst kan du skala din tjänst i två dimensioner: repliker och partitioner. Om du hade valt Basic-nivån hade du bara kunnat lägga till repliker. Skalning är inte tillgängligt om du etablerar tjänsten utan kostnad.

Med ***Partitioner*** kan tjänsten lagra och söka igenom fler dokument.

Med ***Repliker*** kan tjänsten hantera en högre belastning av sökfrågor.

Om du lägger till resurser blir din månatliga faktura större. [Priskalkylatorn](https://azure.microsoft.com/pricing/calculator/) kan hjälpa dig att förstå hur fakturering påverkas när du lägger till resurser. Kom ihåg att du kan ändra resurser baserat på belastning. Du kan till exempel öka resurserna för att skapa den fullständiga initiala indexeringen och sedan minska resurserna till en nivå som är mer lämplig för inkrementell indexering.

> [!Important]
> En tjänst måste ha [2 repliker för skrivskyddad SLA och 3 repliker för läs-/skriv-SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Gå till söktjänstsidan i Azure-portalen.
2. Välj **Inställningar** > **Skala** i det vänstra navigeringsfönstret.
3. Använd reglaget om du vill lägga till resurser av endera typ.

![Lägg till kapacitet](./media/search-create-service-portal/settings-scale.png "Lägg till kapacitet via repliker och partitioner")

> [!Note]
> Lagring och hastighet per partition ökar med högre nivåer. Mer information finns i [kapacitet och begränsningar](search-limits-quotas-capacity.md).

## <a name="when-to-add-a-second-service"></a>När ska du lägga till en andra tjänst?

De flesta kunder använder bara en tjänst som etablerats på en nivå som har [rätt resursbalans](search-sku-tier.md). En tjänst kan vara värd för flera index i enlighet med [gränsvärdena för den nivå du väljer](search-capacity-planning.md), med indexen isolerade från varandra. I Azure Kognitiv sökning kan begär Anden bara dirigeras till ett index, vilket minimerar risken för oavsiktlig eller avsiktlig data hämtning från andra index i samma tjänst.

Även om de flesta kunder bara använder en tjänst kan det vara nödvändigt med tjänstredundans om de operativa kraven omfattar följande:

* Haveriberedskap (avbrott i datacenter). Azure Kognitiv sökning ger inte omedelbar redundans vid ett avbrott. Mer information och rekommendationer finns i [Tjänstadministration](search-manage.md).
* Din undersökning av modeller med flera klientorganisationer har fastställt att ytterligare tjänster är den optimala designen. Mer information finns i [Design för flera klientorganisationer](search-modeling-multitenant-saas-applications.md).
* För globalt distribuerade program kan du behöva en instans av Azure Kognitiv sökning i flera regioner för att minimera svars tiden för programmets internationella trafik.

> [!NOTE]
> I Azure Kognitiv sökning kan du inte åtskilja indexering och frågor om åtgärder. Därför skulle du aldrig skapa flera tjänster för åtskiljda arbets belastningar. Index tillfrågas alltid om vilken tjänst den skapades i (du kan inte skapa ett index i en tjänst och kopiera den till en annan).

Det behövs ingen andra tjänst för hög tillgänglighet. Hög tillgänglighet för frågor uppnås när du använder minst 2 repliker i samma tjänst. Replik uppdateringar är sekventiella, vilket innebär att minst en fungerar när en tjänst uppdatering distribueras. Mer information om drift tid finns i [service nivå avtal](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Nästa steg

När du har slutfört en tjänst kan du fortsätta i portalen för att skapa ditt första index.

> [!div class="nextstepaction"]
> [Snabb start: skapa ett Azure Kognitiv sökning-index i portalen](search-get-started-portal.md)
