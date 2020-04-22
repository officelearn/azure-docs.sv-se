---
title: Vanliga frågor om Azure Cosmos DB API för Cassandra.
description: Få svar på vanliga frågor om Azure Cosmos DB API för Cassandra.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: thvankra
ms.openlocfilehash: 416f0c5f995a101298e84c81317c7d39fb5d43f8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727390"
---
# <a name="frequently-asked-questions-about-the-azure-cosmos-db-api-for-cassandra"></a>Vanliga frågor och svar om Azure Cosmos DB API för Cassandra

## <a name="what-are-some-key-differences-between-apache-cassandra-and-cassandra-api"></a>Vilka är några viktiga skillnader mellan Apache Cassandra och Cassandra API?

- Apache Cassandra rekommenderar en 100 MB gräns för storleken på en partitionsnyckel. Cassandra API tillåter upp till 10 GB per partition.
- Apache Cassandra kan du inaktivera varaktiga åtaganden - dvs hoppa över att skriva till commit loggen och gå direkt till Memtable (s). Detta kan leda till dataförlust om noden går ner innan Memtables spolas till SStables på disken. Cosmos DB alltid gör hållbara begår så att du aldrig kommer att ha dataförlust.
- Apache Cassandra kan se försämrad prestanda om arbetsbelastningen innebär en hel del ersätter och / eller tar bort. Anledningen till detta är gravstenar som läsarbetsbelastningen måste hoppa över för att hämta de senaste data. Cassandra API kommer inte att se minskad läsprestanda när arbetsbelastningen har en hel del ersätter och / eller tar bort.
- Under hög ersättning arbetsbelastning scenarier, packning måste köras för att slå samman SSTables på disk (merge behövs eftersom Apache Cassandra skrivningar läggs bara till, vilket innebär att flera uppdateringar lagras som enskilda SSTable poster som måste regelbundet slås samman). Detta kan också leda till sänkt läsprestanda under packningen. Detta inträffar inte i Cassandra API eftersom det inte implementerar komprimering.
- Det är möjligt att ställa in en replikeringsfaktor på 1 med Apache Cassandra. Det leder dock till låg tillgänglighet om den enda noden med data går ner. Detta är inte ett problem med Azure Cosmos DB Cassandra API eftersom det alltid finns en replikeringsfaktor på 4 (kvorum 3).
- Lägga till / ta bort noder i Apache Cassandra kräver en hel del manuella åtgärder, men också hög CPU på den nya noden medan befintliga noder flytta några av sina token intervall till den nya noden. Detta är samma sak när du inaktiverar en befintlig nod. Skalning sker dock sömlöst under huven i Azure Cosmos DB Cassandra API, utan några problem som observerats i tjänsten/programmet.
- Det finns ingen anledning att ange num_tokens på varje nod i klustret som i Apache Cassandra. Noder och tokenintervall hanteras fullständigt av Cosmos DB.
- Azure Cosmos DB Cassandra API hanteras helt så att du inte behöver nodetool-kommandon som reparation, inaktivering etc. som används i Apache Cassandra.

## <a name="other-frequently-asked-questions"></a>Andra vanliga frågor och svar

### <a name="what-is-the-protocol-version-supported-by-azure-cosmos-db-cassandra-api-is-there-a-plan-to-support-other-protocols"></a>Vad stöds protokollversionen av Azure Cosmos DB Cassandra API? Finns det en plan för att stödja andra protokoll?

