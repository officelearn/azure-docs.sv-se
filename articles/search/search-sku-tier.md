---
title: Välj en pris nivå
titleSuffix: Azure Cognitive Search
description: 'Azure Kognitiv sökning kan tillhandahållas i dessa nivåer: kostnads fri, Basic och standard, och standard är tillgängligt i olika datorkonfigurationer och kapacitets nivåer.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: 3f2cbd7afe206866ae4d5b7c0925c8f3be9ab785
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96558818"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Välj en pris nivå för Azure Kognitiv sökning

När du [skapar en Sök tjänst](search-create-service-portal.md)väljer du en pris nivå som har åtgärd ATS för tjänstens livs längd. Nivån som du väljer avgör:

+ Antal index och andra objekt (max gränser)
+ Storlek och hastighet för partitioner (fysisk lagring)
+ Fakturerbart pris, en fast månads kostnad, men även en stegvis kostnad om du lägger till partitioner eller repliker

Dessutom finns det några [Premium funktioner](#premium-features) med nivå krav.

## <a name="tier-descriptions"></a>Nivå beskrivningar

Nivåerna är **kostnads fria**, **Basic**, **standard** och **Storage-optimerade**. Standard-och lagrings optimering är tillgänglig med flera konfigurationer och kapaciteter.

Följande skärm bild från Azure Portal visar tillgängliga nivåer, minus priser (som du hittar i portalen och på [prissättnings sidan](https://azure.microsoft.com/pricing/details/search/). 

![Pris nivåer för Azure Kognitiv sökning](media/search-sku-tier/tiers.png "Pris nivåer för Azure Kognitiv sökning")

**Kostnads fri** skapar en begränsad Sök tjänst för mindre projekt, som att köra självstudier och kod exempel. Internt delas repliker och partitioner mellan flera prenumeranter. Du kan inte skala en kostnads fri tjänst eller köra betydande arbets belastningar.

**Basic** och **standard** är de vanligaste fakturerbara nivåerna, med **standard standarden** . Med dedikerade resurser under kontrollen kan du distribuera större projekt, optimera prestanda och öka kapaciteten.

Vissa nivåer är optimerade för vissa typer av arbete. Till exempel är **standard 3 hög densitet (S3 HD)** ett *värd läge* för S3, där den underliggande maskin varan är optimerad för ett stort antal mindre index och är avsedd för flera organisationers scenarier. S3 HD har samma kostnad per enhet som S3, men maskin varan är optimerad för snabba fil läsningar på ett stort antal mindre index.

**Lagrings optimerings** nivåer ger större lagrings kapacitet till ett lägre pris per TB än standard nivåerna. Den främsta kompromissen är högre svars tid för frågor, som du bör validera för dina specifika program krav. Mer information om prestanda överväganden för den här nivån finns i [överväganden för prestanda och optimering](search-performance-optimization.md).

Du hittar mer information om olika nivåer på [sidan prissättning](https://azure.microsoft.com/pricing/details/search/)i avsnittet [begränsningar för tjänsten i Azure kognitiv sökning](search-limits-quotas-capacity.md) -artikeln och på Portal sidan när du skapar en tjänst.

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>Funktions tillgänglighet per nivå

I följande tabell beskrivs nivå-relaterade funktions begränsningar.

| Funktion | Begränsningar |
|---------|-------------|
| [indexerare](search-indexer-overview.md) | Indexerare är inte tillgängliga på S3 HD.  |
| [AI-berikning](search-security-manage-encryption-keys.md) | Körs på den kostnads fria nivån men rekommenderas inte. |
| [Kundhanterade krypterings nycklar](search-security-manage-encryption-keys.md) | Inte tillgängligt på den kostnads fria nivån. |
| [Åtkomst till IP-brandvägg](service-configure-firewall.md) | Inte tillgängligt på den kostnads fria nivån. |
| [Privat slut punkt (integrering med Azures privata länk)](service-create-private-endpoint.md) | För inkommande anslutningar till en search-tjänst är inte tillgänglig på den kostnads fria nivån. För utgående anslutningar från indexerare till andra Azure-resurser är de inte tillgängliga på kostnads fria eller S3 HD. För indexerare som använder färdighetsuppsättningar, inte tillgängligt på kostnads fri, grundläggande, S1 eller S3 HD.|

De flesta funktioner är tillgängliga på alla nivåer, inklusive kostnads fria, men resurs intensiva funktioner kanske inte fungerar bra om du inte ger den tillräckligt med kapacitet. [AI-anrikning](cognitive-search-concept-intro.md) har till exempel långvariga färdigheter som tar slut på en kostnads fri tjänst, om inte data uppsättningen är liten.

## <a name="billable-events"></a>Fakturerbara händelser

En lösning som bygger på Azure Kognitiv sökning kan ådra sig kostnaderna på följande sätt:

+ [Kostnaden för själva tjänsten](#service-costs) , som körs dygnet runt, med minsta konfiguration (en partition och replik), enligt bas priset

+ Lägga till kapacitet (repliker eller partitioner), där kostnader ökar vid ökningar av fakturerings takten

+ Bandbredds avgifter (utgående data överföring)

+ Tilläggs tjänster som krävs för vissa funktioner eller funktioner:

  + AI-anrikning (kräver [Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/))
  + kunskaps lager (kräver [Azure Storage](https://azure.microsoft.com/pricing/details/storage/))
  + stegvis anrikning (kräver [Azure Storage](https://azure.microsoft.com/pricing/details/storage/), gäller AI-anrikning)
  + Kundhanterade nycklar och Double Encryption (kräver [Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/))
  + privata slut punkter för en modell utan Internet åtkomst (kräver [Azures privata länk](https://azure.microsoft.com/pricing/details/private-link/))

### <a name="service-costs"></a>Tjänste kostnader

Till skillnad från virtuella datorer eller andra resurser som kan vara "pausade" för att undvika avgifter är en Azure Kognitiv sökning-tjänst alltid tillgänglig på maskin vara som är dedikerad för exklusiv användning. Därför är att skapa en tjänst en fakturerbar händelse som startar när du skapar tjänsten och slutar när du tar bort tjänsten. 

Den lägsta avgiften är den första Sök enheten (en partition med en enda replik x) till fakturerings takten. Detta minimum är fast för tjänstens livs längd eftersom tjänsten inte kan köras på något som är mindre än den här konfigurationen. Utöver det lägsta antalet kan du lägga till repliker och partitioner oberoende av varandra. Stegvisa ökningar i kapaciteten via repliker och partitioner ökar din faktura utifrån följande formel: [(repliker x partitioner x hastighet)](#search-units), där den hastighet du debiteras beror på vilken pris nivå du väljer.

När du uppskattar kostnaden för en Sök lösning bör du tänka på att priserna och kapaciteten inte är linjära. (Dubbla kapaciteten är mer än dubbelt så mycket som kostnaden.) Ett exempel på hur formeln fungerar finns i [så här allokerar du repliker och partitioner](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

### <a name="bandwidth-charges"></a>Bandbredds avgifter

Användningen av [indexerare](search-indexer-overview.md) kan påverka faktureringen, beroende på var tjänsterna finns. Du kan ta bort avgifter för utgående data helt och hållet om du skapar Azure Kognitiv sökning-tjänsten i samma region som dina data. Här är lite information från [prissättnings sidan för bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/):

+ Microsoft debiterar inte några inkommande data till någon tjänst i Azure, eller för utgående data från Azure Kognitiv sökning.
+ I lösningar med flera tjänster är det ingen avgift för data som passerar kabeln när alla tjänster är i samma region.

Avgifterna gäller för utgående data om tjänsterna är i olika regioner. Dessa avgifter ingår inte i din Azure Kognitiv sökning-faktura. De anges här eftersom om du använder data eller AI-berikade indexerare för att hämta data från olika regioner, ser du kostnader som återspeglas i din övergripande faktura.

### <a name="ai-enrichment-with-cognitive-services"></a>AI-anrikning med Cognitive Services

För [AI-anrikning](cognitive-search-concept-intro.md)bör du planera att [koppla en fakturerbar Azure Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md), i samma region som Azure kognitiv sökning, på pris nivån S0 för att betala per användning. Det finns ingen fast kostnad för att bifoga Cognitive Services. Du betalar bara för den bearbetning du behöver.

| Åtgärd | Fakturerings påverkan |
|-----------|----------------|
| Dokument sprickor, text extrahering | Kostnadsfri |
| Dokument sprickor, avbildnings extrahering | Faktureras enligt antalet bilder som har extraherats från dina dokument. I en [indexerare-konfiguration](/rest/api/searchservice/create-indexer#indexer-parameters)är **imageAction** den parameter som utlöser avbildnings extrahering. Om **imageAction** är inställt på "ingen" (standard) debiteras du inte för avbildnings extrahering. Hastigheten för avbildnings extrahering dokumenteras på sidan med [pris information](https://azure.microsoft.com/pricing/details/search/) för Azure kognitiv sökning.|
| [Inbyggda kognitiva kompetenser](cognitive-search-predefined-skills.md) | Faktureras enligt samma takt som om du har utfört uppgiften genom att använda Cognitive Services direkt. |
| Anpassade färdigheter | En anpassad färdighet är funktioner som du anger. Kostnaden för att använda en anpassad färdighet beror helt på om anpassad kod anropar andra avgiftsbelagda tjänster. |

Med funktionen för [förhands granskning (för hands version)](cognitive-search-incremental-indexing-conceptual.md) kan du tillhandahålla ett cacheminne som gör det möjligt för indexeraren att vara mer effektivt när du bara kör de kognitiva färdigheter som behövs om du ändrar din färdigheter i framtiden, vilket sparar tid och pengar.

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>Fakturerings formel (R x P = SU)

Det viktigaste fakturerings konceptet att förstå för Azure Kognitiv sökning-åtgärder är *Sök enheten* (SU). Eftersom Azure Kognitiv sökning är beroende av både repliker och partitioner för indexering och frågor, är det inte bra att fakturera med bara en eller ett annat. Faktureringen baseras i stället på en sammansatt av båda.

SU är produkten av de *repliker* och *partitioner* som används av en tjänst: **(R x P = SU)**.

Varje tjänst börjar med en SU (en replik multiplicerad med en partition) som minimum. Det maximala antalet för en tjänst är 36 SUs. Det här max värdet kan nås på flera sätt: 6 partitioner x 6 repliker eller 3 partitioner x 12 repliker, till exempel. Det är vanligt att använda mindre än den totala kapaciteten (t. ex. en 3-replikering, en 3-partitions tjänst som faktureras som 9 SUs). Se diagrammet [partition och replik kombinations](search-capacity-planning.md#chart) diagram för giltiga kombinationer.

Fakturerings takten är per timme per SU. Varje nivå har en progressivt högre hastighet. Högre nivåer levereras med större och speedier partitioner, och detta bidrar till en högre pris per timme för den nivån. Du kan visa priserna för varje nivå på [pris informations](https://azure.microsoft.com/pricing/details/search/) sidan.

De flesta kunder tar bara en del av den totala kapaciteten online, där resten av reserven behålls. För fakturering bestämmer antalet partitioner och repliker som du ansluter online, vilket avgör vad du betalar per timme.

## <a name="how-to-manage-costs"></a>Så här hanterar du kostnader

Följande förslag kan hjälpa dig att sänka kostnaderna eller hantera kostnader mer effektivt:

+ Skapa alla resurser i samma region, eller i så få regioner som möjligt, för att minimera eller eliminera bandbredds kostnader.

+ Konsolidera alla tjänster till en resurs grupp, till exempel Azure Kognitiv sökning, Cognitive Services och andra Azure-tjänster som används i din lösning. I Azure Portal letar du reda på resurs gruppen och använder **Cost Management** kommandon för att få inblick i faktiska och planerade utgifter.

+ Överväg att använda Azure Web App för ditt klient program så att förfrågningar och svar stannar inom data centrets gränser.

+ Skala upp för resurs krävande åtgärder som indexering och justera sedan nedåt för vanliga frågor och arbets belastningar. Börja med den lägsta konfigurationen för Azure Kognitiv sökning (en SU som består av en partition och en replik) och övervaka sedan användar aktivitet för att identifiera användnings mönster som indikerar ett behov av mer kapacitet. Om det finns ett förutsägbart mönster kanske du kan synkronisera skalning med aktivitet (du måste skriva kod för att automatisera detta).

Du kan också besöka [fakturerings-och kostnads hantering](../cost-management-billing/cost-management-billing-overview.md) för inbyggda verktyg och funktioner som är relaterade till utgifter.

Det går inte att stänga av en Sök tjänst temporärt. Dedikerade resurser är alltid operativa, allokeras för exklusiv användning för din tjänsts livs längd. Att ta bort en tjänst är permanent och tar också bort tillhör ande data.

Vad gäller själva tjänsten är det enda sättet att sänka din faktura till att minska antalet repliker och partitioner till en nivå som fortfarande ger acceptabel prestanda och [SLA-efterlevnad](https://azure.microsoft.com/support/legal/sla/search/v1_0/), eller skapa en tjänst på en lägre nivå (S1 Tim pris är lägre än S2 eller S3-priser). Förutsatt att du etablerar tjänsten i det nedre slutet av dina belastnings prognoser, kan du skapa en andra större tjänst, återskapa dina index på den andra tjänsten och sedan ta bort den första.

## <a name="how-to-evaluate-capacity-requirements"></a>Utvärdera kapacitets kraven

I Azure Kognitiv sökning struktureras kapaciteten som *repliker* och *partitioner*.

+ Repliker är instanser av Sök tjänsten. Varje replik är värd för en belastningsutjämnad kopia av ett index. Till exempel innehåller en tjänst med sex repliker sex kopior av varje index som har lästs in i tjänsten.

+ Partitioner lagrar index och automatiskt delar sökbara data. Två partitioner delar ditt index på hälften, tre partitioner delar upp dem i tredje, och så vidare. Med avseende på kapaciteten är *partitionsstorleken* den primära differentierings funktionen mellan nivåer.

> [!NOTE]
> Alla optimerade nivåer för standard och lagring stöder [flexibla kombinationer av repliker och partitioner](search-capacity-planning.md#chart) , så att du kan [optimera systemet för hastighet eller lagring](search-performance-optimization.md) genom att ändra saldot. Basic-nivån erbjuder upp till tre repliker för hög tillgänglighet men har bara en partition. Kostnads fria nivåer ger inte dedikerade resurser: data bearbetnings resurser delas av flera prenumeranter.

### <a name="evaluating-capacity"></a>Utvärderar kapacitet

Kapacitet och kostnaderna för att köra tjänsten finns i handen. Nivåerna begränsar gränserna på två nivåer: lagring och innehåll (antal index, till exempel). Du bör tänka på båda eftersom den gräns du uppnår först är den effektiva gränsen.

Företags krav avgör vanligt vis hur många index du behöver. Du kan till exempel behöva ett globalt index för en stor lagrings plats för dokument. Eller så kanske du behöver flera index baserade på region, program eller affärs nischmarknader.

Om du vill fastställa storleken på ett index måste du [bygga ett](search-what-is-an-index.md). Storleken kommer att baseras på importerade data och index konfiguration, till exempel om du aktiverar förslag, filtrering och sortering.

För full texts ökning är den primära data strukturen en [inverterad index](https://en.wikipedia.org/wiki/Inverted_index) struktur som har andra egenskaper än källdata. För ett inverterat index bestäms storlek och komplexitet av innehåll, inte nödvändigt vis av mängden data som du matar in i det. En stor data källa med hög redundans kan resultera i ett mindre index än en mindre data mängd som innehåller mycket varierande innehåll. Det går sällan att härleda index storleken baserat på storleken på den ursprungliga data uppsättningen.

> [!NOTE] 
> Även om det går att uppskatta framtida behov för index och lagring kan det vara värt att göra. Om en nivås kapacitet blir för låg måste du etablera en ny tjänst på en högre nivå och sedan [läsa in indexen](search-howto-reindex.md)på nytt. Det finns ingen uppgradering på plats av en tjänst från en nivå till en annan.
>

### <a name="estimate-with-the-free-tier"></a>Uppskatta med den kostnads fria nivån

En metod för att uppskatta kapaciteten är att börja med den kostnads fria nivån. Kom ihåg att den kostnads fria tjänsten erbjuder upp till tre index, 50 MB lagrings utrymme och 2 minuters indexerings tid. Det kan vara svårt att uppskatta en beräknad index storlek med de här begränsningarna, men de är stegen:

+ [Skapa en kostnads fri tjänst](search-create-service-portal.md).
+ Förbered en liten, representativ data uppsättning.
+ [Bygg ett första index i portalen](search-get-started-portal.md) och anteckna dess storlek. Funktioner och attribut påverkar lagringen. Om du till exempel lägger till förslags ställare (sökning efter fråga) ökar lagrings kraven. Med samma data uppsättning kan du prova att skapa flera versioner av ett index, med olika attribut i varje fält, för att se hur lagrings kraven varierar. Mer information finns [i "lagrings konsekvenser" i skapa ett grundläggande index](search-what-is-an-index.md#index-size).

Med en grov uppskattning i handen kan du dubblera beloppet till budget för två index (utveckling och produktion) och sedan välja din nivå.

### <a name="estimate-with-a-billable-tier"></a>Beräkna med en fakturerbar nivå

Dedikerade resurser kan hantera större samplings-och bearbetnings tider för mer realistiska uppskattningar av index antal, storlek och fråga-volymer under utvecklingen. Vissa kunder går direkt till med en fakturerbar nivå och sedan utvärderas igen som utvecklingsprojekt för projektet.

1. [Granska tjänst begränsningar på varje nivå](./search-limits-quotas-capacity.md#index-limits) för att avgöra om lägre nivåer kan stödja det antal index du behöver. På nivåerna Basic, S1 och S2 är index gränserna 15, 50 respektive 200. Lagrings optimerings nivån har en gräns på 10 index eftersom den har utformats för att stödja ett lågt antal mycket stora index.

1. [Skapa en tjänst på en fakturerbar nivå](search-create-service-portal.md):

    + Starta låg, med Basic eller S1, om du inte är säker på den projekterade belastningen.
    + Starta hög, på S2 eller till och med S3 om du vet att du kommer att ha storskalig indexering och läsa in frågor.
    + Börja med den optimerade lagringen, vid L1 eller L2, om du har indexerat en stor mängd data och frågan är relativt låg, precis som med ett internt affärs program.

1. [Bygg ett första index](search-what-is-an-index.md) för att avgöra hur källdata översätts till ett index. Detta är det enda sättet att uppskatta index storleken.

1. [Övervaka lagring, tjänst gränser, fråga volym och svars tid](search-monitor-usage.md) i portalen. Portalen visar frågor per sekund, begränsade frågor och Sök svars tid. Alla dessa värden kan hjälpa dig att avgöra om du har valt rätt nivå. 

Indexets nummer och storlek är lika viktiga för din analys. Detta beror på att max gränserna uppnås genom fullständig användning av lagrings utrymme (partitioner) eller av maximala gränser för resurser (index, indexerare och så vidare), beroende på vilket som kommer först. Portalen hjälper dig att hålla koll på båda, som visar aktuella användnings-och Max gränser sida vid sida på översikts sidan.

> [!NOTE]
> Lagrings kraven kan vara deflata om dokumenten innehåller ovidkommande data. Vi rekommenderar att dokument endast innehåller de data som du behöver för Sök funktionen. Binära data är inte sökbara och bör lagras separat (kanske i en Azure-tabell eller Blob-lagring). Ett fält ska sedan läggas till i indexet för att innehålla en URL-referens till externa data. Den maximala storleken för ett enskilt dokument är 16 MB (eller mindre om du överför flera dokument i en begäran). Mer information finns i [tjänst begränsningar i Azure kognitiv sökning](search-limits-quotas-capacity.md).
>

**Fråga om volym överväganden**

Frågor per sekund (frågor per sekund) är ett viktigt mått under prestanda justeringen, men det är vanligt vis bara en nivå som beaktas om du förväntar dig hög fråga-volym vid början.

Standard-nivåerna kan ge en balans mellan repliker och partitioner. Du kan öka leveransen genom att lägga till repliker för belastnings utjämning eller lägga till partitioner för parallell bearbetning. Du kan sedan justera efter prestanda när tjänsten har tillhandahållits.

Om du förväntar dig hög varaktiga frågegrupper från början bör du överväga högre standard nivåer, med mer kraftfulla maskin vara. Du kan sedan ta partitioner och repliker offline, eller till och med växla till en tjänst på lägre nivå, om dessa fråga-volymer inte inträffar. Mer information om hur du beräknar frågans data flöde finns i [Azure kognitiv sökning prestanda och optimering](search-performance-optimization.md).

De optimerade lagrings nivåerna är användbara för stora data arbets belastningar och stöder mer övergripande tillgängligt index lagring för när svars tids fördröjnings kraven är mindre viktiga. Du bör fortfarande använda ytterligare repliker för belastnings utjämning och ytterligare partitioner för parallell bearbetning. Du kan sedan justera efter prestanda när tjänsten har tillhandahållits.

**Service nivå avtal**

Den kostnads fria nivån och för hands versions funktionerna tillhandahåller inte [service nivå avtal (service avtal)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). För alla fakturerbara nivåer börjar service avtal gälla när du etablerar tillräckligt med redundans för din tjänst. Du måste ha två eller flera repliker för fråga (Läs) service avtal. Du måste ha tre eller fler repliker för fråge-och indexerings-service avtal (Läs-och Skriv behörighet). Antalet partitioner påverkar inte service avtal.

## <a name="tips-for-tier-evaluation"></a>Tips för nivå utvärdering

+ Tillåt att mått skapas runt frågor och samla in data i användnings mönster (frågor under kontors tid, indexering under låg belastnings tider). Använd dessa data för att informera om tjänste etablerings beslut. Även om det inte är praktiskt vid en tim-eller daglig takt, kan du dynamiskt justera partitioner och resurser för att hantera planerade ändringar i frågornas volymer. Du kan också hantera oplanerad men varaktiga ändringar om nivåerna är tillräckligt långa för att motivera att vidta åtgärder.

+ Kom ihåg att den enda nack delen med etablering är att du kan behöva riva av en tjänst om de faktiska kraven är större än dina förutsägelser. För att undvika avbrott i tjänsten skapar du en ny tjänst på en högre nivå och kör den sida vid sida tills alla appar och begär Anden riktar sig mot den nya slut punkten.

## <a name="next-steps"></a>Nästa steg

Börja med en kostnads fri nivå och skapa ett första index genom att använda en delmängd av dina data för att förstå dess egenskaper. Data strukturen i Azure Kognitiv sökning är en inverterad index struktur. Storleken och komplexiteten för ett inverterat index avgörs av innehållet. Kom ihåg att mycket redundant innehåll leder till ett mindre index än innehåll som är mycket oregelbundet. Egenskaper för innehåll i stället för data uppsättningens storlek avgör index lagrings kraven.

När du har en första uppskattning av index storleken kan du [etablera en fakturerings bar tjänst](search-create-service-portal.md) på en av de nivåer som beskrivs i den här artikeln: Basic, standard eller Storage optimerad. Minska eventuella artificiella begränsningar för data storlek och [återskapa indexet](search-howto-reindex.md) så att det innehåller alla data som du vill ska vara sökbara.

[Allokera partitioner och repliker](search-capacity-planning.md) efter behov för att få den prestanda och skalning du behöver.

Om prestanda och kapacitet är bra är du klar. Annars återskapar du en Sök tjänst på en annan nivå som passar bäst för dina behov.

> [!NOTE]
> Om du har frågor kan du publicera på [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) eller [kontakta Azure-supporten](https://azure.microsoft.com/support/options/).