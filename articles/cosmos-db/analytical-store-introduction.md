---
title: Vad är Azure Cosmos DB Analytical Store (för hands version)?
description: Lär dig mer om Azure Cosmos DB transaktionell (rad-och kolumnbaserade) och analys (kolumnbaserade). Fördelar med analytisk lagring, prestanda påverkan för storskaliga arbets belastningar och automatisk synkronisering av data från transaktions lagring till analytisk lagring
author: Rodrigossz
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: rosouz
ms.openlocfilehash: b3d1371f486a73b40d352007e3681fd451a8a8b7
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815835"
---
# <a name="what-is-azure-cosmos-db-analytical-store-preview"></a>Vad är Azure Cosmos DB Analytical Store (för hands version)?

> [!IMPORTANT]
> Azure Cosmos DB Analytical Store är för närvarande en för hands version. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Mer information finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna.

Azure Cosmos DB Analytical Store är ett fullständigt isolerat kolumn lager för att möjliggöra storskalig analys av drift data i din Azure Cosmos DB, utan att påverka dina transaktions arbets belastningar.  

## <a name="challenges-with-large-scale-analytics-on-operational-data"></a>Utmaningar med storskalig analys av drift data

Användnings data för flera modeller i en Azure Cosmos DB-behållare lagras internt i en indexerad rad baserad "transaktions lagring". Rad lagrings formatet är utformat för att tillåta snabba och skrivskyddade transaktions läsningar och skriv åtgärder i svars tiderna för svars tid i millisekunder och operativa frågor. Om din data uppsättning ökar stora kan komplexa analytiska frågor vara dyra när det gäller data flöde för data som lagras i det här formatet. Hög konsumtion av etablerade data flöden i sin tur påverkar prestanda för transaktions arbets belastningar som används av dina program och tjänster i real tid.

Traditionellt, för att analysera stora mängder data, extraheras drift data från Azure Cosmos DB transaktions lager och lagras i ett separat data lager. Data lagras till exempel i ett data lager eller data Lake i ett lämpligt format. Dessa data används senare för storskalig analys och analyseras med hjälp av beräknings motorn, till exempel Apache Spark kluster. Den här separationen av analys lagrings-och beräknings lager från användnings data resulterar i ytterligare svars tider, eftersom ETL-pipelinen (Extract, Transform, Load) körs mindre ofta för att minimera potentiell påverkan på dina transaktions arbets belastningar.

ETL-pipelinen blir också komplexa vid hantering av uppdateringar av användnings data jämfört med hantering av enbart nyligen inmatade drift data. 

## <a name="column-oriented-analytical-store"></a>Kolumn-orienterad analys lager

Azure Cosmos DB Analytical Store tar itu med de komplexitets-och latens utmaningar som inträffar med de traditionella ETL-pipelinen. Azure Cosmos DB Analytical Store kan automatiskt synkronisera dina drift data till ett separat kolumn lager. Formatet för kolumn lagring är lämpligt för analys frågor med stor skala som ska utföras på ett optimerat sätt, vilket leder till att förbättra svars tiderna för sådana frågor.

Med Azure Synapse-länken kan du nu skapa inga ETL HTAP-lösningar genom att länka direkt till Azure Cosmos DB analys lager från Synapse Analytics. Med den kan du köra storskalig storskalig analys i real tid på dina användnings data.

## <a name="analytical-store-details"></a>Information om analys lager

När du aktiverar analytisk lagring på en Azure Cosmos DB behållare skapas en ny kolumn lagring internt baserat på drift data i din behållare. Det här kolumn arkivet är bestående separat från det radbaserade transaktions arkivet för den behållaren. Infogningar, uppdateringar och borttagningar i dina användnings data synkroniseras automatiskt till analytisk lagring. Du behöver inte ändra feed eller ETL för att synkronisera data.

### <a name="column-store-for-analytical-workloads-on-operational-data"></a>Kolumn lagring för analytiska arbets belastningar på operativa data

Analytiska arbets belastningar inkluderar vanligt vis agg regeringar och sekventiella genomsökningar av valda fält. Genom att lagra data i en kolumn – huvud ordning kan du med analys lagret en grupp värden för varje fält serialiseras tillsammans. Det här formatet minskar de IOPS som krävs för att skanna eller beräkna statistik över vissa fält. Det förbättrar kraftigt svars tiderna för genomsökningar över stora data mängder. 

