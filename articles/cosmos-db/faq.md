---
title: Vanliga frågor och svar om Azure Cosmos DB | Microsoft Docs
description: Få svar på vanliga frågor och svar om Azure Cosmos DB, en globalt distribuerad databastjänst. Läs mer om kapacitet, prestandanivåer och skalning.
keywords: Databasfrågor, vanliga frågor, documentdb, azure, Microsoft azure
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: sngun
ms.openlocfilehash: 554718f0bb465ca757fc4dcf6c22d3b0dd80f2fb
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50251098"
---
# <a name="azure-cosmos-db-faq"></a>Azure Cosmos DB vanliga frågor och svar
## <a name="azure-cosmos-db-fundamentals"></a>Grunderna i Azure Cosmos DB

### <a name="what-happened-to-the-documentdb-api"></a>Vad hände med DocumentDB-API?

Azure Cosmos DB DocumentDB API eller SQL (DocumentDB) API kallas nu Azure Cosmos DB SQL API. Du behöver inte ändra något för att fortsätta att köra dina appar som skapats med DocumentDB-API. Funktionen förblir densamma.

Om du har ett DocumentDB-API-konto innan, nu har du en SQL API-konto utan ändring din fakturering. 

### <a name="what-happened-to-azure-documentdb-as-a-service"></a>Vad hände med Azure DocumentDB som en tjänst?

Azure DocumentDB-tjänsten är nu en del av Azure Cosmos DB-tjänsten och visar sig i form av SQL-API. Program som skapats för Azure DocumentDB körs mot Azure Cosmos DB SQL API utan ändringar. Dessutom stöder Azure Cosmos DB Gremlin-API, tabell-API, API för MongoDB och Cassandra-API (förhandsversion).

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Vad är vanliga användningsområden för Azure Cosmos DB?
Azure Cosmos DB är ett bra alternativ för nya webb-, mobil, spel, och IoT-program där automatisk skalning, förutsägbar prestanda, snabba ordningen på svarstider och möjlighet till frågor över schemafria data är viktigt. Azure Cosmos DB lämpar sig för snabb utveckling och stöder kontinuerlig iteration av appens datamodeller. Program som hanterar användargenererat innehåll och data är [vanliga användningsområden för Azure Cosmos DB](use-cases.md). 

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Hur erbjuder förutsägbar prestanda i Azure Cosmos DB?
En [begäransenhet](request-units.md) (RU) är måttenheten för dataflöde i Azure Cosmos DB. Ett dataflöde på 1 RU motsvarar dataflödet av GET för ett 1 KB-dokument. Varje åtgärd i Azure Cosmos DB, inklusive läsningar, skrivningar, SQL-frågor och lagrade procedurkörningar har ett deterministiskt RU-värde som baseras på dataflödet som krävs för att slutföra åtgärden. I stället för att tänka på CPU, I/O och minne och hur de påverkar dataflödet i ditt program kan tänka du i termer av ett enda RU-mått.

