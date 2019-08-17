---
title: Välj en pris nivå eller SKU för Azure Search Service – Azure Search
description: 'Azure Search kan tillhandahållas i följande SKU: er: Kostnads fri, Basic och standard, och standard, är tillgängliga i olika datorkonfigurationer och kapacitets nivåer.'
services: search
author: HeidiSteen
manager: nitinme
tags: ''
ms.service: search
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: heidist
ms.openlocfilehash: d93f8c61511dd1d3fc2bfd253fa7a21857f67ed6
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563361"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Välj en pris nivå för Azure Search

När du skapar en Azure Search tjänst skapas en [resurs](search-create-service-portal.md) på en pris nivå (eller SKU) som har åtgärd ATS för livs längden för tjänsten. Nivåerna är kostnads fria, Basic, standard och Storage-optimerade. Standard-och lagrings optimering är tillgänglig med flera konfigurationer och kapaciteter.

De flesta kunder börjar med den kostnads fria nivån så att de kan utvärdera tjänsten. Efter utvärdering är det vanligt att skapa en andra tjänst på en av de högre nivåerna för utveckling och produktions distributioner.

Även om alla nivåer, inklusive den kostnads fria nivån, vanligt vis erbjuder funktions paritet, kan större arbets belastningar diktera ett behov för högre nivåer. [AI-anrikning med Cognitive Services](cognitive-search-concept-intro.md) har till exempel långvariga färdigheter som tar slut på en kostnads fri tjänst, om inte data uppsättningen är liten.

> [!NOTE] 
> Undantag för funktions paritet är [indexerare](search-indexer-overview.md)som inte är tillgängliga på S3 HD.
>

<!-- For Basic tier and up, you can [adjust replica and partition resources](search-capacity-planning.md) to increase or decrease scale. You could start with one or two of each and then temporarily raise your computational power for a heavy indexing workload. The ability to tune resource levels within a tier adds flexibility, but also slightly complicates your analysis. You might have to experiment to see whether a lower tier with more resources/replicas offers better value and performance than a higher tier with fewer resources. To learn more about when and why you would adjust capacity, see [Performance and optimization considerations](search-performance-optimization.md). -->

## <a name="available-tiers"></a>Tillgängliga nivåer

Nivåer återspeglar egenskaperna hos maskin varan som är värd för tjänsten (i stället för funktioner) och särskiljs av:

+ Antal index och indexerare som du kan skapa
+ Storlek och hastighet för partitioner (fysisk lagring)

