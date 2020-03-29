---
title: Välj en prisnivå eller SKU
titleSuffix: Azure Cognitive Search
description: 'Azure Cognitive Search kan etableras i dessa SKU:er: Kostnadsfria, Grundläggande och Standard och Standard är tillgängligt i olika resurskonfigurationer och kapacitetsnivåer.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 35dbd064a09a96dae58e1b15a6d8889bda45ee0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76899842"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Välj en prisnivå för Azure Cognitive Search

När du skapar en Azure Cognitive Search-tjänst [skapas](search-create-service-portal.md) en resurs på en prisnivå (eller SKU) som är fast för tjänstens livstid. Nivåerna inkluderar free, basic, standard och lagringsoptimerade. Standard- och lagringsoptimerade finns med flera konfigurationer och kapaciteter.

De flesta kunder börjar med den kostnadsfria nivån så att de kan utvärdera tjänsten. Efter utvärdering är det vanligt att skapa en andra tjänst på en av de högre nivåerna för utvecklings- och produktionsdistributioner.

## <a name="feature-availability-by-tier"></a>Funktionstillgänglighet efter nivå

Nästan alla funktioner är tillgängliga på alla nivåer, inklusive gratis, men en funktion eller ett arbetsflöde som är resurskrävande kanske inte fungerar bra om du inte ger den tillräcklig kapacitet. [Ai-anrikning](cognitive-search-concept-intro.md) har till exempel tidskrävande kunskaper som time out på en kostnadsfri tjänst om inte datauppsättningen är liten.

I följande tabell beskrivs nivårelaterade funktionsbegränsningar.

| Funktion | Begränsningar |
|---------|-------------|
| [Indexerare](search-indexer-overview.md) | Indexerare är inte tillgängliga på S3 HD. |
| [Kundhanterade krypteringsnycklar](search-security-manage-encryption-keys.md) | Inte tillgängligt på den kostnadsfria nivån. |

## <a name="tiers-skus"></a>Nivåer (SKU: er)

Nivåer differentieras av:

+ Antal index och indexerare som du kan skapa
+ Partitionernas storlek och hastighet (fysisk lagring)

