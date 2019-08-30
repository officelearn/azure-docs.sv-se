---
title: Modellering av flera innehavare för innehålls isolering i en tjänst-Azure Search
description: Lär dig om vanliga design mönster för SaaS-program med flera innehavare när du använder Azure Search.
manager: nitinme
author: LiamCavanagh
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: liamca
ms.openlocfilehash: 8b7538da41241f005298537c2969e5fce72b3c38
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182231"
---
# <a name="design-patterns-for-multitenant-saas-applications-and-azure-search"></a>Design mönster för SaaS-program för flera innehavare och Azure Search
Ett program för flera innehavare är ett program som tillhandahåller samma tjänster och funktioner för alla klienter som inte kan se eller dela data för någon annan klient organisation. I det här dokumentet beskrivs strategier för klient isolering för program med flera klienter som skapats med Azure Search.

## <a name="azure-search-concepts"></a>Azure Search begrepp
Som en Sök-som-tjänst-lösning ger Azure Search utvecklare möjlighet att lägga till omfattande Sök upplevelser i program utan att behöva hantera en infrastruktur eller bli expert på informations hämtning. Data överförs till tjänsten och lagras sedan i molnet. Med hjälp av enkla begär anden till Azure Search API, kan data sedan ändras och genomsökas. Du hittar en översikt över tjänsten i [den här artikeln](https://aka.ms/whatisazsearch). Innan du diskuterar design mönster är det viktigt att förstå vissa begrepp i Azure Search.

### <a name="search-services-indexes-fields-and-documents"></a>Sök tjänster, index, fält och dokument
När du använder Azure Search en prenumeration på en *Sök tjänst*. När data överförs till Azure Search lagras de i ett *index* i Sök tjänsten. Det kan finnas ett antal index i en enskild tjänst. Om du vill använda de välkända koncepten för databaser kan Sök tjänsten likened till en databas medan indexen i en tjänst kan vara likened till tabeller i en databas.

Varje index i en Sök tjänst har sitt eget schema, vilket definieras av ett antal anpassningsbara *fält*. Data läggs till i ett Azure Search-index i form av enskilda *dokument*. Varje dokument måste överföras till ett visst index och måste passa det indexets schema. När du söker efter data med Azure Search utfärdas full text Sök frågorna mot ett visst index.  Om du vill jämföra dessa begrepp med databaserna i en databas kan du likened kolumner i en tabell och dokument kan likened till rader.

### <a name="scalability"></a>Skalbarhet
Alla Azure Search-tjänster på standard [pris nivån](https://azure.microsoft.com/pricing/details/search/) kan skalas i två dimensioner: lagring och tillgänglighet.

* Du kan lägga till *partitioner* för att öka lagringen för en Sök tjänst.
* *Repliker* kan läggas till i en tjänst för att öka data flödet för begär Anden som en Sök tjänst kan hantera.

Genom att lägga till och ta bort partitioner och repliker på kan du öka kapaciteten för Sök tjänsten med den mängd data och trafik som programmet kräver. För att en Sök tjänst ska kunna uppnå ett [service avtal](https://azure.microsoft.com/support/legal/sla/search/v1_0/)för läsning kräver det två repliker. För att en tjänst ska kunna uppnå ett service [avtal](https://azure.microsoft.com/support/legal/sla/search/v1_0/)med Läs-och skriv åtgärder krävs tre repliker.

### <a name="service-and-index-limits-in-azure-search"></a>Tjänst-och index gränser i Azure Search
Det finns några olika [pris nivåer](https://azure.microsoft.com/pricing/details/search/) i Azure Search, var och en av nivåerna har olika [gränser och kvoter](search-limits-quotas-capacity.md). Några av dessa begränsningar finns på tjänst nivå, vissa finns på index-nivå och några finns på partition-nivå.

|  | Basic | Standard1 | Standard2 | Standard3 | Standard3 HD |
| --- | --- | --- | --- | --- | --- |
| Maximalt antal repliker per tjänst |3 |12 |12 |12 |12 |
| Maximalt antal partitioner per tjänst |1 |12 |12 |12 |3 |
| Maximalt antal Sök enheter (repliker * partitioner) per tjänst |3 |36 |36 |36 |36 (max 3 partitioner) |
| Maximalt lagrings utrymme per tjänst |2 GB |300 GB |1,2 TB |2,4 TB |600 GB |
| Maximalt lagrings utrymme per partition |2 GB |25 GB |100 GB |200 GB |200 GB |
| Maximalt antal index per tjänst |5 |50 |200 |200 |3000 (max 1000-index/partition) |

#### <a name="s3-high-density"></a>S3 hög densitet
I Azure Search är S3-pris nivån, finns ett alternativ för läget hög densitet (HD) som utformats specifikt för scenarier med flera innehavare. I många fall är det nödvändigt att stödja ett stort antal mindre klienter under en enda tjänst för att uppnå fördelarna med enkelhet och kostnads effektivitet.

S3 HD gör att många små index kan packas upp under hanteringen av en enskild Sök tjänst genom att handel kan skala ut index med partitioner för att kunna hantera fler index i en enskild tjänst.

Konkret kan en S3-tjänst ha mellan 1 och 200 index som tillsammans kan vara värd för upp till 1 400 000 000 dokument. En S3 HD på den andra sidan tillåter att enskilda index endast går upp till 1 000 000 dokument, men det kan hantera upp till 1000 index per partition (upp till 3000 per tjänst) med ett totalt antal dokument på 200 000 000 per partition (upp till 600 000 000 per tjänst).

## <a name="considerations-for-multitenant-applications"></a>Att tänka på för program med flera klienter
Program för flera innehavare måste effektivt distribuera resurser mellan klienterna samtidigt som du behåller en viss sekretess nivå mellan olika klienter. Det finns några saker att tänka på när du utformar arkitekturen för ett sådant program:

* *Klient isolering:* Programutvecklare måste vidta lämpliga åtgärder för att säkerställa att inga klienter har obehörig eller oönskad åtkomst till data för andra klienter. Utöver data sekretessens perspektiv kräver klient isolerings strategier effektiv hantering av delade resurser och skydd mot störningar i grannar.
* *Resurs kostnad för moln:* Precis som med alla andra program måste program varu lösningar vara kostnads konkurrens kraftiga som en komponent i ett program för flera innehavare.
* *Enkel drift:* När du utvecklar en arkitektur för flera innehavare är påverkan av programmets drift och komplexitet viktigt att tänka på. Azure Search har ett [service avtal på 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
* *Global storlek:* Program för flera klient organisationer kan behöva betjäna klienter som distribueras över hela världen.
* *Skalbarhet:* Programutvecklare måste fundera över hur de stämmer överens med en tillräckligt låg nivå av program komplexitet och att utforma programmet för skalning med antalet klienter och storleken på klient organisationens data och arbets belastning.

Azure Search erbjuder några gränser som kan användas för att isolera innehavares data och arbets belastning.

## <a name="modeling-multitenancy-with-azure-search"></a>Modellering av flera innehav med Azure Search
I ett scenario med flera innehavare förbrukar programutvecklaren en eller flera Sök tjänster och delar upp sina klienter bland tjänster, index eller både och. Azure Search har några vanliga mönster för att modellera ett scenario med flera innehavare:

1. *Index per klient:* Varje klient har sitt eget index i en Sök tjänst som delas med andra klienter.
2. *Tjänst per klient:* Varje klient har sin egen dedikerade Azure Search tjänst, vilket ger den högsta nivån av data och separering av arbets belastning.
3. *Blandning av båda:* Större, mer-aktiva klienter tilldelas dedikerade tjänster, medan mindre klienter tilldelas enskilda index inom delade tjänster.

## <a name="1-index-per-tenant"></a>1. Index per klient
![En portrayal av modellen index-per-klient](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png)

I en modell för index per klient använder flera klienter en enda Azure Search tjänst där varje klient har sitt eget index.

Klienter uppnår data isolering eftersom alla Sök begär Anden och dokument åtgärder utfärdas på en index nivå i Azure Search. I program lagret behöver du veta mer om hur du dirigerar de olika klienternas trafik till rätt index samtidigt som du även hanterar resurser på tjänst nivå över alla klienter.

Ett nyckelattribut för modellen index-per-klient är möjligheten för programutvecklaren att överlåta kapaciteten hos en Sök tjänst bland programmets klienter. Om klienterna har en ojämn fördelning av arbets belastningen, kan den optimala kombinationen av klienter distribueras över en Sök tjänsts index för att hantera ett antal mycket aktiva, resurs krävande klienter samtidigt som de samtidigt betjänar en lång ände på mindre aktiva klienter. Kompromissen är oförmåga för modellen att hantera situationer där varje klient är samtidigt hög aktiv.

Modellen index-per-klient utgör grunden för en variabel kostnads modell, där en hel Azure Search-tjänst köps upp och sedan sedan fylls med klient organisationer. Detta gör det möjligt för oanvänd kapacitet att utses för försök och kostnads fria konton.

För program med global storlek kanske modellen index per klient inte är den mest effektiva. Om ett programs klienter distribueras över hela världen kan en separat tjänst vara nödvändig för varje region som kan duplicera kostnader för var och en av dem.

Azure Search tillåter skalning av både enskilda index och det totala antalet index som ska växa. Om en lämplig pris nivå väljs, kan partitioner och repliker läggas till i hela Sök tjänsten när ett enskilt index i tjänsten växer för stort när det gäller lagring eller trafik.

Om det totala antalet index blir för stort för en enskild tjänst, måste en annan tjänst tillhandahållas för att hantera de nya klient organisationerna. Om index måste flyttas mellan Sök tjänsterna när nya tjänster läggs till, måste data från indexet kopieras manuellt från ett index till det andra eftersom Azure Search inte tillåter att ett index flyttas.

## <a name="2-service-per-tenant"></a>2. Tjänst per klient
![En portrayal av modellen för tjänst per klient](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png)

I en arkitektur för tjänst per klient organisation har varje klient organisation sin egen Sök tjänst.

I den här modellen uppnår programmet den maximala isolerings nivån för dess klienter. Varje tjänst har dedikerad lagring och data flöde för hantering av Sök begär Anden samt separata API-nycklar.

För program där varje klient har ett stort utrymme eller om arbets belastningen har liten variation från klient organisation till klient organisation, är tjänst per klient modell ett effektivt val som resurser inte delas mellan olika klienters arbets belastningar.

En tjänst per klient modell erbjuder också fördelen med en förutsägbar, fast kostnads modell. Det finns ingen direkt investering i en hel Sök tjänst tills det finns en klient som kan fylla den, men kostnaden per klient är högre än en modell med index per klient.

Modellen för tjänst per klient är ett effektivt alternativ för program med global storlek. Med geografiskt distribuerade klienter är det enkelt att ha varje klients tjänst i lämplig region.

Utmaningarna med att skala det här mönstret uppstår när enskilda klienter utökar sin tjänst. Azure Search stöder för närvarande inte uppgradering av pris nivån för en Sök tjänst, så alla data måste kopieras manuellt till en ny tjänst.

## <a name="3-mixing-both-models"></a>3. Blanda båda modellerna
Ett annat mönster för modellering av flera innehavare är att blanda både index-per-klient-och tjänst per klient-strategier.

Genom att blanda de två mönstren kan ett programs största innehavare ha dedikerade tjänster medan den långa änden av mindre aktiva och mindre klienter kan uppta index i en delad tjänst. Den här modellen säkerställer att de största klient organisationerna har konsekvent höga prestanda från tjänsten samtidigt som den hjälper till att skydda de mindre klienterna från alla störningar i olika störningar.

Att implementera den här strategin är dock beroende av utseende för att förutsäga vilka klienter som kräver en dedikerad tjänst jämfört med ett index i en delad tjänst. Program komplexiteten ökar med behovet av att hantera båda de här flera innehavande modellerna.

## <a name="achieving-even-finer-granularity"></a>Uppnå ännu bättre precision
Ovanstående design mönster för att modellera scenarier för flera innehavare i Azure Search förutsätter en enhetlig omfattning där varje klient är en hel instans av ett program. Program kan dock ibland hantera många mindre omfång.

Om modeller för tjänst per klient och index per klient inte är tillräckligt små omfattningar, är det möjligt att modellera ett index för att uppnå en ännu bättre grad av granularitet.

Om ett enskilt index beter sig annorlunda för olika klient slut punkter kan ett fält läggas till i ett index som anger ett visst värde för varje möjlig klient. Varje gången en klient anropar Azure Search för att fråga eller ändra ett index, anger koden från klient programmet lämpligt värde för fältet med hjälp av Azure Searchs [filter](https://msdn.microsoft.com/library/azure/dn798921.aspx) funktion vid tidpunkten för frågan.

Den här metoden kan användas för att uppnå funktioner i separata användar konton, olika behörighets nivåer och till och med helt separata program.

> [!NOTE]
> Genom att använda den metod som beskrivs ovan för att konfigurera ett enda index för att betjäna flera klienter, påverkar Sök resultatnas relevans. Sök Resultat beräknas på ett omfång på index nivå, inte på en omfattning på klient nivå, så alla innehavares data införlivas i resultat underliggande statistik, till exempel term frekvens.
> 
> 

## <a name="next-steps"></a>Nästa steg
Azure Search är ett övertygande val för många program, [Läs mer om tjänstens robusta funktioner](https://aka.ms/whatisazsearch). När du utvärderar de olika design mönstren för program med flera klienter bör du överväga de [olika pris nivåerna](https://azure.microsoft.com/pricing/details/search/) och respektive [tjänst begränsningar](search-limits-quotas-capacity.md) för bästa anpassade Azure Search för att passa program arbets belastningar och arkitekturer i alla storlekar.

Eventuella frågor om Azure Search och flera klient scenarier kan dirigeras till azuresearch_contact@microsoft.com.