Nivån som du väljer avgör fakturerings priset. Följande skärm bild från Azure Portal visar tillgängliga nivåer, minus priser (som du hittar i portalen och på [prissättnings sidan](https://azure.microsoft.com/pricing/details/search/). **Kostnads fri**, **Basic**och **standard** är de vanligaste nivåerna.

**Kostnads fri** skapar en begränsad Sök tjänst i ett kluster som delas med andra prenumeranter. Du kan slutföra små projekt, inklusive snabb starter och självstudier, men du kan inte skala tjänsten eller köra betydande arbets belastningar. **Basic** och **standard** är de vanligaste fakturerbara nivåerna, med **standard standarden** .

![Pris nivåer för Azure Search](media/search-sku-tier/tiers.png "Pris nivåer för Azure Search")

Vissa nivåer är optimerade för vissa typer av arbete. Till exempel är **standard 3 hög densitet (S3 HD)** ett *värd läge* för S3, där den underliggande maskin varan är optimerad för ett stort antal mindre index och är avsedd för flera organisationers scenarier. S3 HD har samma kostnad per enhet som S3, men maskin varan är optimerad för snabba fil läsningar på ett stort antal mindre index.

**Lagrings optimerings** nivåer ger större lagrings kapacitet till ett lägre pris per TB än standard nivåerna. Den främsta kompromissen är högre svars tid för frågor, som du bör validera för dina specifika program krav.  Mer information om prestanda överväganden för den här nivån finns i [överväganden för prestanda och optimering](search-performance-optimization.md).

Du kan få mer information om olika nivåer på [sidan prissättning](https://azure.microsoft.com/pricing/details/search/)i avsnittet om [tjänst begränsningar i Azure Search](search-limits-quotas-capacity.md) artikel och på Portal sidan när du ska tillhandahålla en tjänst.

## <a name="billable-events"></a>Fakturerbara händelser

En lösning som bygger på Azure Search kan ådra sig kostnaderna på följande sätt:

+ Bas kostnad för tjänst vid minimal konfiguration
+ Stegvis kostnad vid skalning (lägga till repliker eller partitioner)
+ Bandbredds avgifter för utgående data överföring
+ Kognitiv sökning utnyttjar Cognitive Services resurser

### <a name="service-costs"></a>Tjänste kostnader

Till skillnad från virtuella datorer eller andra resurser som kan vara "pausade" för att undvika avgifter, är en Azure Search-tjänst alltid tillgänglig på maskin vara som är dedikerad för exklusiv användning. Därför är att skapa en tjänst en fakturerbar händelse som startar när du skapar tjänsten och slutar när du tar bort tjänsten. 

Den lägsta avgiften är den första Sök enheten (en partition med en enda replik x). Detta minimum är fast för tjänstens livs längd eftersom tjänsten inte kan köras på något som är mindre än den här konfigurationen. Utöver det lägsta antalet kan du lägga till repliker och partitioner oberoende av varandra. Stegvisa ökningar i kapaciteten via repliker och partitioner ökar din faktura utifrån följande formel: [(repliker x partitioner x hastighet)](#search-units), där den hastighet du debiteras beror på vilken pris nivå du väljer.

När du uppskattar kostnaden för en Sök lösning bör du tänka på att priserna och kapaciteten inte är linjära. (Dubbla kapaciteten är mer än dubbelt så mycket som kostnaden.) Ett exempel på hur formeln fungerar finns i [så här allokerar du repliker och partitioner](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

### <a name="bandwidth-charges"></a>Bandbredds avgifter

Att använda [Azure Search indexerare](search-indexer-overview.md) kan påverka faktureringen, beroende på var tjänsterna finns. Du kan ta bort kostnader för utgående data helt och hållet om du skapar tjänsten Azure Search i samma region som dina data. Här är lite information från [prissättnings sidan för bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/):

+ Microsoft debiterar inte några inkommande data till någon tjänst i Azure, eller för utgående data från Azure Search.
+ I lösningar med flera tjänster är det ingen avgift för data som passerar kabeln när alla tjänster är i samma region.

Avgifterna gäller för utgående data om tjänsterna är i olika regioner. Dessa avgifter är inte en del av din Azure Search faktura. De anges här eftersom om du använder data eller AI-berikade indexerare för att hämta data från olika regioner, ser du kostnader som återspeglas i din övergripande faktura.

### <a name="cognitive-search-ai-enrichment-with-cognitive-services"></a>Kognitiv search AI-anrikning med Cognitive Services

För [AI-anrikning med Cognitive Services](cognitive-search-concept-intro.md)bör du planera att [koppla en fakturerbar Azure Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md), i samma region som Azure Search, på pris nivån S0 för att betala per användning. Det finns ingen fast kostnad för att bifoga Cognitive Services. Du betalar bara för den bearbetning du behöver.

| Åtgärd | Fakturerings påverkan |
|-----------|----------------|
| Dokument sprickor, text extrahering | Lediga |
| Dokument sprickor, avbildnings extrahering | Faktureras enligt antalet bilder som har extraherats från dina dokument. I en [indexerare-konfiguration](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters)är **imageAction** den parameter som utlöser avbildnings extrahering. Om **imageAction** är inställt på "ingen" (standard) debiteras du inte för avbildnings extrahering. Hastigheten för avbildnings extrahering dokumenteras på sidan med [pris information](https://azure.microsoft.com/pricing/details/search/) för Azure Search.|
| [Förbyggda kognitiva färdigheter](cognitive-search-predefined-skills.md) | Faktureras enligt samma takt som om du har utfört uppgiften genom att använda Cognitive Services direkt. |
| Anpassade färdigheter | En anpassad färdighet är funktioner som du anger. Kostnaden för att använda en anpassad färdighet beror helt på om anpassad kod anropar andra avgiftsbelagda tjänster. |

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>Fakturerings formel (R x P = SU)

Det viktigaste fakturerings konceptet för att förstå Azure Search åtgärder är *Sök enheten* (SU). Eftersom Azure Search är beroende av både repliker och partitioner för indexering och frågor, är det inte meningsfullt att fakturera med bara en eller ett annat. Faktureringen baseras i stället på en sammansatt av båda.

SU är produkten av de *repliker* och *partitioner* som används av en tjänst: **(R x P = SU)** .

Varje tjänst börjar med en SU (en replik multiplicerad med en partition) som minimum. Det maximala antalet för en tjänst är 36 SUs. Det här max värdet kan nås på flera sätt: 6 partitioner x 6 repliker eller 3 partitioner x 12 repliker, till exempel. Det är vanligt att använda mindre än den totala kapaciteten (t. ex. en 3-replikering, en 3-partitions tjänst som faktureras som 9 SUs). Se diagrammet [partition och replik kombinations](search-capacity-planning.md#chart) diagram för giltiga kombinationer.

Fakturerings takten är per timme per SU. Varje nivå har en progressivt högre hastighet. Högre nivåer levereras med större och speedier partitioner, och detta bidrar till en högre pris per timme för den nivån. Du kan visa priserna för varje nivå på [pris informations](https://azure.microsoft.com/pricing/details/search/) sidan.

De flesta kunder tar bara en del av den totala kapaciteten online, där resten av reserven behålls. För fakturering bestämmer antalet partitioner och repliker som du ansluter online, vilket avgör vad du betalar per timme.

## <a name="how-to-manage-and-reduce-costs"></a>Hantera och minska kostnaderna

Utöver följande förslag kan du gå till fakturerings- [och kostnads hantering](https://docs.microsoft.com/azure/billing/billing-getting-started).

- Skapa alla resurser i samma region, eller i så få regioner som möjligt, för att minimera eller eliminera bandbredds kostnader.

- Konsolidera alla tjänster till en resurs grupp, till exempel Azure Search, Cognitive Services och andra Azure-tjänster som används i din lösning. I Azure Portal letar du reda på resurs gruppen och använder **Cost Management** kommandon för att få inblick i faktiska och planerade utgifter.

- Överväg att använda Azure Web App för ditt klient program så att förfrågningar och svar stannar inom data centrets gränser.

- Skala upp för resurs krävande åtgärder som indexering och justera sedan nedåt för vanliga frågor och arbets belastningar. Börja med den lägsta konfigurationen för Azure Search (en SU som består av en partition och en replik) och övervaka sedan användar aktivitet för att identifiera användnings mönster som indikerar ett behov av mer kapacitet. Om det finns ett förutsägbart mönster kanske du kan synkronisera skalning med aktivitet (du måste skriva kod för att automatisera detta).

Du kan inte stänga av en Sök tjänst för att minska din faktura. Dedikerade resurser är alltid operativa, allokeras för exklusiv användning för din tjänsts livs längd. Vad gäller själva tjänsten är det enda sättet att sänka din faktura till att minska antalet repliker och partitioner till en nivå som fortfarande ger acceptabel prestanda och [SLA-efterlevnad](https://azure.microsoft.com/support/legal/sla/search/v1_0/), eller skapa en tjänst på en lägre nivå (S1 Tim pris är lägre än S2 eller S3-priser). Förutsatt att du etablerar tjänsten i det nedre slutet av dina belastnings prognoser, kan du skapa en andra större tjänst, återskapa dina index på den andra tjänsten och sedan ta bort den första.

## <a name="how-to-evaluate-capacity-requirements"></a>Utvärdera kapacitets kraven

I Azure Search struktureras kapaciteten som *repliker* och *partitioner*.

+ Repliker är instanser av Sök tjänsten. Varje replik är värd för en belastningsutjämnad kopia av ett index. Till exempel innehåller en tjänst med sex repliker sex kopior av varje index som har lästs in i tjänsten.

+ Partitioner lagrar index och automatiskt delar sökbara data. Två partitioner delar ditt index på hälften, tre partitioner delar upp dem i tredje, och så vidare. Med avseende på kapaciteten är *partitionsstorleken* den primära differentierings funktionen mellan nivåer.

> [!NOTE]
> Alla optimerade nivåer för standard och lagring stöder [flexibla kombinationer av repliker och partitioner](search-capacity-planning.md#chart) , så att du kan [optimera systemet för hastighet eller lagring](search-performance-optimization.md) genom att ändra saldot. Basic-nivån erbjuder upp till tre repliker för hög tillgänglighet men har bara en partition. Kostnads fria nivåer ger inte dedikerade resurser: data bearbetnings resurser delas av flera prenumeranter.

<!-- ## Consumption patterns

On the low and high ends, Basic and S3 HD are for important but atypical consumption patterns. Basic is for small production workloads. It offers SLAs, dedicated resources, and high availability, but it provides modest storage, topping out at 2 GB total. This tier was engineered for customers that consistently underutilize available capacity. At the high end, S3 HD is for workloads typical of ISVs, partners, [multitenant solutions](search-modeling-multitenant-saas-applications.md), or any configuration that calls for a large number of small indexes. It's often clear when Basic or S3 HD is the right tier. If you want confirmation, you can post to [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) or [contact Azure support](https://azure.microsoft.com/support/options/) for guidance.

The more commonly used standard tiers, S1 through S3, make up a progression of increasing levels of capacity. There are inflection points on partition size and limits on numbers of indexes, indexers, and corollary resources:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Partition size|  25 GB | 100 GB | 200 GB |  |  |  |  |
| Index and indexer limits| 50 | 200 | 200 |  |  |  |  |

S1 is a common choice for customers that need dedicated resources and multiple partitions. S1 offers partitions of 25 GB and up to 12 partitions, providing a per-service limit of 300 GB if you maximize partitions over replicas. (See [Allocate partitions and replicas](search-capacity-planning.md#chart) for more balanced allocations.)

The portal and pricing pages put the focus on partition size and storage, but, for each tier, all compute capabilities (disk capacity, speed, CPUs) generally increase linearly with price. An S2 replica is faster than S1, and S3 is faster than S2. S3 tiers break from the linear compute-pricing pattern with disproportionately faster I/O. If you expect I/O to be the bottleneck, keep in mind that you can get much more IOPS with S3 than you can get with lower tiers.

S3 and S3 HD are backed by identical high-capacity infrastructure, but they reach their maximum limits in different ways. S3 targets a smaller number of very large indexes, so its maximum limit is resource-bound (2.4 TB for each service). S3 HD targets a large number of very small indexes. At 1,000 indexes, S3 HD reaches its limits in the form of index constraints. If you're an S3 HD customer and you need more than 1,000 indexes, contact Microsoft Support for information about how to proceed.

> [!NOTE]
> Document limits were a consideration at one time, but they're no longer applicable for new services. For information about conditions in which document limits still apply, see [Document limits](search-limits-quotas-capacity.md#document-limits).
>

Storage Optimized tiers, L1 and L2, are ideal for applications with large data requirements but a relatively low number of end users, when minimizing query latency isn't the top priority.  

|  | L1 | L2 |  |  |  |  |  |
|--|----|----|--|--|--|--|--|
| Partition size|  1 TB | 2 TB |  |  |  |  |  |
| Index and indexer limits| 10 | 10 |  |  |  |  |  |

L2 offers twice the overall storage capacity of L1.  Choose your tier based on the maximum amount of data that you think your index needs. The L1 tier partitions scale up in 1-TB increments to a maximum of 12 TB. The L2 partitions increase by 2 TBs per partition up to a maximum of 24 TB. -->

### <a name="evaluating-capacity"></a>Utvärderar kapacitet

Kapaciteten och kostnaderna för att köra tjänsten är direkt relaterade. Nivåerna begränsar gränserna på två nivåer: lagring och resurser. Du bör tänka på båda eftersom den gräns du uppnår först är den effektiva gränsen.

Företags krav avgör vanligt vis hur många index du behöver. Du kan till exempel behöva ett globalt index för en stor lagrings plats för dokument. Eller så kanske du behöver flera index baserade på region, program eller affärs nischmarknader.

Om du vill fastställa storleken på ett index måste du [bygga ett](search-create-index-portal.md). Data strukturen i Azure Search är i första hand en [inverterad index](https://en.wikipedia.org/wiki/Inverted_index) struktur som har andra egenskaper än källdata. För ett inverterat index bestäms storlek och komplexitet av innehåll, inte nödvändigt vis av mängden data som du matar in i det. En stor data källa med hög redundans kan resultera i ett mindre index än en mindre data mängd som innehåller mycket varierande innehåll. Det går sällan att härleda index storleken baserat på storleken på den ursprungliga data uppsättningen.

> [!NOTE] 
> Även om det går att uppskatta framtida behov för index och lagring kan det vara värt att göra. Om en nivås kapacitet blir för låg måste du etablera en ny tjänst på en högre nivå och sedan [läsa in indexen](search-howto-reindex.md)på nytt. Det finns ingen uppgradering på plats av en tjänst från en SKU till en annan.
>

### <a name="step-1-develop-rough-estimates-by-using-the-free-tier"></a>Steg 1: Utveckla grov uppskattningar med den kostnads fria nivån

En metod för att uppskatta kapaciteten är att börja med den kostnads fria nivån. Kom ihåg att den kostnads fria tjänsten erbjuder upp till tre index, 50 MB lagrings utrymme och 2 minuters indexerings tid. Det kan vara svårt att uppskatta en beräknad index storlek med dessa begränsningar. Här är en metod som du kan vidta:

+ [Skapa en kostnads fri tjänst](search-create-service-portal.md).
+ Förbered en liten, representativ data uppsättning (till exempel 5 000 dokument och 10 procents samplings storlek).
+ [Bygg ett första index](search-create-index-portal.md) och anteckna dess storlek i portalen (till exempel 30 MB).

Om exemplet är representativt och 10 procent av hela data källan blir ett 30 MB-index ungefär 300 MB om alla dokument är indexerade. Väpnade med det här preliminära numret kan du dubblera beloppet till budget för två index (utveckling och produktion). Detta ger dig totalt 600 MB i lagrings kraven. Det här kravet är enkelt att uppfyllas av Basic-nivån, så du börjar där.

### <a name="step-2-develop-refined-estimates-by-using-a-billable-tier"></a>Steg 2: Utveckla raffinerade uppskattningar med hjälp av en fakturerbar nivå

Vissa kunder föredrar att börja med dedikerade resurser som kan hantera större samplings-och bearbetnings tider och sedan utveckla realistiska uppskattningar av index antal, storlek och fråga-volymer under utvecklingen. En tjänst har inlednings vis etablerats baserat på en uppskattning för bästa gissning. I takt med att utvecklings projektet mognar vet teamen vanligt vis om den befintliga tjänsten är över eller under kapacitet för projekt arbets belastningar.

1. [Granska tjänst begränsningar på varje nivå](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) för att avgöra om lägre nivåer kan stödja det antal index du behöver. På nivåerna Basic, S1 och S2 är index gränserna 15, 50 respektive 200. Lagrings optimerings nivån har en gräns på 10 index eftersom den har utformats för att stödja ett lågt antal mycket stora index.

1. [Skapa en tjänst på en fakturerbar nivå](search-create-service-portal.md):

    + Starta låg, med Basic eller S1, om du befinner dig i början av din inlärnings kurva.
    + Starta hög, på S2 eller till och med S3 om du vet att du kommer att ha storskalig indexering och läsa in frågor.
    + Börja med den optimerade lagringen, vid L1 eller L2, om du har indexerat en stor mängd data och frågan är relativt låg, precis som med ett internt affärs program.

1. [Bygg ett första index](search-create-index-portal.md) för att avgöra hur källdata översätts till ett index. Detta är det enda sättet att uppskatta index storleken.

1. [Övervaka lagring, tjänst gränser, fråga volym och svars tid](search-monitor-usage.md) i portalen. Portalen visar frågor per sekund, begränsade frågor och Sök svars tid. Alla dessa värden kan hjälpa dig att avgöra om du har valt rätt nivå. Du kan också konfigurera djup övervakning av värden som genomklickning analys genom att aktivera [Sök trafik analys](search-traffic-analytics.md).

Indexets nummer och storlek är lika viktiga för din analys. Detta beror på att max gränserna uppnås genom fullständig användning av lagrings utrymme (partitioner) eller av maximala gränser för resurser (index, indexerare och så vidare), beroende på vilket som kommer först. Portalen hjälper dig att hålla koll på båda, som visar aktuella användnings-och Max gränser sida vid sida på översikts sidan.

> [!NOTE]
> Lagrings kraven kan vara deflata om dokumenten innehåller ovidkommande data. Vi rekommenderar att dokument endast innehåller de data som du behöver för Sök funktionen. Binära data är inte sökbara och bör lagras separat (kanske i en Azure-tabell eller Blob-lagring). Ett fält ska sedan läggas till i indexet för att innehålla en URL-referens till externa data. Den maximala storleken för ett enskilt dokument är 16 MB (eller mindre om du överför flera dokument i en begäran). Mer information finns i [tjänst gränser i Azure Search](search-limits-quotas-capacity.md).
>

**Fråga om volym överväganden**

Frågor per sekund (frågor per sekund) är ett viktigt mått under prestanda justeringen, men det är vanligt vis bara en nivå som beaktas om du förväntar dig hög fråga-volym vid början.

Standard-nivåerna kan ge en balans mellan repliker och partitioner. Du kan öka leveransen genom att lägga till repliker för belastnings utjämning eller lägga till partitioner för parallell bearbetning. Du kan sedan justera efter prestanda när tjänsten har tillhandahållits.

Om du förväntar dig hög varaktiga frågegrupper från början bör du överväga högre standard nivåer, med mer kraftfulla maskin vara. Du kan sedan ta partitioner och repliker offline, eller till och med växla till en tjänst på lägre nivå, om dessa fråga-volymer inte inträffar. Mer information om hur du beräknar frågans data flöde finns i [Azure Search prestanda och optimering](search-performance-optimization.md).

De optimerade lagrings nivåerna är användbara för stora data arbets belastningar och stöder mer övergripande tillgängligt index lagring för när svars tids fördröjnings kraven är mindre viktiga. Du bör fortfarande använda ytterligare repliker för belastnings utjämning och ytterligare partitioner för parallell bearbetning. Du kan sedan justera efter prestanda när tjänsten har tillhandahållits.

**Service nivå avtal**

Den kostnads fria nivån och för hands versions funktionerna tillhandahåller inte [service nivå avtal (service avtal)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). För alla fakturerbara nivåer börjar service avtal gälla när du etablerar tillräckligt med redundans för din tjänst. Du måste ha två eller flera repliker för fråga (Läs) service avtal. Du måste ha tre eller fler repliker för fråge-och indexerings-service avtal (Läs-och Skriv behörighet). Antalet partitioner påverkar inte service avtal.

## <a name="tips-for-tier-evaluation"></a>Tips för nivå utvärdering

+ Lär dig hur du skapar effektiva index och lär dig vilka uppdaterings metoder som har minst effekt. Använd [Sök trafik analys](search-traffic-analytics.md) för att få insikter om frågans aktivitet.

+ Tillåt att mått skapas runt frågor och samla in data i användnings mönster (frågor under kontors tid, indexering under låg belastnings tider). Använd dessa data för att informera om tjänste etablerings beslut. Även om det inte är praktiskt vid en tim-eller daglig takt, kan du dynamiskt justera partitioner och resurser för att hantera planerade ändringar i frågornas volymer. Du kan också hantera oplanerad men varaktiga ändringar om nivåerna är tillräckligt långa för att motivera att vidta åtgärder.

+ Kom ihåg att det enda nack delen med underetablering är att du kan behöva riva av en tjänst om de faktiska kraven är större än dina förutsägelser. För att undvika avbrott i tjänsten skapar du en ny tjänst i samma prenumeration på en högre nivå och kör den sida vid sida tills alla appar och begär Anden riktar sig mot den nya slut punkten.

## <a name="next-steps"></a>Nästa steg

Börja med en kostnads fri nivå och skapa ett första index genom att använda en delmängd av dina data för att förstå dess egenskaper. Data strukturen i Azure Search är en inverterad index struktur. Storleken och komplexiteten för ett inverterat index avgörs av innehållet. Kom ihåg att mycket redundant innehåll leder till ett mindre index än innehåll som är mycket oregelbundet. Egenskaper för innehåll i stället för data uppsättningens storlek avgör index lagrings kraven.

När du har en första uppskattning av index storleken kan du [etablera en fakturerings bar tjänst](search-create-service-portal.md) på en av de nivåer som beskrivs i den här artikeln: Basic, standard eller Storage är optimerat. Minska eventuella artificiella begränsningar för data storlek och [återskapa indexet](search-howto-reindex.md) så att det innehåller alla data som du vill ska vara sökbara.

[Allokera partitioner och repliker](search-capacity-planning.md) efter behov för att få den prestanda och skalning du behöver.

Om prestanda och kapacitet är bra är du klar. Annars återskapar du en Sök tjänst på en annan nivå som passar bäst för dina behov.

> [!NOTE]
> Om du har frågor kan du publicera på [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) eller [kontakta Azure](https://azure.microsoft.com/support/options/)-supporten.
