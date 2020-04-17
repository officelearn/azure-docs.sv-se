---
title: Vanliga frågor om olika API:er i Azure Cosmos DB
description: Få svar på vanliga frågor om Azure Cosmos DB, en globalt distribuerad databastjänst med flera modeller. Lär dig mer om kapacitet, prestandanivåer och skalning.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: e904f650c9fc11aab2dcb002ac68a9f19287940e
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81449960"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Vanliga frågor och svar om olika API: er i Azure Cosmos DB

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Vilka är de typiska användningsfallen för Azure Cosmos DB?

Azure Cosmos DB är ett bra val för nya webb-, mobil-, spel- och IoT-program där automatisk skalning, förutsägbar prestanda, snabb ordning på millisekunders svarstider och möjligheten att fråga över schemafria data är viktigt. Azure Cosmos DB lämpar sig för snabb utveckling och stöd för kontinuerlig iteration av programdatamodeller. Program som hanterar användargenererat innehåll och data är [vanliga användningsfall för Azure Cosmos DB](use-cases.md).

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Hur erbjuder Azure Cosmos DB förutsägbara prestanda?

En [begäranhet](request-units.md) (RU) är måttet på dataflöde i Azure Cosmos DB. Ett 1RU-dataflöde motsvarar dataflödet för GET-dokumentet på 1 KB. Varje åtgärd i Azure Cosmos DB, inklusive läsningar, skrivningar, SQL-frågor och lagrade procedurkörningar, har ett deterministiskt RU-värde som baseras på det dataflöde som krävs för att slutföra åtgärden. Istället för att tänka på CPU, IO och minne och hur de alla påverkar ditt program dataflöde, kan du tänka i termer av en enda RU åtgärd.

