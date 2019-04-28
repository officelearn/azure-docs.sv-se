---
title: Modellering multitenancy för isolering av innehåll i en tjänst – Azure Search
description: Läs mer om vanliga designmönster för SaaS-program med flera klientorganisationer när du använder Azure Search.
manager: jlembicz
author: LiamCavanagh
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: liamca
ms.custom: seodec2018
ms.openlocfilehash: 58d7ca65a14f9f774b19796c9beae2a7c84102ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61288729"
---
# <a name="design-patterns-for-multitenant-saas-applications-and-azure-search"></a>Designmönster för SaaS-program för flera innehavare och Azure Search
Ett program för flera är en som innehåller samma tjänster och funktioner till valfritt antal klienter som inte kan se eller dela data med andra innehavare. Det här dokumentet beskriver innehavare isolering strategier för program för flera innehavare som skapats med Azure Search.

## <a name="azure-search-concepts"></a>Azure Search-begrepp
Azure Search kan utvecklare kan lägga till avancerade sökupplevelser i program utan att hantera all infrastruktur eller bli expert på informationshämtning som en sökning som tjänst-lösning. Data har överförts till tjänsten och lagras sedan i molnet. Med hjälp av enkla begäranden till Azure Search-API, kan data sedan ändras och söks igenom. En översikt över tjänsten finns i [i den här artikeln](https://aka.ms/whatisazsearch). Innan vi diskuterar designmönster, är det viktigt att förstå vissa begrepp i Azure Search.

### <a name="search-services-indexes-fields-and-documents"></a>Söktjänster, index, fält och dokument
När du använder Azure Search, en prenumererar på en *söktjänsten*. När data har överförts till Azure Search, den är lagrad i en *index* i söktjänsten. Det kan finnas flera index i en enskild tjänst. Om du vill använda välbekanta begreppet databaser kan söktjänsten liknas vid en databas medan index i en tjänst kan liknas vid tabeller i en databas.

Varje index i en söktjänst har ett eget schema, som definieras av ett antal olika anpassningsbara *fält*. Data läggs till ett Azure Search-index i form av enskilda *dokument*. Varje dokument måste laddas upp till en viss index och måste rymmas den indexschemat. När du söker data med hjälp av Azure Search, utfärdas fulltextsökning-frågor mot en viss index.  Om du vill jämföra de här koncepten dem från en databas, fält kan liknas vid kolumner i en tabell och dokument kan liknas vid rader.

### <a name="scalability"></a>Skalbarhet
Alla Azure Search-tjänst i vanlig [prisnivån](https://azure.microsoft.com/pricing/details/search/) skala i två dimensioner: lagring och tillgänglighet.

* *Partitioner* kan läggas till öka lagring av en söktjänst.
* *Repliker* kan läggas till en tjänst för att öka dataflödet för begäranden som kan hantera en söktjänst.

Skapa och ta bort partitioner och -repliker på kan kapaciteten för söktjänsten växa med mängden data och trafik kraven för programmet. För en söktjänst att uppnå en läsning [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), det krävs två repliker. För att en tjänst för att uppnå en skrivskyddad [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), den kräver tre repliker.

### <a name="service-and-index-limits-in-azure-search"></a>Begränsningar för tjänsten och index i Azure Search
Det finns några olika [prisnivåer](https://azure.microsoft.com/pricing/details/search/) i Azure Search, var och en av nivåerna har olika [begränsningar och kvoter](search-limits-quotas-capacity.md). Vissa av dessa gränser är på servicenivån, vissa är på nivån index och vissa är på nivån partition.

|  | Basic | Standard1 | Standard2 | Standard3 | Standard3 HD |
| --- | --- | --- | --- | --- | --- |
| Maximal repliker per tjänst |3 |12 |12 |12 |12 |
| Maximal partitioner per tjänst |1 |12 |12 |12 |3 |
| Maximal Search-enheter (repliker * partitioner) per tjänst |3 |36 |36 |36 |36 (max 3 partitioner) |
| Maximalt lagringsutrymme per tjänst |2 GB |300 GB |1,2 TB |2,4 TB |600 GB |
| Maximalt lagringsutrymme per Partition |2 GB |25 GB |100 GB |200 GB |200 GB |
| Maximalt antal index per tjänst |5 |50 |200 |200 |3000 (högst 1000 index/partition) |

#### <a name="s3-high-density"></a>S3 High Density'
I Azure Search S3-prisinivå finns ett alternativ för hög Högdensitetsläge (HD)-läge som utformats speciellt för scenarion med flera klientorganisationer. I många fall är det nödvändigt att stöder ett stort antal mindre klienter under en enda tjänst att uppnå fördelarna med enkelhet och kostnadsbesparingar.

S3 HD möjliggör många små index ska packas under hanteringen av en enda söktjänst av handel med möjligheten att skala ut index med partitioner för möjligheten att vara värd för flera index i en enskild tjänst.

Concretely, kan en S3-tjänsten ha mellan 1 och 200 index som tillsammans kan vara värd för upp till 1,4 miljarder dokument. En S3 HD å andra sidan skulle tillåta individuella index bara gå upp till 1 miljon dokument, men den kan hantera upp till 1000 index per partition (upp till 3 000 per tjänst) med ett antal totala dokument över 200 miljoner per partition (upp till 600 miljoner per tjänst).

## <a name="considerations-for-multitenant-applications"></a>Överväganden för program med flera klienter
Program med flera klienter måste effektivt distribuera resurser mellan klienterna och behålla viss nivå av sekretess mellan olika klienter. Det finns några överväganden vid utformning av arkitekturen för ett sådant program:

* *Klientisolering:* Programmet utvecklare behöver vidta lämpliga åtgärder för att säkerställa att inga klienter har Ej behörig eller oönskad åtkomst till data med andra klienter. Utöver perspektiv på datasekretess kräver strategier för isolering av klienten effektiv hantering av delade resurser och skydd mot störningar grannar.
* *Resurskostnader för molnet:* Precis som med alla andra program måste programvarulösningar vara konkurrenskraftiga som en del av ett program för flera kostnaden.
* *Enkel åtgärder:* När du utvecklar en arkitektur med flera innehavare är viktigt, påverkan på programmets åtgärder och komplexitet. Azure Search har ett [serviceavtal på 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
* *Globala fotavtrycket:* Program med flera klienter kan behöva effektivt hantera klienter som distribueras över hela världen.
* *Skalbarhet:* Programutvecklare måste du överväga hur de stämma av mellan upprätthålla en tillräckligt låg nivå av komplexitet för programmet och utformning av programmet som ska skalas tillsammans med antalet klienter och storleken på klienternas data och arbetsbelastningen.

Azure Search erbjuder några gränser som kan användas för att isolera klienternas data och arbetsbelastningen.

## <a name="modeling-multitenancy-with-azure-search"></a>Modellering flera innehavare med Azure Search
När det gäller ett scenario med flera innehavare programutvecklaren använder en eller flera söktjänster och dela sina klienter mellan tjänster, index eller båda. Azure Search har några vanliga mönster när modellering ett scenario med flera innehavare:

1. *Index per klient:* Varje klient har ett eget index i en söktjänst som delas med andra klienter.
2. *Tjänsten per klient:* Varje klient har sin egen dedikerade Azure Search-tjänst som erbjuder den högsta nivån på data och arbetsbelastningen uppdelning.
3. *Blandning av båda:* Större, mer aktiva klienter tilldelas dedikerade tjänster medan mindre klienter tilldelas individuella index i delade tjänster.

## <a name="1-index-per-tenant"></a>1. Index per klient
![En bild av modellen index-per-klient](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png)

I ett index per klient-modellen upptar flera klienter en enda Azure Search-tjänst där varje klientorganisation har sina egna index.

Klienter uppnå dataisolering eftersom alla Sök begäranden och Dokumentåtgärder utfärdas på indexnivå i Azure Search. I programlagret har medvetenheten behöver dirigera olika klienternas trafik till rätt index och hanterar resurser på tjänstnivå för alla klienter.

Nyckelattribut av modellen index-per-klient är möjligheten för programutvecklare att överprenumerera på kapaciteten för en söktjänst bland programmets klienter. Om klienterna har en ojämn fördelning av arbetsbelastning, kan optimal kombination av klienter distribueras på en söktjänst index för ett antal hög, resurskrävande klienter samtidigt som man betjänar en lång slutet av mindre samtidigt aktiva klienter. En kompromiss är modellen oförmåga att hantera situationer där varje innehavare samtidigt är hög.

Index-per-klient-modellen utgör grunden för en variabel kostnadsmodell där en hel Azure Search-tjänst är köpte startkostnader och sedan fylls sedan med klienter. Det möjliggör outnyttjad kapacitet kan utses för försök och kostnadsfria konton.

Index-per-klient-modellen kanske inte den effektivaste för program med ett globala fotavtryck. Om ett programs klienter är fördelade över hela världen, kan det vara nödvändigt för varje region där duplicera kostnader för var och en av dem en separat tjänst.

Azure Search kan skalan för både individuella index och det totala antalet index att växa. Om en lämplig priser väljs nivå, partitioner och -repliker kan läggas till hela söktjänsten när ett enskilt index i tjänsten blir för stor vad gäller lagring eller trafik.

Om det totala antalet index blir för stor för en enda tjänst, har en annan tjänst som ska etableras för att hantera nya klienter. Om index har flyttas mellan söktjänster som läggs nya tjänster kan måste data från indexet kopieras manuellt från ett index till en annan eftersom Azure Search inte tillåter för ett index som ska flyttas.

## <a name="2-service-per-tenant"></a>2. Tjänsten per klient
![En bild av modellen service-per-klient](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png)

Varje klientorganisation har en egen search-tjänst i en arkitektur för service-per-klient.

I den här modellen uppnår programmet den maximala nivån av isolering för sina klienter. Varje tjänst har dedikerade lagring och dataflöde för att hantera sökbegäran samt separat API-nycklar.

För program där varje klientorganisation har ett stort fotavtryck eller en arbetsbelastning har lite variationer från en klient till en klient, är service-per-klient-modell passar som resurserna inte delas mellan olika klienternas arbetsbelastningar.

En tjänst per klient modell erbjuder även fördelen med en förutsägbar, fast kostnadsmodell. Det finns inga direkta investeringar i en hel search-tjänst tills det är en klient att fylla det, men kostnaden per klient är högre än en modell för index-per-klient.

Modellen service-per-klient är en effektiv val för program med ett globala fotavtryck. Med geografiskt utspridda innehavare är det lätt att service för varje klient i aktuell region.

Utmaningar vid skalning av det här mönstret kan uppstå vid enskilda klienter växa ifrån sina tjänster. Azure Search stöder för närvarande inte uppgradera prisnivån för search-tjänsten så att alla data skulle behöva manuellt kopieras till en ny tjänst.

## <a name="3-mixing-both-models"></a>3. Blanda båda modellerna
En annan mönster för modellering multitenancy blanda strategier för både index per klient och service-per-klient.

Största klienter för ett program kan blandas två mönster, uppta dedikerade tjänster medan lång slutet av mindre aktiva, mindre klienter får ha index i en delad tjänst. Den här modellen garanterar att de största innehavarna har konsekvent hög prestanda från tjänsten samtidigt som man skyddar mindre klienter från alla bort störande grannar.

Implementera den här strategin använder dock förutseende i förutse vilka klienter kräver en dedikerad tjänst jämfört med ett index i en delad tjänst. Programmet komplexitet ökar behovet av att hantera både modellers multitenancy.

## <a name="achieving-even-finer-granularity"></a>Uppnå ännu finare granularitet
Ovanstående designmönster modellera scenarion med flera klientorganisationer i Azure Search förutsätter en uniform scopet där varje innehavare en hel instans av ett program. Program kan ibland hantera många mindre scope.

Om service-per-klient och index per klient modeller inte är tillräckligt små för scope, är det möjligt att utforma ett index för att uppnå en ännu finare graden av kornighet.

Om du vill ha ett enda index beter sig annorlunda för olika klientslutpunkter, kan ett fält läggas till ett index som anger ett visst värde för varje klient som möjligt. Varje gång en klient anropar Azure Search för att fråga efter eller ändra ett index koden från klientprogrammet anger lämpligt värde för fältet med hjälp av Azure Search [filter](https://msdn.microsoft.com/library/azure/dn798921.aspx) funktion när en fråga körs.

Den här metoden kan användas för att möjliggöra funktioner av separata användarkonton, separat behörighetsnivåer och även helt olika program.

> [!NOTE]
> Med den metod som beskrivs ovan påverkar om du vill konfigurera ett enda index för att betjäna flera innehavare relevans för sökresultat. Sök efter relevans poängen beräknas i ett index på servernivå definitionsområde, inte ett på klientnivå omfång, så att alla klienter data ingår i relevans poängen underliggande statistik, till exempel termen frekvens.
> 
> 

## <a name="next-steps"></a>Nästa steg
Azure Search är ett intressant alternativ för många program, [Läs mer om tjänstens robusta funktioner](https://aka.ms/whatisazsearch). När du utvärderar olika designmönster för program med flera klienter, Överväg att den [olika prisnivåer](https://azure.microsoft.com/pricing/details/search/) och motsvarande [tjänstbegränsningar](search-limits-quotas-capacity.md) bäst anpassa Azure Search för att passa program arbetsbelastningar och arkitekturer i alla storlekar.

Frågor om Azure Search och scenarion med flera klientorganisationer kan dirigeras till azuresearch_contact@microsoft.com.