Den nivå du väljer avgör den fakturerbara hastigheten. Följande skärmbild från Azure-portalen visar tillgängliga nivåer, minus priser (som du hittar i portalen och på [prissidan](https://azure.microsoft.com/pricing/details/search/). **Gratis,** **Basic**och **Standard** är de vanligaste nivåerna.

**Free** skapar en begränsad söktjänst i ett kluster som delas med andra prenumeranter. Du kan slutföra små projekt, inklusive snabbstarter och självstudier, men du kan inte skala tjänsten eller köra betydande arbetsbelastningar. **Basic** och **Standard** är de vanligaste fakturerbara nivåerna, med **Standard** som standard.

![Prisnivåer för Azure Cognitive Search](media/search-sku-tier/tiers.png "Prisnivåer för Azure Cognitive Search")

Vissa nivåer är optimerade för vissa typer av arbete. **Standard 3 High Density (S3 HD)** är till exempel ett *värdläge* för S3, där den underliggande maskinvaran är optimerad för ett stort antal mindre index och är avsedd för scenarier med flera innehavare. S3 HD har samma per enhetsladdning som S3, men hårdvaran är optimerad för snabb filläsning på ett stort antal mindre index.

**Lagringsoptimerade** nivåer erbjuder större lagringskapacitet till ett lägre pris per TB än standardnivåerna. Den primära avvägningen är högre frågefördröjning, som du bör validera för dina specifika programkrav.  Mer information om prestandaöverväganden för den här nivån finns i [Prestanda- och optimeringsöverväganden](search-performance-optimization.md).

Du kan läsa mer om de olika nivåerna på [prissidan,](https://azure.microsoft.com/pricing/details/search/)i artikeln [Tjänstgränser i Azure Cognitive Search](search-limits-quotas-capacity.md) och på portalsidan när du etablerar en tjänst.

## <a name="billable-events"></a>Fakturerbara händelser

En lösning som bygger på Azure Cognitive Search kan medföra kostnader på följande sätt:

+ Baskostnad för tjänsten vid minimal konfiguration (skapa en tjänst)
+ Inkrementell kostnad vid uppskalning (lägga till repliker eller partitioner)
+ Bandbreddsavgifter (utgående dataöverföring) 
+ Kognitiv sökning (bifoga Cognitive Services för AI-anrikning, Azure-lagring för kunskapslager)

### <a name="service-costs"></a>Servicekostnader

Till skillnad från virtuella datorer eller andra resurser som kan "pausas" för att undvika avgifter är en Azure Cognitive Search-tjänst alltid tillgänglig på maskinvara som är dedikerad för din exklusiva användning. Att skapa en tjänst är därför en fakturerbar händelse som startar när du skapar tjänsten och slutar när du tar bort tjänsten. 

Den minsta avgiften är den första sökenheten (en replik x en partition) med den fakturerbara hastigheten. Det här minimumet har åtgärdats för tjänstens livstid eftersom tjänsten inte kan köras på något mindre än den här konfigurationen. Utöver det minsta kan du lägga till repliker och partitioner oberoende av varandra. Inkrementella kapacitetsökningar via repliker och partitioner ökar fakturan baserat på följande formel: [(repliker x partitioner x hastighet)](#search-units), där den hastighet du debiteras beror på vilken prisnivå du väljer.

När du uppskattar kostnaden för en söklösning bör du tänka på att prissättning och kapacitet inte är linjära. (Fördubblingskapacitet mer än fördubblar kostnaden.) Ett exempel på hur formeln fungerar finns i [Så här allokerar du repliker och partitioner](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

### <a name="bandwidth-charges"></a>Bandbredd avgifter

Om du använder [Azure Cognitive Search-indexerare](search-indexer-overview.md) kan det påverka faktureringen, beroende på var dina tjänster finns. Du kan eliminera datautgående avgifter helt om du skapar Azure Cognitive Search-tjänsten i samma region som dina data. Här är lite information från [bandbredd prissättning sida:](https://azure.microsoft.com/pricing/details/bandwidth/)

+ Microsoft debiterar inte för inkommande data till någon tjänst på Azure eller för utgående data från Azure Cognitive Search.
+ I multiservicelösningar debiteras ingen avgift för data som passerar kabeln när alla tjänster finns i samma region.

Avgifter gäller för utgående data om tjänsterna finns i olika regioner. Dessa avgifter är egentligen inte en del av din Azure Cognitive Search faktura. De nämns här för om du använder data eller AI-berikade indexerare för att hämta data från olika regioner ser du kostnader som återspeglas i din totala faktura.

### <a name="ai-enrichment-with-cognitive-services"></a>AI-anrikning med Kognitiva tjänster

För [AI-anrikning](cognitive-search-concept-intro.md)bör du planera att [bifoga en fakturerbar Azure Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md), i samma region som Azure Cognitive Search, på S0-prisnivån för användningsbaserad bearbetning. Det finns ingen fast kostnad i samband med att bifoga Cognitive Services. Du betalar bara för den behandling du behöver.

| Åtgärd | Faktureringspåverkan |
|-----------|----------------|
| Dokumentsprickning, textextrahering | Kostnadsfri |
| Dokumentsprickor, bildextrahering | Faktureras enligt antalet bilder som extraherats från dina dokument. I en [indexeringskonfiguration](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters)är **imageAction** den parameter som utlöser utvinning av bilder. Om **imageAction** är inställt på "ingen" (standard) debiteras du inte för bildextrahering. Hastigheten för utvinning av avbildning dokumenteras på [prisinformationssidan](https://azure.microsoft.com/pricing/details/search/) för Azure Cognitive Search.|
| [Inbyggda kognitiva kompetenser](cognitive-search-predefined-skills.md) | Faktureras i samma takt som om du hade utfört uppgiften med hjälp av Cognitive Services direkt. |
| Anpassade färdigheter | En anpassad färdighet är funktioner som du tillhandahåller. Kostnaden för att använda en anpassad färdighet beror helt på om anpassad kod anropar andra tjänster med datapriser. |

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>Faktureringsformel (R x P = SU)

Det viktigaste faktureringskonceptet att förstå för Azure Cognitive Search-åtgärder är *sökenheten* (SU). Eftersom Azure Cognitive Search är beroende av både repliker och partitioner för indexering och frågor, är det inte meningsfullt att fakturera med bara det ena eller det andra. Fakturering baseras i stället på en sammansatt av båda.

SU är produkten av *repliker* och *partitioner* som används av en tjänst: **(R x P = SU)**.

Varje tjänst börjar med en SU (en replik multiplicerad med en partition) som minimum. Det maximala för alla tjänster är 36 SUs. Det här maxbeloppet kan nås på flera sätt: 6 partitioner x 6 repliker eller 3 partitioner x 12 repliker, till exempel. Det är vanligt att använda mindre än den totala kapaciteten (till exempel en 3-replik, 3-partition tjänst faktureras som 9 SUs). Se diagrammet [Partition och replikkombinationer](search-capacity-planning.md#chart) för giltiga kombinationer.

Faktureringspriset är per timme per SU. Varje nivå har en gradvis högre hastighet. Högre nivåer levereras med större och snabbare partitioner, och detta bidrar till en övergripande högre timtaxa för den nivån. Du kan visa priserna för varje nivå på [prisinformationssidan.](https://azure.microsoft.com/pricing/details/search/)

De flesta kunder tar bara en del av den totala kapaciteten online, hålla resten i reserv. För fakturering avgör antalet partitioner och repliker som du tar med online, beräknat med SU-formeln, vad du betalar per timme.

## <a name="how-to-manage-costs"></a>Så här hanterar du kostnader

Följande förslag kan hjälpa dig att hålla kostnaderna på ett minimum:

- Skapa alla resurser i samma region, eller i så få regioner som möjligt, för att minimera eller eliminera bandbreddsavgifter.

- Konsolidera alla tjänster i en resursgrupp, till exempel Azure Cognitive Search, Cognitive Services och alla andra Azure-tjänster som används i din lösning. I Azure-portalen letar du reda på resursgruppen och använder **kommandona Kostnadshantering** för att få information om faktiska och beräknade utgifter.

- Överväg Azure Web App för ditt frontend-program så att begäranden och svaren håller sig inom datacentergränsen.

- Skala upp för resursintensiva åtgärder som indexering och justera sedan nedåt för vanliga frågearbetsbelastningar. Börja med den minsta konfigurationen för Azure Cognitive Search (en SU som består av en partition och en replik) och övervaka sedan användaraktivitet för att identifiera användningsmönster som indikerar ett behov av mer kapacitet. Om det finns ett förutsägbart mönster kanske du kan synkronisera skala med aktivitet (du måste skriva kod för att automatisera detta).

Besök dessutom [fakturering och kostnadshantering](https://docs.microsoft.com/azure/billing/billing-getting-started) för inbyggda verktyg och funktioner som är relaterade till utgifter.

Det går inte att stänga av en söktjänst tillfälligt. Dedikerade resurser är alltid i drift, som allokeras för din exklusiva användning under tjänstens livstid. Att ta bort en tjänst är permanent och tar även bort tillhörande data.

När det gäller själva tjänsten är det enda sättet att sänka fakturan att minska repliker och partitioner till en nivå som fortfarande ger acceptabel prestanda och [SLA-efterlevnad](https://azure.microsoft.com/support/legal/sla/search/v1_0/), eller skapa en tjänst på en lägre nivå (S1 timpriser är lägre än S2 eller S3 priser). Förutsatt att du etablerar tjänsten i den nedre delen av dina belastningsprojektioner, om du växlar ur tjänsten, kan du skapa en andra tjänst med större nivå, återskapa indexen på den andra tjänsten och sedan ta bort den första.

## <a name="how-to-evaluate-capacity-requirements"></a>Så här utvärderar du kapacitetskrav

I Azure Cognitive Search är kapaciteten strukturerad som *repliker* och *partitioner*.

+ Repliker är instanser av söktjänsten. Varje replik är värd för en belastningsbalanserad kopia av ett index. En tjänst med sex repliker har till exempel sex kopior av varje index som läses in i tjänsten.

+ Partitioner lagrar index och delar automatiskt upp sökbara data. Två partitioner delar upp indexet på mitten, tre partitioner delar upp det i tredjedelar och så vidare. När det gäller kapacitet är *partitionsstorlek* den primära särskiljande funktionen mellan nivåer.

> [!NOTE]
> Alla standard- och lagringsoptimerade nivåer stöder [flexibla kombinationer av repliker och partitioner](search-capacity-planning.md#chart) så att du kan [optimera systemet för hastighet eller lagring](search-performance-optimization.md) genom att ändra balansen. Basic-nivån erbjuder upp till tre repliker för hög tillgänglighet men har bara en partition. Kostnadsfria nivåer ger inte dedikerade resurser: datorresurser delas av flera prenumeranter.


### <a name="evaluating-capacity"></a>Utvärdering av kapacitet

Kapacitet och kostnader för att driva tjänsten går hand i hand. Nivåer sätter gränser för två nivåer: lagring och resurser. Du bör tänka på båda eftersom oavsett vilken gräns du når först är den effektiva gränsen.

Affärskrav dikterar vanligtvis antalet index du behöver. Du kan till exempel behöva ett globalt index för en stor databas med dokument. Eller så kan du behöva flera index baserat på region, program eller affärsnisch.

Om du vill bestämma storleken på ett index måste du [skapa ett](search-create-index-portal.md). Dess storlek baseras på importerade data och indexkonfiguration, till exempel om du aktiverar förslagslämnare, filtrering och sortering. Mer information om konfigurationspåverkan på storleken finns i [Skapa ett grundläggande index ](search-what-is-an-index.md).

För fulltextsökning är den primära datastrukturen en [inverterad indexstruktur](https://en.wikipedia.org/wiki/Inverted_index) som har andra egenskaper än källdata. För ett inverterat index bestäms storlek och komplexitet av innehåll, inte nödvändigtvis av mängden data som du matar in i det. En stor datakälla med hög redundans kan resultera i ett mindre index än en mindre datauppsättning som innehåller mycket varierande innehåll. Så det är sällan möjligt att sluta indexstorlek baserat på storleken på den ursprungliga datauppsättningen.

> [!NOTE] 
> Även om uppskattning av framtida behov av index och lagring kan kännas som gissningar, är det värt att göra. Om en nivås kapacitet visar sig vara för låg måste du etablera en ny tjänst på en högre nivå och sedan [ladda om indexen.](search-howto-reindex.md) Det finns ingen uppgradering på plats av en tjänst från en SKU till en annan.
>

### <a name="estimate-with-the-free-tier"></a>Uppskatta med den kostnadsfria nivån

En metod för att uppskatta kapacitet är att börja med den kostnadsfria nivån. Kom ihåg att den kostnadsfria tjänsten erbjuder upp till tre index, 50 MB lagringsutrymme och 2 minuters indexeringstid. Det kan vara svårt att uppskatta en beräknad indexstorlek med dessa begränsningar, men det här är stegen:

+ [Skapa en kostnadsfri tjänst](search-create-service-portal.md).
+ Förbered en liten, representativ datauppsättning.
+ [Skapa ett första index i portalen](search-create-index-portal.md) och notera dess storlek. Funktioner och attribut påverkar lagringen. Om du till exempel lägger till förslagsföremän (typeahead) ökar lagringsbehovet. Med samma datauppsättning kan du prova att skapa flera versioner av ett index, med olika attribut på varje fält, för att se hur lagringskraven varierar. Mer information finns [i "Lagringskonsekvenser" i Skapa ett grundläggande index](search-what-is-an-index.md#index-size).

Med en grov uppskattning i handen kan du fördubbla det beloppet till budget för två index (utveckling och produktion) och sedan välja din nivå därefter.

### <a name="estimate-with-a-billable-tier"></a>Uppskatta med en fakturerbar nivå

Dedikerade resurser kan hantera större samplings- och bearbetningstider för mer realistiska uppskattningar av indexkvantitet, storlek och frågevolymer under utveckling. Vissa kunder hoppar rakt in med en fakturerbar nivå och utvärderar sedan om när utvecklingsprojektet mognar.

1. [Granska tjänstgränser på varje nivå](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) för att avgöra om lägre nivåer kan stödja antalet index du behöver. Indexgränserna för nivåerna Basic, S1 och S2 är 15, 50 respektive 200. Den lagringsoptimerade nivån har en gräns på 10 index eftersom den är utformad för att stödja ett lågt antal mycket stora index.

1. [Skapa en tjänst på en fakturerbar nivå:](search-create-service-portal.md)

    + Börja lågt, på Basic eller S1, om du inte är säker på den planerade belastningen.
    + Börja högt, på S2 eller till och med S3, om du vet att du kommer att ha storskalig indexering och frågebelastningar.
    + Börja med Lagringsoptimerad, vid L1 eller L2, om du indexerar en stor mängd data och frågebelastningen är relativt låg, som med ett internt affärsprogram.

1. [Skapa ett första index](search-create-index-portal.md) för att avgöra hur källdata översätts till ett index. Detta är det enda sättet att uppskatta indexstorlek.

1. [Övervaka lagring, tjänstgränser, frågevolym och svarstid](search-monitor-usage.md) i portalen. Portalen visar frågor per sekund, begränsade frågor och sökfördröjning. Alla dessa värden kan hjälpa dig att avgöra om du har valt rätt nivå. 

Indexnummer och storlek är lika viktiga för din analys. Detta beror på att maximala gränser nås genom fullt utnyttjande av lagring (partitioner) eller av högsta gränser för resurser (index, indexerare och så vidare), beroende på vilket som inträffar först. Portalen hjälper dig att hålla reda på båda, visar aktuell användning och maximala gränser sida vid sida på sidan Översikt.

> [!NOTE]
> Lagringskraven kan blåsas upp om dokument innehåller främmande data. I idealfallet innehåller dokument bara de data som du behöver för sökupplevelsen. Binära data är inte sökbara och bör lagras separat (kanske i en Azure-tabell eller blob-lagring). Ett fält ska sedan läggas till i indexet för att innehålla en URL-referens till externa data. Den maximala storleken på ett enskilt dokument är 16 MB (eller mindre om du massöverladdningar av flera dokument i en begäran). Mer information finns [i Tjänstgränser i Azure Cognitive Search](search-limits-quotas-capacity.md).
>

**Frågevolymöverväganden**

Frågor per sekund (QPS) är ett viktigt mått under prestandajustering, men det är i allmänhet bara en nivåövervägande om du förväntar dig hög frågevolym från början.

Standardnivåerna kan ge en balans mellan repliker och partitioner. Du kan öka frågevändningen genom att lägga till repliker för belastningsutjämning eller lägga till partitioner för parallell bearbetning. Du kan sedan ställa in för prestanda när tjänsten har etablerats.

Om du förväntar dig stora ihållande frågevolymer från början bör du överväga högre standardnivåer, som backas upp av mer kraftfull maskinvara. Du kan sedan koppla från partitioner och repliker, eller till och med växla till en tjänst på lägre nivå, om dessa frågevolymer inte inträffar. Mer information om hur du beräknar frågedataflöde finns i [Azure Cognitive Search prestanda och optimering](search-performance-optimization.md).

Lagringsoptimerade nivåer är användbara för stora dataarbetsbelastningar, vilket stöder mer övergripande tillgängligt indexlagring för när frågefördröjningskrav är mindre viktiga. Du bör fortfarande använda ytterligare repliker för belastningsutjämning och ytterligare partitioner för parallell bearbetning. Du kan sedan ställa in för prestanda när tjänsten har etablerats.

**Avtal på servicenivå**

Funktionerna för kostnadsfri nivå och förhandsversion tillhandahåller inte [servicenivåavtal (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). För alla fakturerbara nivåer börjar SLA:er gälla när du etablerar tillräcklig redundans för din tjänst. Du måste ha två eller flera repliker för frågeslalor (läs). Du måste ha tre eller flera repliker för fråge- och indexeringsslalaord (läs-skriv). Antalet partitioner påverkar inte SLA.

## <a name="tips-for-tier-evaluation"></a>Tips för nivåutvärdering

+ Tillåt att mått bygger runt frågor och samlar in data kring användningsmönster (frågor under kontorstid, indexering under lågtrafik). Använd dessa data för att informera beslut om tillhandahållande av tjänster. Även om det inte är praktiskt vid en timme eller daglig kadens, kan du dynamiskt justera partitioner och resurser för att hantera planerade ändringar i frågevolymer. Du kan också hantera oplanerade men ihållande ändringar om nivåerna håller tillräckligt länge för att motivera att åtgärder vidtas.

+ Kom ihåg att den enda nackdelen med underetablering är att du kanske måste riva en tjänst om de faktiska kraven är större än dina förutsägelser. För att undvika avbrott i tjänsten skapar du en ny tjänst på en högre nivå och kör den sida vid sida tills alla appar och begäranden är inriktade på den nya slutpunkten.

## <a name="next-steps"></a>Nästa steg

Börja med en kostnadsfri nivå och skapa ett första index genom att använda en delmängd av dina data för att förstå dess egenskaper. Datastrukturen i Azure Cognitive Search är en inverterad indexstruktur. Storleken och komplexiteten hos ett inverterat index bestäms av innehållet. Kom ihåg att mycket överflödigt innehåll tenderar att resultera i ett mindre index än mycket oregelbundet innehåll. Så innehållsegenskaper snarare än storleken på datauppsättningen bestämmer indexlagringskraven.

När du har en första uppskattning av indexstorleken [etablerar du en fakturerbar tjänst](search-create-service-portal.md) på en av de nivåer som beskrivs i den här artikeln: Basic, Standard eller Storage Optimized. Ta bort eventuella artificiella begränsningar för datastorlek och [återskapa ditt index](search-howto-reindex.md) så att det innehåller alla data som du vill ska vara sökbara.

[Allokera partitioner och repliker](search-capacity-planning.md) efter behov för att få den prestanda och skala du behöver.

Om prestanda och kapacitet är bra, är du klar. Annars återskapar du en söktjänst på en annan nivå som ligger närmare dina behov.

> [!NOTE]
> Om du har frågor kan du publicera [på StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) eller [kontakta Azure-supporten](https://azure.microsoft.com/support/options/).