Du kan konfigurera varje Azure Cosmos-behållare med etablerat dataflöde när det gäller ru:er för dataflöde per sekund. För program av alla skalar kan du jämföra enskilda begäranden om att mäta deras RU-värden och etablera en behållare för att hantera summan av begärandeenheter över alla begäranden. Du kan också skala upp eller skala ned behållarens dataflöde när behoven i ditt program utvecklas. Mer information om begäranheter och hjälp med att fastställa behållarbehoven finns i [kalkylatorn](https://www.documentdb.com/capacityplanner)för dataflöde .

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Hur stöder Azure Cosmos DB olika datamodeller som nyckel/värde, columnar, dokument och diagram?

Nyckel-/värde (tabell), columnar-, dokument- och diagramdatamodeller stöds alla internt på grund av den AS-design (atomer, poster och sekvenser) som Azure Cosmos DB bygger på. Atomer, poster och sekvenser kan enkelt mappas och projiceras till olika datamodeller. API:erna för en delmängd av modeller är tillgängliga just nu (SQL, MongoDB, Table och Gremlin) och andra som är specifika för ytterligare datamodeller kommer att vara tillgängliga i framtiden.

Azure Cosmos DB har en schemaagnostisk indexeringsmotor som automatiskt kan indexera alla data som används utan att kräva några schema eller sekundära index från utvecklaren. Motorn förlitar sig på en uppsättning logiska indexlayouter (inverterad, columnar, träd) som frikopplar lagringslayouten från undersystemen index- och frågebearbetning. Cosmos DB har också möjlighet att stödja en uppsättning trådprotokoll och API:er på ett utbyggbart sätt och översätta dem effektivt till kärndatamodellen (1) och de logiska indexlayouterna (2) vilket gör den unikt kapabel att stödja mer än en datamodell internt.

### <a name="can-i-use-multiple-apis-to-access-my-data"></a>Kan jag använda flera API:er för att komma åt mina data?

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Där flera modeller innebär att Azure Cosmos DB stöder flera API:er och flera datamodeller använder olika API:er olika dataformat för lagring och trådprotokoll. SQL använder till exempel JSON, MongoDB använder BSON, Table använder EDM, Cassandra använder CQL, Gremlin använder JSON-format. Därför rekommenderar vi att du använder samma API för all åtkomst till data i ett visst konto.

Varje API fungerar oberoende av varandra, förutom Gremlin och SQL API, som är driftskompatibla.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Är Azure Cosmos DB HIPAA kompatibel?

Ja, Azure Cosmos DB är HIPAA-kompatibel. HIPAA fastställer kraven för användning, redovisning, och skydd av individuellt identifierbar hälsoinformation. Mer information finns i [Microsoft Trust Center](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Vilka är lagringsgränserna för Azure Cosmos DB?

Det finns ingen gräns för den totala mängden data som en behållare kan lagra i Azure Cosmos DB.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Vilka är dataflödets gränser för Azure Cosmos DB?

Det finns ingen gräns för den totala mängden dataflöde som en behållare kan stödja i Azure Cosmos DB. Den viktigaste idén är att fördela din arbetsbelastning ungefär jämnt mellan ett tillräckligt stort antal partitionsnycklar.

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>Är direkt- och gatewayanslutningslägen krypterade?

Ja båda lägena är alltid helt krypterade.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Hur mycket kostar Azure Cosmos DB?

Mer information finns på sidan [Azure Cosmos DB-prisinformation.](https://azure.microsoft.com/pricing/details/cosmos-db/) Azure Cosmos DB-användningsavgifter bestäms av antalet etablerade behållare, antalet timmar som behållarna var online och det etablerade dataflödet för varje behållare.

### <a name="is-a-free-account-available"></a>Är ett gratis konto tillgängligt?

Ja, du kan registrera dig för ett tidsbegränsadt konto utan kostnad, utan åtagande. Om du vill registrera dig besöker du [Prova Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) eller läs mer i vanliga frågor och svar om Prova Azure [Cosmos DB](#try-cosmos-db).

Om du inte har tidigare i Azure kan du registrera dig för ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/), vilket ger dig 30 dagar och en kredit för att prova alla Azure-tjänster. Om du har en Visual Studio-prenumeration är du också berättigad till [kostnadsfria Azure-krediter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) som du kan använda på alla Azure-tjänster.

Du kan också använda [Azure Cosmos DB Emulator](local-emulator.md) för att utveckla och testa ditt program lokalt utan att skapa en Azure-prenumeration. När du är nöjd med hur programmet fungerar i Azure Cosmos DB-emulatorn kan du växla till ett Azure Cosmos DB-konto i molnet.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Hur kan jag få ytterligare hjälp med Azure Cosmos DB?

För att ställa en teknisk fråga kan du skicka till en av dessa två frågor och svar forum:

* [MSDN-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack överflöd](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow är bäst för programmeringsfrågor. Se till att din fråga är [på ämnet](https://stackoverflow.com/help/on-topic) och ge så många detaljer [som möjligt, vilket gör frågan tydlig och ansvarig](https://stackoverflow.com/help/how-to-ask).

Om du vill begära nya funktioner skapar du en ny begäran på [användarens röst](https://feedback.azure.com/forums/263030-azure-cosmos-db).

Om du vill åtgärda ett problem med ditt konto, skickar du in ett [supportärende](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) i Azure-portalen.

## <a name="try-azure-cosmos-db-subscriptions"></a><a id="try-cosmos-db"></a>Prova Azure Cosmos DB-prenumerationer

Du kan nu njuta av en tidsbegränsad Azure Cosmos DB-upplevelse utan prenumeration, kostnadsfritt och åtaganden. Om du vill registrera dig för en Prova Azure Cosmos DB-prenumeration går du till [Prova Azure Cosmos DB utan kostnad](https://azure.microsoft.com/try/cosmosdb/) och använder alla personliga Microsoft-konton (MSA). Den här prenumerationen är skild från [Den kostnadsfria utvärderingsversionen](https://azure.microsoft.com/free/)av Azure och kan användas tillsammans med en kostnadsfri azure-utvärderingsversion eller en Azure-betald prenumeration.

Prova Azure Cosmos DB-prenumerationer visas i Azure-portalen nästa andra prenumerationer som är associerade med ditt användar-ID.

Följande villkor gäller för Prova Azure Cosmos DB-prenumerationer:

* Kontoåtkomst kan beviljas till personliga Microsoft-konton (MSA). Undvik att använda Active Directory-konton eller -konton som tillhör företagets AAD-klienter, de kan ha begränsningar som kan blockera åtkomstgivning.
* En [dataflödesetablerad behållare](./set-throughput.md#set-throughput-on-a-container) per prenumeration för SQL-, Gremlin API- och Table-konton.
* Upp till tre [dataflödesetablerade samlingar](./set-throughput.md#set-throughput-on-a-container) per prenumeration för MongoDB-konton.
* En [dataflödesetablerade databas](./set-throughput.md#set-throughput-on-a-database) per prenumeration. Dataflödesetablerade databaser kan innehålla valfritt antal behållare inuti.
* 10 GB lagringskapacitet.
* Global replikering är tillgänglig i följande [Azure-regioner:](https://azure.microsoft.com/regions/)Centrala USA, Norra Europa och Sydostasien
* Maximal genomströmning på 5 K RU/s vid etablering på behållarnivå.
* Maximal genomströmning på 20 K RU/s vid etablering på databasnivå.
* Prenumerationer upphör att gälla efter 30 dagar och kan förlängas till maximalt 31 dagar totalt.
* Det går inte att skapa Azure-supportärenden för Prova Azure Cosmos DB-konton. Stöd ges dock till abonnenter med befintliga supportplaner.

## <a name="set-up-azure-cosmos-db"></a>Konfigurera Azure Cosmos DB

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Hur registrerar jag mig för Azure Cosmos DB?

Azure Cosmos DB är tillgängligt i Azure-portalen. Registrera dig först för en Azure-prenumeration. När du har registrerat dig kan du lägga till ett Azure Cosmos DB-konto i din Azure-prenumeration.

### <a name="what-is-a-master-key"></a>Vad är en huvudnyckel?

En huvudnyckel är en säkerhetstoken som ger åtkomst till alla resurser för ett konto. Personer med nyckeln har läs- och skrivåtkomst till alla resurser i databaskontot. Var försiktig när du distribuerar huvudnycklar. Den primära huvudnyckeln och sekundär huvudnyckeln är tillgängliga på **bladet Nycklar** i [Azure-portalen][azure-portal]. Mer information om nycklar finns i [Visa, kopiera och generera åtkomstnycklar på nytt](manage-with-cli.md#list-account-keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Vilka regioner kan PreferredLocations ställas in på?

PreferredLocations-värdet kan ställas in på alla Azure-regioner där Cosmos DB är tillgängligt. En lista över tillgängliga regioner finns i [Azure-regioner](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Finns det något jag bör vara medveten om när jag distribuerar data över hela världen via Azure-datacenter?

Azure Cosmos DB finns i alla Azure-regioner, enligt vad som anges på sidan [Azure-regioner.](https://azure.microsoft.com/regions/) Eftersom det är kärntjänsten har varje nytt datacenter en Azure Cosmos DB-närvaro.

När du anger en region bör du komma ihåg att Azure Cosmos DB respekterar suveräna moln och myndighetsmoln. Det vill säga om du skapar ett konto i en [suverän region](https://azure.microsoft.com/global-infrastructure/)kan du inte replikera från den [suveräna regionen.](https://azure.microsoft.com/global-infrastructure/) På samma sätt kan du inte aktivera replikering till andra suveräna platser från ett externt konto.

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>Är det möjligt att växla från etablering av dataflöde på behållarnivå till etablering av dataflöde på databasnivå? Eller vice versa

Etablering av dataflöde på behållar- och databasnivå är separata erbjudanden och växling mellan något av dessa kräver migrering av data från källa till mål. Vilket innebär att du måste skapa en ny databas eller en ny behållare och sedan migrera data med hjälp av [bulk executor library](bulk-executor-overview.md) eller [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>Stöder Azure CosmosDB tidsserieanalys?

Ja Azure CosmosDB stöder tidsserieanalys, här är ett exempel för [tidsseriemönster](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns). Det här exemplet visar hur du använder ändringsflödet för att skapa aggregerade vyer över tidsseriedata. Du kan utöka den här metoden genom att använda gnistströmning eller en annan dataprocessor för ström.

## <a name="what-are-the-azure-cosmos-db-service-quotas-and-throughput-limits"></a>Vilka är Azure Cosmos DB-tjänstkvoter och begränsningar för dataflöde

Mer information finns i Azure Cosmos [DB-tjänstkvoterna](concepts-limits.md) och [under gränserna per behållare och databasartiklar.](set-throughput.md#comparison-of-models)

## <a name="sql-api"></a>API för SQL

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Hur börjar jag utveckla mot SQL API?

Först måste du registrera dig för en Azure-prenumeration. När du har registrerat dig för en Azure-prenumeration kan du lägga till en SQL API-behållare i din Azure-prenumeration. Instruktioner om hur du lägger till ett Azure Cosmos DB-konto finns i [Skapa ett Azure Cosmos-databaskonto](create-sql-api-dotnet.md#create-account).

[SDK:er](sql-api-sdk-dotnet.md) är tillgängliga för .NET, Python, Node.js, JavaScript och Java. Utvecklare kan också använda [RESTful HTTP API:er](/rest/api/cosmos-db/) för att interagera med Azure Cosmos DB-resurser från olika plattformar och språk.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Kan jag komma åt några färdiga prover för att få ett försprång?

Exempel för SQL API [.NET](sql-api-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](sql-api-nodejs-samples.md)och [Python](sql-api-python-samples.md) SDK:er finns på GitHub.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>Stöder SQL API-databasen schemafria data?

Ja, SQL API tillåter program för att lagra godtyckliga JSON-dokument utan schemadefinitioner eller tips. Data är omedelbart tillgängliga för fråga via Azure Cosmos DB SQL-frågegränssnittet.

### <a name="does-the-sql-api-support-acid-transactions"></a>Stöder SQL API ACID-transaktioner?

Ja, SQL API stöder korsdokumenttransaktioner som uttrycks som JavaScript-lagrade procedurer och utlösare. Transaktioner begränsas till en enda partition i varje behållare och körs med ACID semantik som "allt eller inget", isolerat från andra samtidigt köra kod och användarbegäranden. Om undantag genereras genom serverkörningen av JavaScript-programkod återställs hela transaktionen. 

### <a name="what-is-a-container"></a>Vad är en container?

En behållare är en grupp dokument och deras associerade JavaScript-programlogik. En behållare är en fakturerbar entitet, där [kostnaden](performance-levels.md) bestäms av dataflödet och använt lagringsutrymme. Behållare kan sträcka sig över en eller flera partitioner eller servrar och kan skalas för att hantera praktiskt taget obegränsade volymer av lagring eller dataflöde.

* För SQL API mappas en behållare till en behållare.
* För Cosmos DB:s API för MongoDB-konton mappas en behållare till en samling.
* För Cassandra- och Table API-konton mappas en behållare till en tabell.
* För Gremlin API-konton mappas en behållare till ett diagram.

Behållare är också faktureringsenheter för Azure Cosmos DB. Varje behållare faktureras per timme, baserat på etablerat dataflöde och använt lagringsutrymme. Mer information finns i [Azure Cosmos DB Pricing](https://azure.microsoft.com/pricing/details/cosmos-db/).

### <a name="how-do-i-create-a-database"></a>Hur skapar jag en databas?

Du kan skapa databaser med hjälp av [Azure-portalen](https://portal.azure.com)enligt beskrivningen i [Lägg till en behållare](create-sql-api-java.md#add-a-container), en av Azure [Cosmos DB SDK:er](sql-api-sdk-dotnet.md)eller [REST-API:erna](/rest/api/cosmos-db/).

### <a name="how-do-i-set-up-users-and-permissions"></a>Hur ställer jag in användare och behörigheter?

Du kan skapa användare och behörigheter med hjälp av en av [Kosmos DB API-SDK:er](sql-api-sdk-dotnet.md) eller [REST-API:erna](/rest/api/cosmos-db/).

### <a name="does-the-sql-api-support-sql"></a>Stöder SQL API SQL?

SQL-frågespråket som stöds av SQL API-konton är en förbättrad delmängd av frågefunktionen som stöds av SQL Server. Azure Cosmos DB SQL-frågespråket ger omfattande hierarkiska och relationsoperatorer och utökningsbarhet via JavaScript-baserade, användardefinierade funktioner (UDF). JSON grammatik gör det möjligt att modellera JSON-dokument som träd med etiketterade noder, som används av både Azure Cosmos DB automatiska indexeringstekniker och SQL-frågedialekten för Azure Cosmos DB. Information om hur du använder SQL-grammatik finns i SQL [Query-artikeln.][query]

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>Stöder SQL API SQL-aggregeringsfunktioner?

SQL API stöder aggregering med låg latens `COUNT`i `MIN` `MAX`valfri `AVG`skala `SUM` via mängdfunktioner , , , och via SQL-grammatiken. Mer information finns i [Aggregera funktioner](sql-query-aggregates.md).

### <a name="how-does-the-sql-api-provide-concurrency"></a>Hur ger SQL API samtidighet?

SQL API stöder optimistisk samtidighetskontroll (OCC) via HTTP-entitetstaggar eller ETags. Varje SQL API-resurs har en ETag och ETag är inställd på servern varje gång ett dokument uppdateras. ETag-huvudet och det aktuella värdet ingår i alla svarsmeddelanden. ETags kan användas med if-match-huvudet för att servern ska kunna avgöra om en resurs ska uppdateras. If-Match-värdet är det ETag-värde som ska kontrolleras mot. Om ETag-värdet matchar server-ETag-värdet uppdateras resursen. Om ETag inte längre är aktuell avvisar servern åtgärden med svarskoden "HTTP 412 Villkorsfel". Klienten refetches sedan resursen för att hämta det aktuella ETag-värdet för resursen. Dessutom kan ETags användas med if-none-match-huvudet för att avgöra om en refetch av en resurs behövs.

Om du vill använda optimistisk samtidighet i .NET använder du klassen [AccessCondition.](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) Ett .NET-exempel finns [i Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) i documentmanagement-exemplet på GitHub.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Hur utför jag transaktioner i SQL API?

SQL API stöder språkintegrerade transaktioner via JavaScript-lagrade procedurer och utlösare. Alla databasåtgärder inuti skript körs under ögonblicksbildisolering. Om det är en behållare med en partition begränsas körningen till behållaren. Om behållaren är partitionerad begränsas körningen till dokument med samma partitionsnyckelvärde i behållaren. En ögonblicksbild av dokumentversionerna (ETags) tas i början av transaktionen och verkställs endast om skriptet lyckas. Om JavaScript genererar ett fel återställs transaktionen. Mer information finns i [JavaScript-programmering på serversidan för Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Hur kan jag massinfoga in dokument i Cosmos DB?

Du kan massinfoga in dokument i Azure Cosmos DB på något av följande sätt:

* Massutn0-körningsverktyget, enligt beskrivningen i [Använda massutnrör .NET-bibliotek](bulk-executor-dot-net.md) och [Använda Java-bibliotek för massutnrör](bulk-executor-java.md)
* Datamigreringsverktyget enligt beskrivningen i [Verktyget Databasmigrering för Azure Cosmos DB](import-data.md).
* Lagrade procedurer enligt beskrivningen i [JavaScript-programmering på serversidan för Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="does-the-sql-api-support-resource-link-caching"></a>Stöder SQL API cachelagring av resurslänkar?

Ja, eftersom Azure Cosmos DB är en RESTful-tjänst är resurslänkar oföränderliga och kan cachelagras. SQL API-klienter kan ange en "If-None-Match"-rubrik för läsningar mot alla resursliknande dokument eller behållare och sedan uppdatera sina lokala kopior när serverversionen har ändrats.

### <a name="is-a-local-instance-of-sql-api-available"></a>Är en lokal instans av SQL API tillgänglig?

Ja. [Azure Cosmos DB Emulator](local-emulator.md) tillhandahåller en hifi-emulering av Cosmos DB-tjänsten. Den stöder funktioner som är identiska med Azure Cosmos DB, inklusive stöd för att skapa och fråga JSON-dokument, etablera och skala samlingar och köra lagrade procedurer och utlösare. Du kan utveckla och testa program med hjälp av Azure Cosmos DB Emulator och distribuera dem till Azure på global nivå genom att göra en enda konfigurationsändring till anslutningsslutpunkten för Azure Cosmos DB.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>Varför är långa flyttalsvärden i ett dokument avrundat när de visas från datautforskaren i portalen.

Detta är en begränsning av JavaScript. JavaScript använder dubbel precision flyttalsformat nummer som anges i IEEE 754 och det kan säkert hålla siffror mellan -(2<sup>53</sup> - 1) och 2<sup>53</sup>-1 (dvs. 9007199254740991) bara.

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Var är behörigheter tillåtna i objekthierarkin?

Det är tillåtet att skapa behörigheter med Hjälp av ResourceTokens på behållarnivå och dess underordnade (till exempel dokument, bifogade filer). Detta innebär att det för närvarande inte är tillåtet att försöka skapa en behörighet på databasen eller en kontonivå.

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>API för Azure Cosmos DB för MongoDB

### <a name="what-is-the-azure-cosmos-dbs-api-for-mongodb"></a>Vad är Azure Cosmos DB:s API för MongoDB?

Azure Cosmos DB:s API för MongoDB är ett kompatibilitetslagret för trådprotokoll som gör att program enkelt och transparent kan kommunicera med den inbyggda Azure Cosmos-databasmotorn med hjälp av befintliga SDK:er som stöds av communityn och drivrutiner för MongoDB. Utvecklare kan nu använda befintliga MongoDB-verktygskedjor och kunskaper för att skapa program som utnyttjar Azure Cosmos DB. Utvecklare drar nytta av de unika funktionerna i Azure Cosmos DB, som inkluderar global distribution med multi-master replikering, automatisk indexering, säkerhetskopiering underhåll, ekonomiskt stödda servicenivåavtal (SLA) etc.

### <a name="how-do-i-connect-to-my-database"></a>Hur ansluter jag till min databas?

Det snabbaste sättet att ansluta till en Cosmos-databas med Azure Cosmos DB:s API för MongoDB är att gå över till [Azure-portalen](https://portal.azure.com). Gå till ditt konto och klicka sedan på **Snabbstart**på menyn till vänster . Snabbstart är det bästa sättet att få kodavsnitt för att ansluta till databasen.

Azure Cosmos DB tillämpar strikta säkerhetskrav och standarder. Azure Cosmos DB-konton kräver autentisering och säker kommunikation via TLS, så se till att använda TLSv1.2.

Mer information finns i [Ansluta till cosmos-databasen med Azure Cosmos DB:s API för MongoDB](connect-mongodb-account.md).

### <a name="are-there-additional-error-codes-that-i-need-to-deal-with-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Finns det ytterligare felkoder som jag måste hantera när jag använder Azure Cosmos DB:s API för MongoDB?

Tillsammans med de vanliga MongoDB-felkoderna har Azure Cosmos DB:s API för MongoDB sina egna specifika felkoder:

| Fel               | Kod  | Beskrivning  | Lösning  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Det totala antalet använda begärandeenheter är mer än den etablerade begäranhetsgraden för behållaren och har begränsats. | Överväg att skala dataflödet som tilldelats en behållare eller en uppsättning behållare från Azure-portalen eller försöka igen. |
| ExceededMemoryLimit | 16501 | Som en tjänst med flera innehavare har åtgärden gått igenom klientens minnestilldelning. | Minska åtgärdens omfattning genom mer restriktiva frågevillkor eller kontaktstöd från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br><em> &nbsp; &nbsp;Exempel: &nbsp; &nbsp;db.getCollection('users').aggregate([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {namn: "Andy"}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {ålder: -1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])</em>) |

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>Stöds Simba-drivrutinen för MongoDB för användning med Azure Cosmos DB:s API för MongoDB?

Ja, du kan använda Simbas Mongo ODBC-drivrutin med Azure Cosmos DB:s API för MongoDB

## <a name="table-api"></a><a id="table"></a>Tabell-API

### <a name="how-can-i-use-the-table-api-offering"></a>Hur kan jag använda tabell-API-erbjudandet?

Azure Cosmos DB Table API är tillgängligt i [Azure-portalen][azure-portal]. Först måste du registrera dig för en Azure-prenumeration. När du har registrerat dig kan du lägga till ett Azure Cosmos DB Table API-konto i din Azure-prenumeration och sedan lägga till tabeller i ditt konto.

Du hittar språk som stöds och tillhörande snabbstarter i [API:et introduktion till Azure Cosmos DB Table](table-introduction.md).

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Behöver jag en ny SDK för att använda tabell-API:et?

Nej, befintliga lagrings-SDK:er bör fortfarande fungera. Det rekommenderas dock att man alltid får de senaste SDK:erna för bästa stöd och i många fall överlägsen prestanda. Se listan över tillgängliga språk i [Api:et för introduktion till Azure Cosmos DB](table-introduction.md)Table .

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>På vilka punkter är inte tabell-API:et identiskt med Azure Table Storage-beteendet?

Det finns vissa beteendeskillnader som användare som kommer från Azure Table storage och som vill skapa tabeller med Azure Cosmos DB Table API bör vara medvetna om:

* Azure Cosmos DB Table API använder en modell med reserverad kapacitet för att säkerställa garanterad prestanda, men det innebär att man betalar för kapaciteten så snart tabellen skapas, även om kapaciteten inte används. Med Azure Table storage betalar man bara för kapacitet som används. Detta hjälper till att förklara varför Tabell API kan erbjuda en 10 ms läsa och 15 ms skriva SLA vid den 99: e percentilen medan Azure Table lagring erbjuder en 10-sekunders SLA. Men som en följd, med Tabell API-tabeller, även tomma tabeller utan några begäranden, kostar pengar för att säkerställa att kapaciteten är tillgänglig för att hantera alla förfrågningar till dem på SLA som erbjuds av Azure Cosmos DB.
* Frågeresultat som returneras av tabell-API:et sorteras inte i partitionsnyckel/radnyckelordning som de finns i Azure Table Storage.
* Radtangenter kan bara vara upp till 255 byte
* Partier kan bara ha upp till 2 MB
* CORS stöds för närvarande inte
* Tabellnamn i Azure Table storage är inte skiftlägeskänsliga, men de finns i Azure Cosmos DB Table API
* Några av Azure Cosmos DB:s interna format för kodningsinformation, till exempel binära fält, är för närvarande inte så effektiva som man skulle vilja. Därför kan detta orsaka oväntade begränsningar för datastorlek. För närvarande kan man till exempel inte använda den fullständiga meg av en tabell entitet för att lagra binära data eftersom kodningen ökar datas storlek.
* Ett entitetsegenskapsnamn 'ID' stöds inte för närvarande
* TableQuery TakeCount är inte begränsat till 1000

När det gäller REST API finns det ett antal slutpunkter/frågealternativ som inte stöds av Azure Cosmos DB Table API:

| Vila metod (er) | Alternativet Rest slutpunkt/fråga | Url:er för dokument | Förklaring |
| ------------| ------------- | ---------- | ----------- |
| FÅ, SÄTT | /?restype=service@comp=egenskaper| [Egenskaper för ange tabelltjänst](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties) och [egenskaper för hämta tabelltjänst](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) | Den här slutpunkten används för att ange CORS-regler, konfiguration av lagringsanalys och loggningsinställningar. CORS stöds för närvarande inte och analys och loggning hanteras på olika sätt i Azure Cosmos DB än Azure Storage Tables |
| Alternativ | /\<tabell-resursnamn> | [Begäran om CORS-bord före flygning](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) | Detta är en del av CORS som Azure Cosmos DB för närvarande inte stöder. |
| HÄMTA | /?restype=service@comp=statistik | [Hämta statistik för bordstjänst](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) | Ger information om hur snabbt data replikerar mellan primära och sekundära. Detta behövs inte i Cosmos DB eftersom replikeringen är en del av skrivningar. |
| FÅ, SÄTT | /mytable?comp=acl | [Skaffa tabell ACL](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) och [dutbord ACL](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) | Detta hämtar och anger de principer för lagrad åtkomst som används för att hantera SIGNATURER för delad åtkomst (SAS). Även om SAS stöds ställs de in och hanteras på olika sätt. |

Dessutom Azure Cosmos DB Tabell API stöder endast JSON-format, inte ATOM.

Azure Cosmos DB stöder SIGNATURER för delad åtkomst (SAS) men det finns vissa principer som det inte stöder, särskilt de som är relaterade till hanteringsåtgärder som rätten att skapa nya tabeller.

För .NET SDK i synnerhet finns det några klasser och metoder som Azure Cosmos DB för närvarande inte stöder.

| Klass | Metod som inte stöds |
|-------|-------- |
| CloudTableClient | \*ServiceEgenskaper* |
|                  | \*ServiceStats* |
| CloudTable (CloudTable) | SetPermissions* |
|            | FåPermissions* |
| TableServiceContext | * (denna klass är föråldrad) |
| TableServiceEntity | " " |
| TableServiceExtensions | " " |
| TableServiceQuery | " " |

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Hur ger jag feedback om SDK eller buggar?

Du kan dela din feedback på något av följande sätt:

* [Användarens röst](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [MSDN-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack överflöd](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow är bäst för programmeringsfrågor. Se till att din fråga är [på ämnet](https://stackoverflow.com/help/on-topic) och ge så många detaljer [som möjligt, vilket gör frågan tydlig och ansvarig](https://stackoverflow.com/help/how-to-ask).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Vad är anslutningssträngen som jag behöver använda för att ansluta till tabell-API:et?

Anslutningssträngen är:

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

Du kan hämta anslutningssträngen från sidan Anslutningssträng i Azure-portalen.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Hur åsidosätter jag konfigurationsinställningarna för begäransalternativen i .NET SDK för tabell-API:et?

Vissa inställningar hanteras på Metoden CreateCloudTableClient och andra via app.config i avsnittet appSettings i klientprogrammet. Information om konfigurationsinställningar finns i [Azure Cosmos DB-funktioner](tutorial-develop-table-dotnet.md).

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Finns det några ändringar för kunder som använder de befintliga Azure Table Storage SDK:erna?

Inga. Det finns inga ändringar för befintliga eller nya kunder som använder de befintliga Azure Table Storage SDK:erna.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Hur visar jag tabelldata som lagras i Azure Cosmos DB för användning med tabell-API: et?

Du kan använda Azure-portalen för att bläddra bland data. Du kan också använda tabell-API-koden eller de verktyg som nämns i nästa svar.

### <a name="which-tools-work-with-the-table-api"></a>Vilka verktyg fungerar med tabell-API:et?

Du kan använda [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

Verktyg med flexibiliteten att ta en anslutningssträng i det format som tidigare angetts kan stödja det nya tabell-API:et. En lista över tabellverktyg finns på sidan [Azure Storage Client Tools.](../storage/common/storage-explorers.md)

### <a name="is-the-concurrency-on-operations-controlled"></a>Är samtidighet i driftkontrolleras?

Ja, optimistisk samtidighet tillhandahålls med hjälp av ETag-mekanismen.

### <a name="is-the-odata-query-model-supported-for-entities"></a>Stöds OData-frågemodellen för entiteter?

Ja, tabell-API:et stöder OData-fråga och LINQ-fråga.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Kan jag ansluta till Azure Table Storage och Azure Cosmos DB Table API sida vid sida i samma program?

Ja, du kan ansluta genom att skapa två separata instanser av CloudTableClient, var och en pekar på sin egen URI via anslutningssträngen.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Hur migrerar jag ett befintligt Azure Table-lagringsprogram till det här erbjudandet?

[AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) och [Azure Cosmos DB Data Migration Tool](import-data.md) stöds båda.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Hur görs utvidgningen av lagringsstorleken för den här tjänsten om jag till exempel börjar med *n* GB data och mina data kommer att växa till 1 TB med tiden?

Azure Cosmos DB är utformad för att tillhandahålla obegränsad lagring med hjälp av horisontell skalning. Tjänsten kan övervaka och effektivt öka din lagring.

### <a name="how-do-i-monitor-the-table-api-offering"></a>Hur övervakar jag tabell-API-erbjudandet?

Du kan använda fönstret **Tabell-API-mått** för att övervaka begäranden och lagringsanvändningen.

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Hur beräknar jag det dataflöde jag behöver?

Du kan använda kapacitetskalkylatorn för att beräkna det tablethroughput som krävs för operationerna. Mer information finns i [Uppskatta begäranheter och datalagring](https://www.documentdb.com/capacityplanner). I allmänhet kan du visa din entitet som JSON och ange siffrorna för dina operationer.

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Kan jag använda tabell-API SDK lokalt med emulatorn?

Inte just nu.

### <a name="can-my-existing-application-work-with-the-table-api"></a>Kan mitt befintliga program fungera med tabell-API:et?

Ja, samma API stöds.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>Måste jag migrera mina befintliga Azure Table-lagringsprogram till SDK om jag inte vill använda tabell-API-funktionerna?

Nej, du kan skapa och använda befintliga Azure Table-lagringsresurser utan avbrott av något slag. Men om du inte använder tabell-API:et kan du inte dra nytta av det automatiska indexet, alternativet ytterligare konsekvens eller global distribution.

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>Hur lägger jag till replikering av data i tabell-API:et i mer än en region i Azure?

Du kan använda Azure Cosmos DB-portalens [globala replikeringsinställningar](tutorial-global-distribution-sql-api.md#portal) för att lägga till regioner som är lämpliga för ditt program. Om du vill utveckla ett globalt distribuerat program bör du också lägga till ditt program med PreferredLocation-informationen inställd på den lokala regionen för att ge låg läsfördröjning.

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Hur ändrar jag den primära skrivregionen för kontot i tabell-API:et?

Du kan använda azure Cosmos DB globala replikeringsportalfönstret för att lägga till en region och sedan växla över till den region som krävs. Instruktioner finns i [Utveckla med Azure Cosmos DB-konton med flera regioner](high-availability.md).

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Hur konfigurerar jag mina önskade läsregioner för låg latens när jag distribuerar mina data?

Om du vill hjälpa dig att läsa från den lokala platsen använder du nyckeln PreferredLocation i filen app.config. För befintliga program genererar tabell-API:et ett fel om LocationMode har angetts. Ta bort koden eftersom tabell-API:et hämtar den här informationen från filen app.config. 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Hur ska jag tänka på konsekvensnivåer i tabell-API:et?

Azure Cosmos DB ger välmotiverade kompromisser mellan konsekvens, tillgänglighet och svarstid. Azure Cosmos DB erbjuder fem konsekvensnivåer till Tabell API-utvecklare, så du kan välja rätt konsekvensmodell på tabellnivå och göra enskilda begäranden samtidigt som du frågar efter data. När en klient ansluter kan den ange en konsekvensnivå. Du kan ändra nivån via argumentet consistencyLevel i CreateCloudTableClient.

Tabell-API:et ger låg latensläsningar med "Läs dina egna skrivningar", med begränsad föråldringskonsekvens som standard. Mer information finns i [Konsekvensnivåer](consistency-levels.md).

Som standard ger Azure Table Storage stark konsekvens inom en region och slutlig konsekvens på de sekundära platserna.

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Erbjuder Azure Cosmos DB Table API fler konsekvensnivåer än Azure Table Storage?

Ja, mer information om hur du drar nytta av den distribuerade karaktären av Azure Cosmos DB finns i [konsekvensnivåer](consistency-levels.md). Eftersom garantier ges för konsekvensnivåerna kan du använda dem med tillförsikt.

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Hur lång tid tar det att replikera data när global distribution är aktiverat?

Azure Cosmos DB arkiverar data durably i den lokala regionen och skickar data till andra regioner omedelbart i några millisekunder. Den här replikeringen är endast beroende av datacentrets rundresa (RTT). Mer information om den globala distributionskapaciteten i Azure Cosmos DB finns i [Azure Cosmos DB: En globalt distribuerad databastjänst på Azure](distribute-data-globally.md).

### <a name="can-the-read-request-consistency-level-be-changed"></a>Kan konsekvensnivån för läsbegäran ändras?

Med Azure Cosmos DB kan du ställa in konsekvensnivån på behållarnivå (i tabellen). Genom att använda .NET SDK kan du ändra nivån genom att ange värdet för TableConsistencyLevel-tangenten i filen app.config. De möjliga värdena är: Stark, Begränsad föråldring, Session, Konsekvent prefix och eventuellt. Mer information finns [i Kompatibilitetsnivåer för datakonsekvens i Azure Cosmos DB](consistency-levels.md). Den viktigaste idén är att du inte kan ställa in begäran konsekvens nivå på mer än inställningen för tabellen. Du kan till exempel inte ange konsekvensnivån för tabellen på Slutlig och konsekvensnivån för begäran på Stark.

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Hur hanterar tabell-API-redundansen om en region går ner?

Tabell-API:et utnyttjar den globalt distribuerade plattformen för Azure Cosmos DB. För att säkerställa att ditt program kan tolerera datacenter driftstopp, aktivera minst en region för kontot i Azure Cosmos DB portal [Utveckla med flera regioner Azure Cosmos DB-konton](high-availability.md). Du kan ange regionens prioritet med hjälp av portalen [Utveckla med Azure Cosmos DB-konton med flera regioner](high-availability.md).

Du kan lägga till så många regioner du vill för kontot och kontrollen där det kan växla över till genom att ange en redundansprioritet. Om du vill använda databasen måste du också tillhandahålla ett program där. När du gör det kommer dina kunder inte att uppleva driftstopp. Den [senaste .NET-klienten SDK](table-sdk-dotnet.md) är automatisk sändare men de andra SDK:erna är det inte. Det vill än, det kan identifiera den region som är nere och automatiskt växla över till den nya regionen.

### <a name="is-the-table-api-enabled-for-backups"></a>Är tabell-API:et aktiverat för säkerhetskopiering?

Ja, tabell-API:et utnyttjar plattformen för Azure Cosmos DB för säkerhetskopiering. Säkerhetskopior görs automatiskt. Mer information finns i [Online säkerhetskopiering och återställning med Azure Cosmos DB](online-backup-and-restore.md).

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>Indexerar tabell-API alla attribut för en entitet som standard?

Ja, alla attribut för en entitet indexeras som standard. Mer information finns i [Azure Cosmos DB: Indexeringsprinciper](index-policy.md).

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>Betyder det att jag inte behöver skapa mer än ett index för att tillfredsställa frågorna?

Ja, Azure Cosmos DB Table API ger automatisk indexering av alla attribut utan schemadefinition. Denna automatisering frigör utvecklare att fokusera på programmet snarare än på skapande och hantering av index. Mer information finns i [Azure Cosmos DB: Indexeringsprinciper](index-policy.md).

### <a name="can-i-change-the-indexing-policy"></a>Kan jag ändra indexeringsprincipen?

Ja, du kan ändra indexeringsprincipen genom att ange indexdefinitionen. Du måste korrekt koda och komma undan inställningarna.

För de non-.NET SDK:er kan indexeringsprincipen bara anges i portalen i **Utforskaren,** navigera till den specifika tabell som du vill ändra och sedan gå till **skala & inställningar**->indexeringsprincipen, göra önskad ändring och sedan **spara**.

Från .NET SDK kan den skickas i filen app.config:

```JSON
{
  "indexingMode": "consistent",
  "automatic": true,
  "includedPaths": [
    {
      "path": "/somepath",
      "indexes": [
        {
          "kind": "Range",
          "dataType": "Number",
          "precision": -1
        },
        {
          "kind": "Range",
          "dataType": "String",
          "precision": -1
        }
      ]
    }
  ],
  "excludedPaths":
[
 {
      "path": "/anotherpath"
 }
]
}
```

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Azure Cosmos DB som en plattform verkar ha många funktioner, till exempel sortering, aggregat, hierarki och andra funktioner. Kommer du att lägga till dessa funktioner i tabell-API: et?

Tabell-API:et innehåller samma frågefunktioner som Azure Table Storage. Azure Cosmos DB stöder också sortering, samlingar, geospatiala frågor, hierarki och en mängd inbyggda funktioner. Mer information finns i [SQL-frågor](how-to-sql-query.md).

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>När ska jag ändra TableThroughput för tabell-API: et?

Du bör ändra TableThroughput när något av följande villkor gäller:

* Du utför ett utdrag, transformerar och läser in (ETL) data, eller så vill du ladda upp en hel del data på kort tid.
* Du behöver mer dataflöde från behållaren eller från en uppsättning behållare i den bakre änden. Du ser till exempel att det använda dataflödet är mer än det etablerade dataflödet och att du får begränsat. Mer information finns i [Ange dataflöde för Azure Cosmos-behållare](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Kan jag skala upp eller skala ned dataflödet i tabellen Tabell API?

Ja, du kan använda Azure Cosmos DB-portalens skalfönster för att skala dataflödet. Mer information finns i [Ange dataflöde](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Är en standard tablethroughput inställd för nyligen etablerade tabeller?

Ja, om du inte åsidosätter TableThroughput via app.config och inte använder en förskapad behållare i Azure Cosmos DB, skapar tjänsten en tabell med dataflöde på 400.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Finns det någon ändring av priser för befintliga kunder i Azure Table storage-tjänsten?

Inga. Priset för befintliga Azure Table-lagringskunder har inte ändrats.

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Hur beräknas priset för tabell-API:et?

Priset beror på det tilldelade TableThroughput.

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Hur hanterar jag alla prisbegränsningar på tabellerna i Table API-erbjudande?

Om begäranden är större än kapaciteten för det etablerade dataflödet för den underliggande behållaren eller en uppsättning behållare får du ett fel och SDK försöker samtalet igen genom att tillämpa återförsöksprincipen.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Varför måste jag välja ett dataflöde förutom PartitionKey och RowKey för att dra nytta av tabell-API-erbjudandet i Azure Cosmos DB?

Azure Cosmos DB anger ett standarddataflöde för din behållare om du inte tillhandahåller ett i filen app.config eller via portalen.

Azure Cosmos DB ger garantier för prestanda och svarstid, med övre gränser vid drift. Den här garantin är möjlig när motorn kan upprätthålla styrning på klientens verksamhet. Ställa in TableThroughput säkerställer att du får garanterat dataflöde och svarstid, eftersom plattformen reserverar den här kapaciteten och garanterar operativ framgång.

Genom att använda dataflödesspecifikationen kan du elastiskt ändra den för att dra nytta av säsongsvariationen för ditt program, uppfylla dataflödesbehoven och spara kostnader.

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>Azure Table lagring har varit billigt för mig, eftersom jag betalar bara för att lagra data, och jag sällan fråga. Azure Cosmos DB Table API-erbjudandet verkar debitera mig även om jag inte har utfört en enda transaktion eller lagrat något. Kan du förklara?

Azure Cosmos DB är utformad för att vara ett globalt distribuerat, SLA-baserat system med garantier för tillgänglighet, svarstid och dataflöde. När du reserverar dataflöde i Azure Cosmos DB garanteras det, till skillnad från dataflödet för andra system. Azure Cosmos DB innehåller ytterligare funktioner som kunderna har begärt, till exempel sekundära index och global distribution.

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Jag får aldrig en fullständig kvotavisering (som anger att en partition är full) när jag inmatar data i Azure Table storage. Med tabell-API:et får jag det här meddelandet. Är detta erbjudande begränsa mig och tvinga mig att ändra min befintliga ansökan?

Azure Cosmos DB är ett SLA-baserat system som ger obegränsad skala, med garantier för svarstid, dataflöde, tillgänglighet och konsekvens. Se till att datastorleken och indexet är hanterbara och skalbara för att säkerställa garanterad premiumprestanda. Gränsen på 10 GB för antalet entiteter eller objekt per partitionsnyckel är att säkerställa att vi tillhandahåller bra uppslags- och frågeprestanda. För att säkerställa att ditt program skalas väl, även för Azure Storage, rekommenderar vi att du *inte* skapar en het partition genom att lagra all information i en partition och fråga den.

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Så PartitionKey och RowKey krävs fortfarande med tabell-API: et?

Ja. Eftersom tabell-API:ets yta liknar den i Azure Table Storage SDK, ger partitionsnyckeln ett effektivt sätt att distribuera data. Radnyckeln är unik i den partitionen. Radnyckeln måste finnas och kan inte vara null som i standard-SDK. Längden på RowKey är 255 byte och längden på PartitionKey är 1 KB.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Vilka är felmeddelandena för tabell-API:et?

Azure Table storage och Azure Cosmos DB Table API använder samma SDK:er så de flesta felen blir desamma.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Varför begränsas jag när jag försöker skapa många tabeller en efter en i tabell-API:et?

Azure Cosmos DB är ett SLA-baserat system som tillhandahåller svarstid, dataflöde, tillgänglighet och konsekvensgarantier. Eftersom det är ett etablerat system reserverar det resurser för att garantera dessa krav. Den snabba genereringshastigheten för tabeller identifieras och begränsas. Vi rekommenderar att du tittar på hur snabbt du skapar tabeller och sänker den till mindre än 5 per minut. Kom ihåg att tabell-API:et är ett etablerat system. Så fort du etablerar det, börjar du betala för det.

## <a name="gremlin-api"></a>Gremlin-API

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>För C#/.NET-utveckling, ska jag använda Microsoft.Azure.Graphs-paketet eller Gremlin.NET?

Azure Cosmos DB Gremlin API utnyttjar drivrutinerna med öppen källkod som huvudanslutningar för tjänsten. Så det rekommenderade alternativet är att använda [drivrutiner som stöds av Apache Tinkerpop](https://tinkerpop.apache.org/).

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>Hur debiteras RU/s när du kör frågor i en grafdatabas?

Alla diagramobjekt, hörn och kanter visas som JSON-dokument i backend. Eftersom en Gremlin-fråga kan ändra ett eller flera diagramobjekt i taget, är kostnaden som är kopplad till objekten, kanter som bearbetas av frågan. Det här är samma process som Azure Cosmos DB använder för alla andra API:er. Mer information finns i [Enheter för programbegäran i Azure Cosmos DB](request-units.md).

RU-laddningen baseras på den fungerande datauppsättningen för traversalen och inte resultatuppsättningen. Om en fråga till exempel syftar till att hämta ett enda hörn som ett resultat men behöver passera mer än ett annat objekt på vägen, baseras kostnaden på alla diagramobjekt som krävs för att beräkna ett resultatvertex.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Vilken är den maximala skalan som en grafdatabas kan ha i Azure Cosmos DB Gremlin API?

Azure Cosmos DB använder sig av [horisontell partitionering](partition-data.md) för att automatiskt hantera ökning av lagrings- och dataflödeskrav. Den maximala dataflödet och lagringskapaciteten för en arbetsbelastning bestäms av antalet partitioner som är associerade med en viss behållare. En Gremlin API-behållare har dock en specifik uppsättning riktlinjer för att säkerställa en korrekt prestandaupplevelse i stor skala. Mer information om partitionering och metodtips finns [i partitionering i Azure Cosmos](partition-data.md) DB-artikel.

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Hur skyddar jag mot injektionsattacker med Gremlin-drivrutiner?

De flesta inbyggda Apache Tinkerpop Gremlin-drivrutiner tillåter alternativet att tillhandahålla en ordlista med parametrar för körning av frågor. Detta är ett exempel på hur man gör det i [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) och i [Gremlin-Javascript](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js).

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Varför får jag felmeddelandet "Gremlin Query Compilation Error: Lyckades inte hitta någon metod"?

Azure Cosmos DB Gremlin API implementerar en delmängd av de funktioner som definierats i Gremlin-ytan. Steg och mer information som stöds finns i [Gremlins supportartikel.](gremlin-support.md)

Den bästa lösningen är att skriva om de nödvändiga Gremlin-stegen med de funktioner som stöds, eftersom alla viktiga Gremlin-steg stöds av Azure Cosmos DB.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Varför får jag felet "WebSocketException: Servern returnerade statuskoden "200" när statuskoden '101' förväntades"- fel?

Det här felet genereras troligen när fel slutpunkt används. Slutpunkten som genererar det här felet har följande mönster:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

Det här är dokumentslutpunkten för diagramdatabasen.  Den korrekta slutpunkten som ska användas är Gremlin-slutpunkten, som har följande format:

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>Varför får jag felet "RequestRateIsTooLarge"?

Det här felet innebär att de allokerade begärandeenheterna per sekund inte räcker till för att kunna betjäna frågan. Det här felet visas vanligtvis när du kör en fråga som hämtar alla hörn:

```
// Query example:
g.V()
```

Den här frågan försöker hämta alla hörn från diagrammet. Så kostnaden för den här frågan kommer att vara lika med minst antalet hörn när det gäller ru: er. INSTÄLLNINGEN FÖR RU/s bör justeras för att åtgärda den här frågan.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Varför tappas mina Gremlin-drivrutinsanslutningar så småningom?

En Gremlin-anslutning görs via en WebSocket-anslutning. Även om WebSocket-anslutningar inte har en viss tid att leva, avslutar Azure Cosmos DB Gremlin API inaktiva anslutningar efter 30 minuters inaktivitet.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Varför kan jag inte använda flytande API-anrop i de inbyggda Gremlin-drivrutinerna?

Flytande API-anrop stöds ännu inte av Azure Cosmos DB Gremlin API. Flytande API-anrop kräver en intern formateringsfunktion som kallas bytekodstöd som för närvarande inte stöds av Azure Cosmos DB Gremlin API. Av samma anledning stöds den senaste Gremlin-JavaScript-drivrutinen för närvarande inte.

### <a name="how-can-i-evaluate-the-efficiency-of-my-gremlin-queries"></a>Hur kan jag utvärdera effektiviteten i mina Gremlin-frågor?

**Förhandsgranskningssteget executionProfile()** kan användas för att tillhandahålla en analys av frågekörningsplanen. Det här steget måste läggas till i slutet av en Gremlin-fråga, vilket illustreras av följande exempel:

**Exempel på fråga**

```
g.V('mary').out('knows').executionProfile()
```

**Exempel på utdata**

```json
[
  {
    "gremlin": "g.V('mary').out('knows').executionProfile()",
    "totalTime": 8,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 3,
        "annotations": {
          "percentTime": 37.5
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 62.5
        },
        "counts": {
          "resultCount": 0
        },
        "storeOps": [
          {
            "count": 0,
            "size": 0,
            "time": 0.6
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 0
        }
      }
    ]
  }
]
```

Utdata för ovanstående profil visar hur mycket tid som går åt att hämta hörnobjekten, kantobjekten och storleken på arbetsdatauppsättningen. Detta är relaterat till standardkostnadsmätningar för Azure Cosmos DB-frågor.

## <a name="cassandra-api"></a><a id="cassandra"></a>Cassandra API

### <a name="what-is-the-protocol-version-supported-by-azure-cosmos-db-cassandra-api-is-there-a-plan-to-support-other-protocols"></a>Vad stöds protokollversionen av Azure Cosmos DB Cassandra API? Finns det en plan för att stödja andra protokoll?

Apache Cassandra API för Azure Cosmos DB stöder idag CQL version 4. Om du har feedback om att stödja andra protokoll, låt oss veta via [feedback från användarens röst](https://feedback.azure.com/forums/263030-azure-cosmos-db) eller skicka ett e-postmeddelande till [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Varför är det ett krav att välja ett dataflöde för en tabell?

Azure Cosmos DB anger standarddataflöde för din behållare baserat på var du skapar tabellen från - portal eller CQL.
Azure Cosmos DB ger garantier för prestanda och svarstid, med övre gränser vid drift. Den här garantin är möjlig när motorn kan upprätthålla styrning på klientens verksamhet. Ställa in dataflöde säkerställer att du får garanterat dataflöde och svarstid, eftersom plattformen reserverar den här kapaciteten och garanterar åtgärden lyckades.
Du kan elastiskt ändra dataflödet för att dra nytta av säsongsvariationen i ditt program och spara kostnader.

Dataflödeskonceptet förklaras i artikeln [Begär enheter i Azure Cosmos DB.](request-units.md) Dataflödet för en tabell fördelas på de underliggande fysiska partitionerna lika.

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>Vilka är standard-RU/s-tabellen när den skapas via CQL? Vad händer om jag behöver ändra det?

Azure Cosmos DB använder begäranheter per sekund (RU/s) som valuta för att tillhandahålla dataflöde. Tabeller som skapats genom CQL har 400 RU. Du kan ändra RU från portalen.

CQL (på andra)

```
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

Lagringskapaciteten hanteras automatiskt och ökar när du skickar in mer data. Så du kan tryggt importera så mycket data som du behöver utan att hantera och etablera noder och mycket mer.

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
Om du vill ändra kvarhållning och [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) frekvens skickar du ett e-postmeddelande till eller höjer ett supportärende. Information om säkerhetskopieringsfunktioner finns i [den automatiska säkerhetskopieringen och återställningen online med](online-backup-and-restore.md) azure Cosmos DB-artikeln.

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Hur hanterar Cassandra API-kontot redundans om en region går ner?

Azure Cosmos DB Cassandra API lånar från den globalt distribuerade plattformen för Azure Cosmos DB. För att säkerställa att ditt program kan tolerera datacenter driftstopp, aktivera minst en region för kontot i Azure Cosmos DB portal [Utveckla med flera regioner Azure Cosmos DB-konton](high-availability.md). Du kan ange regionens prioritet med hjälp av portalen [Utveckla med Azure Cosmos DB-konton med flera regioner](high-availability.md).

Du kan lägga till så många regioner du vill för kontot och kontrollen där det kan växla över till genom att ange en redundansprioritet. Om du vill använda databasen måste du också tillhandahålla ett program där. När du gör det kommer dina kunder inte att uppleva driftstopp.

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Indexerar Apache Cassandra API alla attribut för en entitet som standard?

Cassandra API planerar att stödja sekundär indexering för att skapa selektivt index på vissa attribut. 


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Kan jag använda den nya Cassandra API SDK lokalt med emulatorn?

Ja detta stöds.

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-change-feed-and-other-functionality-will-these-capabilities-be-added-to-the-cassandra-api"></a>Azure Cosmos DB som en plattform verkar ha många funktioner, till exempel ändringsflöde och andra funktioner. Kommer dessa funktioner att läggas till i Cassandra API?

Apache Cassandra API ger samma CQL-funktionalitet som Apache Cassandra. Vi planerar att undersöka möjligheten att stödja olika möjligheter i framtiden.

### <a name="feature-x-of-regular-cassandra-api-isnt-working-as-today-where-can-the-feedback-be-provided"></a>Funktion x av vanliga Cassandra API fungerar inte som idag, var kan feedback ges?

Ge feedback via [feedback från användarens röst](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md
