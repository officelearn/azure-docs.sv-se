---
title: Multitenancy och innehållsisolering
titleSuffix: Azure Cognitive Search
description: Lär dig mer om vanliga designmönster för SaaS-program med flera egenskaper när du använder Azure Cognitive Search.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d8e453336005f3389f67e9571fac438bfc340c1b
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549016"
---
# <a name="design-patterns-for-multitenant-saas-applications-and-azure-cognitive-search"></a>Designmönster för SaaS-program med flera trogna och Azure Cognitive Search
Ett program med flera innehavare är ett program som tillhandahåller samma tjänster och funktioner till valfritt antal klienter som inte kan se eller dela data för någon annan klient. I det här dokumentet beskrivs strategier för klientisolering för program med flera innehavare som skapats med Azure Cognitive Search.

## <a name="azure-cognitive-search-concepts"></a>Azure Cognitive Search-begrepp
Som en sök-som-en-tjänst-lösning gör Azure Cognitive Search det möjligt för utvecklare att lägga till omfattande sökupplevelser i program utan att hantera någon infrastruktur eller bli expert på informationshämtning. Data överförs till tjänsten och lagras sedan i molnet. Med hjälp av enkla begäranden till Azure Cognitive Search API kan data sedan ändras och sökas igenom. En översikt över tjänsten finns i den [här artikeln](https://aka.ms/whatisazsearch). Innan du diskuterar designmönster är det viktigt att förstå vissa begrepp i Azure Cognitive Search.

### <a name="search-services-indexes-fields-and-documents"></a>Söktjänster, index, fält och dokument
När du använder Azure Cognitive Search prenumererar man på en *söktjänst*. När data överförs till Azure Cognitive Search lagras de i ett *index* i söktjänsten. Det kan finnas ett antal index inom en enda tjänst. Om du vill använda de välbekanta begreppen databaser kan söktjänsten liknas vid en databas medan indexen i en tjänst kan liknas vid tabeller i en databas.

Varje index i en söktjänst har ett eget schema, som definieras av ett antal anpassningsbara *fält*. Data läggs till i ett Azure Cognitive Search-index i form av enskilda *dokument*. Varje dokument måste överföras till ett visst index och måste passa indexets schema. När du söker efter data med Azure Cognitive Search utfärdas sökfrågor i fulltext mot ett visst index.  Om du vill jämföra dessa begrepp med begreppen i en databas kan fält liknas vid kolumner i en tabell och dokument kan liknas vid rader.

### <a name="scalability"></a>Skalbarhet
Alla Azure Cognitive Search-tjänster på [standardprisnivån](https://azure.microsoft.com/pricing/details/search/) kan skalas i två dimensioner: lagring och tillgänglighet.

* *Partitioner* kan läggas till för att öka lagringen av en söktjänst.
* *Repliker* kan läggas till i en tjänst för att öka dataflödet för begäranden som en söktjänst kan hantera.

Genom att lägga till och ta bort partitioner och repliker gör det möjligt för söktjänstens kapacitet att växa med den mängd data och trafik som programmet kräver. För att en söktjänst ska uppnå ett [läs-SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)krävs det två repliker. För att en tjänst ska uppnå ett [läs-skriv SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)krävs tre repliker.

### <a name="service-and-index-limits-in-azure-cognitive-search"></a>Tjänst- och indexgränser i Azure Cognitive Search
Det finns några olika [prisnivåer](https://azure.microsoft.com/pricing/details/search/) i Azure Cognitive Search, var och en av nivåerna har olika [gränser och kvoter](search-limits-quotas-capacity.md). Vissa av dessa gränser är på servicenivå, vissa är på indexnivå och vissa är på partitionsnivå.

|  | Basic | Standard1 | Standard2 | Standard3 | Standard3 HD |
| --- | --- | --- | --- | --- | --- |
| Maximala repliker per tjänst |3 |12 |12 |12 |12 |
| Maximala partitioner per tjänst |1 |12 |12 |12 |3 |
| Maximala sökenheter (repliker*partitioner) per tjänst |3 |36 |36 |36 |36 (max 3 partitioner) |
| Maximal lagring per tjänst |2 GB |300 GB |1,2 TB |2,4 TB |600 GB |
| Maximal lagring per partition |2 GB |25 GB |100 GB |200 GB |200 GB |
| Maximala index per tjänst |5 |50 |200 |200 |3000 (max 1000 index/partition) |

#### <a name="s3-high-density"></a>S3 hög densitet"
I Azure Cognitive Search s S3-prisnivå finns det ett alternativ för HD-läget (High Density) som utformats speciellt för scenarier med flera innehavare. I många fall är det nödvändigt att stödja ett stort antal mindre hyresgäster under en enda tjänst för att uppnå fördelarna med enkelhet och kostnadseffektivitet.

S3 HD gör det möjligt för många små index som ska packas under ledning av en enda söktjänst genom att handla möjligheten att skala ut index med hjälp av partitioner för möjligheten att vara värd för fler index i en enda tjänst.

En S3-tjänst är utformad för att vara värd för ett fast antal index (högst 200) och låta varje index skalas i storlek horisontellt när nya partitioner läggs till i tjänsten. Om du lägger till partitioner i S3 HD-tjänster ökar det maximala antalet index som tjänsten kan vara värd för. Den idealiska maximala storleken för ett enskilt S3HD-index är cirka 50 - 80 GB, även om det inte finns någon hård storleksgräns för varje index som införts av systemet.

## <a name="considerations-for-multitenant-applications"></a>Överväganden för multitenant applikationer
Multitenant program måste effektivt fördela resurser mellan hyresgästerna samtidigt som en viss nivå av integritet mellan de olika hyresgästerna. Det finns några saker att tänka på när du utformar arkitekturen för ett sådant program:

* *Isolering av klienter:* Programutvecklare måste vidta lämpliga åtgärder för att säkerställa att inga klienter har obehörig eller oönskad åtkomst till data från andra klienter. Utöver datasekretessen kräver strategier för klientisolering effektiv hantering av delade resurser och skydd från bullriga grannar.
* *Kostnad för molnresurs:* Som med alla andra program måste programvarulösningar förbli kostnadsbegripliga som en komponent i ett program med flera beståndsdelar.
* *Enkel drift:* När du utvecklar en arkitektur med flera trogna är påverkan på programmets verksamhet och komplexitet en viktig faktor. Azure Cognitive Search har ett [serviceavtal på 99,9 %](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
* *Globalt fotavtryck:* Multitenant program kan behöva effektivt betjäna hyresgäster som är fördelade över hela världen.
* *Skalbarhet:* Programutvecklare måste överväga hur de stämmer av mellan att upprätthålla en tillräckligt låg nivå av programkomplexitet och utforma programmet för att skala med antalet klienter och storleken på klienternas data och arbetsbelastning.

Azure Cognitive Search erbjuder några gränser som kan användas för att isolera klienters data och arbetsbelastning.

## <a name="modeling-multitenancy-with-azure-cognitive-search"></a>Modellering multitenancy med Azure Cognitive Search
När det gäller ett scenario med flera innehavare förbrukar programutvecklaren en eller flera söktjänster och delar upp sina klienter mellan tjänster, index eller båda. Azure Cognitive Search har några vanliga mönster när du modellerar ett scenario med flera egenskaper:

1. *Index per klient:* Varje klient har ett eget index i en söktjänst som delas med andra klienter.
2. *Service per klient:* Varje klient har sin egen dedikerade Azure Cognitive Search-tjänst som erbjuder högsta nivå av data- och arbetsbelastningsseparation.
3. *Blandning av båda:* Större, mer aktiva klienter tilldelas dedikerade tjänster medan mindre klienter tilldelas enskilda index inom delade tjänster.

## <a name="1-index-per-tenant"></a>1. Index per hyresgäst
![En skildring av index-per-klient-modellen](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png)

I en index-per-klientmodell upptar flera klienter en enda Azure Cognitive Search-tjänst där varje klient har sitt eget index.

Klienter uppnår dataisolering eftersom alla sökbegäranden och dokumentåtgärder utfärdas på indexnivå i Azure Cognitive Search. I programskiktet finns det behov av medvetenhet om att rikta de olika hyresgästernas trafik till rätt index samtidigt som man hanterar resurser på servicenivå över alla klienter.

Ett viktigt attribut för index-per-klient-modellen är möjligheten för programutvecklaren att överteckna kapaciteten för en söktjänst bland programmets klienter. Om klienterna har en ojämn fördelning av arbetsbelastningen kan den optimala kombinationen av klienter fördelas över en söktjänsts index för att rymma ett antal mycket aktiva, resursintensiva klienter samtidigt som de betjänar en lång svans av mindre aktiva klienter. Avvägningen är modellens oförmåga att hantera situationer där varje klient samtidigt är mycket aktiv.

Index-per-klient-modellen utgör grunden för en modell för rörlig kostnad, där en hel Azure Cognitive Search-tjänst köps i förskott och sedan fylls med klienter. Detta gör det möjligt att ange outnyttjad kapacitet för utvärderingsversioner och kostnadsfria konton.

För program med ett globalt fotavtryck kanske index-per-klientmodellen inte är den mest effektiva. Om ett programs klienter distribueras över hela världen kan en separat tjänst vara nödvändig för varje region som kan duplicera kostnaderna för var och en av dem.

Azure Cognitive Search gör det möjligt att skala både de enskilda indexen och det totala antalet index som ska växa. Om en lämplig prisnivå väljs kan partitioner och repliker läggas till i hela söktjänsten när ett enskilt index inom tjänsten blir för stort när det gäller lagring eller trafik.

Om det totala antalet index blir för stort för en enskild tjänst måste en annan tjänst etableras för att rymma de nya klienterna. Om index måste flyttas mellan söktjänster när nya tjänster läggs till måste data från indexet kopieras manuellt från ett index till ett annat eftersom Azure Cognitive Search inte tillåter att ett index flyttas.

## <a name="2-service-per-tenant"></a>2. Service per hyresgäst
![En skildring av modellen service per hyresgäst](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png)

I en service-per-klientarkitektur har varje klient en egen söktjänst.

I den här modellen uppnår programmet den maximala isoleringsnivån för sina klienter. Varje tjänst har dedikerat lagrings- och dataflöde för hantering av sökbegäran samt separata API-nycklar.

För program där varje klient har ett stort fotavtryck eller arbetsbelastningen har liten variation från klient till klient, är modellen service per klient ett effektivt val eftersom resurser inte delas mellan olika klienters arbetsbelastningar.

En tjänst per klientmodell erbjuder också fördelen med en förutsägbar modell med fast kostnad. Det finns ingen up-front investering i en hel söktjänst tills det finns en klient att fylla den, men kostnaden per klient är högre än en index-per-klientmodell.

Service-per-klient-modellen är ett effektivt val för program med ett globalt fotavtryck. Med geografiskt fördelade klienter är det enkelt att ha varje klients tjänst i rätt region.

Utmaningarna i skalning detta mönster uppstår när enskilda hyresgäster växa ur sin tjänst. Azure Cognitive Search stöder för närvarande inte uppgradering av prisnivån för en söktjänst, så alla data måste kopieras manuellt till en ny tjänst.

## <a name="3-mixing-both-models"></a>3. Blanda båda modellerna
Ett annat mönster för modellering multitenancy är att blanda både index-per-klient och service-per-klient strategier.

Genom att blanda de två mönstren kan ett programs största hyresgäster uppta dedikerade tjänster medan den långa svansen av mindre aktiva, mindre klienter kan uppta index i en delad tjänst. Den här modellen säkerställer att de största hyresgästerna har genomgående hög prestanda från tjänsten samtidigt som den hjälper till att skydda de mindre hyresgästerna från bullriga grannar.

Implementering av den här strategin bygger dock framsynthet i att förutsäga vilka klienter som kommer att kräva en dedikerad tjänst jämfört med ett index i en delad tjänst. Programmets komplexitet ökar med behovet av att hantera båda dessa multitenancy modeller.

## <a name="achieving-even-finer-granularity"></a>Uppnå ännu finare granularitet
Ovanstående designmönster för att modellera multitenant scenarier i Azure Cognitive Search förutsätter ett enhetligt omfång där varje klient är en hel instans av ett program. Program kan dock ibland hantera många mindre scope.

Om modeller för service per klient och index per klient inte är tillräckligt små scope, är det möjligt att modellera ett index för att uppnå en ännu finare grad av granularitet.

Om du vill att ett enda index ska fungera på olika sätt för olika klientslutpunkter kan ett fält läggas till i ett index som anger ett visst värde för varje möjlig klient. Varje gång en klient anropar Azure Cognitive Search för att fråga eller ändra ett index anger koden från klientprogrammet det lämpliga värdet för det fältet med hjälp av Azure Cognitive Searchs [filterfunktion](https://msdn.microsoft.com/library/azure/dn798921.aspx) vid frågetillfället.

Den här metoden kan användas för att uppnå funktioner för separata användarkonton, separata behörighetsnivåer och till och med helt separata program.

> [!NOTE]
> Om du använder den metod som beskrivs ovan för att konfigurera ett enda index för att betjäna flera klienter påverkas relevansen av sökresultaten. Sökrelevanspoäng beräknas på ett omfång på indexnivå, inte ett omfång på klientnivå, så alla klienters data ingår i relevanspoängens underliggande statistik, till exempel termfrekvens.
> 
> 

## <a name="next-steps"></a>Nästa steg
Azure Cognitive Search är ett övertygande val för många program. När du utvärderar de olika designmönstren för program med flera användare bör du tänka på de [olika prisnivåerna](https://azure.microsoft.com/pricing/details/search/) och respektive [tjänstgränser](search-limits-quotas-capacity.md) för att bäst skräddarsy Azure Cognitive Search så att de passar programarbetsbelastningar och arkitekturer av alla storlekar.

Alla frågor om Azure Cognitive Search och scenarier azuresearch_contact@microsoft.commed flera innehavare kan dirigeras till .

