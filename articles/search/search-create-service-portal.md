---
title: 'Snabbstart: Skapa en söktjänst i portalen'
titleSuffix: Azure Cognitive Search
description: I den här portalens snabbstart får du lära dig hur du konfigurerar en Azure Cognitive Search-resurs i Azure-portalen. Välj resursgrupper, regioner, samt SKU eller prisnivå.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 02/10/2020
ms.openlocfilehash: 3bc3edcd0e75d8f6e3e4d6f9b200032909318040
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77209366"
---
# <a name="quickstart-create-an-azure-cognitive-search-service-in-the-portal"></a>Snabbstart: Skapa en Azure Cognitive Search-tjänst i portalen

Azure Cognitive Search är en fristående resurs som används för att koppla en sökupplevelse till anpassade appar. Azure Cognitive Search integreras enkelt med andra Azure-tjänster, med appar på nätverksservrar eller med programvara som körs på andra molnplattformar.

I den här artikeln får du lära dig hur du skapar en resurs i [Azure-portalen](https://portal.azure.com/).

[![Animerad GIF](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

Föredrar du PowerShell? Använd [tjänstmallen](https://azure.microsoft.com/resources/templates/101-azure-search-create/) för Azure Resource Manager. Mer information om hur du kommer igång finns i [Hantera Azure Cognitive Search med PowerShell](search-manage-powershell.md).

## <a name="subscribe-free-or-paid"></a>Prenumerera (kostnadsfritt eller betalt)

[Öppna ett kostnadsfritt Azure-konto](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) och använd kostnadsfria krediter för att prova betalda Azure-tjänster. Efter att krediterna är slut behåller du kontot och fortsätter använda kostnadsfria Azure-tjänster, till exempel Websites. Ditt kreditkort debiteras aldrig om du inte specifikt ändrar dina inställningar och ber om debitering.

Du kan också [aktivera MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Med en MSDN-prenumeration får du krediter varje månad som du kan använda Azure-betaltjänster. 

## <a name="find-azure-cognitive-search"></a>Hitta Azure Cognitive Search

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på plustecknet (”+ Skapa resurs”) i det övre vänstra hörnet.
3. Använd sökfältet för att hitta "Azure Cognitive Search" eller navigera till resursen via **Web** > **Azure Cognitive Search**.

![Skapa en resurs i portalen](./media/search-create-service-portal/find-search3.png "Skapa en resurs i portalen")

## <a name="choose-a-subscription"></a>Välj en prenumeration

Om du har fler än en prenumeration väljer du en för söktjänsten.

## <a name="set-a-resource-group"></a>Ange en resursgrupp

En resursgrupp är en behållare som innehåller relaterade resurser för din Azure-lösning. Det krävs för söktjänsten. Det är också användbart för att hantera resurser all-up, inklusive kostnader. En resursgrupp kan bestå av en tjänst eller flera tjänster som används tillsammans. Om du till exempel använder Azure Cognitive Search för att indexera en Azure Cosmos DB-databas kan du göra båda tjänsterna till en del av samma resursgrupp för hanteringsändamål. 

Om du inte kombinerar resurser till en enda grupp, eller om befintliga resursgrupper är fyllda med resurser som används i orelaterade lösningar, skapar du en ny resursgrupp bara för din Azure Cognitive Search-resurs. 

![Skapa en ny resursgrupp](./media/search-create-service-portal/new-resource-group.png "Skapa en ny resursgrupp")

Med tiden kan du spåra aktuella och beräknade kostnader helt och främst eller visa avgifter för enskilda resurser. Följande skärmbild visar vilken typ av kostnadsinformation du kan förvänta dig när du kombinerar flera resurser till en grupp.

![Hantera kostnader på resursgruppsnivå](./media/search-create-service-portal/resource-group-cost-management.png "Hantera kostnader på resursgruppsnivå")

> [!TIP]
> Resursgrupper förenklar rensningen eftersom det tar bort alla tjänster i den om du tar bort en grupp. Om du har ett prototypprojekt som använder flera tjänster kan du placera dem i samma resursgrupp. Då är det lättare att rensa upp när projektet är slutfört.

## <a name="name-the-service"></a>Namnge tjänsten

Ange ett tjänstnamn i **fältet URL** i instansinformation. Namnet är en del av URL-slutpunkten som `https://your-service-name.search.windows.net`API-anrop utfärdas mot: . Till exempel, om du vill att slutpunkten ska vara `https://myservice.search.windows.net`, anger du `myservice`.

Kraven för tjänstnamn:

* Det måste vara unikt inom namnområdet search.windows.net
* Det måste vara mellan 2 och 60 tecken i längd
* Du måste använda gemener, siffror eller streck ("-")
* Använd inte streck ("-") i de första två tecknen eller som det sista enskilda tecknet
* Du får inte använda på varandra följande streck ("--") någonstans

> [!TIP]
> Om du tror att du använder flera tjänster rekommenderar vi att regionen (eller platsen) i tjänstnamnet inkluderas som en namngivningskonvention. Tjänster inom samma region kan utbyta data utan kostnad, så om Azure Cognitive Search finns i västra `mysearchservice-westus` USA och du har andra tjänster även i västra USA kan ett namn som kan spara en resa till egenskapssidan när du bestämmer hur du ska kombinera eller koppla resurser.

## <a name="choose-a-location"></a>Välj en plats

Som en Azure-tjänst kan Azure Cognitive Search finnas i datacenter runt om i världen. Listan över regioner som stöds finns på [prissidan](https://azure.microsoft.com/pricing/details/search/). 

Du kan minimera eller undvika bandbreddsavgifter genom att välja samma plats för flera tjänster. Om du till exempel indexerar data som tillhandahålls av en annan Azure-tjänst (Azure storage, Azure Cosmos DB, Azure SQL Database) undviker du bandbreddsavgifter när du skapar din Azure Cognitive Search-tjänst i samma region (det finns inga avgifter för utgående data när tjänster finns i samma region).

Om du använder AI-anrikning skapar du söktjänsten i samma region som Cognitive Services. *Samlokalisering av Azure Cognitive Search och Cognitive Services i samma region är ett krav för AI-anrikning*.

> [!Note]
> Centrala Indien är för närvarande inte tillgängligt för nya tjänster. För tjänster som redan finns i centrala Indien kan du skala upp utan begränsningar och din tjänst stöds fullt ut i den regionen. Begränsningen av denna region är tillfällig och begränsad till nya tjänster. Vi tar bort den här anteckningen när begränsningen inte längre gäller.

## <a name="choose-a-pricing-tier-sku"></a>Välj en prisnivå (SKU)

[Azure Cognitive Search erbjuds för närvarande i flera prisnivåer:](https://azure.microsoft.com/pricing/details/search/)Gratis, Basic eller Standard. Nivåerna har olika [kapacitet och begränsningar](search-limits-quotas-capacity.md). Mer information finns i [Välj en prisnivå nivå eller SKU](search-sku-tier.md).

Basic och Standard är de vanligaste alternativen för produktionsarbetsbelastningar, men de flesta kunder börjar med den kostnadsfria tjänsten. Viktiga skillnader mellan nivåer är partitionsstorlek och hastighet och gränser för antalet objekt som du kan skapa.

Kom ihåg att en prisnivå inte kan ändras när tjänsten har skapats. Om du behöver en högre eller lägre nivå måste du återskapa tjänsten.

## <a name="create-your-service"></a>Skapa din tjänst

När du har angett de nödvändiga indata, gå vidare och skapa tjänsten. 

![Granska och skapa tjänsten](./media/search-create-service-portal/new-service3.png "Granska och skapa tjänsten")

Din tjänst distribueras inom några minuter. Du kan övervaka förloppet via Azure-meddelanden. Överväg att fästa tjänsten på instrumentpanelen för enkel åtkomst i framtiden.

![Övervaka och fästa tjänsten](./media/search-create-service-portal/monitor-notifications.png "Övervaka och fästa tjänsten")

## <a name="get-a-key-and-url-endpoint"></a>Hämta en nyckel och URL-slutpunkt

Om du inte använder portalen kräver programmatisk åtkomst till den nya tjänsten att du anger URL-slutpunkten och en api-nyckel för autentisering.

1. Leta reda på och kopiera URL-slutpunkten till höger på sidan på sidan **Översikt.**

2. Kopiera någon av administratörsnycklarna på sidan **Nycklar** (de är likvärdiga). API-administratörsnycklarna krävs för att skapa, uppdatera och ta bort objekt i tjänsten. Frågenycklar ger däremot läsbehörighet till indexinnehåll.

   ![Sidan Serviceöversikt med URL-slutpunkt](./media/search-create-service-portal/get-url-key.png "URL-slutpunkt och annan tjänstinformation")

Det behövs ingen slutpunkt eller nyckel för portalbaserade uppgifter. Portalen är redan länkad till din Azure Cognitive Search-resurs med administratörsrättigheter. För en portalgenomgång börjar du med [Snabbstart: Skapa ett Azure Cognitive Search-index i portalen](search-get-started-portal.md).

## <a name="scale-your-service"></a>Skala din tjänst

När tjänsten har etablerats kan du skala den så att den passar dina behov. Om du väljer standardnivån för din Azure Cognitive Search-tjänst kan du skala tjänsten i två dimensioner: repliker och partitioner. Om du hade valt Basic-nivån hade du bara kunnat lägga till repliker. Skalning är inte tillgängligt om du etablerar tjänsten utan kostnad.

Med ***Partitioner*** kan tjänsten lagra och söka igenom fler dokument.

Med ***Repliker*** kan tjänsten hantera en högre belastning av sökfrågor.

Om du lägger till resurser blir din månatliga faktura större. [Priskalkylatorn](https://azure.microsoft.com/pricing/calculator/) kan hjälpa dig att förstå hur fakturering påverkas när du lägger till resurser. Kom ihåg att du kan ändra resurser baserat på belastning. Du kan till exempel öka resurserna för att skapa den fullständiga initiala indexeringen och sedan minska resurserna till en nivå som är mer lämplig för inkrementell indexering.

> [!Important]
> En tjänst måste ha [2 repliker för skrivskyddad SLA och 3 repliker för läs-/skriv-SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

1. Gå till söktjänstsidan i Azure-portalen.
2. Välj **Inställningar** > **skala**i fönstret till vänster .
3. Använd reglaget om du vill lägga till resurser av endera typ.

![Lägg till kapacitet](./media/search-create-service-portal/settings-scale.png "Lägga till kapacitet via repliker och partitioner")

> [!Note]
> Lagring och hastighet per partition ökar på högre nivåer. Mer information finns i [kapacitet och begränsningar](search-limits-quotas-capacity.md).

## <a name="when-to-add-a-second-service"></a>När ska du lägga till en andra tjänst?

De flesta kunder använder bara en tjänst som etablerats på en nivå som har [rätt resursbalans](search-sku-tier.md). En tjänst kan vara värd för flera index i enlighet med [gränsvärdena för den nivå du väljer](search-capacity-planning.md), med indexen isolerade från varandra. I Azure Cognitive Search kan begäranden endast dirigeras till ett index, vilket minimerar risken för oavsiktlig eller avsiktlig datahämtning från andra index i samma tjänst.

Även om de flesta kunder bara använder en tjänst kan det vara nödvändigt med tjänstredundans om de operativa kraven omfattar följande:

* Haveriberedskap (avbrott i datacenter). Azure Cognitive Search ger inte omedelbar redundans i händelse av ett avbrott. Mer information och rekommendationer finns i [Tjänstadministration](search-manage.md).
* Din undersökning av modeller med flera klientorganisationer har fastställt att ytterligare tjänster är den optimala designen. Mer information finns i [Design för flera klientorganisationer](search-modeling-multitenant-saas-applications.md).
* För globalt distribuerade program kan du kräva en instans av Azure Cognitive Search i flera regioner för att minimera svarstiden för programmets internationella trafik.

> [!NOTE]
> I Azure Cognitive Search kan du inte segregera indexerings- och frågeåtgärder. Därför skulle du aldrig skapa flera tjänster för segregerade arbetsbelastningar. Index tillfrågas alltid om vilken tjänst den skapades i (du kan inte skapa ett index i en tjänst och kopiera den till en annan).

Det behövs ingen andra tjänst för hög tillgänglighet. Hög tillgänglighet för frågor uppnås när du använder minst 2 repliker i samma tjänst. Replikuppdateringar är sekventiella, vilket innebär att minst en är i drift när en tjänstuppdatering distribueras. Mer information om drifttid finns i [Servicenivåavtal](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Nästa steg

När du har etablerat en tjänst kan du fortsätta i portalen för att skapa ditt första index.

> [!div class="nextstepaction"]
> [Snabbstart: Skapa ett Azure Cognitive Search-index i portalen](search-get-started-portal.md)