Du kan reservera varje Azure Cosmos DB-behållare med etablerat dataflöde räknat ru: er av dataflöde per sekund. För appar oavsett skala, kan du jämföra enskilda förfrågningar för att mäta deras RU-värden och etablera en behållare för att hantera det totala antalet av frågeenheter över alla förfrågningar. Du kan även skala upp eller skala ned dataflödet för en behållare som behov utvecklas. Mer information om begäransenheter och hjälp med att fastställa din behållare behöver finns i [beräknar dataflödet behöver](request-units.md#estimating-throughput-needs) och försök på [dataflöde Kalkylatorn](https://www.documentdb.com/capacityplanner). Termen *behållare* här refererar till en SQL API-samling, Gremlin-API-diagram, MongoDB API-samling och tabell-API-tabellen. 

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Hur stöder olika datamodeller som nyckel/värde, kolumner, dokument och graph i Azure Cosmos DB?

Nyckelvärdedata (tabell), kolumner, dokument och diagramdata modeller internt stöds på grund av ARS (atomer, poster och sekvenser) kan du utforma att Azure Cosmos DB bygger på. Atomer, poster och sekvenser kan enkelt mappas och planerade att olika datamodeller. API: er för en delmängd av modeller är tillgängliga just nu (SQL, MongoDB, Table och Gremlin-API: er) och andra som är specifika för ytterligare datamodeller kommer att finnas i framtiden.

Azure Cosmos DB har en schemat schemaoberoende indexering motor som kan indexera automatiskt alla data den tar in utan att kräva något schema eller sekundära index från utvecklaren. Motorn är beroende av en uppsättning logiskt index layouter (vägar i inverterad, kolumner,-träd) som frikopplar lagringslayout från indexet och frågebearbetning undersystem. Cosmos DB har också möjlighet att stödja en uppsättning wire-protokoll och API: er på ett utökningsbart sätt och effektivt översätta dem till datamodellen core (1) och de logiska index layouter (2) vilket gör det unikt kan stödja flera datamodeller internt.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Är Azure Cosmos DB HIPAA-kompatibelt?
Ja, Azure Cosmos DB är HIPAA-kompatibelt. HIPAA fastställer kraven för användning, redovisning, och skydd av individuellt identifierbar hälsoinformation. Mer information finns i [Microsoft Trust Center](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Vad är Azure Cosmos DB Lagringsgränser?
Det finns ingen gräns för den totala mängden data som en behållare kan lagra i Azure Cosmos DB.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Vad är genomflödesgränserna hos Azure Cosmos DB?
Det finns ingen gräns för den totala mängden dataflöde som har stöd för en behållare i Azure Cosmos DB. Viktiga tanken är att fördela arbetsbelastningen ungefär jämnt mellan ett tillräckligt stort antal partitionsnycklar.

### <a name="are-direct-and-gateway-connectivity-modes-encrypted"></a>Krypteras Direct och Gateway-anslutningslägen 
Ja krypteras alltid helt båda lägena. 

### <a name="how-much-does-azure-cosmos-db-cost"></a>Hur mycket kostar Azure Cosmos DB?
Mer information finns i [prisinformation för Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) sidan. Azure Cosmos DB-användningskostnader bestäms av antalet etablerade behållare, antalet timmar behållarna var online, och det etablerade dataflödet för varje behållare. Beteckningen container här refererar till SQL API-samling, Gremlin-API (graf), MongoDB API-samling och tabeller för tabell-API. 

### <a name="is-a-free-account-available"></a>Är ett kostnadsfritt konto tillgängligt?
Ja, kan du registrera dig för en tidsbegränsad kontot utan kostnad, utan förpliktelser. Om du vill registrera dig, besök [testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/) eller Läs mer i den [prova Azure Cosmos DB vanliga frågor och svar](#try-cosmos-db).

Om du är nybörjare på Azure kan du registrera dig för en [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/), som ger dig 30 dagar och en kredit att prova alla Azure-tjänster. Om du har en Visual Studio-prenumeration kan du också är berättigad till [kostnadsfri Azure-kredit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) att använda på valfri Azure-tjänst. 

Du kan också använda den [Azure Cosmos DB-emulatorn](local-emulator.md) att utveckla och testa ditt program lokalt för kostnad, utan att skapa en Azure-prenumeration. När du är nöjd med hur programmet fungerar i Azure Cosmos DB-emulatorn kan du växla till ett Azure Cosmos DB-konto i molnet.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Hur kan jag få ytterligare hjälp med Azure Cosmos DB?

Om du vill ställa en teknisk fråga kan du i en av dessa två fråga och besvara forum:
* [MSDN-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stackspill](http://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow är bäst för frågor om programmering. Kontrollera att din fråga är [på avsnittet](https://stackoverflow.com/help/on-topic) och [innehåller samma information som möjligt, vilket gör frågan tydlig och besvaras](https://stackoverflow.com/help/how-to-ask). 

Skapa en ny begäran för att efterfråga nya funktioner på [User voice](https://feedback.azure.com/forums/263030-azure-cosmos-db).

Om du vill åtgärda ett problem med ditt konto, skickar du in ett [supportärende](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) i Azure-portalen.

Andra frågor kan skickas till teamet på [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com); men detta inte är ett alias för teknisk support. 


## <a id="try-cosmos-db"></a>Prova Azure Cosmos DB-prenumerationer

Du kan nu få en tidsbegränsad Azure Cosmos DB-upplevelse utan prenumeration, kostnadsfritt och åtaganden. Om du vill registrera dig för en testa Azure Cosmos DB-prenumeration kan du gå till [testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/). Den här prenumerationen är separat från den [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/free/), och kan användas tillsammans med en kostnadsfri utvärderingsversion av Azure eller en Azure betald prenumeration. 

Prova Azure Cosmos DB-prenumerationer som visas i Azure-portalen bredvid andra prenumerationer som är associerade med ditt användar-ID. 

Följande villkor gäller för testa Azure Cosmos DB-prenumerationer:

* Behållare per prenumeration för SQL, Gremlin-API och Table-konton.
* Upp till tre samlingarna per prenumeration för MongoDB-konton.
* 10 GB lagringskapacitet.
* Global replikering finns tillgängligt i följande [Azure-regioner](https://azure.microsoft.com/regions/): centrala USA, Norra Europa och Asien, sydöstra
* Maximalt dataflöde på 5 K RU/s.
* Prenumerationer upphör att gälla efter 24 timmar och kan utökas till högst 48 timmar totalt.
* Azure supportärenden kan skapas för testa Azure Cosmos DB-konton dock tillhandahåller support till prenumeranter med befintliga supportavtal. 

## <a name="set-up-azure-cosmos-db"></a>Ställ in Azure Cosmos DB
### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Hur registrerar jag mig för Azure Cosmos DB?
Azure Cosmos DB är tillgängliga i Azure-portalen. Först registrera dig för en Azure-prenumeration. När du har registrerat dig kan du lägga till en SQL-API, Gremlin-API, tabell-API, MongoDB API eller Cassandra API-konto på Azure-prenumerationen.

### <a name="what-is-a-master-key"></a>Vad är en huvudnyckel?
En huvudnyckel är en säkerhetstoken som ger åtkomst till alla resurser för ett konto. Personer med nyckeln har läs- och skrivbehörighet till alla resurser i databaskontot. Var försiktig när du distribuerar huvudnycklar. Den primära och sekundära huvudnycklarna finns på den **nycklar** bladet för den [Azure-portalen][azure-portal]. Mer information om nycklar finns i [Visa, kopiera och generera åtkomstnycklar på nytt](manage-account.md#keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Vilka är de regioner som PreferredLocations kan anges till? 
PreferredLocations-värdet kan anges till någon av de Azure-regioner som Cosmos DB är tillgängligt. En lista över tillgängliga regioner finns i [Azure-regioner](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Finns det något jag bör känna till när du distribuerar data över hela världen via Azure-Datacenter? 
Azure Cosmos DB finns i alla Azure-regioner, enligt vad som anges på den [Azure-regioner](https://azure.microsoft.com/regions/) sidan. Eftersom det är core-tjänst har ett Azure Cosmos DB närvaro i alla nya datacenter. 

När du ställer in en region, Tänk på att Azure Cosmos DB respekterar suveräna och government-moln. Det vill säga om du skapar ett konto i en [suveräna region](https://azure.microsoft.com/global-infrastructure/), du kan inte replikera ut ur [suveräna region](https://azure.microsoft.com/global-infrastructure/). Du kan inte på samma sätt Aktivera replikering till andra nationella platser från en extern konto. 

### <a name="is-it-possible-to-switch-from-container-level-throughput-provisioning-to-database-level-throughput-provisioning-or-vice-versa"></a>Är det möjligt att växla från nivå av dataflöden i behållare på dataflöde Databasetableringen etablering? Eller tvärtom

Behållare och nivån dataflöde Databasetableringen är två separata erbjudanden och växla mellan något av dessa kräver att migrera data från källa till mål. Vilket innebär att du behöver skapa en ny databas eller en ny samling och sedan migrera data med hjälp av [bulk executor biblioteket](bulk-executor-overview.md) eller [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md).

### <a name="how-do-i-create-fixed-collection-with-partition-key"></a>Hur gör jag för att skapa fasta samlingen med partitionsnyckel

Du kan för närvarande skapa samling med en partition viktiga dataflöde med hjälp av den [CreatePartitionedCollection](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/CollectionManagement/Program.cs#L118) metod av .net SDK eller med hjälp av den [Azure CLI](https://docs.microsoft.com/cli/azure/cosmosdb/collection?view=azure-cli-latest#az-cosmosdb-collection-create). Skapa en fast samling med hjälp av Azure-portalen stöds inte för närvarande.  

### <a name="does-azure-cosmosdb-support-time-series-analysis"></a>Azure cosmos DB har stöd för analys av tidsserier? 
Ja Azure cosmos DB har stöd för analys av tidsserier, här är ett exempel för [serien tidmönster](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/samples/Patterns). Det här exemplet visar hur du använder ändringsflödet för att skapa sammansatta vyer över time series-data. Du kan utöka den här metoden med hjälp av spark streaming eller en annan stream registerförare.


## <a name="sql-api"></a>API för SQL

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Hur börjar jag utveckla mot SQL-API: et?
Du måste först registrera dig för en Azure-prenumeration. När du registrerar dig för en Azure-prenumeration kan du lägga till en SQL API-behållare på Azure-prenumerationen. Anvisningar för att lägga till ett Azure Cosmos DB-konto finns i [skapa ett Azure Cosmos DB-databaskonto](create-sql-api-dotnet.md#create-account). 

[SDK:er](sql-api-sdk-dotnet.md) är tillgängliga för .NET, Python, Node.js, JavaScript och Java. Utvecklare kan också använda den [RESTful http-API: er](/rest/api/cosmos-db/) att interagera med Azure Cosmos DB-resurser från olika plattformar och språk.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Får jag åtkomst till några färdiga exempel för att komma igång?
Exempel för SQL-API: T [.NET](sql-api-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](sql-api-nodejs-samples.md), och [Python](sql-api-python-samples.md) SDK: er finns på GitHub.


### <a name="does-the-sql-api-database-support-schema-free-data"></a>API för SQL-databas som har stöd för schemafria data?
Ja, SQL-API kan appar lagrar godtyckliga JSON-dokument utan schemadefinitioner eller tips. Data är omedelbart tillgängliga för frågor via Azure Cosmos DB SQL-gränssnitt.  

### <a name="does-the-sql-api-support-acid-transactions"></a>Stöder SQL API ACID-transaktioner?
Ja, SQL-API: et stöder transaktioner mellan dokument uttryckta som JavaScript-lagrade procedurer och utlösare. Transaktioner är begränsade till en enda partition inom varje behållare och utförs med ACID-semantik som ”allt eller inget”, isolerade från andra kod och andra användarförfrågningar som körs samtidigt. Om undantag utlöses via serversidan körning av JavaScript-appkoden, återställs hela transaktionen. Mer information om transaktioner finns [databasen programtransaktionerna](programming.md#database-program-transactions).

### <a name="what-is-a-container"></a>Vad är en behållare?
En behållare är en uppsättning dokument och deras associerade JavaScript-programlogik. En behållare är en fakturerbar enhet där den [kostnaden](performance-levels.md) bestäms av dataflödet och används för lagring. Behållare kan sträcka sig över en eller flera partitioner eller servrar och kan skalas för att hantera praktiskt taget obegränsade volymer av lagring eller dataflöde. 

* För SQL- och MongoDB API-konton mappar en behållare till en samling. 
* För Cassandra och tabell-API-konton mappar en behållare till en tabell. 
* För Gremlin-API-konton mappar en behållare till ett diagram. 

Behållare är också faktureringsenheterna för Azure Cosmos DB. Varje behållare debiteras per timme, baserat på dataflöden och använt lagringsutrymme. Mer information finns i [priser för Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). 

### <a name="how-do-i-create-a-database"></a>Hur skapar jag en databas?
Du kan skapa databaser med hjälp av den [Azure-portalen](https://portal.azure.com), enligt beskrivningen i [Lägg till en samling](create-sql-api-dotnet.md#create-collection), någon av de [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md), eller [REST API: er](/rest/api/cosmos-db/). 

### <a name="how-do-i-set-up-users-and-permissions"></a>Hur ställer jag in användare och behörigheter?
Du kan skapa användare och behörigheter på något av de [Cosmos DB API SDK](sql-api-sdk-dotnet.md) eller [REST API: er](/rest/api/cosmos-db/).  

### <a name="does-the-sql-api-support-sql"></a>SQL-API som har stöd för SQL?
SQL-frågespråket som stöds av SQL API-konton är en förbättrad underuppsättning av de frågefunktioner som stöds av SQL Server. Azure Cosmos DB SQL-frågespråket innehåller omfattande operatorer för hierarkisk och relationsoperatorer och utvidgas via JavaScript-baserade, användardefinierade funktioner (UDF). JSON-grammatik gör det möjligt för att modellera JSON-dokument som träd med märkta noderna, vilket används av både Azure Cosmos DB automatiska indexeringstekniker och SQL-fråga dialekt av Azure Cosmos DB. Information om hur du använder SQL-grammatik finns i den [SQL-fråga] [ query] artikeln.

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>SQL-API som har stöd för SQL-aggregeringsfunktioner?
SQL-API: et stöder med låg latens aggregering i valfri skala via mängdfunktioner `COUNT`, `MIN`, `MAX`, `AVG`, och `SUM` via SQL-grammatik. Mer information finns i [mängdfunktioner](sql-api-sql-query.md#Aggregates).

### <a name="how-does-the-sql-api-provide-concurrency"></a>Hur tillhandahåller SQL API samtidighet?
SQL-API: et stöder optimistisk samtidighetskontroll (OCC) via HTTP-entitetstaggar eller ETags. Varje SQL API-resursen har en ETag och ETag som anges på servern varje gång ett dokument har uppdaterats. ETag-sidhuvud och det aktuella värdet ingår i alla svarsmeddelanden. ETags kan användas med If-Match-huvudet så att servern för att avgöra om en resurs ska uppdateras. If-Match-värdet är ETag-värdet som ska kontrolleras mot. Om ETag-värdet matchar serverns ETag-värdet, uppdateras resursen. Om en ETag är inte längre aktuell, avvisar servern igen med ett ”HTTP 412 Förutsättningsfel” svarskod. Klienten refetches sedan resurs för att hämta det aktuella ETag-värdet för resursen. ETags kan dessutom användas med If-None-Match-huvudet för att avgöra om en återigen hämta en resurs är nödvändigt.

Om du vill använda Optimistisk samtidighet i .NET, använda den [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) klass. Ett .NET-exempel finns [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) i DocumentManagement exemplet på GitHub.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Hur gör jag transaktioner i SQL-API: et?
SQL-API: et stöder språkintegrerade transaktioner via JavaScript-lagrade procedurer och utlösare. Alla databasåtgärder i skript körs under ögonblicksbildisolering. Om det är en samling för en partition är körningen begränsad till samlingen. Om samlingen är partitionerad är körningen begränsad till dokument med samma partitionsnyckel värde i samlingen. En ögonblicksbild av dokumentversionerna (ETags) tas i början av transaktionen och verkställs endast om skriptet lyckas. Om JavaScript genererar ett fel återställs transaktionen. Mer information finns i [programmering av serversidan JavaScript för Azure Cosmos DB](programming.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Hur kan jag-massinfogning dokument till Cosmos DB?
Du kan-massinfogning dokument till Azure Cosmos DB på något av följande sätt:

* Verktyget bulk-executor, enligt beskrivningen i [Using bulk executor .NET-biblioteket](bulk-executor-dot-net.md) och [Using bulk executor Java-bibliotek](bulk-executor-java.md)
* Datamigreringsverktyget som beskrivs i [verktyg för Azure Cosmos DB](import-data.md).
* Lagrade procedurer, enligt beskrivningen i [programmering av serversidan JavaScript för Azure Cosmos DB](programming.md).

### <a name="i-have-setup-my-container-to-use-lazy-indexing-i-see-that-my-queries-do-not-return-expected-results"></a>Jag har installationen min behållare att använda lazy indexering, visas att frågorna inte returnerar förväntade resultat. 
Enligt beskrivningen i avsnittet indexering kan lazy indexering resultera i det här beteendet. Du bör alltid använda konsekvent indexering för alla program. 


### <a name="does-the-sql-api-support-resource-link-caching"></a>Stöder de SQL API stöd för cachelagring av resurslänkar?
Ja, eftersom Azure Cosmos DB är en RESTful-tjänst, resurslänkar är oföränderliga och kan cachelagras. SQL API-klienter kan ange en ”If-None-Match”-rubrik för läsning mot valfri resurs-liknande dokument eller en samling och sedan uppdaterar sina lokala kopior när serverversionen har ändrats.

### <a name="is-a-local-instance-of-sql-api-available"></a>Är en lokal instans av SQL API tillgängligt?
Ja. Den [Azure Cosmos DB-emulatorn](local-emulator.md) ger en hifi-emulering av Cosmos DB-tjänsten. Den stöder funktioner som är identisk med Azure Cosmos DB, inklusive stöd för att skapa och fråga JSON-dokument, etablering och skalning av samlingar och köra lagrade procedurer och utlösare. Du kan utveckla och testa program med hjälp av Azure Cosmos DB-emulatorn och distribuera dem till Azure i global skala med en enkel konfigurationsändring till anslutningens slutpunkt för Azure Cosmos DB.

### <a name="why-are-long-floating-point-values-in-a-document-rounded-when-viewed-from-data-explorer-in-the-portal"></a>Varför är länge med värden i ett dokument som avrundas när de visas i datautforskaren i portalen. 
Detta är begränsning JavaScript-kod. JavaScript använder dubbel precision med formatera tal som anges i IEEE 754 och det kan på ett säkert sätt visa tal mellan-(253 - 1) och 253-1 (d.v.s. 9007199254740991) endast.

### <a name="where-are-permissions-allowed-in-the-object-hierarchy"></a>Där tillåts behörigheter i objekthierarkin?

Skapa behörigheter med hjälp av ResourceTokens tillåts på behållarenivån och dess underordnade (t.ex dokument, bifogade filer). Detta innebär att försök att skapa en behörighet på databasen eller kontonivå tillåts inte för närvarande.


## <a name="mongodb-api"></a>MongoDB-API
### <a name="what-is-the-azure-cosmos-db-api-for-mongodb"></a>Vad är Azure Cosmos DB API för MongoDB?
Azure Cosmos DB API för MongoDB är ett lager för kompatibilitet som gör att program kan enkelt och transparent kommunicerar med inbyggda Azure Cosmos DB-databasmotorn med hjälp av befintliga, community-stöds Apache MongoDB APIs och drivrutiner. Utvecklare kan nu använda kedjor med befintliga MongoDB-verktyg och kunskaper för att bygga program som drar nytta av Azure Cosmos DB. Utvecklare dra nytta av de unika funktionerna i Azure Cosmos DB, bland annat underhåll för automatisk indexering, säkerhetskopiering, ekonomisk servicenivåavtal (SLA) och så vidare.

### <a name="how-do-i-connect-to-my-api-for-mongodb-database"></a>Hur ansluter jag till min API för MongoDB-databas?
Det snabbaste sättet att ansluta till Azure Cosmos DB API för MongoDB är att gå över till den [Azure-portalen](https://portal.azure.com). Gå till ditt konto och klicka sedan på den vänstra navigeringsmenyn **Snabbstart**. Snabbstart är det bästa sättet att få kodfragment för att ansluta till databasen. 

Azure Cosmos DB tillämpar strikta säkerhetskrav och standarder. Azure Cosmos DB-konton kräver autentisering och säker kommunikation via SSL, så var noga med att använda TLSv1.2.

Mer information finns i [Anslut till ditt API för MongoDB-databas](connect-mongodb-account.md).

### <a name="are-there-additional-error-codes-for-an-api-for-mongodb-database"></a>Finns det ytterligare felkoder för ett API för MongoDB-databas?
Förutom MongoDB vanliga felkoder har MongoDB-API sin egen specifika felkoder:


| Fel               | Kod  | Beskrivning  | Lösning  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Det totala antalet enheter för programbegäran som förbrukas har överskridit den etablerade begäransenhet hastigheten för samlingen och har begränsats. | Överväg att skala dataflöde som tilldelats till en behållare eller en uppsättning behållare från Azure portal eller omförsök igen. |
| ExceededMemoryLimit | 16501 | Som en tjänst för flera innehavare överskred åtgärden klientens minne mängd. | Minska omfånget för åtgärd via mer restriktiva frågevillkor eller kontakta support från den [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br>Exempel:  *&nbsp; &nbsp; &nbsp; &nbsp;db.getCollection('users').aggregate ([<br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;{$match: {name: ”Andy”}}, <br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;{$sort: {ålder: -1} }<br>&nbsp;&nbsp;&nbsp;&nbsp;])*) |

### <a name="is-the-simba-driver-for-mongodb-supported-for-use-with-azure-cosmosdb-mongodb-api"></a>Är Simba drivrutinen för MongoDB som stöds för användning med Azure cosmos DB MongoDB API?
Ja, du kan använda Simba's Mongo ODBC-drivrutinen med Azure cosmos DB MongoDB API


## <a id="table"></a>Tabell-API

### <a name="how-can-i-use-the-table-api-offering"></a>Hur kan jag använda tabell-API-erbjudandet? 
Azure Cosmos DB Table API är tillgängligt i den [Azure-portalen][azure-portal]. Du måste först registrera dig för en Azure-prenumeration. När du har registrerat dig kan du lägga till ett Azure Cosmos DB Table API-konto i din Azure-prenumeration och sedan lägga till tabeller i ditt konto. 

Du kan hitta de språk som stöds och associerade snabbstarter för lösningar i den [introduktion till Azure Cosmos DB Table API](table-introduction.md).

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Behöver jag en ny SDK att använda tabell-API? 
Nej, befintliga storage SDK: er ska fungera. Men vi rekommenderar att man alltid får de senaste SDK: er för bästa supporten och i många fall överlägsna prestanda. Se en lista över tillgängliga språk i den [introduktion till Azure Cosmos DB Table API](table-introduction.md).

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Om tabell-API inte är identiska med Azure Table storage beteende?
Det finns vissa skillnader i beteende som användare som kommer från Azure-tabellagring och som vill skapa tabeller med Azure Cosmos DB Table API bör vara medveten om:

* Azure Cosmos DB Table API använder en modell med reserverad kapacitet för att säkerställa garanterade prestanda men det innebär att någon betalar för kapaciteten när tabellen har skapats, även om kapaciteten som inte används. Med Azure Table storage betalar en bara för kapacitet som används. Detta är för att förklara varför tabell-API kan erbjuda en 10 ms läsa och 15 ms skriva SLA 99: e percentilen medan Azure Table storage erbjuder ett serviceavtal på 10 sekunder. Men det betyder med tabell-API-tabeller, även tomma tabeller utan alla begäranden, kostnad pengar för att säkerställa att kapaciteten är tillgänglig för att hantera alla begäranden till dem i serviceavtalet erbjuds av Azure Cosmos DB.
* Frågeresultat som returneras av tabell-API sorteras inte i nyckelordning för partition och radnyckel som de är i Azure Table storage.
* Radnycklar får bara innehålla upp till 255 byte
* Batchar får bara innehålla upp till 2 MB
* CORS stöds inte för närvarande
* Tabellnamn i Azure Table storage är inte skiftlägeskänsliga, men de finns i Azure Cosmos DB Table API
* Vissa av Azure Cosmos DB-internt format kodning information, till exempel binära fält, är för närvarande inte lika effektiva som en kanske gillar. Det kan därför orsaka oväntade begränsningar för datastorlek. Till exempel använder för närvarande en kunde inte fullständig en Meg för en tabellentitet för att lagra binär data eftersom den kodning ökar storleken på data.
* Entitetsnamn egenskapen ”Id” för närvarande inte
* TableQuery TakeCount är inte begränsat till 1000

Det finns ett antal slutpunkter/frågealternativ som inte stöds av Azure Cosmos DB Table API när det gäller REST-API:
| REST-metoder | REST-slutpunkt/frågealternativet | URL: er för dokument | Förklaring |
| ------------| ------------- | ---------- | ----------- |
| HÄMTA, PLACERA | /? restype =service@comp= egenskaper| [Ange egenskaper för tabellen](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties) och [hämta egenskaper för tabell](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) | Den här slutpunkten används för att ange CORS-regler, lagringskonfiguration för analys och loggningsinställningar. CORS stöds inte för närvarande och analyser och loggning hanteras annorlunda i Azure Cosmos DB än Azure Storage-tabeller |
| ALTERNATIV | / < table-resource-name > | [Tabell före flygning CORS-förfrågan](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) | Det här är en del av CORS som Azure Cosmos DB inte stöder för närvarande. |
| HÄMTA | /? restype =service@comp= stats | [Hämta statistik för tjänsten i tabellen](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) | Innehåller information hur snabbt data replikeras mellan primära och sekundära databaser. Detta är inte nödvändigt i Cosmos DB som replikeringen är en del av skrivningar. |
| HÄMTA, PLACERA | /mytable? comp = acl | [Hämta ACL-tabellen](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) och [ange tabellen ACL](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) | Detta hämtar och anger de lagrade åtkomstprinciper som används för att hantera signaturer för delad åtkomst (SAS). Även om SAS stöds kan de ställs och hanteras annorlunda. |

Azure Cosmos DB Table API stöder dessutom endast JSON-format, inte ATOM.

Medan Azure Cosmos DB har stöd för signaturer för delad åtkomst (SAS) det finns vissa principer som det inte stöder, särskilt de som är relaterade till hanteringsåtgärder, till exempel behörighet att skapa nya tabeller.

För .NET SDK framför allt, finns det vissa klasser och metoder som Azure Cosmos DB inte stöder för närvarande.

| Klass | Metod som inte stöds |
|-------|-------- |
| CloudTableClient | \*ServiceProperties * |
|                  | \*ServiceStats * |
| CloudTable | Behörighetsgruppbehörighet * |
|            | GetPermissions * |
| TableServiceContext | * (är den här klassen föråldrad) |
| TableServiceEntity | " " |
| TableServiceExtensions | " " |
| TableServiceQuery | " " |

Om någon av dessa skillnader är ett problem för ditt projekt kan du kontakta [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com) och berätta för oss.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Hur ger jag feedback om SDK eller buggar?
Du kan dela din feedback i något av följande sätt:

* [Feedback](https://feedback.azure.com/forums/263030-azure-cosmos-db)
* [MSDN-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stackspill](http://stackoverflow.com/questions/tagged/azure-cosmosdb). Stack Overflow är bäst för frågor om programmering. Kontrollera att din fråga är [på avsnittet](https://stackoverflow.com/help/on-topic) och [innehåller samma information som möjligt, vilket gör frågan tydlig och besvaras](https://stackoverflow.com/help/how-to-ask).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Vad är den anslutningssträng som jag vill använda för att ansluta till tabell-API?
Anslutningssträngen är:
```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```
Du kan hämta anslutningssträngen från sidan anslutningssträng i Azure-portalen. 

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Hur åsidosätter delaktig för begäran alternativen i .NET SDK för tabell-API?
Läs om hur delaktig [funktioner för Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Vissa inställningar hanteras på CreateCloudTableClient metod och andra via app.config i avsnittet appSettings i klientprogrammet.

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Finns det några ändringar för kunder som använder den befintliga Azure Table storage SDK: er?
Ingen. Det finns inga ändringar för befintliga eller nya kunder som använder den befintliga Azure Table storage SDK: er. 

### <a name="how-do-i-view-table-data-that-is-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Hur gör jag för att visa data från tabeller som lagras i Azure Cosmos DB för användning med tabell-API? 
Du kan använda Azure-portalen för att söka data. Du kan också använda tabell-API-koden eller verktyg som nämns i nästa svar. 

### <a name="which-tools-work-with-the-table-api"></a>Vilka verktyg fungerar med tabell-API? 
Du kan använda den [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

Verktyg med flexibiliteten att kunna ta en anslutningssträng i det format som anges har tidigare stöd för nya tabell-API. En lista över Tabellverktyg tillhandahålls på den [Azure Storage-klientverktyg](../storage/common/storage-explorers.md) sidan. 

### <a name="is-the-concurrency-on-operations-controlled"></a>Är samtidigheten på åtgärder som kontrolleras?
Ja, tillhandahålls Optimistisk samtidighet med hjälp av mekanismen för ETag. 

### <a name="is-the-odata-query-model-supported-for-entities"></a>Stöds OData-frågemodell för entiteter? 
Ja, tabell-API har stöd för OData-fråge- och LINQ-frågan. 

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Kan jag ansluta till Azure Table Storage och Azure Cosmos DB Table API sida vid sida i samma program? 
Ja, du kan ansluta genom att skapa två separata instanser av CloudTableClient varje som pekar på sin egen URI via anslutningssträngen.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Hur migrerar jag ett befintligt Azure Table storage-program till det här erbjudandet?
[AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) och [Migreringsverktyget för Azure Cosmos DB Data](import-data.md) är båda stöds.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Hur är utökning av lagringsutrymmet görs för den här tjänsten om du till exempel börjar jag med *n* GB data och Mina data kommer att växa till 1 TB över tid? 
Azure Cosmos DB är utformad att ge obegränsad lagring med hjälp av horisontell skalning. Tjänsten kan övervaka och effektivt öka din lagring. 

### <a name="how-do-i-monitor-the-table-api-offering"></a>Hur övervakar jag tabell-API-erbjudandet?
Du kan använda tabell-API **mått** fönstret för att övervaka förfrågningar och lagringsanvändning. 

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Hur jag för att beräkna det dataflöde som jag behöver?
Du kan använda kapacitet kostnadsuppskattning för att beräkna TableThroughput som krävs för åtgärderna. Mer information finns i [uppskattning programbegäran och datalagring](https://www.documentdb.com/capacityplanner). Du kan i allmänhet representerar din entitet som JSON och ange siffrorna för din verksamhet. 

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Kan jag använda SDK: N för tabell-API lokalt med emulatorn?
Inte just nu.

### <a name="can-my-existing-application-work-with-the-table-api"></a>Fungerar mitt befintliga program med tabell-API? 
Ja, stöds samma API.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-do-not-want-to-use-the-table-api-features"></a>Behöver jag migrera min befintliga Azure Table storage-program till SDK om jag inte vill använda funktioner för tabell-API?
Nej, du kan skapa och använda befintliga Azure Table storage-resurser utan avbrott av något slag. Om du inte använder tabell-API, kan inte du dra nytta av automatisk indexet, ytterligare konsekvens alternativet eller global distribution. 

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-multiple-regions-of-azure"></a>Hur lägger jag till replikeringen av data i tabell-API i flera Azure-regioner?
Du kan använda Azure Cosmos DB-portalen [globala replikeringsinställningar](tutorial-global-distribution-sql-api.md#portal) att lägga till regioner som är lämpliga för ditt program. Om du vill utveckla ett globalt distribuerade program, bör du också lägga till ditt program PreferredLocation uppgifterna till den lokala regionen för att tillhandahålla Läs svarstider. 

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Hur ändrar jag den primära skrivningsregionen för kontot i tabell-API?
Du kan använda fönstret Azure Cosmos DB global replikering portal för att lägga till en region och sedan växla över till den nödvändiga regionen. Anvisningar finns i [utveckla med Azure Cosmos DB-konton för flera regioner](high-availability.md). 

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Hur konfigurerar jag min önskade läsregioner för låg latens när jag distribuera Mina data? 
Använd PreferredLocation nyckeln i app.config-filen för att läsa från den lokala platsen. Tabell-API genererar ett fel om LocationMode har angetts för befintliga program. Ta bort den koden eftersom tabell-API hämtar informationen från filen app.config. Mer information finns i [funktioner för Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities).

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Hur ska jag tänka konsekvensnivåer i tabell-API? 
Azure Cosmos DB tillhandahåller väl motiverad avvägningarna mellan konsekvens, tillgänglighet och svarstid. Azure Cosmos DB erbjuder fem konsekvensnivåer för tabell-API för utvecklare, så du kan välja den rätta konsekvensmodellen på tabellnivå och göra enskilda förfrågningar vid fråga till data. När en klient ansluter ange det en konsekvensnivå. Du kan ändra nivån via argumentet consistencyLevel för CreateCloudTableClient. 

Tabell-API ger låg fördröjning läser med ”Läs dina egna skrivningar” med begränsad föråldring konsekvens som standard. Mer information finns i [konsekvensnivåer](consistency-levels.md). 

Som standard ger Azure Table storage stark konsekvens inom en region och eventuell konsekvens på sekundära platser. 

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Erbjuder mer konsekvensnivåer än Azure-tabellagring i Azure Cosmos DB Table API?
Ja, information om hur du dra nytta av distribuerade natur i Azure Cosmos DB finns i [konsekvensnivåer](consistency-levels.md). Eftersom garantier har angetts för konsekvensnivåerna och kan du använda dem med tillförsikt. Mer information finns i [funktioner för Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities).

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>När globala distributionen är aktiverat, hur lång tid tar det för att replikera data?
Azure Cosmos DB sparar data varaktigt i den lokala regionen och skickar data till andra regioner direkt på bara några millisekunder. Replikeringen är beroende av endast den fram och åter tid för datacentret. Läs mer om global distribution möjligheterna för Azure Cosmos DB i [Azure Cosmos DB: en globalt distribuerad databastjänst på Azure](distribute-data-globally.md).

### <a name="can-the-read-request-consistency-level-be-changed"></a>Kan konsekvensnivå läsbegäran ändras?
Med Azure Cosmos DB kan du ange konsekvensnivån på behållarenivån (på tabellen). Du kan ändra nivån genom att ange värdet för TableConsistencyLevel nyckel i app.config-filen med hjälp av .NET SDK. Möjliga värden är: stark, begränsad föråldring, Session, konsekventa Prefix och eventuell. Mer information finns i [data justerbara konsekvensnivåer i Azure Cosmos DB](consistency-levels.md). Viktiga tanken är att du inte kan ange begäran-konsekvens nivå på fler än inställningen för tabellen. Exempelvis kan ange du inte konsekvensnivå för tabellen på eventuell och begäran konsekvensnivå på starka. 

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Hur hanterar redundans tabell-API om en region slutar att fungera? 
Tabell-API använder Azure Cosmos DB globalt distribuerad plattform. Aktivera minst en mer region för kontot i Azure Cosmos DB-portalen för att säkerställa att ditt program kan tolerera fel datacenter-avbrott, [utveckla med Azure Cosmos DB-konton för flera regioner](high-availability.md). Du kan ange prioritet för regionen med hjälp av portalen [utveckla med Azure Cosmos DB-konton för flera regioner](high-availability.md). 

Du kan lägga till så många regioner som du vill använda för kontot och styr där det kan växlas över till genom att tillhandahålla en redundansprioritet. Naturligtvis för att använda databasen måste du ge det ett program för. När du gör detta får inte driftstopp för dina kunder. Den [senaste SDK för .NET-klient](table-sdk-dotnet.md) är automatisk värdskap men den andra SDK: er finns inte. Det vill säga identifieras den region som ligger nere och automatiskt växla över till det nya området.

### <a name="is-the-table-api-enabled-for-backups"></a>Tabell-API har aktiverats för säkerhetskopiering?
Ja, tabell-API utnyttjar plattformen i Azure Cosmos DB för säkerhetskopior. Säkerhetskopieringar görs automatiskt. Mer information finns i [Online säkerhetskopiering och återställning med Azure Cosmos DB](online-backup-and-restore.md).

 
### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>Tabell-API index alla attribut för en entitet som standard?
Ja, indexeras alla attribut för en entitet som standard. Mer information finns i [Azure Cosmos DB: Indexeringsprinciper](indexing-policies.md). 

### <a name="does-this-mean-i-do-not-have-to-create-multiple-indexes-to-satisfy-the-queries"></a>Betyder detta jag inte behöver skapa flera index för att uppfylla frågorna? 
Ja, Azure Cosmos DB Table API har automatisk indexering av alla attribut utan någon schemadefinition. Denna automatisering Frigör utvecklare fokusera på programmet i stället för skapande av index och hantering. Mer information finns i [Azure Cosmos DB: Indexeringsprinciper](indexing-policies.md).

### <a name="can-i-change-the-indexing-policy"></a>Kan jag ändra indexeringsprincipen?
Ja, du kan ändra indexprincip genom att tillhandahålla indexdefinitionen. Mer information finns i [funktioner för Azure Cosmos DB](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Du behöver att korrekt koda och escape-inställningarna. 

för icke - .NET SDK: erna indexeringsprincipen kan bara ställas in i portalen på **Datautforskaren**, navigera till tabellen du vill ändra och gå sedan till den **skala och inställningar**Indexeringsprincip, -> Gör önskade ändringar och sedan **spara**.

Den kan från .NET SDK skickas i filen app.config:
```
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

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Det verkar som om Azure Cosmos DB som en plattform har många funktioner, till exempel sortering, samlingar, hierarkin och andra funktioner. Du lägger till de här funktionerna till tabell-API? 
Tabell-API ger samma frågefunktion som Azure Table storage. Azure Cosmos DB stöder också sortering, samlingar, geospatiala frågor, hierarki och en mängd inbyggda funktioner. Vi ger ytterligare funktioner i tabell-API i en framtida tjänstuppdatering. Mer information finns i [SQL-frågor](sql-api-sql-query.md).
 
### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>När bör jag ändra TableThroughput för tabell-API?
Du bör ändra TableThroughput när något av följande villkor gäller:
* Du utför en extrahering, transformering och laddning (ETL) av data eller ladda upp stora mängder data på kort tid. 
* Du behöver större dataflöde från behållaren eller från en uppsättning behållare i serverdelen. Exempelvis kan se du att dataflöden som används är mer än det etablerade dataflödet och du komma begränsas. Mer information finns i [ange dataflöde för Azure Cosmos DB-behållare](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Kan jag skala upp eller skala ned dataflödet för min tabell-API-tabellen? 
Ja, du kan använda Azure Cosmos DB-portalen skala fönstret för att skala dataflödet. Mer information finns i [ange dataflöde](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Är en standarduppsättning TableThroughput för nyetablerade tabeller?
Ja, om du inte åsidosätter TableThroughput via app.config och Använd inte en förskapad behållare i Azure Cosmos DB, tjänsten skapar en tabell med dataflöde på 400.
 
### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Finns det några ändringar av prissättningen för befintliga kunder med Azure Table storage-tjänsten?
Ingen. Det finns ingen ändring i priset för befintliga Azure Table storage-kunder. 

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Hur beräknas priset för tabell-API? 
Priset beror på den allokerade TableThroughput. 

### <a name="how-do-i-handle-any-rate-limiting-on-the-tables-in-table-api-offering"></a>Hur ska jag hantera eventuella hastighetsbegränsning på tabellerna i tabell-API-erbjudandet? 
Om den begäran överskrider kapaciteten för det etablerade dataflödet för den underliggande behållaren eller en uppsättning behållare, du får ett felmeddelande och SDK: N försöker anropet genom att tillämpa återförsöksprincipen.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Varför behöver jag väljer en genomströmning förutom PartitionKey och RowKey för att dra nytta av i tabell-API-versionen av Azure Cosmos DB?
Azure Cosmos DB anger en standard-genomströmning för din behållare om du inte anger något i filen app.config eller via portalen. 

Azure Cosmos DB erbjuder garantier för prestanda och svarstid med övre gränser för åtgärden. Garantin är möjligt om motorn kan tillämpa styrningsåtgärder på klientens åtgärder. Ange TableThroughput säkerställer att du får garanterat dataflöde och svarstid, eftersom plattformen förbehåller sig den här kapaciteten och garanterar operativa lyckades. 

Med specifikationen dataflöde kan ändra du Elastiskt det om du vill dra nytta av säsongsberoende ditt program efter dataflödesbehov och sänka kostnaderna.

### <a name="azure-table-storage-has-been-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-have-not-performed-a-single-transaction-or-stored-anything-can-you-please-explain"></a>Azure Table storage har billigt mig, eftersom jag betalar endast att lagra data och jag sällan fråga. Azure Cosmos DB Table API-erbjudandet verkar debitera mig även om jag inte har utförts av en enda transaktion eller lagras allt. Kan du förklara?

Azure Cosmos DB är avsett att vara en globalt distribuerad, SLA-baserade system med garantier för tillgänglighet, svarstid och dataflöde. När du reserverar dataflöde i Azure Cosmos DB är det säkert, till skillnad från dataflödet för andra system. Azure Cosmos DB erbjuder ytterligare funktioner som kunder har begärt, till exempel sekundära index och global distribution.  

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Jag får aldrig en kvot för fullständig ”meddelande (som anger att en partition är full) när jag mata in data i Azure Table storage. Jag får det här meddelandet med tabell-API. Är detta erbjudande begränsar mig och mig att ändra Mina befintliga program?

Azure Cosmos DB är en SLA-baserade system som ger obegränsad skala med garantier för svarstid, dataflöde, tillgänglighet och konsekvens. Kontrollera att datastorlek och index är hanterbara och skalbar för att säkerställa garanterad premium-prestanda. 10 GB-gränsen för antalet entiteter eller poster per partitionsnyckel är att säkerställa att vi ger utmärkt prestanda för sökning och fråga. För att säkerställa att ditt program skalas, även för Azure Storage, rekommenderar vi att du *inte* skapa en frekvent partition genom att lagra all information i en partition och direkta frågor. 

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Så PartitionKey och RowKey krävs fortfarande med tabell-API? 
Ja. Eftersom ytan på tabell-API är liknande den i Azure-tabellagring SDK innehåller Partitionsnyckeln ett effektivt sätt att distribuera data. Radnyckeln är unikt i partitionen. Radnyckel måste finnas och får inte vara null som standard SDK. Längden på RowKey är 255 byte och längden på PartitionKey är 1 KB. 

### <a name="what-are-the-error-messages-for-the-table-api"></a>Vad är felmeddelanden för tabell-API?
Azure Table storage och Azure Cosmos DB Table API kan du använda samma SDK: erna så att de flesta felen är samma.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Varför jag begränsas när jag försöker skapa många tabeller efter varandra i tabell-API?
Azure Cosmos DB är en SLA-baserade system som ger svarstid, dataflöde, tillgänglighet och konsekvensgarantier. Eftersom det är en etablerad system, reserverar resurser för att garantera att dessa krav. Snabb frekvensen för skapande av tabellerna har identifierats och begränsas. Vi rekommenderar att du tittar på frekvensen för skapandet av tabeller och sänka den till mindre än 5 per minut. Kom ihåg att tabell-API är ett etablerade system. Den tidpunkt då du etablerar den, kommer du börja betala för den. 

## <a name="gremlin-api"></a>Gremlin-API

### <a name="for-cnet-development-should-i-use-the-microsoftazuregraphs-package-or-gremlinnet"></a>För C# / .NET-utveckling ska jag använda Microsoft.Azure.Graphs paketet eller Gremlin.NET? 

Azure Cosmos DB Gremlin-API använder open source-drivrutiner som de huvudsakliga kopplingarna för tjänsten. Så det rekommenderade alternativet är att använda [drivrutiner som stöds av Apache Tinkerpop](http://tinkerpop.apache.org/).

### <a name="how-are-rus-charged-when-running-queries-on-a-graph-database"></a>Hur debiteras RU/s när du kör frågor på en grafdatabas? 

Alla grafobjekt hörn och kanter, representeras som JSON-dokument i serverdelen. Eftersom en Gremlin-fråga kan ändra en eller flera diagram objekt i taget, relaterade den kostnad som hör till den direkt till objekt, kanter som bearbetas av frågan. Det här är samma process som använder Azure Cosmos DB för alla andra API: er. Mer information finns i [ru i Azure Cosmos DB](request-units.md).

RU-kostnad är baserad på data arbetsminnet för övergången och för resultatuppsättningen inte. Till exempel om en fråga syftar till att hämta en enskild brytpunkt därmed men måste passera flera objekt på sättet, baseras sedan kostnaden på alla graph-objekt som det tar för att beräkna ett resultat hörn.

### <a name="whats-the-maximum-scale-that-a-graph-database-can-have-in-azure-cosmos-db-gremlin-api"></a>Vad är maximal skala som en grafdatabas kan ha i Azure Cosmos DB Gremlin API? 

Azure Cosmos DB använder [horisontell partitionering](partition-data.md) automatiskt adress ökning krav för lagring och dataflöde. Maxkapacitet för dataflöde och lagring på en arbetsbelastning bestäms av antalet partitioner som är associerade med en viss samling. En samling med Gremlin-API har dock en specifik uppsättning riktlinjer för att säkerställa en korrekt prestandaupplevelse i stor skala. Mer information och bästa praxis, se [metodtipsen för partitionering](partition-data.md#best-practices-when-choosing-a-partition-key) dokumentet. 

### <a name="how-can-i-protect-against-injection-attacks-using-gremlin-drivers"></a>Hur kan jag skydda mot inmatningsattacker med hjälp av Gremlin drivrutiner? 

De flesta interna Tinkerpop Gremlin-drivrutiner kan alternativet för att skapa en ordlista med parametrar för frågekörning. Det här är ett exempel på hur du gör i [Gremlin.Net](http://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet) och i [Gremlin-Javascript](https://github.com/Azure-Samples/azure-cosmos-db-graph-nodejs-getting-started/blob/master/app.js).

### <a name="why-am-i-getting-the-gremlin-query-compilation-error-unable-to-find-any-method-error"></a>Varför får jag det ”Gremlin Frågekompileringsfel: Det gick inte att hitta någon av metoderna” fel?

Azure Cosmos DB Gremlin API implementerar en deluppsättning av de funktioner som definierats i Gremlin ytan. Stöds anvisningar och mer information finns i [Gremlin-support](gremlin-support.md) artikeln.

Den bästa lösningen är att skriva om steg som krävs Gremlin med funktioner som stöds eftersom alla viktiga Gremlin-steg som stöds av Azure Cosmos DB.

### <a name="why-am-i-getting-the-websocketexception-the-server-returned-status-code-200-when-status-code-101-was-expected-error"></a>Varför får jag det ”WebSocketException: servern returnerade statuskoden” 200 ”när statuskoden” 101' förväntades ”fel?

Det här felet returneras sannolikt när fel slutpunkten som används. Den slutpunkt som genererar det här felet har följande mönster:

`https:// YOUR_DATABASE_ACCOUNT.documents.azure.com:443/` 

Är detta dokument-slutpunkten för graph-databasen.  Du använder rätt slutpunkt är Gremlin-slutpunkt, vilket har följande format: 

`https://YOUR_DATABASE_ACCOUNT.gremlin.cosmosdb.azure.com:443/`

### <a name="why-am-i-getting-the-requestrateistoolarge-error"></a>Varför får jag ”RequestRateIsTooLarge”-fel?

Detta fel innebär att den allokerade programbegäran per sekund inte är tillräckligt för att hantera frågan. Det här felet visas vanligtvis när du kör en fråga som hämtar alla hörn:

```
// Query example:
g.V()
```

Den här frågan kommer att försöka hämta alla hörnen från diagrammet. Kostnaden för den här frågan kommer därför vara lika med minst antal hörn när det gäller ru: er. RU/s-inställningen ska justeras för att åtgärda den här frågan.

### <a name="why-do-my-gremlin-driver-connections-get-dropped-eventually"></a>Varför min Gremlin-drivrutinen anslutningar tas bort så småningom?

En Gremlin-anslutning görs via en WebSocket-anslutning. Även om WebSocket-anslutningar inte har en viss tid TTL-värde, avslutas inaktiva anslutningar i Azure Cosmos DB Gremlin API efter 30 minuter av inaktivitet. 

### <a name="why-cant-i-use-fluent-api-calls-in-the-native-gremlin-drivers"></a>Varför kan jag använda fluent-API-anrop i de interna Gremlin-drivrutinerna?

Fluent-API-anrop ännu stöds inte av Azure Cosmos DB Gremlin-API. Fluent-API-anrop kräver en intern formatering funktion som kallas bytecode stöd som för närvarande inte stöds av Azure Cosmos DB Gremlin API. På grund av samma anledning stöds den senaste Gremlin-JavaScript-drivrutinen för närvarande inte heller. 

### <a name="how-can-i-evaluate-the-efficiency-of-my-gremlin-queries"></a>Hur kan jag utvärdera effektiviteten för min Gremlin-frågor?

Den **executionProfile()** förhandsversion steg kan användas för att tillhandahålla en analys av frågeplan för körning. Det här steget måste du lägga till i slutet av en Gremlin-fråga som du ser i följande exempel:

**Exempel på sökfråga**

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
            "partitionsAccessed": 1,
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

Utdata från profilen ovan visar hur mycket tid det tar att hörn och Edge-objekt, samt storleken på data arbetsminne. Detta är relaterade till mått som standard kostnaden för Azure Cosmos DB-frågor.

## <a id="cassandra"></a> API för Cassandra

### <a name="what-is-the-protocol-version-supported-in-the-private-preview-is-there-a-plan-to-support-other-protocols"></a>Vad är protokoll-version som stöds i den privata förhandsgranskningen? Finns det en plan för att stödja andra protokoll?
Apache Cassandra API för Azure Cosmos DB stöder idag CQL version 4. Om du har feedback om stöd för andra protokoll, berätta för oss [user voice-feedback](https://feedback.azure.com/forums/263030-azure-cosmos-db) eller skicka ett e- [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com). 

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Anledningen är att välja ett dataflöde för en tabell ett krav?
Azure Cosmos DB anger standarddataflödet för din behållare baserat på där du skapar tabellen från - portalen eller CQL. Azure Cosmos DB erbjuder garantier för prestanda och svarstid med övre gränser för åtgärden. Garantin är möjligt om motorn kan tillämpa styrningsåtgärder på klientens åtgärder. Inställningen dataflöde säkerställer att du får garanterat dataflöde och svarstid, eftersom plattformen förbehåller sig den här kapaciteten och garanterar åtgärden har genomförts. Du kan Elastiskt ändra genomflöde och kan dra nytta av säsongsberoende ditt program och sänka kostnaderna.

Dataflöde konceptet förklaras i den [ru i Azure Cosmos DB](request-units.md) artikeln. Dataflödet för en tabell fördelas jämnt över de underliggande fysiska partitionerna.  

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>Vad är standard RU/s i tabellen när skapats via CQL? Vad händer om jag behöver ändra den?
Azure Cosmos DB använder begäransenheter per sekund (RU/s) som en valuta för att tillhandahålla dataflöde. Tabeller som skapats via CQL har 400 RU. Du kan ändra den mediereserverade enheten från portalen. 

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

### <a name="what-happens-when-throughput-is-exceeded"></a>Vad händer när dataflödet har överskridits? 
Azure Cosmos DB erbjuder garantier för prestanda och svarstid med övre gränser för åtgärden. Garantin är möjligt om motorn kan tillämpa styrningsåtgärder på klientens åtgärder. Detta är möjligt baserat på inställningen dataflöde, vilket säkerställer att du får garanterat dataflöde och svarstid, eftersom plattformen förbehåller sig den här kapaciteten och garanterar åtgärden har genomförts. Du får när du överskrider den här kapaciteten är överbelastad felmeddelande som anger din kapacitet har överskridits. 0x1001 överbelastad: begäran kan inte bearbetas eftersom ”förfrågan är stor”. I detta läge är det viktigt att se vilka åtgärder och volym gör det här problemet. Du kan få en uppfattning om förbrukade kapacitet som överstiger etablerad kapacitet med mått på portalen. Måste du se till att kapaciteten används nästan lika för alla underliggande partitioner. Om du ser de flesta av dataflödet som förbrukas av en partition, har du skeva av arbetsbelastning. 

Mått är tillgängliga som visar dig hur dataflöde används över timmar, dagar, och per sju dagar mellan partitioner eller aggregering. Mer information finns i [övervakning och felsöka med mått i Azure Cosmos DB](use-metrics.md).

Diagnostikloggar beskrivs i den [Diagnostisk loggning för Azure Cosmos DB](logging.md) artikeln.

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>Stöder primära nyckel kartan för att partitionen viktiga konceptet med Azure Cosmos DB?
Ja, Partitionsnyckeln används för att placera entiteten på rätt plats. I Azure Cosmos DB används det för att hitta rätt logisk partition som lagras på en fysisk partition. Partitionering konceptet bra förklaras i den [partitionera och skala i Azure Cosmos DB](partition-data.md) artikeln. Grundläggande ta är bort här att en logisk partition inte bör överskrider gränsen på 10 GB i dag. 

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>Vad händer när jag får en fullständig kvot ”meddelande som anger att en partition är full?
Azure Cosmos DB är ett SLA-baserade system som ger obegränsad skala med garantier för svarstid, dataflöde, tillgänglighet och konsekvens. Den här obegränsad lagring baseras på horisontell skalning av data med hjälp av partitionering som viktiga begrepp. Partitionering konceptet bra förklaras i den [partitionera och skala i Azure Cosmos DB](partition-data.md) artikeln.

10 GB-gränsen för antalet entiteter eller objekt per logisk partition du bör följa. För att säkerställa att ditt program goda, rekommenderar vi att du *inte* skapa en frekvent partition genom att lagra all information i en partition och direkta frågor. Det här felet kan endast komma om dina data är förvrängd - som du har stora mängder data för en partitionsnyckel – det vill säga mer än 10 GB. Du kan hitta fördelningen av data med hjälp av storage-portalen. Sättet att åtgärda det här felet är att återskapa tabellen och välja en detaljerad primär (partitionsnyckel), vilket gör att bättre fördelning av data.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>Är det möjligt att använda Cassandra-API som nyckelvärde med flera miljoner eller miljarder enskilda partitionsnycklar?
Azure Cosmos DB kan lagra ett obegränsat antal data genom att skala ut lagringen. Det här är oberoende av dataflödet. Ja kan alltid bara använder Cassandra API för att lagra och hämta nyckel/värde genom att ange primära/partitionsnyckel. Nycklarna enskilda få sina egna logisk partition och finns på fysisk partition utan problem. 

### <a name="is-it-possible-to-create-multiple-tables-with-apache-cassandra-api-of-azure-cosmos-db"></a>Är det möjligt att skapa flera tabeller med Apache Cassandra API för Azure Cosmos DB?
Ja, det är möjligt att skapa flera tabeller med Apache Cassandra API. Var och en av tabellerna behandlas som enhet för dataflöde och lagring. 

### <a name="is-it-possible-to-create-multiple-tables-in-succession"></a>Är det möjligt att skapa flera tabeller i följd?
Azure Cosmos DB är resursstyrd system för både data och kontroll plan aktiviteter. Behållare som samlingar, tabeller är runtime-entiteter som har etablerats för angivna dataflödeskapacitet. Skapandet av de här behållarna i snabb följd är inte förväntade aktivitet och begränsas. Om du har tester som släpp/skapa tabeller direkt - försök att fördela dem.

### <a name="what-is-maximum-number-of-tables-that-can-be-created"></a>Vad är maximala antalet tabeller som kan skapas?
Det finns inga fysiska gränsen för antalet tabeller, skicka ett e-postmeddelande på [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) om du har många tabeller (där den totala stadig storleken överskrider 10 TB data) som måste skapas från vanliga 10s eller 100-tal. 

### <a name="what-is-the-maximum--of-keyspace-that-we-can-create"></a>Vad är det maximala antal keyspace som vi kan skapa? 
Det finns inga fysiska gränsen för antalet keyspaces som de är behållare för metadata, skicka ett e-postmeddelande på [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) om du har ett stort antal keyspaces av någon anledning. 

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>Är det möjligt att hämta stora mängder data när du har startat från normala tabellen? 
Lagringskapaciteten hanteras automatiskt och ökar när du push-överföra mer data. Så kan du tryggt importera så mycket information som du behöver utan att hantera och etablera noder och så vidare. 

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>Är det möjligt att ange inställningar för yaml-filen för att konfigurera beteendet för Apache Casssandra API för Azure Cosmos DB?
Apache Cassandra API för Azure Cosmos DB är en plattformstjänst. Det ger nivå protokollkompatibilitet för att köra åtgärder. Bort döljer komplexiteten i hantering, övervakning och konfiguration. Du behöver inte bekymra dig om tillgänglighet, tombstones, viktiga cache, rad cache, Blom filter och flera olika typer av andra inställningar som en utvecklare/användare. Azure Cosmos DB Apache Cassandra API fokuserar på att tillhandahålla läser och skriver prestanda du behöver utan att behöva konfiguration och hantering.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>Apache Cassandra API för Azure Cosmos DB stöder noden tillägg/kluster status/node status kommandon?
Apache Cassandra API är en plattformstjänst som gör kapacitetsplanering, svarar på flexibilitet när det gäller kraven för dataflöde och lagring enkelt. Med Azure Cosmos DB du etablera dataflöde, du behöver. Du kan sedan skala den upp och ned valfritt antal gånger via dagen utan att behöva bekymra dig om att lägga till/ta bort noder eller hantera dem. Detta innebär att du inte behöver använda nod, klusterhanteringsverktyg för. 

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>Vad händer med avseende på olika config-inställningar för att skapa en keyspace som enkel/nätverk?
Azure Cosmos DB tillhandahåller global distribution direkt ur lådan för tillgänglighet och låg latens orsaker. Du behöver inte installationsprogrammet repliker osv. Alla skrivåtgärder är alltid varaktigt kvorum som allokerat i en valfri region där du skriver samtidigt som prestanda.  

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gcgrace-compression-memtableflushperiod-etc"></a>Vad händer med avseende på olika inställningar för tabellmetadata som Blom filter, cachelagring, läsa reparera ändringar, gc_grace, komprimering memtable_flush_period osv.?
Azure Cosmos DB erbjuder prestanda för läsningar/skrivningar och dataflöde utan att behöva röra någon av konfigurationsinställningarna och ändra dem av misstag.  

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Stöds time to live (TTL) för Cassandra-tabeller? 
Ja, TTL stöds. 

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>Är det möjligt att övervaka nodstatus, replikens status, gc och OS-parametrar tidigare med olika verktyg? Vad som behöver övervakas nu?
Azure Cosmos DB är en plattformstjänst som hjälper dig att öka produktiviteten och inte oroa dig hantera och övervaka infrastruktur. Du behöver bara ta hand om dataflödet som är tillgänglig på portalen mått för att hitta om du är komma att begränsas och öka eller minska att dataflödet. Övervaka [serviceavtal](monitor-accounts.md).
Använd [mått](use-metrics.md) Använd [diagnostikloggar](logging.md).

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Vilken klient-SDK fungerar tillsammans med Apache Cassandra API för Azure Cosmos DB?
Drivrutiner som använder CQLv3 användes i privat förhandsversion Apache Cassandra SDK-klienten för klientprogram. Om du har andra drivrutiner som du använder eller om du får problem kan skicka e-post till [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com). 

### <a name="is-composite-partition-key-supported"></a>Det finns stöd för sammansatt partitionsnyckel
Ja, du kan använda vanliga syntax skapa sammansatta partitionsnyckel. 

### <a name="can-i-use-stable-loader-for-data-loading"></a>Kan jag använda stabil inläsaren för inläsning av data?
Nej, förhandsversionen stabil inläsaren stöds inte. 

### <a name="can-an-on-premises-cassandra-cluster-be-paired-with-azure-cosmos-dbs-apache-cassandra-api"></a>Kan en lokal cassandra-klustret användas ihop med Azure Cosmos DB Apache Cassandra API?
När finns Azure Cosmos DB har en optimerad upplevelse för molnmiljö utan omkostnader för åtgärder. Om du behöver länka kan du skicka e-post till [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) med en beskrivning av ditt scenario.

### <a name="does-cassandra-api-provide-full-backups"></a>Tillhandahåller Cassandra API och fullständiga säkerhetskopieringar? 
Azure Cosmos DB tillhandahåller två kostnadsfria fullständiga säkerhetskopieringar med fyra timmar intervall idag i alla API: er. Detta säkerställer att du inte behöver konfigurera ett schema för säkerhetskopiering osv. Om du vill ändra kvarhållning och frekvens, skicka ett e- [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) eller generera ett supportärende. Information om säkerhetskopiering finns i den [automatisk online säkerhetskopiering och återställning med Azure Cosmos DB](online-backup-and-restore.md) artikeln. 

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Hur hanterar redundans Cassandra API-konto om en region slutar att fungera? 
Azure Cosmos DB Cassandra API lånar från globalt distribuerade Azure Cosmos DB-plattformen. Aktivera minst en mer region för kontot i Azure Cosmos DB-portalen för att säkerställa att ditt program kan tolerera fel datacenter-avbrott, [utveckla med Azure Cosmos DB-konton för flera regioner](high-availability.md). Du kan ange prioritet för regionen med hjälp av portalen [utveckla med Azure Cosmos DB-konton för flera regioner](high-availability.md). 

Du kan lägga till så många regioner som du vill använda för kontot och styr där det kan växlas över till genom att tillhandahålla en redundansprioritet. Du måste ge det ett program för att använda databasen. När du gör detta får inte driftstopp för dina kunder. 

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Apache Cassandra API indexera alla attribut för en entitet som standard?
Ja, indexeras alla attribut för en entitet som standard av Azure Cosmos DB. Mer information finns i [Azure Cosmos DB: Indexeringsprinciper](indexing-policies.md). Du får fördelarna med garanterade prestanda med konsekvent indexering och varaktiga kvorum allokerat skriver alltid. 

### <a name="does-this-mean-i-do-not-have-to-create-multiple-indexes-to-satisfy-the-queries"></a>Betyder detta jag inte behöver skapa flera index för att uppfylla frågorna? 
Ja, Azure Cosmos DB tillhandahåller automatisk indexering av alla attribut utan någon schemadefinition. Denna automatisering Frigör utvecklare fokusera på programmet i stället för skapande av index och hantering. Mer information finns i [Azure Cosmos DB: Indexeringsprinciper](indexing-policies.md).

### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Kan jag använda det nya Cassandra API SDK lokalt med emulatorn?
Vi planerar att stödja den här funktionen i framtiden. 

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-changefeed-and-other-functionality-will-these-capabilities-be-added-to-the-cassandra-api"></a>Det verkar som om Azure Cosmos DB som en plattform har många funktioner, till exempel changefeed och andra funktioner. Dessa funktioner läggs till Cassandra-API? 
Apache Cassandra API innehåller samma CQL-funktioner som Apache Cassandra. Vi planerar att titta på synpunkt stöder olika funktioner i framtiden.

### <a name="feature-x-of-regular-cassandra-api-is-not-working-as-today-where-can-the-feedback-be-provided"></a>Funktionen x av regelbundna Cassandra-API fungerar inte som idag där kan feedbacken anges?
Ge feedback via [user voice-feedback](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md