Azure Cosmos DB Cassandra API stöder CQL version 3.x. Dess CQL-kompatibilitet är baserad på den offentliga [Apache Cassandra GitHub-arkivet](https://github.com/apache/cassandra/blob/trunk/doc/cql3/CQL.textile). Om du har feedback om att stödja andra protokoll, låt oss veta via [feedback från användarens röst](https://feedback.azure.com/forums/263030-azure-cosmos-db) eller skicka ett e-postmeddelande till [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Varför är det ett krav att välja ett dataflöde för en tabell?

Azure Cosmos DB anger standarddataflöde för din behållare baserat på var du skapar tabellen från - portal eller CQL.
Azure Cosmos DB ger garantier för prestanda och svarstid, med övre gränser vid drift. Den här garantin är möjlig när motorn kan upprätthålla styrning på klientens verksamhet. Ställa in dataflöde säkerställer att du får garanterat dataflöde och svarstid, eftersom plattformen reserverar den här kapaciteten och garanterar åtgärden lyckades.
Du kan [elastiskt ändra dataflödet](manage-scale-cassandra.md) för att dra nytta av säsongsvariationen i ditt program och spara kostnader.

Dataflödeskonceptet förklaras i artikeln [Begär enheter i Azure Cosmos DB.](request-units.md) Dataflödet för en tabell fördelas på de underliggande fysiska partitionerna lika.

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>Vilka är standard-RU/s-tabellen när den skapas via CQL? Vad händer om jag behöver ändra det?

Azure Cosmos DB använder begäranheter per sekund (RU/s) som valuta för att tillhandahålla dataflöde. Tabeller som skapats genom CQL har 400 RU. Du kan ändra RU från portalen.

CQL (på andra)

```shell
CREATE TABLE keyspaceName.tablename (user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=1200
```

.NET

```csharp
int provisionedThroughput = 400;
var simpleStatement = new SimpleStatement($"CREATE TABLE {keyspaceName}.{tableName} (user_id int PRIMARY KEY, lastname text)");
var outgoingPayload = new Dictionary<string, byte[]>();
outgoingPayload["cosmosdb_provisioned_throughput"] = Encoding.UTF8.GetBytes(provisionedThroughput.ToString());
simpleStatement.SetOutgoingPayload(outgoingPayload);
```

### <a name="what-happens-when-throughput-is-used-up"></a>Vad händer när dataflödet förbrukas?

Azure Cosmos DB ger garantier för prestanda och svarstid, med övre gränser vid drift. Den här garantin är möjlig när motorn kan upprätthålla styrning på klientens verksamhet. Detta är möjligt baserat på att ställa in dataflödet, vilket säkerställer att du får garanterat dataflöde och svarstid, eftersom plattformen reserverar den här kapaciteten och garanterar åtgärden lyckades.
När du går igenom den här kapaciteten får du ett överbelastat felmeddelande om att din kapacitet har förbrukats.
0x1001 Överbelastad: begäran kan inte bearbetas eftersom "Begäranden är stor". I detta skede är det viktigt att se vilka åtgärder och deras volym orsakar problemet. Du kan få en uppfattning om förbrukad kapacitet som går över den etablerade kapaciteten med mått på portalen. Då måste du se till att kapaciteten förbrukas nästan lika över alla underliggande partitioner. Om du ser att det mesta av dataflödet förbrukas av en partition har du skevhet av arbetsbelastning.

Mått är tillgängliga som visar hur dataflöde används under timmar, dagar och per sju dagar, över partitioner eller sammanlagt. Mer information finns i [Övervaka och felsöka med mått i Azure Cosmos DB](use-metrics.md).

Diagnostikloggar förklaras i azure [cosmos DB-diagnostikloggningsartikeln.](logging.md)

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>Mappas primärnyckeln till partitionsnyckelkonceptet för Azure Cosmos DB?

Ja, partitionsnyckeln används för att placera entiteten på rätt plats. I Azure Cosmos DB används den för att hitta rätt logisk partition som lagras på en fysisk partition. Partitioneringskonceptet förklaras väl i [partitionen och skalan i Azure Cosmos DB-artikeln.](partition-data.md) Det väsentliga ta bort här är att en logisk partition inte bör gå över 10 GB gränsen idag.

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>Vad händer när jag får en fullständig kvot"-meddelande som anger att en partition är full?

Azure Cosmos DB är ett SLA-baserat system som ger obegränsad skala, med garantier för svarstid, dataflöde, tillgänglighet och konsekvens. Den här obegränsade lagringen baseras på horisontell skala av data med partitionering som nyckelbegrepp. Partitioneringskonceptet förklaras väl i [partitionen och skalan i Azure Cosmos DB-artikeln.](partition-data.md)

Gränsen på 10 GB för antalet entiteter eller objekt per logisk partition som du bör följa. För att säkerställa att ditt program skalas väl rekommenderar vi att du *inte* skapar en frekvent partition genom att lagra all information i en partition och fråga den. Det här felet kan bara komma om dina data är skeva: det vill säga du&nbsp;har mycket data för en partitionsnyckel (mer än 10 GB). Du hittar datadistributionen med hjälp av lagringsportalen. Sättet att åtgärda det här felet är att återskapa tabellen och välja en detaljerad primär (partitionsnyckel), som möjliggör bättre distribution av data.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>Är det möjligt att använda Cassandra API som nyckelvärde butik med miljontals eller miljarder enskilda partitionsnycklar?

Azure Cosmos DB kan lagra obegränsade data genom att skala ut lagringen. Detta är oberoende av dataflödet. Ja du kan alltid bara använda Cassandra API för att lagra och hämta nyckel / värden genom att ange rätt primär / partition nyckel. Dessa enskilda nycklar får sin egen logiska partition och sitter ovanpå fysisk partition utan problem.

### <a name="is-it-possible-to-create-more-than-one-table-with-apache-cassandra-api-of-azure-cosmos-db"></a>Är det möjligt att skapa mer än en tabell med Apache Cassandra API i Azure Cosmos DB?

Ja, det är möjligt att skapa mer än en tabell med Apache Cassandra API. Var och en av dessa tabeller behandlas som enhet för dataflöde och lagring.

### <a name="is-it-possible-to-create-more-than-one-table-in-succession"></a>Är det möjligt att skapa mer än en tabell i följd?

Azure Cosmos DB är resursstyrdt system för både data- och kontrollplanaktiviteter. Behållare som samlingar, tabeller är runtime entiteter som är etablerade för given dataflödeskapacitet. Skapandet av dessa behållare i snabb följd förväntas inte aktivitet och begränsas. Om du har tester som släpper/skapar tabeller omedelbart, försök att utrymmet ut dem.

### <a name="what-is-maximum-number-of-tables-that-can-be-created"></a>Vad är maximalt antal tabeller som kan skapas?

Det finns ingen fysisk gräns för antalet tabeller, skicka ett e-postmeddelande till [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) om du har ett stort antal tabeller (där den totala stadiga storleken går över 10 TB data) som måste skapas från vanliga 10s eller 100s.

### <a name="what-is-the-maximum--of-keyspace-that-we-can-create"></a>Vad är det maximala # av keyspace som vi kan skapa?

Det finns ingen fysisk gräns för antalet nyckelområden eftersom de är [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) metadatabehållare, skicka ett e-postmeddelande till om du har ett stort antal nyckelområden av någon anledning.

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>Är det möjligt att ta in mycket data efter start från normal tabell?

Ja, förutsatt att jämnt fördelade partitioner hanteras lagringskapaciteten automatiskt och ökar när du skickar in mer data. Så du kan tryggt importera så mycket data som du behöver utan att hantera och etablera noder och mycket mer. Men om du förutser en hel del omedelbar datatillväxt, är det mer meningsfullt att direkt [tillhandahålla för den förväntade genomströmningen](set-throughput.md) snarare än att börja lägre och öka den omedelbart.

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>Är det möjligt att ange yaml-filinställningar för att konfigurera Apache Casssandra API för Azure Cosmos DB-beteende?

Apache Cassandra API för Azure Cosmos DB är en plattformstjänst. Det ger kompatibilitet på protokollnivå för att utföra åtgärder. Det döljer komplexiteten i hantering, övervakning och konfiguration. Som utvecklare/användare behöver du inte oroa dig för tillgänglighet, gravstenar, nyckelcache, radcache, blomfilter och många andra inställningar. Azure Cosmos DB:s Apache Cassandra API fokuserar på att tillhandahålla läs- och skrivprestanda som du behöver utan konfiguration och hantering.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>Stöder Apache Cassandra API för Azure Cosmos DB statuskommandon för tillägg/klusterstatus/nod i Azure Cosmos DB?

Apache Cassandra API är en plattformstjänst som gör kapacitetsplanering, som svarar på elasticitetskraven för dataflöde & lagring en vind. Med Azure Cosmos DB du etablerar dataflöde, behöver du. Då kan du skala upp och ner valfritt antal gånger under dagen utan att oroa dig för att lägga till / ta bort noder eller hantera dem. Detta innebär att du inte behöver använda noden, klusterhanteringsverktyg också.

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>Vad händer med avseende på olika config inställningar för keyspace skapande som enkel / nätverk?

Azure Cosmos DB tillhandahåller global distribution direkt för tillgänglighet och låg latens. Du behöver inte ställa in repliker eller andra saker. Alla skrivningar är alltid durably kvorum begåtts i alla regioner där du skriver samtidigt som prestandagarantier.

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gc_grace-compression-memtable_flush_period-and-more"></a>Vad händer med olika inställningar för tabellmetadata som blomfilter, cachelagring, läsreparationsändring, gc_grace, komprimering memtable_flush_period med mera?

Azure Cosmos DB ger prestanda för läsningar/skrivningar och dataflöde utan att behöva röra någon av konfigurationsinställningarna och av misstag manipulera dem.

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Stöds time-to-live (TTL) för Cassandra-tabeller?

Ja, TTL stöds.

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>Är det möjligt att övervaka nodstatus, replikstatus, gc och OS parametrar tidigare med olika verktyg? Vad behöver övervakas nu?

Azure Cosmos DB är en plattformstjänst som hjälper dig att öka produktiviteten och inte oroa dig för att hantera och övervaka infrastruktur. Du behöver bara ta hand om dataflöde som är tillgängligt på portalmått för att hitta om du får begränsat och ökar eller minskar dataflödet.
Övervaka [SLA.](monitor-accounts.md)
Använd [mått](use-metrics.md) Använd [diagnostikloggar](logging.md).

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Vilka klient-SDK:er kan arbeta med Apache Cassandra API för Azure Cosmos DB?

Apache Cassandra SDK:s klientdrivrutiner som använder CQLv3 användes för klientprogram. Om du har andra drivrutiner som du använder eller om [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com)du står inför problem skickar du e-post till .

### <a name="is-composite-partition-key-supported"></a>Stöds sammansatt partitionsnyckel?

Ja, du kan använda vanlig syntax för att skapa sammansatt partitionsnyckel.

### <a name="can-i-use-sstableloader-for-data-loading"></a>Kan jag använda sstableloader för datainläsning?

Nej, sstableloader stöds inte.

### <a name="can-an-on-premises-apache-cassandra-cluster-be-paired-with-azure-cosmos-dbs-cassandra-api"></a>Kan ett lokalt Apache Cassandra-kluster paras ihop med Azure Cosmos DB:s Cassandra API?

För närvarande azure Cosmos DB har en optimerad upplevelse för molnmiljö utan overhead av åtgärder. Om du behöver para ihop [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) dig skickar du e-post till med en beskrivning av ditt scenario. Vi arbetar med att erbjuda för att hjälpa till att para ihop det lokala/olika molnet Cassandra-klustret till Cosomos DB:s Cassandra API.

### <a name="does-cassandra-api-provide-full-backups"></a>Tillhandahåller Cassandra API fullständiga säkerhetskopior?

Azure Cosmos DB tillhandahåller två kostnadsfria fullständiga säkerhetskopieringar som tas med fyra timmars intervall idag i alla API:er. Detta säkerställer att du inte behöver ställa in ett säkerhetskopieringsschema och andra saker.
Om du vill ändra kvarhållning och [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) frekvens skickar du ett e-postmeddelande till eller höjer ett supportärende. Information om säkerhetskopieringsfunktioner finns i [den automatiska säkerhetskopieringen och återställningen online med](../synapse-analytics/sql-data-warehouse/backup-and-restore.md) azure Cosmos DB-artikeln.

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Hur hanterar Cassandra API-kontot redundans om en region går ner?

Azure Cosmos DB Cassandra API lånar från den globalt distribuerade plattformen för Azure Cosmos DB. För att säkerställa att ditt program kan tolerera datacenter driftstopp, aktivera minst en region för kontot i Azure Cosmos DB portal [Utveckla med flera regioner Azure Cosmos DB-konton](high-availability.md). Du kan ange regionens prioritet med hjälp av portalen [Utveckla med Azure Cosmos DB-konton med flera regioner](high-availability.md).

Du kan lägga till så många regioner du vill för kontot och kontrollen där det kan växla över till genom att ange en redundansprioritet. Om du vill använda databasen måste du också tillhandahålla ett program där. När du gör det kommer dina kunder inte att uppleva driftstopp.

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Indexerar Apache Cassandra API alla attribut för en entitet som standard?

Nej. Cassandra API stöder [sekundära index](cassandra-secondary-index.md), som beter sig på ett mycket liknande sätt som Apache Cassandra. Alla attribut indexeras inte som standard.  


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Kan jag använda den nya Cassandra API SDK lokalt med emulatorn?

Ja detta stöds. Du kan hitta information om hur du aktiverar detta [här](local-emulator.md#cassandra-api)


### <a name="how-can-i-migrate-data-from-their-apache-cassandra-clusters-to-cosmos-db"></a>Hur migrerar jag data från deras Apache Cassandra-kluster till Cosmos DB?

Du kan läsa om migreringsalternativ [här](cassandra-import-data.md).


### <a name="feature-x-of-regular-cassandra-api-isnt-working-as-today-where-can-the-feedback-be-provided"></a>Funktion x av vanliga Cassandra API fungerar inte som idag, var kan feedback ges?

Ge feedback via [feedback från användarens röst](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md

## <a name="next-steps"></a>Nästa steg

- Kom igång med [att elastiskt skala ett Azure Cosmos DB Cassandra API-konto](manage-scale-cassandra.md).