Om dina operativa tabeller till exempel är i följande format:

:::image type="content" source="./media/analytical-store-introduction/sample-operational-data-table.png" alt-text="Exempel på drift tabell" border="false":::

Rad arkivet behåller ovanstående data i ett serialiserat format, per rad, på disken. Det här formatet möjliggör snabbare transaktionella läsningar, skrivningar och operativa frågor, till exempel "returnera information om Product1". Men eftersom data mängden växer stor och om du vill köra komplexa analys frågor på data kan det vara dyrt. Om du till exempel vill hämta "försäljnings trender för en produkt under kategorin" utrustning "i olika affär senheter och månader" måste du köra en komplex fråga. Stora genomsökningar i den här data uppsättningen kan bli dyra när det gäller data flöde och kan också påverka prestandan för transaktions arbets belastningar som påverkar dina real tids program och-tjänster.

Analys lager, som är en kolumn lagring, passar bättre för sådana frågor eftersom det serialiserar likartade data fält tillsammans och minskar disken IOPS.

Följande bild visar transaktions rad lagring jämfört med analytisk kolumn lagring i Azure Cosmos DB:

:::image type="content" source="./media/analytical-store-introduction/transactional-analytical-data-stores.png" alt-text="Transaktions rad lagring vs analytisk kolumn lagring i Azure Cosmos DB" border="false":::

### <a name="decoupled-performance-for-analytical-workloads"></a>Frikopplad prestanda för analytiska arbets belastningar

Det påverkar inte prestandan hos dina transaktions arbets belastningar på grund av analytiska frågor, eftersom analys lagret är skilt från transaktions arkivet.  Ett analys lager behöver inte separera ru: er-enheter för att allokeras.

### <a name="auto-sync"></a>Automatisk synkronisering

Automatisk synkronisering syftar på den fullständigt hanterade funktionen i Azure Cosmos DB där infogningar, uppdateringar och borttagningar av drifts data automatiskt synkroniseras från transaktions lagring till analytisk lagring i nära real tid inom 5 minuter.

Funktionen för automatisk synkronisering tillsammans med analytiskt lager ger följande viktiga fördelar:

#### <a name="scalability--elasticity"></a>Skalbarhets & elastiskhet

Genom att använda vågrät partitionering kan Azure Cosmos DB transaktions lager elastiskt skala lagringen och data flödet utan avbrott. Horisontell partitionering i transaktions arkivet ger skalbarhet & elastiskhet i automatisk synkronisering för att säkerställa att data synkroniseras med analys lagret i nära real tid. Datasynkroniseringen sker oavsett transaktions trafik genom strömning, om det är 1000 åtgärder/SEK eller 1 000 000 åtgärder/SEK, och det inte påverkar det etablerade data flödet i transaktions arkivet. 

#### <a name="automatically-handle-schema-updates"></a><a id="analytical-schema"></a>Hantera schema uppdateringar automatiskt

Azure Cosmos DB transaktions lager är schema-oberoende, och det gör att du kan iterera i dina transaktions program utan att behöva hantera schema-eller index hantering. I motsats till detta är Azure Cosmos DB Analytical Store schematiserade för att optimera prestanda för analytiska frågor. Med funktionen för automatisk synkronisering hanterar Azure Cosmos DB schemats härledning över de senaste uppdateringarna från transaktions arkivet.  Den hanterar också schema representationen i den analytiska lagringen som ingår i den här typen av hantering av kapslade data typer.

Om det finns en schema utveckling där nya egenskaper läggs till över tid, visar analys arkivet automatiskt ett uppdelat schema över alla historiska scheman i transaktions arkivet.

Om alla drift data i Azure Cosmos DB följer ett väldefinierat schema för analys, härleds schemat automatiskt och visas korrekt i analys lagret. Om det väldefinierade schemat för analys, enligt definitionen nedan, överträds av vissa objekt, kommer de inte att tas med i analys lagret. Om du har blockerade scenarier på grund av ett väldefinierat schema för analys definition, e-posta [Azure Cosmos DB-teamet](mailto:cosmosdbsynapselink@microsoft.com).

Ett väldefinierat schema för analys definieras med följande överväganden:

* En egenskap har alltid samma typ över flera objekt

  * Har till exempel `{"a":123} {"a": "str"}` inte ett väldefinierat schema eftersom det `"a"` ibland är en sträng och ibland ett tal. 
  
    I det här fallet registrerar analys arkivet data typen `“a”` som data typen för `“a”` i det första objektet i behållarens livs längd. Objekt där data typen `“a”` skiljer sig, tas inte med i analys lagret.
  
    Det här villkoret gäller inte för null-egenskaper. Är till exempel `{"a":123} {"a":null}` fortfarande väl definierat.

* Mat ris typer måste innehålla en enda upprepad typ

  * Är till exempel `{"a": ["str",12]}` inte ett väldefinierat schema eftersom matrisen innehåller en blandning av heltals-och sträng typer

* Det finns högst 200 egenskaper på alla kapslings nivåer för ett schema och ett maximalt kapslings djup på 5

  * Ett objekt med 201 egenskaper på den översta nivån har inte ett väldefinierat schema.

  * Ett objekt med fler än fem kapslade nivåer i schemat har inte heller ett väldefinierat schema. Till exempel `{"level1": {"level2":{"level3":{"level4":{"level5":{"too many":12}}}}}}`

* Egenskaps namn är unika när de jämförs i ett skift läges okänsligt sätt

  * Följande objekt har till exempel inte ett väldefinierat schema `{"Name": "fred"} {"name": "john"}` – `"Name"` och `"name"` är samma vid jämförelse i ett skift läges okänsligt sätt

### <a name="cost-effective-archival-of-historical-data"></a>Kostnads effektiv arkivering av historiska data

Data skiktning avser separering av data mellan lagrings infrastrukturer som är optimerade för olika scenarier. Därigenom förbättra den övergripande prestandan och kostnads effektiviteten hos data stacken från slut punkt till slut punkt. Med analys lagring har Azure Cosmos DB nu stöd för automatisk data nivåer från transaktions lagringen till analytisk lagring med olika datalayouter. Med analys lagrings utrymme som är optimerat i förhållande till lagrings kostnaden jämfört med transaktions lagret, kan du behålla mycket längre horisonter av drift data för historisk analys.

När analys lagret har Aktiver ATS, baserat på data lagrings behoven för transaktions arbets belastningar, kan du konfigurera egenskapen transaktions Arkiv Time to Live (transaktions-TTL) så att poster tas bort automatiskt från transaktions arkivet efter en viss tids period. På samma sätt kan du med "analytiskt lagrings tid för Live (analytiskt TTL)" hantera livs cykeln för data som bevaras i analys lagret, oberoende av transaktions arkivet. Genom att aktivera analytisk lagring och konfigurera TTL-egenskaperna kan du smidigt och definiera data lagrings perioden för de två butikerna.

### <a name="global-distribution"></a>Global distribution

Om du har ett globalt distribuerat Azure Cosmos DB konto kommer det att vara tillgängligt i alla regioner i det kontot när du har aktiverat analytiskt lagrings utrymme för en behållare.  Ändringar i drift data replikeras globalt i alla regioner. Du kan köra analytiska frågor effektivt mot den närmaste regionala kopian av dina data i Azure Cosmos DB.

### <a name="security"></a>Säkerhet

Autentisering med analys lagret är detsamma som transaktions arkivet för en specifik databas. Du kan använda Master-eller skrivskyddade nycklar för autentisering. Du kan utnyttja den länkade tjänsten i Synapse Studio för att förhindra att Azure Cosmos DB nycklar klistras in i Spark-anteckningsbokarna. Åtkomst till den här länkade tjänsten är tillgänglig för alla som har åtkomst till arbets ytan.

### <a name="support-for-multiple-azure-synapse-analytics-runtimes"></a>Stöd för flera Azure Synapse Analytics-körningar

Analys lagret är optimerat för att ge skalbarhet, elastiskhet och prestanda för analytiska arbets belastningar utan något beroende på beräknings körnings tiden. Lagrings tekniken är själv hanterad för att optimera dina analys arbets belastningar utan manuella åtgärder.

Genom att frikoppla det analytiska lagrings systemet från analys beräknings systemet, kan data i Azure Cosmos DB analys lager frågas samtidigt från olika analys körningar som stöds av Azure Synapse Analytics. Från och med idag stöder Synapse Analytics Apache Spark och SQL Server utan Azure Cosmos DB analys lager.

