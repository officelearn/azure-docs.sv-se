---
title: 'Vanliga frågor och svar om olika API: er i Azure Cosmos DB'
description: Få svar på vanliga frågor om Azure Cosmos DB, en globalt distribuerad databas tjänst för flera data modeller. Lär dig mer om kapacitet, prestanda nivåer och skalning.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 15e4fc3ce412807908c75fe25acecac0fe86d261
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2019
ms.locfileid: "72262795"
---
# <a name="frequently-asked-questions-about-different-apis-in-azure-cosmos-db"></a>Vanliga frågor och svar om olika API: er i Azure Cosmos DB

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Vilka är de vanligaste användnings fallen för Azure Cosmos DB?

Azure Cosmos DB är ett bra alternativ för nya webb-, mobil-, spel-och IoT-program där automatisk skalning, förutsägbar prestanda, snabb ordning i millisekunder och möjligheten att fråga över schema fria data är viktigt. Azure Cosmos DB lånar ut sig för att snabbt utveckla och stödja kontinuerlig upprepning av program data modeller. Program som hanterar innehåll som skapats av användare och data är [vanliga användnings fall för Azure Cosmos DB](use-cases.md).

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Hur erbjuder Azure Cosmos DB förutsägbar prestanda?

En [enhet för programbegäran](request-units.md) (ru) är måttet för data flödet i Azure Cosmos dB. Ett 1RU-genomflöde motsvarar genomflödet i Hämta ett dokument på 1 KB. Varje åtgärd i Azure Cosmos DB, inklusive läsningar, skrivningar, SQL-frågor och lagrade procedur körningar har ett deterministiskt RU-värde som baseras på det data flöde som krävs för att slutföra åtgärden. I stället för att tänka på CPU, i/o och minne och hur de påverkar programmets data flöde kan du tänka på ett enda RU-mått.

