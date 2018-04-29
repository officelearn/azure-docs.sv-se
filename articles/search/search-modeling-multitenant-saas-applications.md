---
title: Modeling Multitenancy i Azure Search | Microsoft Docs
description: Mer information om vanliga designmönster för multitenant SaaS-program när du använder Azure Search.
manager: jlembicz
author: ashmaka
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 11/09/2017
ms.author: ashmaka
ms.openlocfilehash: 765f9c4600f762efdd7d57681529751e99c13894
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="design-patterns-for-multitenant-saas-applications-and-azure-search"></a>Designmönster för multitenant SaaS-program och Azure Search
Flera program är en som innehåller samma tjänster och funktioner till flera klienter som inte kan se eller dela data med andra innehavare. Det här dokumentet beskrivs klient isolering strategier för flera program som skapats med Azure Search.

## <a name="azure-search-concepts"></a>Azure Search-begrepp
Azure Search kan utvecklare lägga till omfattande sökning upplevelser i program utan att hantera någon infrastruktur eller bli expert i hämtning av information som en sökning som en tjänst-lösning. Data har överförts till tjänsten och sedan lagras i molnet. Med hjälp av enkla förfrågningar till Azure Search-API, kan data sedan ändras och söka i. En översikt över tjänsten finns i [i den här artikeln](http://aka.ms/whatisazsearch). Innan vi diskuterar designmönster, är det viktigt att förstå några begrepp i Azure Search.

### <a name="search-services-indexes-fields-and-documents"></a>Search-tjänster, index, fält och dokument
När du använder Azure Search kan en prenumererar på en *söktjänsten*. När data överförs till Azure Search lagras i en *index* inom search-tjänsten. Det kan finnas ett antal index i en enskild tjänst. Om du vill använda bekant begreppet databaser kan söktjänsten liknas vid en databas medan index i en tjänst kan liknas vid tabeller i en databas.

Varje index i en söktjänst har ett eget schema som definieras av ett antal anpassningsbara *fält*. Data läggs till ett Azure Search-index i form av enskilda *dokument*. Varje dokument måste laddas upp till ett visst index och måste passa den indexeringsschema. När du söker efter data med Azure Search utfärdas fulltextsökning frågor mot ett visst index.  Om du vill jämföra de här koncepten med en databas, fält kan liknas vid kolumner i en tabell och dokument kan liknas vid rader.

### <a name="scalability"></a>Skalbarhet
Alla Azure Search-tjänster i standarden [prisnivån](https://azure.microsoft.com/pricing/details/search/) kan skala i två dimensioner: lagring och tillgänglighet.

* *Partitioner* kan läggas till öka lagring av en söktjänst.
* *Repliker* kan läggas till en tjänst för att öka genomflödet av förfrågningar som en söktjänst kan hantera.

Att lägga till och ta bort partitioner och repliker vid tillåter kapaciteten för search-tjänsten att växa med mängden data och trafiken kraven för programmet. För search-tjänsten att uppnå en Läs [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), den kräver två repliker. För en tjänst att få en skrivskyddad [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), kräver tre repliker.

### <a name="service-and-index-limits-in-azure-search"></a>Tjänsten och index gränser i Azure Search
Det finns några olika [prisnivåer](https://azure.microsoft.com/pricing/details/search/) i Azure Search nivåerna har olika [gränser och kvoter](search-limits-quotas-capacity.md). Vissa av dessa gränser är på servicenivån, vissa är på nivån index och vissa är på nivån partition.

|  | Basic | Standard1 | Standard2 | Standard3 | Standard3 HD |
| --- | --- | --- | --- | --- | --- |
| Maximal repliker per tjänst |3 |12 |12 |12 |12 |
| Maximal partitioner per tjänst |1 |12 |12 |12 |3 |
| Maximal Search-enheter (repliker * partitioner) per tjänst |3 |36 |36 |36 |36 (max 3 partitioner) |
| Maximal dokument per tjänst |1 miljon |180 miljoner |720 miljoner |1.4 miljarder |600 miljoner |
| Maximalt lagringsutrymme per tjänst |2 GB |300 GB |1,2 TB |2,4 TB |600 GB |
| Maximal dokument per Partition |1 miljon |15 miljoner |60 miljoner |120 miljoner |200 miljoner |
| Maximalt lagringsutrymme per Partition |2 GB |25 GB |100 GB |200 GB |200 GB |
| Maximal index per tjänst |5 |50 |200 |200 |3000 (max 1000 index/partition) |

#### <a name="s3-high-density"></a>S3 Hög densitet '
Det finns ett alternativ för hög densitet (HD)-läge som utformats speciellt för multitenant-scenarier i Azure Search S3 prisnivån. I många fall är det nödvändigt att stödja ett stort antal mindre klienter under en enskild tjänst för att få fördelarna med enkelhet och kostnadsbesparingar.

S3 HD tillåter många små index ska packas under hanteringen av en enda söktjänsten av handel möjligheten att skala ut index med partitioner för möjligheten att vara värd för flera index i en enskild tjänst.

S3-tjänsten kan concretely, ha mellan 1 och 200 index som tillsammans kan vara värd för upp till 1.4 miljarder dokument. En S3 HD å andra sidan skulle låta enskilda index bara välja upp till 1 miljoner dokument, men den kan hantera upp till 1 000 index per partition (upp till 3000 per service) och det totala dokumentantal 200 miljoner per partition (upp till 600 miljoner per tjänst).

## <a name="considerations-for-multitenant-applications"></a>Överväganden för flera program
Flera program måste effektivt distribuera resurser mellan klienterna och behålla vissa andelen sekretess mellan olika klienter. Det finns några överväganden vid utformning av arkitekturen för ett sådant program:

* *Klientisolering:* programutvecklare behöver vidta åtgärder för att se till att inga klienter har Ej behörig eller oönskad åtkomst till data med andra klienter. Utöver perspektivet datasekretess kräver strategier för isolering av innehavare effektiv hantering av delade resurser och skydd mot störningar grannar.
* *Molnet resurskostnader:* med alla andra program måste programlösningar förblir kostnaden konkurrenskraftiga som en del av ett flera program.
* *Enkel Operations:* när du utvecklar en arkitektur med flera påverkan på programmets åtgärder och komplexitet är viktigt. Azure Search har en [SLA för 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
* *Globala storleken:* flera program kan behöva effektivt hantera klienter som distribueras över hela världen.
* *Skalbarhet:* programutvecklare behöver tänka på hur de stämmer mellan upprätthålla en tillräckligt låg nivå av programmet komplexitet och utformning av programmet som ska skalas med många klienter och storleken på klienternas data och arbetsbelastning.

Azure Search erbjuder några gränser som kan användas för att isolera klienternas data och arbetsbelastning.

## <a name="modeling-multitenancy-with-azure-search"></a>Modeling multitenancy med Azure Search
Vid ett scenario med flera programutvecklaren förbrukar en eller flera search-tjänster och dela sina klienter bland tjänster, index eller båda. Azure Search har några vanliga mönster när modeling ett scenario med flera:

1. *Index per klient:* varje innehavare har sin egen index i en söktjänst som delas med andra klienter.
2. *Tjänsten per klient:* varje innehavare har en egen dedikerade Azure Search-tjänst ger högsta nivå uppdelning av data och arbetsbelastning.
3. *Blandning av båda:* större och mer aktiva klienter tilldelas dedikerade tjänster medan mindre klienter tilldelas enskilda index i delade tjänster.

## <a name="1-index-per-tenant"></a>1. Index per klient
![En bild av modellen index per klient](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png)

I ett index per klient modellen anges flera innehavare på en enda Azure Search-tjänst där varje innehavare har sina egna index.

Klienter uppnå isolering av data eftersom alla söka begäranden och dokumentet operations utfärdas på indexnivå i Azure Search. I program-lagret finns medvetenheten behovet av att dirigera om de olika innehavare trafik till rätt index och hanterar resurser på tjänstnivå över alla klienter.

Nyckelattribut modellens index per klient är möjligheten för programutvecklare att överprenumerera kapaciteten för en söktjänst mellan programmets klienter. Om klienterna har ojämn fördelning av arbetsbelastning, kan optimal kombination av klienter fördelas på en söktjänst index för ett antal mycket aktiv, resurskrävande klienter samtidigt samtidigt som man betjänar en lång slutet av mindre aktiva klienter. En kompromiss är modellen oförmåga att hantera situationer där varje innehavare samtidigt är mycket aktiv.

Index per klient modellen utgör grunden för en modell med rörlig kostnad, där en hela Azure Search-tjänsten köps direkta och därefter fylls med klienter. Detta ger outnyttjad kapacitet kan utses för försök och kostnadsfria konton.

För program med en global storleken kanske inte index per klient modellen det mest effektiva sättet. Om klienter för ett program distribueras över hela världen, kan det vara nödvändigt för varje region som duplicera kostnader över dem en separat tjänst.

Azure Search kan skalan för både enskilda index och det totala antalet index att växa. Om en lämplig priser valt nivån partitioner och repliker kan läggas till hela söktjänsten när ett enskilt index i tjänsten blir för stor vad gäller lagring eller trafik.

Om det totala antalet index blir för stor för en enskild tjänst har en annan tjänst som ska etableras för att anpassa de nya klienterna. Om index har flyttas mellan search-tjänster som har lagts till av nya tjänster, måste data från indexet kopieras manuellt från ett index till ett annat som inte tillåter Azure Search index som ska flyttas.

## <a name="2-service-per-tenant"></a>2. Tjänsten per klient
![En bild av service per klient](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png)

Varje innehavare har sin egen söktjänsten i en arkitektur med tjänsten per klient.

I den här modellen ger programmet den maximala nivån för isolering för sina klienter. Varje tjänst har särskilda lagrings- och genomströmning för hantering av sökbegäran samt separata API-nycklar.

För program där varje innehavare har ett stort utrymme eller arbetsbelastningen har lite variationen från klient till klienten, är tjänsten per klient modellen passar som resurser inte delas mellan olika klienternas arbetsbelastningar.

En tjänst per klient modell erbjuder också fördelen med en förutsägbar, fast kostnad-modell. Det finns inga direkta investeringar i ett hela söktjänsten tills det är en klient att fylla det, men kostnaden per klient är högre än en modell för index per klient.

Tjänsten per klient modellen är ett effektivt val för program med en global storleken. Med geografiskt spridd klienter är det lätt att varje klient service i rätt region.

När enskilda klienter växa ifrån tjänsten uppstå utmaningarna med skalning detta mönster. Azure Search stöder för närvarande inte uppgradera prisnivån för search-tjänsten så att alla data måste kopieras manuellt till en ny tjänst.

## <a name="3-mixing-both-models"></a>3. Blandning av båda modellerna
En annan mönster för modellering multitenancy blanda strategier för både index per klient och tjänstens per klient.

Största klienter för ett program kan blandas två mönster, uppta dedikerade tjänster medan mindre aktiva, mindre hyresgäster lång slutet får ha index i en delad tjänst. Den här modellen säkerställer att de största innehavarna konsekvent hög prestanda från tjänsten samtidigt skydda mindre klienter från alla störningar grannar.

Implementera den här strategin bygger dock förutseende förutsäga vilken klienter kräver en särskild service jämfört med ett index i en delad tjänst. Programmet komplexitet ökar behovet av att hantera både modellers multitenancy.

## <a name="achieving-even-finer-granularity"></a>Uppnå även ökad detaljnivå
Ovanstående designmönster modellera flera scenarier i Azure Search förutsätter en enhetlig scopet där varje innehavare en hel instans av ett program. Program kan ibland hantera många mindre scope.

Om tjänsten per klient och index per klient modeller inte är tillräckligt små för scope är det möjligt att utforma ett index för att uppnå även finmaskig granularitet.

Om du vill ha ett index beter sig på olika sätt för olika klientslutpunkter kan ett fält läggas till ett index som anger ett visst värde för varje klient som möjligt. Varje gång en klient anropar Azure Search att söka eller ändra ett index koden från klientprogrammet anger lämpligt värde för fältet med Azure Search [filter](https://msdn.microsoft.com/library/azure/dn798921.aspx) kapaciteten när databasfrågan.

Den här metoden kan användas för att möjliggöra funktioner av separata användarkonton, separat behörighetsnivåer och även helt olika program.

> [!NOTE]
> Den metod som beskrivs ovan påverkar för att konfigurera ett index för att betjäna flera innehavare för sökresultat. Sök relevans poäng beräknas på ett index på objektnivå scope, inte ett klient-nivå scope, så alla klienter data ingår i relevans poäng underliggande statistik, till exempel termen frekvens.
> 
> 

## <a name="next-steps"></a>Nästa steg
Azure Search är en tvingande val för många program [Läs mer om tjänstens robusta funktionerna](http://aka.ms/whatisazsearch). När du utvärderar de olika designmönster för flera program, Överväg att i [olika prisnivåer](https://azure.microsoft.com/pricing/details/search/) och motsvarande [gränser](search-limits-quotas-capacity.md) bäst anpassa Azure Search så att de passar programbelastningar och arkitekturer i alla storlekar.

Frågor om Azure Search och flera scenarier kan dirigeras till azuresearch_contact@microsoft.com.