> [!NOTE]
> Du kan bara läsa från analys lager med kör tid för Synapse analys. Du kan skriva tillbaka data till transaktions arkivet som ett betjänande lager.

## <a name="pricing"></a><a id="analytical-store-pricing"></a> Priset

Analytiskt lager följer en förbruknings pris modell där du debiteras för:

* Lagring: mängden data som behålls i analys lagret varje månad, inklusive historiska data som definieras av analys-TTL.

* Analytiska Skriv åtgärder: den fullständigt hanterade synkroniseringen av drift data uppdateringar till analys lagret från transaktions arkivet (automatisk synkronisering)

* Analytiska Läs åtgärder: Läs åtgärder som utförts mot analys lagret från Synapse Analytics Spark och SQL Server-körnings tider.

> [!NOTE]
> Azure Cosmos DB Analytical Store är för närvarande tillgängligt i en offentlig för hands version utan kostnad.

Priset för analytiskt lager skiljer sig från pris modellen för transaktions lager. Det finns inget koncept för etablerade ru: er i analys lagret. Se [Azure Cosmos DB prissättnings sida](https://azure.microsoft.com/pricing/details/cosmos-db/)för fullständig information om pris modellen för analys lagring.

För att få en kostnads uppskattning på hög nivå för att aktivera analys av en Azure Cosmos DB behållare kan du använda [Azure Cosmos DB kapacitets planering](https://cosmos.azure.com/capacitycalculator/) och få en uppskattning av kostnaderna för analytisk lagring och skriv åtgärder. Kostnaderna för analys av Läs åtgärder beror på de analytiska arbets belastnings egenskaperna, men som en uppskattning på hög nivå, resulterar det vanligt vis i 130 000 analytiska Läs åtgärder, och resultatet kostar $0,065.

## <a name="analytical-time-to-live-ttl"></a><a id="analytical-ttl"></a> Analytisk tid till Live (TTL)

TTL för analys anger hur länge data ska behållas i analysarkivet, för en container. 

Om analytiskt arkiv är aktiverat, kommer infogningar, uppdateringar och borttagningar av användnings data automatiskt att synkroniseras från transaktions lager till analytisk lagring, oavsett konfiguration av transaktions-TTL. Kvarhållning av dessa drift data i analys lagret kan styras av det analytiska TTL-värdet på behållar nivån, enligt vad som anges nedan:

Analytiskt TTL på en behållare anges med hjälp av `AnalyticalStoreTimeToLiveInSeconds` egenskapen:

* Om värdet är inställt på 0, saknas (eller är inställt på null): analys arkivet är inaktiverat och inga data replikeras från transaktions arkivet till ett analys lager

* Om det finns och värdet är inställt på "-1": analys arkivet behåller alla historiska data, oavsett lagring av data i transaktions arkivet. Den här inställningen anger att analys lagret har oändlig kvarhållning av dina användnings data

* Om detta är tillgängligt och värdet är inställt på ett positivt tal "n": objekt upphör att gälla från analys lagret "n" efter deras senaste ändrings tid i transaktions arkivet. Den här inställningen kan utnyttjas om du vill behålla dina användnings data under en begränsad tids period i analys lagret, oavsett lagring av data i transaktions arkivet

Några saker att tänka på:
*   När analys lagret har Aktiver ATS med ett analytiskt TTL-värde kan det uppdateras till ett annat giltigt värde senare. 
*   Även om transaktions-TTL kan anges på behållarens eller objekt nivån, kan analytiskt TTL endast anges på behållar nivån för närvarande.
*   Du kan få längre kvarhållning av dina drift data i analys lagret genom att ange analytiskt TTL->= transaktions-TTL på behållar nivån.
*   Analys lagret kan göras för att spegla transaktions lagret genom att ställa in analys-TTL = transaktions-TTL.

Mer information finns i [så här konfigurerar du analytiskt TTL-värde på en behållare](configure-synapse-link.md#create-analytical-ttl).

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande dokument:

* [Azure Synapse-länk för Azure Cosmos DB](synapse-link.md)

* [Kom igång med Azure Synapse Link för Azure Cosmos DB](configure-synapse-link.md)

* [Vanliga frågor och svar om Synapse Link för Azure Cosmos DB](synapse-link-frequently-asked-questions.md)

* [Användningsfall för Azure Synapse Link för Azure Cosmos DB](synapse-link-use-cases.md)