Du kan konfigurera varje Azure Cosmos-behållare med ett tillhandahållet data flöde i form av ru: er data flöde per sekund. För program oavsett skala kan du mäta enskilda begär Anden för att mäta sina RU-värden och etablera en behållare för att hantera summan av enheter för programbegäran över alla förfrågningar. Du kan också skala upp eller ned din behållares data flöde när behoven i programmet utvecklas. Om du vill ha mer information om enheter för programbegäran och om hur du avgör vilka behållare du behöver kan du prova med [data flödes kalkylatorn](https://www.documentdb.com/capacityplanner)

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Hur stöder Azure Cosmos DB olika data modeller som nyckel/värde, kolumner, dokument och diagram?

Nyckel/värde-(tabell), kolumner, dokument-och diagram data modeller stöds helt och hållet på grund av designen för ARS (atomer, poster och sekvenser) som Azure Cosmos DB bygger på. Atomer, poster och sekvenser kan enkelt mappas och projiceras till olika data modeller. API: erna för en delmängd av modeller är tillgängliga just nu (SQL, MongoDB, Table och Gremlin) och andra som är speciella för ytterligare data modeller kommer att vara tillgängliga i framtiden.

Azure Cosmos DB har en schema oberoende indexerings motor som kan indexera alla data som matas in utan att kräva något schema eller sekundära index från utvecklaren. Motorn förlitar sig på en uppsättning logiska index-layouter (inverterad, kolumn, träd) som frikopplar lagrings platsen från under systemen index och fråga bearbetning. Cosmos DB har också möjlighet att stödja en uppsättning av kabel protokoll och API: er på ett utöknings Bart sätt och översätta dem effektivt till kärn data modellen (1) och de logiska index layouterna (2) som gör det möjligt att stödja mer än en data modell internt.

### <a name="can-i-use-multiple-apis-to-access-my-data"></a>Kan jag använda flera API: er för att få åtkomst till mina data?

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Om flera modeller innebär Azure Cosmos DB stöder flera API: er och flera data modeller, använder olika API: er olika data format för lagrings-och överförings protokoll. SQL använder till exempel JSON, MongoDB använder BSON, tabellen använder EDM, Cassandra använder CQL, Gremlin använder GraphSON. Därför rekommenderar vi att du använder samma API för all åtkomst till data i ett specifikt konto.

Varje API fungerar oberoende, förutom Gremlin och SQL API, som är driftskompatibla.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Är Azure Cosmos DB HIPAA-kompatibel?

Ja, Azure Cosmos DB är HIPAA-kompatibel. HIPAA fastställer kraven för användning, redovisning, och skydd av individuellt identifierbar hälsoinformation. Mer information finns i [Microsoft Trust Center](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Vilka är lagrings gränserna för Azure Cosmos DB?

Det finns ingen gräns för den totala mängden data som en behållare kan lagra i Azure Cosmos DB.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Vilka är data flödes gränserna för Azure Cosmos DB?

Det finns ingen gräns för den totala mängden data flöde som en behållare har stöd för i Azure Cosmos DB. Den viktiga idén är att distribuera arbets belastningen jämnt mellan ett tillräckligt stort antal partitionstyper.

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>Är direkta och gateway-anslutnings lägen krypterade?

Ja båda lägena är alltid fullständigt krypterade.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Hur mycket kostar Azure Cosmos DB?

Mer information finns på sidan med [pris information för Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) . Azure Cosmos DB användnings kostnader bestäms av antalet etablerade behållare, antalet timmar som behållarna var online och det etablerade data flödet för varje behållare.

### <a name="is-a-free-account-available"></a>Är ett kostnads fritt konto tillgängligt?

Ja, du kan registrera dig för ett tidsbegränsat konto utan kostnad, utan något åtagande. Registrera dig genom att gå till [Azure Cosmos DB kostnads fritt](https://azure.microsoft.com/try/cosmosdb/) eller Läs mer i [Azure Cosmos DB vanliga frågor och svar](#try-cosmos-db).

Om du är nybörjare på Azure kan du registrera dig för ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/), vilket ger 30 dagar och en kredit för att testa alla Azure-tjänster. Om du har en Visual Studio-prenumeration är du också berättigad till [kostnads fria Azure-krediter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) som kan användas på alla Azure-tjänster.

Du kan också använda [Azure Cosmos DB emulatorn](local-emulator.md) för att utveckla och testa programmet lokalt utan kostnad, utan att skapa en Azure-prenumeration. När du är nöjd med hur programmet fungerar i Azure Cosmos DB-emulatorn kan du växla till ett Azure Cosmos DB-konto i molnet.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Hur kan jag få ytterligare hjälp med Azure Cosmos DB?

Om du vill ställa en teknisk fråga kan du publicera till någon av dessa två fråge-och svars Forum:

* [MSDN-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow är bäst för programmerings frågor. Se till att din fråga är [i ämne](https://stackoverflow.com/help/on-topic) och [Ange så många detaljer som möjligt, så att frågan blir tydlig och kan besvaras](https://stackoverflow.com/help/how-to-ask).

Om du vill begära nya funktioner skapar du en ny begäran på [användarens röst](https://feedback.azure.com/forums/263030-azure-cosmos-db).

Om du vill åtgärda ett problem med ditt konto, skickar du in ett [supportärende](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) i Azure-portalen.

Andra frågor kan skickas till teamet på [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com); Detta är dock inte ett alias för teknisk support.

## <a id="try-cosmos-db"></a>Försök Azure Cosmos DB prenumerationer

Du kan nu få en tidsbegränsad Azure Cosmos DB upplevelse utan prenumeration, utan kostnad och åtaganden. Om du vill registrera dig för en try Azure Cosmos DB-prenumeration går du till [Azure Cosmos DB kostnads fritt](https://azure.microsoft.com/try/cosmosdb/). Den här prenumerationen är separat från den [kostnads fria utvärderings versionen av Azure](https://azure.microsoft.com/free/)och kan användas tillsammans med en kostnads fri utvärderings version av Azure eller en Azure-betald prenumeration.

Försök Azure Cosmos DB prenumerationer visas i Azure Portal nästa andra prenumerationer som är associerade med ditt användar-ID.

Följande villkor gäller för försök Azure Cosmos DB prenumerationer:

* En samlad [data flödes behållare](./set-throughput.md#set-throughput-on-a-container) per prenumeration för SQL-, GREMLIN-API och tabell konton.
* Upp till tre [data flöden etablerade samlingar](./set-throughput.md#set-throughput-on-a-container) per prenumeration för MongoDB-konton.
* En [data flödes etablerad databas](./set-throughput.md#set-throughput-on-a-database) per prenumeration. Data flödes etablerade databaser kan innehålla valfritt antal behållare i.
* 10 GB lagrings kapacitet.
* Global replikering är tillgängligt i följande [Azure-regioner](https://azure.microsoft.com/regions/): centrala USA, norra europa och Sydostasien
* Maximalt data flöde på 5 K RU/s vid etablering på container nivå.
* Maximalt data flöde för 20 000 RU/s vid etablering på databas nivå.
* Prenumerationerna upphör att gälla efter 30 dagar och kan utökas till maximalt 31 dagar.
* Det går inte att skapa biljetter för Azure-Support för try Azure Cosmos DB-konton. Support ges dock för prenumeranter med befintliga support avtal.

## <a name="set-up-azure-cosmos-db"></a>Konfigurera Azure Cosmos DB

### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Hur gör jag för att registrera dig för Azure Cosmos DB?

Azure Cosmos DB finns i Azure Portal. Börja med att registrera dig för en Azure-prenumeration. När du har registrerat dig kan du lägga till ett Azure Cosmos DB-konto i din Azure-prenumeration.

### <a name="what-is-a-master-key"></a>Vad är en huvudnyckel?

En huvudnyckel är en säkerhetstoken som ger åtkomst till alla resurser för ett konto. Individer med nyckeln har Läs-och Skriv behörighet för alla resurser i databas kontot. Använd försiktighet när du distribuerar huvud nycklar. Den primära huvud nyckeln och den sekundära huvud nyckeln är tillgängliga på bladet **nycklar** i [Azure Portal][azure-portal]. Mer information om nycklar finns i [Visa, kopiera och generera åtkomstnycklar på nytt](manage-with-cli.md#list-account-keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Vilka regioner som PreferredLocations kan ställas in på?

PreferredLocations-värdet kan anges till någon av de Azure-regioner där Cosmos DB är tillgängligt. En lista över tillgängliga regioner finns i [Azure-regioner](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Är det något jag bör vara medveten om när jag distribuerar data över hela världen via Azure-datacentren?

Azure Cosmos DB finns i alla Azure-regioner, enligt vad som anges på sidan [Azure-regioner](https://azure.microsoft.com/regions/) . Eftersom det är kärn tjänsten har alla nya data Center en Azure Cosmos DB närvaro.

När du ställer in en region bör du komma ihåg att Azure Cosmos DB respekterar suveräna och offentliga moln. Det innebär att om du skapar ett konto i en [suverän region](https://azure.microsoft.com/global-infrastructure/)kan du inte replikera ut ur den [suveräna regionen](https://azure.microsoft.com/global-infrastructure/). På samma sätt kan du inte aktivera replikering till andra suveräna platser från ett externt konto.

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>Är det möjligt att växla från data flödes etablering på behållar nivå till data flödes etablering på databas nivå? Eller vice versa

Data flödes etablering för behållare och databas nivå är separata erbjudanden och växling mellan dessa kräver att data migreras från källa till mål. Det innebär att du måste skapa en ny databas eller en ny behållare och sedan migrera data med hjälp av [utförar-bibliotek](bulk-executor-overview.md) eller [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>Stöder Azure CosmosDB Time Series Analysis?

Ja Azure-CosmosDB stöder tids serie analys, här är ett exempel på ett [mönster för tids serier](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns). Det här exemplet visar hur du använder ändra feed för att bygga sammansatta vyer över tids serie data. Du kan utöka den här metoden genom att använda Spark-direktuppspelning eller en annan data processor.

## <a name="what-are-the-azure-cosmos-db-service-quotas-and-throughput-limits"></a>Vilka är Azure Cosmos DB tjänst kvoter och data flödes gränser

Mer information finns i avsnittet om kvoter för Azure Cosmos DB [-tjänsten](concepts-limits.md) och över [gränser per behållare och databas](set-throughput.md#comparison-of-models) artiklar.

## <a name="sql-api"></a>API för SQL

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Hur gör jag för att börja utveckla mot SQL-API: et?

Först måste du registrera dig för en Azure-prenumeration. När du har registrerat dig för en Azure-prenumeration kan du lägga till en SQL API-behållare i din Azure-prenumeration. Anvisningar om hur du lägger till ett Azure Cosmos DB konto finns i [skapa ett Azure Cosmos Database-konto](create-sql-api-dotnet.md#create-account).

[SDK:er](sql-api-sdk-dotnet.md) är tillgängliga för .NET, Python, Node.js, JavaScript och Java. Utvecklare kan också använda [RESTful HTTP-API: er](/rest/api/cosmos-db/) för att interagera med Azure Cosmos DB resurser från olika plattformar och språk.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Kan jag komma åt några färdiga exempel för att få en första start?

Exempel på SQL API [.net](sql-api-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node. js](sql-api-nodejs-samples.md)och [python](sql-api-python-samples.md) SDK: er finns på GitHub.

### <a name="does-the-sql-api-database-support-schema-free-data"></a>Stöder SQL-API-databasen schema fria data?

Ja, SQL-API: et gör det möjligt för program att lagra godtyckliga JSON-dokument utan schema definitioner eller tips. Data är omedelbart tillgängliga för frågor via gränssnittet för Azure Cosmos DB SQL-fråga.

### <a name="does-the-sql-api-support-acid-transactions"></a>Har SQL API stöd för transaktioner i SQL API?

Ja, SQL-API: et stöder transaktioner mellan dokument, uttryckt som JavaScript-lagrade procedurer och utlösare. Transaktionerna är begränsade till en enda partition inom varje behållare och körs med sur semantik som "allt eller Nothing", isolerade från andra körning av kod-och användar förfrågningar som körs samtidigt. Om undantag uppstår genom körning av JavaScript-programkod på Server sidan återställs hela transaktionen. 

### <a name="what-is-a-container"></a>Vad är en behållare?

En behållare är en grupp med dokument och deras associerade JavaScript-programlogik. En behållare är en fakturerbar entitet där [kostnaden](performance-levels.md) bestäms av data flödet och det använda lagrings utrymmet. Behållare kan omfatta en eller flera partitioner eller servrar och kan skalas för att hantera praktiskt taget obegränsade volymer av lagring eller data flöde.

* För SQL API mappar en behållare till en behållare.
* För Cosmos DB s API för MongoDB-konton mappar en behållare till en samling.
* För Cassandra-och Tabell-API-konton mappar en behållare till en tabell.
* För Gremlin-API-konton mappar en behållare till en graf.

Behållare är även fakturerings enheter för Azure Cosmos DB. Varje behållare faktureras per timme baserat på det etablerade data flödet och det använda lagrings utrymmet. Mer information finns i [Azure Cosmos DB prissättning](https://azure.microsoft.com/pricing/details/cosmos-db/).

### <a name="how-do-i-create-a-database"></a>Hur skapar jag en databas?

Du kan skapa databaser med hjälp av [Azure Portal](https://portal.azure.com), enligt beskrivningen i [Lägg till en behållare](create-sql-api-java.md#add-a-container), en av [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md): er eller [REST-API: er](/rest/api/cosmos-db/).

### <a name="how-do-i-set-up-users-and-permissions"></a>Hur ställer jag in användare och behörigheter?

Du kan skapa användare och behörigheter genom att använda någon av [Cosmos DB API-SDK](sql-api-sdk-dotnet.md) : [er eller REST-API: er](/rest/api/cosmos-db/).

### <a name="does-the-sql-api-support-sql"></a>Stöder SQL-API SQL?

SQL-frågespråket som stöds av SQL API-konton är en förbättrad delmängd av de fråge funktioner som stöds av SQL Server. Språket Azure Cosmos DB SQL-fråga ger omfattande hierarkiska och relationella operatörer och utöknings barhet via JavaScript-baserade, användardefinierade funktioner (UDF: er). Med JSON-grammatik kan du utforma JSON-dokument som träd med märkta noder, vilka används av både Azure Cosmos DB automatiska indexerings tekniker och SQL-dialekten för Azure Cosmos DB. Information om hur du använder SQL-grammatik finns i artikeln [SQL-fråga][query] .

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>Stöder SQL-API SQL agg regerings funktioner?

SQL-API: et stöder låg latens agg regering i valfri skala via mängd funktioner `COUNT`, `MIN`, `MAX`, `AVG` och `SUM` via SQL-grammatiken. Mer information finns i [mängd funktioner](sql-query-aggregates.md).

### <a name="how-does-the-sql-api-provide-concurrency"></a>Hur tillhandahåller SQL-API samtidighet?

SQL-API: et stöder optimistisk samtidighets kontroll (OCC) via HTTP-entitetstyper eller ETags. Varje SQL-API-resurs har en ETag och ETag anges på servern varje gång ett dokument uppdateras. ETag-rubriken och det aktuella värdet ingår i alla svarsmeddelanden. ETags kan användas med If-Match-huvudet för att tillåta att servern bestämmer om en resurs ska uppdateras. Värdet If-Match är ETag-värdet som ska kontrol leras mot. Om ETag-värdet matchar Server-ETag-värdet, uppdateras resursen. Om ETag inte längre är aktuell, avvisar servern åtgärden med svars koden "HTTP 412-förväntas haveri". Klienten hämtar sedan resursen för att hämta det aktuella ETag-värdet för resursen. Dessutom kan ETags användas med rubriken If-None-Match för att avgöra om en återhämtning av en resurs krävs.

Om du vill använda optimistisk samtidighet i .NET använder du klassen [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) . Ett .NET-exempel finns i [program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) i DocumentManagement-exemplet på GitHub.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Hur gör jag för att utföra transaktioner i SQL-API: et?

SQL-API: et stöder språk integrerade transaktioner via JavaScript-lagrade procedurer och utlösare. Alla databas åtgärder inuti skript körs under ögonblicks bild isolering. Om det är en behållare för en enda partition, är körningen begränsad till behållaren. Om behållaren är partitionerad, begränsas körningen till dokument med samma partitions nyckel värde i behållaren. En ögonblicksbild av dokumentversionerna (ETags) tas i början av transaktionen och verkställs endast om skriptet lyckas. Om JavaScript genererar ett fel återställs transaktionen. Mer information finns i [JavaScript-programmering på Server sidan för Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Hur kan jag massredigera dokument i Cosmos DB?

Du kan massredigera dokument i Azure Cosmos DB på något av följande sätt:

* Verktyget för Mass utförar, som beskrivs i [använda Mass utförar .NET-bibliotek](bulk-executor-dot-net.md) och [med hjälp av utförar Java-bibliotek](bulk-executor-java.md)
* Verktyget datamigrering, enligt beskrivningen i [Database Migration Tool för Azure Cosmos DB](import-data.md).
* Lagrade procedurer, enligt beskrivningen i [programmerings programmering på Server sidan för Azure Cosmos DB](stored-procedures-triggers-udfs.md).

### <a name="does-the-sql-api-support-resource-link-caching"></a>Stöder SQL-API-cachelagring av resurs länkar?

Ja, eftersom Azure Cosmos DB är en RESTful-tjänst är resurs länkar oföränderliga och kan cachelagras. SQL-API-klienter kan ange ett "If-None-Match"-huvud för läsningar mot resurs-liknande dokument eller behållare och sedan uppdatera sina lokala kopior när Server versionen har ändrats.

### <a name="is-a-local-instance-of-sql-api-available"></a>Är en lokal instans av SQL API tillgängligt?

Ja. [Azure Cosmos DB emulatorn](local-emulator.md) ger en effektiv emulering av Cosmos DBS tjänsten. Den har stöd för funktioner som är identiska med Azure Cosmos DB, inklusive stöd för att skapa och skicka frågor mot JSON-dokument, etablerings-och skalnings samlingar och körning av lagrade procedurer och utlösare. Du kan utveckla och testa program med hjälp av Azure Cosmos DB emulatorn och distribuera dem till Azure i en global skala genom att göra en enda konfigurations ändring av anslutnings slut punkten för Azure Cosmos DB.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>Varför är långa flytt ALS värden i ett dokument avrundade när de visas från data Utforskaren i portalen.

Detta är en begränsning i Java Script. Java Script använder dubbla precisions tal för flytt ALS formatering som anges i IEEE 754 och det kan på ett säkert sätt innehålla tal mellan-(2<sup>53</sup> -1) och 2<sup>53</sup>-1 (dvs. 9007199254740991).

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Var är behörigheter tillåtna i Object-hierarkin?

Att skapa behörigheter med ResourceTokens tillåts på behållar nivån och dess underordnade (till exempel dokument, bilagor). Detta innebär att försök att skapa en behörighet till databasen eller en konto nivå inte är tillåtet för tillfället.

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>API för Azure Cosmos DB för MongoDB

### <a name="what-is-the-azure-cosmos-dbs-api-for-mongodb"></a>Vad är Azure Cosmos DBs API för MongoDB?

Azure Cosmos DBs API för MongoDB är ett protokoll för högpresterande protokoll som gör det möjligt för program att enkelt och transparent kommunicera med den inbyggda Azure Cosmos-databasmotorn genom att använda befintliga SDK: er och driv rutiner som stöds av communityn för MongoDB. Utvecklare kan nu använda befintliga MongoDB-verktygs kedjor och-kunskaper för att skapa program som utnyttjar Azure Cosmos DB. Utvecklare drar nytta av de unika funktionerna i Azure Cosmos DB, som inkluderar global distribution med multi-master-replikering, automatisk indexering, säkerhets kopierings underhåll, ekonomiskt säkerhetskopierade service nivå avtal (service avtal) osv.

### <a name="how-do-i-connect-to-my-database"></a>Hur gör jag för att ansluta till min databas?

Det snabbaste sättet att ansluta till en Cosmos-databas med Azure Cosmos DB s API för MongoDB är att gå vidare till [Azure Portal](https://portal.azure.com). Gå till ditt konto och klicka sedan på **Snabbstart**på den vänstra navigerings menyn. Snabb start är det bästa sättet att hämta kodfragment för att ansluta till databasen.

Azure Cosmos DB tillämpar strikta säkerhets krav och standarder. Azure Cosmos DB konton kräver autentisering och säker kommunikation via SSL, så se till att använda TLSv 1.2.

Mer information finns i [ansluta till din Cosmos-databas med Azure Cosmos DB s API för MongoDB](connect-mongodb-account.md).

### <a name="are-there-additional-error-codes-that-i-need-to-deal-with-while-using-azure-cosmos-dbs-api-for-mongodb"></a>Finns det ytterligare felkoder som jag måste hantera när jag använder Azure Cosmos DBs API för MongoDB?

Tillsammans med de vanliga fel koderna för MongoDB har Azure Cosmos DBs API för MongoDB sina egna specifika felkoder:

| Fel               | Programmera  | Beskrivning  | Lösning  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Det totala antalet förbrukade enheter för förbrukad enhet är mer än den etablerade enhets enhets frekvensen för behållaren och har begränsats. | Överväg att skala data flödet som är kopplat till en behållare eller en uppsättning behållare från Azure Portal eller försök igen. |
| ExceededMemoryLimit | 16501 | Som en tjänst för flera innehavare har åtgärden gått över klientens minnes tilldelning. | Minska åtgärds området genom mer restriktiva frågevillkor eller kontakta supporten från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br>Exempel: <em> @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4DB. getCollection (' Users '). Aggregation ([<br>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 {$match: {Name: "Andy"}}, <br>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 {$sort: {Age:-1}}<br>&nbsp; @ no__t-1 @ no__t-2 @ no__t-3]) </em>) |

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmos-dbs-api-for-mongodb"></a>Finns Simba-drivrutinen för MongoDB som stöds för användning med Azure Cosmos DB s API för MongoDB?

Ja, du kan använda Simba Mongo ODBC-drivrutin med Azure Cosmos DB s API för MongoDB

## <a id="table"></a>Tabell-API

### <a name="how-can-i-use-the-table-api-offering"></a>Hur kan jag använda det Tabell-API erbjudandet?

Azure Cosmos DB Tabell-API finns i [Azure Portal][azure-portal]. Först måste du registrera dig för en Azure-prenumeration. När du har registrerat dig kan du lägga till ett Azure Cosmos DB Tabell-API-konto till din Azure-prenumeration och sedan lägga till tabeller i ditt konto.

Du hittar de språk som stöds och de kopplade snabb starterna i [introduktionen till Azure Cosmos DB tabell-API](table-introduction.md).

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Behöver jag ett nytt SDK för att använda Tabell-API?

Nej, befintliga SDK: er för lagring bör fortfarande fungera. Vi rekommenderar dock att man alltid får de senaste SDK: erna för bästa möjliga support och i många fall överlägsna prestanda. Se listan över tillgängliga språk i [introduktionen till Azure Cosmos DB tabell-API](table-introduction.md).

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Var är Tabell-API inte identiska med Azure Table Storage-beteendet?

Det finns vissa beteende skillnader som användare kommer från Azure Table Storage som vill skapa tabeller med Azure Cosmos DB Tabell-API bör vara medvetna om:

* Azure Cosmos DB Tabell-API använder en reserverad kapacitets modell för att säkerställa garanterad prestanda men det innebär att en betalar för kapaciteten så fort tabellen skapas, även om kapaciteten inte används. Med Azure Table Storage betalar det bara för den kapacitet som används. Detta hjälper till att förklara varför Tabell-API kan erbjuda ett 10 MS Read-och 15 MS-Skriv-SLA på 99 percentilen medan Azure Table Storage erbjuder ett service avtal på 10 sekunder. Men till följd, med Tabell-API tabeller, även tomma tabeller utan begär Anden, kostar pengar för att se till att kapaciteten är tillgänglig för att hantera begär anden till dem enligt SLA som erbjuds av Azure Cosmos DB.
* Frågeresultat som returneras av Tabell-API sorteras inte i partitionsnyckel eller rad nyckel ordning som de är i Azure Table Storage.
* Rad nycklar får bara vara upp till 255 byte
* Batchar kan bara ha upp till 2 MB
* CORS stöds inte för närvarande
* Tabell namn i Azure Table Storage är inte Skift läges känsliga, men de är i Azure Cosmos DB Tabell-API
* Några av Azure Cosmos DBs interna format för kodnings information, till exempel binära fält, är för närvarande inte lika effektiva som en sådan. Detta kan därför orsaka oväntade begränsningar av data storleken. För närvarande kan till exempel en inte använda en fullständig MB av en tabell enhet för att lagra binära data eftersom kodningen ökar data storleken.
* Enhets egenskaps namnet "ID" stöds inte för närvarande
* TableQuery-TakeCount är inte begränsat till 1000

I termer av REST API finns det ett antal slut punkter/frågealternativ som inte stöds av Azure Cosmos DB Tabell-API:

| Rest-metod (er) | REST-slutpunkt/frågealternativ | Dokument-URL: er | Förklaring |
| ------------| ------------- | ---------- | ----------- |
| HÄMTA, PLACERA | /? restype = service@comp = egenskaper| [Ange tabell tjänst egenskaper](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties) och [Hämta Table service-egenskaper](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) | Den här slut punkten används för att ange CORS-regler, Storage Analytics-konfiguration och loggnings inställningar. CORS stöds för närvarande inte och analys och loggning hanteras på ett annat sätt i Azure Cosmos DB än Azure Storage tabeller |
| SÄTT | / @ no__t-1Table-Resource-Name > | [CORS-begäran för CORS-tabellen](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) | Detta är en del av CORS som Azure Cosmos DB för närvarande inte stöder. |
| HÄMTA | /? restype = service@comp = statistik | [Hämta Table service stats](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) | Innehåller information om hur snabbt data replikeras mellan primär och sekundär. Detta behövs inte i Cosmos DB eftersom replikeringen är en del av skrivningar. |
| HÄMTA, PLACERA | /mytable? comp = ACL | [Hämta tabell-ACL](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) och [Ange tabell-ACL](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) | Detta hämtar och anger de lagrade åtkomst principerna som används för att hantera signaturer för delad åtkomst (SAS). Även om SAS stöds, är de inställda och hanterade på olika sätt. |

Azure Cosmos DB Tabell-API stöder bara JSON-formatet, inte ATOM.

Medan Azure Cosmos DB stöder signaturer för delad åtkomst (SAS) finns det vissa principer som inte stöds, särskilt de som är relaterade till hanterings åtgärder, till exempel rätten att skapa nya tabeller.

För .NET SDK är det särskilt vissa klasser och metoder som Azure Cosmos DB för närvarande inte stöder.

| Klass | Metoden stöds inte |
|-------|-------- |
| CloudTableClient | \*ServiceProperties * |
|                  | \*ServiceStats * |
| CloudTable | SetPermissions* |
|            | GetPermissions* |
| TableServiceContext | * (den här klassen är föråldrad) |
| TableServiceEntity | " " |
| TableServiceExtensions | " " |
| TableServiceQuery | " " |

Om någon av dessa skillnader är problem med projektet kontaktar du [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) och meddelar oss.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Hur gör jag för att ge feedback om SDK eller buggar?

Du kan dela din feedback på något av följande sätt:

* [Användar röst](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [MSDN-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow är bäst för programmerings frågor. Se till att din fråga är [i ämne](https://stackoverflow.com/help/on-topic) och [Ange så många detaljer som möjligt, så att frågan blir tydlig och kan besvaras](https://stackoverflow.com/help/how-to-ask).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Vilken anslutnings sträng jag behöver använda för att ansluta till Tabell-API?

Anslutnings strängen är:

```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```

Du kan hämta anslutnings strängen från sidan anslutnings sträng i Azure Portal.

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Hur gör jag för att Åsidosätt konfigurations inställningarna för begär ande alternativen i .NET SDK för Tabell-API?

Vissa inställningar hanteras på CreateCloudTableClient-metoden och andra via app. config i avsnittet appSettings i klient programmet. Information om konfigurations inställningar finns i [Azure Cosmos DB funktioner](tutorial-develop-table-dotnet.md).

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Finns det några ändringar för kunder som använder befintliga SDK: er för Azure Table Storage?

Inget. Det finns inga ändringar för befintliga eller nya kunder som använder befintliga SDK: er för Azure Table Storage.

### <a name="how-do-i-view-table-data-thats-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Hur gör jag för att Visa tabell data som lagras i Azure Cosmos DB för användning med Tabell-API?

Du kan använda Azure Portal för att söka i data. Du kan också använda Tabell-APIs koden eller de verktyg som beskrivs i nästa svar.

### <a name="which-tools-work-with-the-table-api"></a>Vilka verktyg fungerar med Tabell-API?

Du kan använda [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

Verktyg med flexibiliteten att ta en anslutnings sträng i det tidigare formatet kan stödja den nya Tabell-API. En lista med tabell verktyg finns på sidan [Azure Storage klient verktyg](../storage/common/storage-explorers.md) .

### <a name="is-the-concurrency-on-operations-controlled"></a>Är samtidigheten i åtgärder kontrollerade?

Ja, optimistisk samtidighet tillhandahålls via användning av ETag-mekanismen.

### <a name="is-the-odata-query-model-supported-for-entities"></a>Stöds OData-fråge modellen för entiteter?

Ja, Tabell-API stöder OData-frågor och LINQ-frågor.

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Kan jag ansluta till Azure Table Storage och Azure Cosmos DB Tabell-API sida vid sida i samma program?

Ja, du kan ansluta genom att skapa två separata instanser av CloudTableClient, vart och ett som pekar på sin egen URI via anslutnings strängen.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Hur gör jag för att migrera ett befintligt Azure Table Storage-program till det här erbjudandet?

[AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) och [verktyget Azure Cosmos db data migration](import-data.md) stöds båda.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Hur ökar expansionen av lagrings storleken för den här tjänsten om jag exempelvis börjar med *n* GB data och mina data växer till 1 TB över tid?

Azure Cosmos DB är utformad för att ge obegränsad lagrings kapacitet genom att använda horisontell skalning. Tjänsten kan övervaka och effektivt öka lagringen.

### <a name="how-do-i-monitor-the-table-api-offering"></a>Hur gör jag för att övervaka Tabell-API erbjudandet?

Du kan använda fönstret Tabell-API **mått** för att övervaka begär Anden och lagrings användning.

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Hur gör jag för att beräkna det data flöde jag behöver?

Du kan använda kapacitets uppskattningen för att beräkna de TableThroughput som krävs för åtgärderna. Mer information finns i [Beräkna enheter för programbegäran och data lagring](https://www.documentdb.com/capacityplanner). I allmänhet kan du Visa din entitet som JSON och ange värden för dina åtgärder.

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Kan jag använda Tabell-API SDK lokalt med emulatorn?

Nej, inte just nu.

### <a name="can-my-existing-application-work-with-the-table-api"></a>Kan mitt befintliga program arbeta med Tabell-API?

Ja, samma API stöds.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-dont-want-to-use-the-table-api-features"></a>Måste jag migrera mina befintliga Azure Table Storage-program till SDK om jag inte vill använda Tabell-API funktionerna?

Nej, du kan skapa och använda befintliga Azure Table Storage-tillgångar utan avbrott av något slag. Men om du inte använder Tabell-API kan du inte dra nytta av det automatiska indexet, ytterligare konsekvens alternativ eller global distribution.

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-more-than-one-region-of-azure"></a>Hur gör jag för att du lägga till replikering av data i Tabell-API över mer än en region i Azure?

Du kan använda Azure Cosmos DB portalens [globala replikeringsinställningar](tutorial-global-distribution-sql-api.md#portal) för att lägga till regioner som är lämpliga för ditt program. Om du vill utveckla ett globalt distribuerat program bör du även lägga till programmet med PreferredLocation-informationen inställd på den lokala regionen för att ge låg Läs fördröjning.

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Hur gör jag för att ändra primär Skriv region för kontot i Tabell-API?

Du kan använda fönstret Azure Cosmos DB global replikeringsprovider för att lägga till en region och sedan redundansväxla till den region som krävs. Instruktioner finns i [utveckla med Azure Cosmos DB konton för flera regioner](high-availability.md).

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Hur gör jag för att konfigurera mina önskade Läs regioner för låg latens när jag distribuerar mina data?

Använd nyckeln PreferredLocation i filen app. config för att läsa från den lokala platsen. För befintliga program genererar Tabell-API ett fel om LocationMode har angetts. Ta bort koden eftersom Tabell-API hämtar informationen från app. config-filen. 

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Hur ska jag tänka på konsekvens nivåer i Tabell-API?

Azure Cosmos DB ger välgrundade kompromisser mellan konsekvens, tillgänglighet och svars tid. Azure Cosmos DB erbjuder fem konsekvens nivåer för att Tabell-API utvecklare, så du kan välja rätt konsekvens modell på tabell nivå och göra enskilda förfrågningar vid frågor mot data. När en klient ansluter kan den ange en konsekvens nivå. Du kan ändra nivån via consistencyLevel-argumentet för CreateCloudTableClient.

Tabell-API ger låg latens läsningar med "Läs dina egna skrivningar" med begränsad föråldrad konsekvens som standard. Mer information finns i [konsekvens nivåer](consistency-levels.md).

Som standard ger Azure Table Storage stark konsekvens inom en region och eventuell konsekvens på de sekundära platserna.

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Erbjuder Azure Cosmos DB Tabell-API fler konsekvens nivåer än Azure Table Storage?

Ja, mer information om hur du drar nytta av den distribuerade typen av Azure Cosmos DB finns i [konsekvens nivåer](consistency-levels.md). Eftersom garantier tillhandahålls för konsekvens nivåerna kan du använda dem med förtroende.

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>Hur lång tid tar det att replikera data när global distribution har Aktiver ATS?

Azure Cosmos DB allokerar data varaktigt i den lokala regionen och skickar data till andra regioner omedelbart på några millisekunder. Replikeringen är endast beroende av tur och retur-tid för data centret. Mer information om den globala distributions funktionen i Azure Cosmos DB finns i [Azure Cosmos DB: en globalt distribuerad databas tjänst på Azure](distribute-data-globally.md).

### <a name="can-the-read-request-consistency-level-be-changed"></a>Kan konsekvens nivån för läsning av begäran ändras?

Med Azure Cosmos DB kan du ange konsekvens nivån på behållar nivån (i tabellen). Med hjälp av .NET SDK kan du ändra nivån genom att ange värdet för TableConsistencyLevel-nyckeln i filen app. config. Möjliga värden är: stark, begränsad föråldrad, session, konsekvent prefix och eventuell. Mer information finns i [justerbara data konsekvens nivåer i Azure Cosmos DB](consistency-levels.md). Den viktiga idén är att du inte kan ställa in konsekvens nivån för begäran på mer än inställningen för tabellen. Du kan till exempel inte ange konsekvens nivån för tabellen vid fel och konsekvens nivån för begäran på stark nivå.

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Hur hanterar Tabell-API redundans om en region kraschar?

Tabell-API utnyttjar den globalt distribuerade plattformen av Azure Cosmos DB. För att säkerställa att ditt program kan tolerera Data Center stillestånd, aktiverar du minst en region för kontot i Azure Cosmos DB Portal [som utvecklar med flera regioner Azure Cosmos DB-konton](high-availability.md). Du kan ställa in prioriteten för regionen genom att använda portalen som [utvecklar med Azure Cosmos DB konton i flera regioner](high-availability.md).

Du kan lägga till så många regioner som du vill för kontot och styra vart det kan redundansväxla till genom att ange en växlings prioritet. Om du vill använda databasen måste du ange ett program där. När du gör det kommer kunderna inte att uppleva nedtid. Den [senaste .NET Client SDK](table-sdk-dotnet.md) är automatiskt värdar men de andra SDK: erna är inte. Det innebär att den kan identifiera den region som är nere och automatiskt växla över till den nya regionen.

### <a name="is-the-table-api-enabled-for-backups"></a>Är Tabell-API aktiverat för säkerhets kopieringar?

Ja, Tabell-API utnyttjar plattformen för Azure Cosmos DB för säkerhets kopiering. Säkerhets kopieringar görs automatiskt. Mer information finns i [säkerhets kopiering och återställning online med Azure Cosmos DB](online-backup-and-restore.md).

### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>Har Tabell-API indexera alla attribut för en entitet som standard?

Ja, alla attribut för en entitet indexeras som standard. Mer information finns i [Azure Cosmos DB: indexerings principer](index-policy.md).

### <a name="does-this-mean-i-dont-have-to-create-more-than-one-index-to-satisfy-the-queries"></a>Betyder det att jag inte behöver skapa fler än ett index för att uppfylla frågorna?

Ja, Azure Cosmos DB Tabell-API tillhandahåller automatisk indexering av alla attribut utan någon schema definition. Den här automationen frigör utvecklare för att fokusera på programmet i stället för att skapa och hantera index. Mer information finns i [Azure Cosmos DB: indexerings principer](index-policy.md).

### <a name="can-i-change-the-indexing-policy"></a>Kan jag ändra indexerings principen?

Ja, du kan ändra indexerings principen genom att tillhandahålla index definitionen. Du måste koda och kringgå inställningarna korrekt.

För non-.NET SDK: er kan indexerings principen bara ställas in i portalen på **datautforskaren**, navigera till den speciella tabell som du vill ändra och sedan gå till **skalnings & inställningar**-> indexerings princip, göra önskade ändringar och **Spara** .

Du kan skicka in .NET SDK i filen app. config:

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

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Azure Cosmos DB som plattform verkar ha många funktioner, till exempel sortering, agg regeringar, hierarki och andra funktioner. Kommer du att lägga till dessa funktioner i Tabell-API?

Tabell-API tillhandahåller samma fråge funktioner som Azure Table Storage. Azure Cosmos DB stöder också sortering, samlingar, geospatiala frågor, hierarki och en mängd inbyggda funktioner. Mer information finns i [SQL-frågor](how-to-sql-query.md).

### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>När ska jag ändra TableThroughput för Tabell-API?

Du bör ändra TableThroughput när något av följande villkor gäller:

* Du utför en data extrahering, transformering och inläsning (ETL), eller så vill du ladda upp en stor mängd data på kort tid.
* Du behöver mer data flöde från behållaren eller från en uppsättning behållare på Server delen. Du ser till exempel att det använda data flödet är mer än det etablerade data flödet och att du får en begränsning. Mer information finns i [set genomflödet for Azure Cosmos containers](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Kan jag skala upp eller ned data flödet i Tabell-APIs tabellen?

Ja, du kan använda den Azure Cosmos DB portalens skalnings fönster för att skala data flödet. Mer information finns i [set genomflödet](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Är en standard uppsättning TableThroughput för nyligen etablerade tabeller?

Ja, om du inte åsidosätter TableThroughput via app. config och inte använder en i förväg skapade behållare i Azure Cosmos DB, skapar tjänsten en tabell med data flödet på 400.

### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Finns det någon förändring av prissättningen för befintliga kunder i Azure Table Storage-tjänsten?

Inget. Det finns ingen ändring i priset för befintliga Azure Table Storage-kunder.

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Hur beräknas priset för Tabell-API?

Priset beror på de allokerade TableThroughput.

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Hur gör jag för att hanterar du en hastighets begränsning för tabellerna i Tabell-API erbjudande?

Om begär ande frekvensen är större än kapaciteten för det etablerade data flödet för den underliggande behållaren eller en uppsättning behållare får du ett fel meddelande och SDK: n försöker anropa anropet igen genom att använda principen för återförsök.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Varför måste jag välja ett data flöde förutom PartitionKey och RowKey för att kunna dra nytta av Tabell-API Azure Cosmos DB?

Azure Cosmos DB anger ett standard flöde för din behållare om du inte anger en i filen app. config eller via portalen.

Azure Cosmos DB ger garantier för prestanda och latens, med övre gränser för åtgärd. Den här säkerheten är möjlig när motorn kan genomdriva styrning på klientens verksamhet. Genom att ange TableThroughput ser du till att du får garanterat data flöde och svars tid, eftersom plattformen reserverar den här kapaciteten och garanterar driften.

Genom att använda data flödes specifikationen kan du elastiskt ändra den för att dra nytta av säsongs beroende i ditt program, uppfylla data flödes behoven och spara kostnaderna.

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-havent-performed-a-single-transaction-or-stored-anything-can-you-explain"></a>Azure Table Storage har varit billigt för mig, eftersom jag bara betalar för att lagra data, och jag frågar sällan. Azure Cosmos DB Tabell-API-erbjudandet verkar vara att debitera mig även om jag inte har genomfört en enskild transaktion eller sparat något. Kan du förklara?

Azure Cosmos DB är utformat för att vara ett globalt distribuerat, SLA-baserat system med garantier för tillgänglighet, svars tid och data flöde. När du reserverar data flödet i Azure Cosmos DB, är det garanterat, till skillnad från data flödet för andra system. Azure Cosmos DB tillhandahåller ytterligare funktioner som kunder har begärt, till exempel sekundära index och global distribution.

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Jag får aldrig en fullständig kvot "meddelande (som anger att en partition är full) när jag matar in data i Azure Table Storage. Med Tabell-API får jag det här meddelandet. Är det här erbjudandet som begränsar mig och tvingar mig att ändra mitt befintliga program?

Azure Cosmos DB är ett SLA-baserat system som ger obegränsad skalning, med garantier för svars tid, data flöde, tillgänglighet och konsekvens. Säkerställ att data storleken och indexet kan hanteras och vara skalbart för att garantera förstklassig prestanda. Gränsen på 10 GB för antalet entiteter eller objekt per partitionsnyckel är att se till att vi ger fantastisk sökning och fråga prestanda. För att säkerställa att ditt program skalar bra, även för Azure Storage, rekommenderar vi att du *inte* skapar en aktiv partition genom att lagra all information i en partition och fråga den.

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Så PartitionKey och RowKey krävs fortfarande med Tabell-API?

Ja. Eftersom området i Tabell-API liknar det för Azure Table Storage SDK, är partitionsnyckel ett effektivt sätt att distribuera data. Rad nyckeln är unik i den partitionen. Rad nyckeln måste finnas och får inte vara null som i standard-SDK. Längden på RowKey är 255 byte och längden på PartitionKey är 1 KB.

### <a name="what-are-the-error-messages-for-the-table-api"></a>Vilka är fel meddelandena för Tabell-API?

Azure Table Storage och Azure Cosmos DB Tabell-API använda samma SDK: er så att de flesta av felen blir desamma.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Varför får jag en begränsning när jag försöker skapa flera tabeller efter varandra i Tabell-API?

Azure Cosmos DB är ett SLA-baserat system som tillhandahåller svars tid, data flöde, tillgänglighet och konsekvens garantier. Eftersom det är ett allokerat system reserverar den resurser för att garantera dessa krav. Den snabba takten med att skapa tabeller identifieras och begränsas. Vi rekommenderar att du tittar på hur många tabeller som skapas och sänker dem till mindre än 5 per minut. Kom ihåg att Tabell-API är ett allokerat system. Den tidpunkt då du etablerar det börjar du betala för det.

## <a name="gremlin-api"></a>Gremlin-API

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>Ska C#jag använda Microsoft. Azure. graphs-paketet eller Gremlin.net för/.NET-distribution.-utveckling?

Azure Cosmos DB Gremlin API använder driv rutiner med öppen källkod som huvud anslutningar för tjänsten. Det rekommenderade alternativet är att använda [driv rutiner som stöds av Apache Tinkerpop](https://tinkerpop.apache.org/).

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>Hur debiteras RU/s vid körning av frågor i en diagram databas?

Alla graf-objekt, hörn och kanter visas som JSON-dokument i Server delen. Eftersom en Gremlin-fråga kan ändra ett eller flera diagram objekt i taget, är kostnaden som är kopplad till den direkt relaterad till de objekt, kanter som bearbetas av frågan. Detta är samma process som Azure Cosmos DB används för alla andra API: er. Mer information finns i [enheter för programbegäran i Azure Cosmos DB](request-units.md).

Avgiften för RU baseras på arbets data uppsättningen för genom gången och inte i resultat uppsättningen. Om en fråga till exempel syftar på att få ett enda formhörn som ett resultat men behöver gå över till fler än ett annat objekt på vägen, kommer kostnaden att baseras på alla graf-objekt som det tar för att beräkna den ena resultat Bryt punkten.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Vad är den maximala skalan som en diagram databas kan ha i Azure Cosmos DB Gremlin API?

Azure Cosmos DB använder [vågrät partitionering](partition-data.md) för att automatiskt öka antalet lagrings-och data flödes krav. Högsta data flöde och lagrings kapacitet för en arbets belastning bestäms av antalet partitioner som associeras med en specifik behållare. En Gremlin API-behållare har dock en uppsättning rikt linjer för att säkerställa en korrekt prestanda upplevelse i stor skala. Mer information om partitionering och bästa praxis finns i [partitionering i Azure Cosmos DB](partition-data.md) artikel.

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Hur kan jag skydda mot injektions angrepp med Gremlin-drivrutiner?

De flesta inbyggda Apache Tinkerpop Gremlin-drivrutiner ger möjlighet att tillhandahålla en lista över parametrar för frågekörningen. Detta är ett exempel på hur du gör det i [Gremlin.net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) och i [Gremlin-Java Script](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js).

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Varför får jag fel meddelandet "Gremlin Query Compilation: det går inte att hitta någon metod"?

Azure Cosmos DB Gremlin-API implementerar en delmängd av funktionerna som definierats i området Gremlin-yta. För steg som stöds och mer information, se [Gremlin support](gremlin-support.md) -artikeln.

Den bästa lösningen är att skriva om nödvändiga Gremlin-steg med funktioner som stöds, eftersom alla viktiga Gremlin-steg stöds av Azure Cosmos DB.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Varför får jag "WebSocketException: Servern returnerade status koden" 200 "när status koden" 101 "förväntades" fel?

Det här felet uppstår troligt vis när fel slut punkten används. Slut punkten som genererar det här felet har följande mönster:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/`

Detta är dokument slut punkten för diagram databasen.  Den korrekta slut punkten som ska användas är Gremlin-slutpunkten, som har följande format:

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>Varför får jag fel meddelandet "RequestRateIsTooLarge"?

Det här felet innebär att allokerade enheter för programbegäran per sekund inte räcker för att betjäna frågan. Det här felet visas vanligt vis när du kör en fråga som hämtar alla formhörn:

```
// Query example:
g.V()
```

Den här frågan kommer att försöka hämta alla hörn från grafen. Detta innebär att kostnaden för den här frågan motsvarar minst antalet hörn i ru: er. Inställningen RU/s bör justeras för att åtgärda den här frågan.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Varför försvinner mina Gremlin-drivrutins anslutningar så småningom?

En Gremlin-anslutning görs via en WebSocket-anslutning. Även om WebSocket-anslutningar inte har en angiven tid för Live, avslutar Azure Cosmos DB Gremlin-API: et inaktiva anslutningar efter 30 minuters inaktivitet.

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Varför kan jag inte använda Fluent API-anrop i de interna Gremlin-drivrutinerna?

Fluent API-anrop stöds ännu inte av Azure Cosmos DB Gremlin-API: et. Fluent API-anrop kräver en intern formaterings funktion som kallas bytekod-stöd som för närvarande inte stöds av Azure Cosmos DB Gremlin API. På grund av samma orsak stöds inte den senaste Gremlin-JavaScript-drivrutinen för närvarande.

### <a name="how-can-i-evaluate-the-efficiency-of-my-gremlin-queries"></a>Hur kan jag utvärdera effektiviteten hos mina Gremlin-frågor?

Förhands gransknings steget **executionProfile ()** kan användas för att tillhandahålla en analys av fråge körnings planen. Det här steget måste läggas till i slutet av en Gremlin-fråga som illustreras i följande exempel:

**Exempel på frågor**

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

Resultatet av profilen ovan visar hur lång tid som krävs för att hämta hörn objekt, kant objekt och storleken på den aktiva data uppsättningen. Detta är relaterat till standardkostnads måtten för Azure Cosmos DB frågor.

## <a id="cassandra"></a>API för Cassandra

### <a name="what-is-the-protocol-version-supported-by-azure-cosmso-db-cassandra-api-is-there-a-plan-to-support-other-protocols"></a>Vilken protokoll version stöds av Azure Cosmso DB API för Cassandra? Finns det någon plan för att stödja andra protokoll?

API för Apache Cassandra för Azure Cosmos DB stöder idag CQL version 4. Om du har feedback om stöd för andra protokoll kan du meddela oss via [feedback från användarens röst](https://feedback.azure.com/forums/263030-azure-cosmos-db) eller skicka ett e-postmeddelande till [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Varför ska jag välja ett data flöde för en tabell som krävs?

Azure Cosmos DB anger standard data flödet för din behållare baserat på var du skapar tabellen från-portal eller CQL.
Azure Cosmos DB ger garantier för prestanda och latens, med övre gränser för åtgärd. Den här säkerheten är möjlig när motorn kan genomdriva styrning på klientens verksamhet. Genom att ställa in data flöde ser du till att du får garanterat data flöde och svars tid, eftersom plattformen reserverar denna kapacitet och garanterar att åtgärden
Du kan ändra data flödet elastiskt för att dra nytta av säsongs beroende i ditt program och spara kostnaderna.

Data flödes konceptet förklaras i [enheter för programbegäran i Azure Cosmos DB](request-units.md) artikeln. Data flödet för en tabell fördelas jämnt över de underliggande fysiska partitionerna.

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>Vad är standard RU/s för tabell när den skapas via CQL? What If jag ändra det?

Azure Cosmos DB använder enheter för programbegäran per sekund (RU/s) som en valuta för att tillhandahålla data flöde. Tabeller som skapats med CQL har 400 RU. Du kan ändra RU från portalen.

CQL

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

### <a name="what-happens-when-throughput-is-used-up"></a>Vad händer när data flödet används?

Azure Cosmos DB ger garantier för prestanda och latens, med övre gränser för åtgärd. Den här säkerheten är möjlig när motorn kan genomdriva styrning på klientens verksamhet. Detta är möjligt baserat på att ställa in data flödet, vilket garanterar att du får garanterat data flöde och svars tid, eftersom plattformen reserverar denna kapacitet och garanterar att åtgärden lyckades.
När du går över den här kapaciteten får du ett överbelastat fel meddelande som anger att din kapacitet har använts.
överbelastade 0x1001: begäran kan inte bearbetas eftersom "begär ande frekvens är stor". I det här juncture är det viktigt att se vilka åtgärder och deras volym orsakar det här problemet. Du kan få en uppfattning om förbrukad kapacitet som går över den etablerade kapaciteten med mått på portalen. Sedan måste du se till att kapaciteten konsumeras nästan lika över alla underliggande partitioner. Om du ser det mesta av data flödet som används av en partition har du skevat arbets belastningen.

Mått är tillgängliga som visar hur data flödet används över timmar, dagar och per sju dagar, mellan partitioner eller i mängd. Mer information finns i [övervakning och fel sökning med mått i Azure Cosmos DB](use-metrics.md).

Diagnostikloggar beskrivs i artikeln [Azure Cosmos DB diagnostisk loggning](logging.md) .

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>Mappar primär nyckeln till partitionens nyckel koncept för Azure Cosmos DB?

Ja, partitionsnyckel används för att placera entiteten på rätt plats. I Azure Cosmos DB används den för att hitta rätt logisk partition som lagras på en fysisk partition. Partitionerings begreppet är väl förklarat i [partitionen och skala i Azure Cosmos DB](partition-data.md) artikel. Det är viktigt att ta bort en logisk partition som inte överskrider gränsen på 10 GB idag.

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>Vad händer när jag får en fullständig kvot "meddelande" som anger att en partition är full?

Azure Cosmos DB är ett SLA-baserat system som ger obegränsad skalning, med garantier för svars tid, data flöde, tillgänglighet och konsekvens. Detta obegränsade lagrings utrymme baseras på vågrät skala av data med partitionering som nyckel koncept. Partitionerings begreppet är väl förklarat i [partitionen och skala i Azure Cosmos DB](partition-data.md) artikel.

10 GB-gränsen för antalet entiteter eller objekt per logisk partition som du bör följa. För att säkerställa att ditt program skalar bra, rekommenderar vi att du *inte* skapar en aktiv partition genom att lagra all information i en partition och fråga den. Det här felet kan bara uppstå om dina data har skevas: det vill säga att du har stor mängd data för en partitionsnyckel (mer än 10 @ no__t-0GB). Du kan hitta data fördelningen med hjälp av lagrings portalen. Sätt att åtgärda det här felet är att återskapa tabellen och välja en detaljerad primär nyckel (partitionsnyckel), vilket gör att data kan distribueras bättre.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>Är det möjligt att använda API för Cassandra som nyckel värdes lager med miljon tals eller miljarder enskilda partitionsuppsättningar?

Azure Cosmos DB kan lagra obegränsade data genom att skala ut lagringen. Detta är oberoende av data flödet. Ja du kan alltid använda API för Cassandra för att lagra och hämta nyckel/värden genom att ange rätt primär nyckel/partitionsnyckel. Dessa enskilda nycklar får sin egen logiska partition och Sit ovanpå fysiska partition utan problem.

### <a name="is-it-possible-to-create-more-than-one-table-with-apache-cassandra-api-of-azure-cosmos-db"></a>Är det möjligt att skapa mer än en tabell med API för Apache Cassandra av Azure Cosmos DB?

Ja, det är möjligt att skapa mer än en tabell med API för Apache Cassandra. Var och en av dessa tabeller behandlas som enhet för data flöde och lagring.

### <a name="is-it-possible-to-create-more-than-one-table-in-succession"></a>Är det möjligt att skapa fler än en tabell i följd?

Azure Cosmos DB är ett resurs styrt system för både data-och kontroll Plans aktiviteter. Behållare som samlingar, tabeller är körnings enheter som är etablerade för den aktuella data flödes kapaciteten. Att skapa dessa behållare i snabb följd är inte förväntad aktivitet och begränsad. Om du har test som direkt släpper/skapar tabeller kan du försöka med att ta bort dem.

### <a name="what-is-maximum-number-of-tables-that-can-be-created"></a>Vad är det högsta antalet tabeller som kan skapas?

Det finns ingen fysisk gräns för antalet tabeller, skicka ett e-postmeddelande på [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) om du har ett stort antal tabeller (där den totala fördelade storleken är över 10 TB data) som måste skapas från vanliga tiotal eller 100s.

### <a name="what-is-the-maximum--of-keyspace-that-we-can-create"></a>Vilket är det högsta antalet tecken som vi kan skapa?

Det finns ingen fysisk gräns för antalet tecken som de är behållare för metadata, skicka ett e-postmeddelande på [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) om du har ett stort antal par av olika orsaker.

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>Är det möjligt att hämta stora mängder data efter start från normal tabell?

Lagrings kapaciteten hanteras automatiskt och ökar när du push-överför data. Det innebär att du på ett säkert sätt kan importera så mycket data som du behöver utan att behöva hantera och etablering av noder.

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>Är det möjligt att tillhandahålla yaml-filinställningar för att konfigurera Apache Casssandra API för Azure Cosmos DB beteende?

API för Apache Cassandra av Azure Cosmos DB är en plattforms tjänst. Den tillhandahåller kompatibilitet på protokoll nivå för att köra åtgärder. Den döljer komplexiteten vid hantering, övervakning och konfiguration. Som utvecklare/användare behöver du inte bekymra dig om tillgänglighet, tombstones, nyckel-cache, rad-cache, blomma filter och andra inställningar. Azure Cosmos DB API för Apache Cassandra fokuserar på att tillhandahålla Läs-och skriv prestanda som du behöver utan att behöva konfigurera och hantera.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>Kommer API för Apache Cassandra för Azure Cosmos DB stöd för noden tillägg/kluster status/nod status kommandon?

API för Apache Cassandra är en plattforms tjänst som ger kapacitets planering och som svarar på elastiska krav för data flöde & Storage a enkelt. Med Azure Cosmos DB du etablerar data flödet behöver du. Sedan kan du skala upp och ned valfritt antal gånger under dagen utan att behöva oroa dig för att lägga till/ta bort noder eller hantera dem. Detta innebär att du inte behöver använda noden, kluster hanterings verktyget.

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>Vad händer i förhållande till olika konfigurations inställningar för att skapa tecken i efterhand som enkla/nätverk?

Azure Cosmos DB tillhandahåller global distribution direkt från rutan för tillgänglighets-och låg latens orsaker. Du behöver inte konfigurera repliker eller andra saker. Alla skrivningar är alltid varaktigt-kvorum allokerat i valfri region där du skriver och ger prestanda garantier.

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gc_grace-compression-memtable_flush_period-and-more"></a>Vad händer med avseende på olika inställningar för tabellmetadata, till exempel blomma-filter, cachelagring, reparations ändring, gc_grace, komprimering memtable_flush_period och mycket mer?

Azure Cosmos DB ger prestanda för läsningar/skrivningar och data flöden utan att behöva röra någon konfigurations inställning och manipulera dem av misstag.

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Stöds TTL (Time-to-Live) för Cassandra-tabeller?

Ja, TTL stöds.

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>Är det möjligt att övervaka nodens status, replik status, GC och OS-parametrar tidigare med olika verktyg? Vad behöver övervakas nu?

Azure Cosmos DB är en plattforms tjänst som hjälper dig att öka produktiviteten och inte oroa dig för att hantera och övervaka infrastruktur. Du behöver bara ta hand om data flödet som är tillgängligt på Portal mått för att ta reda på om du får en begränsning och ökar eller minskar data flödet.
Övervaka [service avtal](monitor-accounts.md).
Använd [mått](use-metrics.md) använder [diagnostikloggar](logging.md).

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Vilka klient-SDK: er kan fungera med API för Apache Cassandra av Azure Cosmos DB?

Apache Cassandra SDK: s klient driv rutiner som använder CQLv3 användes för klient program. Om du har andra driv rutiner som du använder eller om du har problem kan du skicka e-post till [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="is-composite-partition-key-supported"></a>Stöds sammansatt partitionsnyckel?

Ja, du kan använda normal syntax för att skapa en sammansatt partitionsnyckel.

### <a name="can-i-use-sstableloader-for-data-loading"></a>Kan jag använda sstableloader för data inläsning?

Nej, sstableloader stöds inte.

### <a name="can-an-on-premises-apache-cassandra-cluster-be-paired-with-azure-cosmos-dbs-cassandra-api"></a>Kan ett lokalt Apache Cassandra-kluster kombineras med Azure Cosmos DB API för Cassandra?

För närvarande har Azure Cosmos DB en optimerad upplevelse för moln miljön utan arbete med åtgärder. Om du behöver koppling skickar du e-post till [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) med en beskrivning av ditt scenario. Vi arbetar på erbjudandet för att hjälpa till att para ihop lokala/andra moln Cassandra-kluster med Cosomos DB-API för Cassandra.

### <a name="does-cassandra-api-provide-full-backups"></a>Tillhandahåller API för Cassandra fullständiga säkerhets kopior?

Azure Cosmos DB ger två kostnads fria fullständiga säkerhets kopieringar med intervallet fyra timmar idag över alla API: er. Detta garanterar att du inte behöver skapa ett schema för säkerhets kopiering och andra saker.
Om du vill ändra kvarhållning och frekvens skickar du ett e-postmeddelande till [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com) eller ökar ett support ärende. Information om säkerhets kopierings funktioner finns i den [automatiska säkerhets kopieringen och återställningen med Azure Cosmos DB](online-backup-and-restore.md) artikel.

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Hur hanterar API för Cassandra-kontot redundans om en region kraschar?

Azure Cosmos DB API för Cassandra lånar från den globalt distribuerade plattformen av Azure Cosmos DB. För att säkerställa att ditt program kan tolerera Data Center stillestånd, aktiverar du minst en region för kontot i Azure Cosmos DB Portal [som utvecklar med flera regioner Azure Cosmos DB-konton](high-availability.md). Du kan ställa in prioriteten för regionen genom att använda portalen som [utvecklar med Azure Cosmos DB konton i flera regioner](high-availability.md).

Du kan lägga till så många regioner som du vill för kontot och styra vart det kan redundansväxla till genom att ange en växlings prioritet. Om du vill använda databasen måste du ange ett program där. När du gör det kommer kunderna inte att uppleva nedtid.

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Har API för Apache Cassandra indexera alla attribut för en entitet som standard?

API för Cassandra planerar att stödja sekundär indexering för att skapa selektivt index för vissa attribut. 


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Kan jag använda den nya API för Cassandra SDK lokalt med emulatorn?

Ja detta stöds.

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-change-feed-and-other-functionality-will-these-capabilities-be-added-to-the-cassandra-api"></a>Azure Cosmos DB som plattform verkar ha många funktioner, till exempel ändra feed och andra funktioner. Kommer dessa funktioner att läggas till i API för Cassandra?

API för Apache Cassandra ger samma CQL-funktionalitet som Apache Cassandra. Vi planerar att titta på möjligheten att stödja olika funktioner i framtiden.

### <a name="feature-x-of-regular-cassandra-api-isnt-working-as-today-where-can-the-feedback-be-provided"></a>Funktionen x av vanliga API för Cassandra fungerar inte som idag, där kan feedback tillhandahållas?

Ge feedback via [feedback från användarens röst](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md
