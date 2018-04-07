---
title: Vanliga frågor och svar om Azure Cosmos-DB | Microsoft Docs
description: Få svar på vanliga frågor och svar om Azure Cosmos DB, ett globalt distribuerade och flera olika modeller database-tjänsten. Läs mer om kapacitet, prestandanivåer och skalning.
keywords: Databasfrågor, vanliga frågor, documentdb, azure, Microsoft azure
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: b68d1831-35f9-443d-a0ac-dad0c89f245b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: sngun
ms.openlocfilehash: 0118e78ee7240c139ff808582d6b9b47c6b64b4b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="azure-cosmos-db-faq"></a>Vanliga frågor om Azure Cosmos DB
## <a name="azure-cosmos-db-fundamentals"></a>Azure DB Cosmos-grunderna
### <a name="what-is-azure-cosmos-db"></a>Vad är Azure Cosmos DB?
Azure Cosmos-DB är en tjänst för flera modell globalt replikerad databas som erbjuder många frågealternativ över schemafria data, hjälper till att leverera konfigurerbar och tillförlitlig prestanda och möjliggör snabb utveckling. Det är alla uppnås genom en hanterad plattform som backas upp av kraften och räckvidden hos Microsoft Azure. 

Azure Cosmos-DB är den rätta lösningen för webb-, mobil-, spel- och IoT-appar när förutsägbart dataflöde, hög tillgänglighet, låg latens och en schemafri datamodell är viktiga krav. Det ger schemaflexibilitet och omfattande indexering och innehåller transaktionellt stöd för flera dokument med integrerat JavaScript. 

Mer databasfrågor, svar och instruktioner för att distribuera och använda den här tjänsten, finns det [dokumentationssidan för Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/).

### <a name="what-happened-to-the-documentdb-api"></a>Vad hände med DocumentDB-API: et?

Azure Cosmos DB DocumentDB API eller SQL (DocumentDB) API är nu kallat Azure SQL DB-API Cosmos. Du behöver inte ändra någonting om du vill fortsätta att köra dina appar som utvecklats med DocumentDB-API. Funktionen är densamma.

Om du har ett DocumentDB-API-konto innan har du nu ett SQL-API-konto med faktureringen ändras inte. 

### <a name="what-happened-to-azure-documentdb-as-a-service"></a>Vad hände med Azure DocumentDB som en tjänst?

Azure DocumentDB-tjänsten är nu en del av tjänsten Azure Cosmos DB och visar sig i form av SQL-API. Program som skapats mot Azure DocumentDB körs mot Azure SQL DB-API Cosmos utan några ändringar. Dessutom stöder Azure Cosmos DB Graph API, tabell-API, MongoDB-API och Cassandra API (förhandsversion).

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Vad är vanliga användningsområden för Azure Cosmos DB?
Azure Cosmos-DB är ett bra alternativ för nya webb-, mobil-, spel- och IoT-appar där automatisk skalning, förutsägbar prestanda, snabba ordning med svarstider på millisekunder och möjlighet till frågor över schemafria data är viktigt. Azure Cosmos-DB lämpar sig för snabb utveckling och stöder kontinuerlig iteration av appens datamodeller. Program som hanterar innehåll som skapats av användare och data är [vanliga användningsområden för Azure Cosmos DB](use-cases.md). 

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Hur erbjuder Azure Cosmos DB förutsägbar prestanda?
En [begäran enhet](request-units.md) (RU) är ett mått på dataflödet i Azure Cosmos DB. En genomströmning 1 RU motsvarar dataflödet i GET av ett dokument på 1 KB. Varje åtgärd i Azure Cosmos DB, inklusive läsning, skrivning, SQL-frågor och lagrade procedurkörningar har en deterministisk RU-värde som är baserad på dataflödet som krävs för att slutföra åtgärden. I stället för att fundera på CPU, IO, och minne och hur de påverkar appens dataflöde tänka du på ett enda RU mått.

Du kan reservera varje Azure DB som Cosmos-behållare med etablerat dataflöde räknat RUs genomströmning per sekund. För appar oavsett skala, kan du jämföra enskilda förfrågningar för att mäta deras RU-värden och etablera en behållare för att hantera totalsumman av frågeenheter över alla förfrågningar. Du kan även skala upp eller ned din behållaren genomströmning som behov utvecklas. Mer information om frågeenheter och hjälp att fastställa din behållaren måste, se [uppskatta behov för genomströmning](request-units.md#estimating-throughput-needs) och försök den [genomströmning Kalkylatorn](https://www.documentdb.com/capacityplanner). Termen *behållare* här refererar till refererar till en SQL API samling, Graph API diagram, MongoDB API-samling och tabellen API-tabellen. 

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Hur stöder Azure Cosmos DB olika datamodeller som nyckel/värde, kolumner, dokument och diagrammet?

Nyckel/värde (tabellen)-kolumner, dokumentera och diagramdata modeller är alla inbyggt stöd på grund av ARS (atomer, poster och sekvenser) design som Azure Cosmos DB bygger på. Atomer, poster och sekvenser kan enkelt mappas och planerad till olika datamodeller. API: er för en delmängd av modeller är tillgängliga just nu (SQL, MongoDB, tabell och Graph API: er) och andra som är specifika för ytterligare datamodeller blir tillgängliga i framtiden.

Azure Cosmos-DB har ett schema oberoende indexering motor kan indexera automatiskt alla data som den en utan att något schema eller sekundärindex från utvecklaren. Motorn är beroende av en uppsättning logiska index layouter (inverterad, kolumner, träd) som frikopplar lagringslayout från index och frågebearbetning undersystem. Cosmos DB har också möjlighet att stödja en uppsättning överföring protokoll och API: er på ett utökningsbart sätt och effektivt översätta dem till datamodellen kärnor (1) och de logiska index layouter (2) vilket gör det unikt kan stödja flera datamodeller internt.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Är Azure Cosmos DB HIPAA kompatibel?
Ja, Azure Cosmos DB är HIPAA-kompatibelt. HIPAA fastställer kraven för användning, redovisning, och skydd av individuellt identifierbar hälsoinformation. Mer information finns i [Microsoft Trust Center](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Vad är Azure Cosmos DB Lagringsgränser?
Det finns ingen gräns för den totala mängden data som en behållare kan lagra i Azure Cosmos DB.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Vad är Azure Cosmos DB genomströmning gränser?
Det finns ingen gräns för totala genomflödet som en behållare kan stödja i Azure Cosmos DB. Viktiga idé är att distribuera din arbetsbelastning ungefär jämnt mellan ett tillräckligt stort antal partitionsnycklar.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Hur mycket kostar Azure Cosmos DB?
Mer information finns i den [Azure Cosmos DB prisinformation](https://azure.microsoft.com/pricing/details/cosmos-db/) sidan. Avgifterna för användning av Azure DB Cosmos bestäms av antalet etablerade behållare, antalet timmar behållarna var online, och etablerat dataflöde för varje behållare. Termen *behållare* här refererar till SQL API insamling, Graph API diagram, MongoDB API-samling och tabellen API-tabeller. 

### <a name="is-a-free-account-available"></a>Finns det ett kostnadsfritt konto?
Ja, kan du registrera dig för ett konto för tidsbegränsade utan kostnad, utan bindning. Om du vill registrera dig finns [försök Azure Cosmos-DB gratis](https://azure.microsoft.com/try/cosmosdb/) eller Läs mer i den [försök Azure Cosmos DB vanliga frågor och svar](#try-cosmos-db).

Om du är nybörjare på Azure, kan du registrera dig för en [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/), som ger dig 30 dagar och och ett kreditkort för att prova alla Azure-tjänster. Om du har en prenumeration på Visual Studio kan du också är berättigad till [kostnadsfria Azure-krediter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) att använda i valfria Azure-tjänster. 

Du kan också använda den [Azure Cosmos DB emulatorn](local-emulator.md) att utveckla och testa programmet lokalt för gratis, utan att skapa en Azure-prenumeration. När du är nöjd med hur programmet fungerar i Azure Cosmos DB-emulatorn kan växla du till med ett Azure DB som Cosmos-konto i molnet.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Hur kan jag få ytterligare hjälp med Azure Cosmos DB?

Om du vill ställa en teknisk fråga kan du i en av dessa två fråga och besvara forum:
* [MSDN-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stacken spill](http://stackoverflow.com/questions/tagged/azure-cosmosdb). Stackspill är bäst för programmering frågor. Se till att din fråga [på avsnittet](https://stackoverflow.com/help/on-topic) och [ange så många detaljer som möjligt, göra frågan tydliga och besvaras](https://stackoverflow.com/help/how-to-ask). 

Skapa en ny begäran om du vill begära nya funktioner på [Uservoice](https://feedback.azure.com/forums/263030-azure-cosmos-db).

Om du vill åtgärda ett problem med ditt konto, filen en [supportbegäran](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) i Azure-portalen.

Andra frågor kan skickas till teamet på [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com); men detta inte är ett alias för teknisk support. 

<a id="try-cosmos-db"></a>
## <a name="try-azure-cosmos-db-subscriptions"></a>Prova Azure Cosmos DB prenumerationer

Nu kan du få en tidsbegränsade Azure Cosmos DB upplevelse utan någon prenumeration gratis och åtaganden. Om du vill registrera dig för en försök Azure DB Cosmos-prenumeration går du till [försök Azure Cosmos-DB gratis](https://azure.microsoft.com/try/cosmosdb/). Den här prenumerationen är separat från den [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/free/), och kan användas utöver en kostnadsfri utvärderingsversion av Azure eller ett Azure betald prenumeration. 

Prova Azure Cosmos DB prenumerationer som visas i Azure portal bredvid andra prenumerationer som är associerade med ditt användar-ID. 

Följande villkor gäller för försök Azure Cosmos DB prenumerationer:

* En behållare per prenumeration för SQL, Gremlin (Graph API) och tabellen konton.
* Upp till 3 samlingar per prenumeration för MongoDB-konton.
* 10 GB lagringskapacitet.
* Globala replikering är tillgänglig i följande [Azure-regioner](https://azure.microsoft.com/regions/): centrala USA, Norra Europa och Asien/Stillahavsområdet, sydost
* Maximalt dataflöde på 5 K RU/s.
* Prenumerationer upphör efter 24 timmar och kan utökas till maximalt 48 timmar totalt.
* Azure supportärenden kan inte skapas för försök Azure Cosmos DB redovisning. däremot finns stöd för prenumeranter med befintliga supportplaner. 

## <a name="set-up-azure-cosmos-db"></a>Konfigurera Azure Cosmos DB
### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Hur loggar jag för Azure Cosmos DB?
Azure Cosmos-DB är tillgängliga i Azure-portalen. Först registrera dig för en Azure-prenumeration. När du har registrerat dig du lägger till en SQL-API, Graph API, tabell-API, MongoDB API eller Cassandra API-kontot för din Azure-prenumeration.

### <a name="what-is-a-master-key"></a>Vad är en huvudnyckel?
En huvudnyckel är en säkerhetstoken som ger åtkomst till alla resurser för ett konto. Personer med nyckeln har läs- och skrivbehörighet till alla resurser i databaskontot. Var försiktig när du distribuerar huvudnycklar. Master primärnyckeln och sekundärnyckeln master är tillgängliga på den **nycklar** bladet för den [Azure-portalen][azure-portal]. Mer information om nycklar finns i [Visa, kopiera och generera åtkomstnycklar på nytt](manage-account.md#keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Vilka är de regioner som PreferredLocations kan anges till? 
PreferredLocations-värdet kan anges till någon Azure-regioner där Cosmos DB är tillgänglig. En lista över tillgängliga regioner, se [Azure-regioner](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Finns det något jag bör vara medveten om när du distribuerar data över hela världen via Azure-Datacenter? 
Azure Cosmos-DB finns över alla Azure-regioner, som anges på den [Azure-regioner](https://azure.microsoft.com/regions/) sidan. Eftersom den är kärntjänsten har en Azure Cosmos DB förekomst varje ny datacenter. 

Kom ihåg att Azure Cosmos DB respekterar statliga och offentliga moln när du ställer in en region. Som är om du skapar ett konto i en suveräna region, kan du replikera utanför suveräna regionen. På samma sätt kan du aktivera replikering till andra suveräna platser från ett utanför konto. 

## <a name="develop-against-the-sql-api"></a>Utveckla mot SQL-API

### <a name="how-do-i-start-developing-against-the-sql-api"></a>Hur börjar jag utveckla mot SQL-API?
Du måste först registrera dig för en Azure-prenumeration. När du registrerar dig för en Azure-prenumeration kan du lägga till en SQL-API-behållare till din Azure-prenumeration. Anvisningar för att lägga till ett konto i Azure Cosmos DB finns [skapa ett databaskonto Azure Cosmos DB](create-sql-api-dotnet.md#create-account). 

[SDK:er](sql-api-sdk-dotnet.md) är tillgängliga för .NET, Python, Node.js, JavaScript och Java. Utvecklare kan också använda den [RESTful http-API: er](/rest/api/cosmos-db/) att interagera med Azure Cosmos DB resurser från olika plattformar och språk.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Kan jag använda vissa färdiga prov för att komma igång?
Exempel för SQL-API [.NET](sql-api-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](sql-api-nodejs-samples.md), och [Python](sql-api-python-samples.md) SDK finns på GitHub.


### <a name="does-the-sql-api-database-support-schema-free-data"></a>API för SQL-databas som har stöd för schemafria data?
Ja, tillåter SQL API appar lagrar godtyckliga JSON-dokument utan schemadefinitioner eller tips. Data är omedelbart tillgängligt för frågor via Azure Cosmos-Databasens SQL-gränssnitt.  

### <a name="does-the-sql-api-support-acid-transactions"></a>Stöder SQL-API ACID-transaktioner?
Ja, till SQL API stöder transaktioner mellan dokument uttryckta som JavaScript-lagrade procedurer och utlösare. Transaktioner är begränsade till en enda partition inom varje samling och utförs med ACID-semantik som ”allt eller inget”, isoleras från andra samtidigt köra kod och begäranden. Om undantag utlöses via server-sida-körning av JavaScript, återställs hela transaktionen. Mer information om transaktioner finns [databasen transaktioner automatiskt](programming.md#database-program-transactions).

### <a name="what-is-a-collection"></a>Vad är en samling?
En samling är en grupp av dokument och deras associerade JavaScript-programlogik. En samling är en fakturerbar enhet där den [kostnaden](performance-levels.md) bestäms av dataflödet och använt lagring. Samlingar kan omfatta en eller flera partitioner eller servrar och kan skalas för att hantera praktiskt taget obegränsade volymer av lagring eller dataflöde.

Samlingar är också faktureringsenheterna för Azure Cosmos DB. Varje samling faktureras timvis baserat på etablerat dataflöde och använt lagringsutrymme. Mer information finns i [priser för Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). 

### <a name="how-do-i-create-a-database"></a>Hur skapar jag en databas?
Du kan skapa databaser med hjälp av den [Azure-portalen](https://portal.azure.com), enligt beskrivningen i [lägger till en samling](create-sql-api-dotnet.md#create-collection), med en av de [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md), eller [REST API: er](/rest/api/cosmos-db/). 

### <a name="how-do-i-set-up-users-and-permissions"></a>Hur ställer jag in användare och behörigheter?
Du kan skapa användare och behörigheter genom att använda en av de [Cosmos DB API SDK](sql-api-sdk-dotnet.md) eller [REST API: er](/rest/api/cosmos-db/).  

### <a name="does-the-sql-api-support-sql"></a>Stöder SQL-API SQL?
SQL-frågespråket stöds av SQL-API-konton är en förbättrad underuppsättning av de frågefunktioner som stöds av SQL Server. Azure Cosmos-Databasens SQL-frågespråket ger omfattande hierarkiska och relationella operatorer och kan utvidgas via JavaScript-baserade, användardefinierade funktioner (UDF). JSON-grammatik gör det möjligt för att modellera JSON-dokument som träd med märkt noder som används av både Azure Cosmos DB tekniker för automatisk indexering och SQL-frågedialekt av Azure Cosmos DB. Information om hur du använder SQL-grammatik finns i [SQL-frågan] [ query] artikel.

### <a name="does-the-sql-api-support-sql-aggregation-functions"></a>Stöder SQL-API SQL aggregeringsfunktioner?
SQL-API: et stöder låg latens aggregering skaländras via mängdfunktioner `COUNT`, `MIN`, `MAX`, `AVG`, och `SUM` via SQL-grammatik. Mer information finns i [mängdfunktionerna](sql-api-sql-query.md#Aggregates).

### <a name="how-does-the-sql-api-provide-concurrency"></a>Hur tillhandahåller till SQL API samtidighet?
SQL-API: et stöder optimistisk samtidighetskontroll (OCC) via HTTP-entitetstaggar eller ETags. Varje SQL API-resurs har en ETag och ETag är inställd på servern varje gång ett dokument har uppdaterats. ETag-sidhuvud och det aktuella värdet ingår i alla svarsmeddelanden. ETags kan användas med If-Match-sidhuvudet så att servern för att avgöra om en resurs ska uppdateras. Värdet för If-Match är ETag-värde som ska kontrolleras mot. Om ETag-värde matchar serverns ETag-värde, uppdateras resursen. Om en ETag är inte längre aktuell, avvisar servern igen med ett ”HTTP 412 Förutsättningsfel” svarskoden. Klienten hämtar sedan nytt resurs för att få det aktuella ETag-värdet för resursen. ETags kan dessutom användas med If-None-Match-sidhuvudet för att avgöra om nytt hämta av en resurs som behövs.

Om du vill använda Optimistisk samtidighet i .NET den [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) klass. Ett .NET-exempel finns [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) i DocumentManagement prov på GitHub.

### <a name="how-do-i-perform-transactions-in-the-sql-api"></a>Hur gör jag transaktioner i SQL-API
SQL-API: et stöder språkintegrerade transaktioner via JavaScript-lagrade procedurer och utlösare. Alla databasåtgärder i skript körs under ögonblicksbildisolering. Om det är en enskild partition samling är körningen begränsad till samlingen. Om samlingen är partitionerad är körningen begränsad till dokument med samma partitionsnyckel värde i samlingen. En ögonblicksbild av dokumentversionerna (ETags) tas i början av transaktionen och verkställs endast om skriptet lyckas. Om JavaScript genererar ett fel återställs transaktionen. Mer information finns i [programmering av serversidan JavaScript för Azure Cosmos DB](programming.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Hur kan jag-massinfogning dokument i Cosmos DB?
Du kan-massinfogning dokument i Azure Cosmos DB på två sätt:

* Datamigreringsverktyget enligt beskrivningen i [verktyg för Azure Cosmos DB](import-data.md).
* Lagrade procedurer som beskrivs i [programmering av serversidan JavaScript för Azure Cosmos DB](programming.md).

### <a name="does-the-sql-api-support-resource-link-caching"></a>Stöder den SQL API stöd för cachelagring av resurslänkar?
Ja, eftersom Azure Cosmos DB är en RESTful-tjänst, resurslänkar är oföränderlig och kan cachelagras. SQL-API-klienter kan ange en ”If-None-Match”-rubrik för läsning mot ett resurs-liknande dokument eller en samling och sedan uppdatera sina lokala kopior när serverversionen har ändrats.

### <a name="is-a-local-instance-of-sql-api-available"></a>Är en lokal instans av SQL API tillgängligt?
Ja. Den [Azure Cosmos DB emulatorn](local-emulator.md) ger en hög återgivning emulering av Cosmos-DB-tjänsten. Den stöder funktioner som är identisk med Azure Cosmos DB, inklusive stöd för att skapa och hämtning av JSON-dokument, etablering och skalning samlingar och köra lagrade procedurer och utlösare. Du kan utveckla och testa program med hjälp av Azure Cosmos DB-emulatorn och distribuera dem till Azure på global nivå genom att göra en enda konfigurationen av anslutningens slutpunkt för Azure Cosmos DB.

## <a name="develop-against-the-api-for-mongodb"></a>Utveckla mot API: et för MongoDB
### <a name="what-is-the-azure-cosmos-db-api-for-mongodb"></a>Vad är Azure Cosmos DB API för MongoDB?
Azure Cosmos DB API för MongoDB är ett lager för kompatibilitet som gör att program kan enkelt och transparent kommunicera med interna Azure Cosmos DB databasmotorn via befintliga, community-stödda Apache MongoDB APIs och drivrutiner. Utvecklare kan nu använda befintliga MongoDB verktyget kedjor och kunskaper för att bygga program som utnyttjar Azure Cosmos DB. Utvecklare nytta av de unika funktionerna i Azure Cosmos DB, bland annat underhåll för automatisk indexering, säkerhetskopiering, säkerhetskopieras ekonomiskt servicenivåavtal (SLA) och så vidare.

### <a name="how-do-i-connect-to-my-api-for-mongodb-database"></a>Hur ansluter till min API för MongoDB-databas?
Det snabbaste sättet att ansluta till Azure Cosmos DB API för MongoDB är att head över till den [Azure-portalen](https://portal.azure.com). Gå till ditt konto och klicka sedan på den vänstra navigeringsmenyn **Snabbstart**. Snabbstart är det bästa sättet att hämta kodfragment för att ansluta till databasen. 

Azure Cosmos-DB tillämpar stränga säkerhetskrav och standarder. Azure DB Cosmos-konton kräver autentisering och säker kommunikation via SSL, så måste du använda TLSv1.2.

Mer information finns i [Anslut till ditt API för MongoDB databasen](connect-mongodb-account.md).

### <a name="are-there-additional-error-codes-for-an-api-for-mongodb-database"></a>Finns det ytterligare felkoder för en API för MongoDB-databas?
Förutom MongoDB vanliga felkoder har MongoDB-API: et sin egen specifika felkoder:


| Fel               | Kod  | Beskrivning  | Lösning  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Det totala antalet frågeenheter förbrukas har överskridit den etablerade begärt-enhet hastigheten för insamling och har begränsats. | Överväg att skalning genomflödet i samlingen från Azure-portalen eller du försöker igen. |
| ExceededMemoryLimit | 16501 | Som en tjänst med flera innehavare överskred åtgärden klientens minne tilldelning. | Minska omfånget för åtgärden via mer restriktiva frågevillkor eller kontakta support från den [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br>Exempel:  *&nbsp; &nbsp; &nbsp; &nbsp;db.getCollection('users').aggregate ([<br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;{$match: {namn: ”Anders”}}, <br> &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;{$sort: {ålder: -1} }<br>&nbsp;&nbsp;&nbsp;&nbsp;])*) |

## <a name="develop-with-the-table-api"></a>Utveckla med tabell-API

### <a name="how-can-i-use-the-table-api-offering"></a>Hur kan jag använda tabellen API-erbjudande? 
Azure Cosmos DB tabell API är tillgängliga i den [Azure-portalen][azure-portal]. Du måste först registrera dig för en Azure-prenumeration. När du har registrerat dig kan du lägga till ett Azure Cosmos DB tabell API-konto till din Azure-prenumeration och sedan lägga till tabeller i ditt konto. 

Du kan hitta de språk som stöds och associerade quick-startar i den [introduktion till Azure Cosmos DB tabell API](table-introduction.md).

### <a name="do-i-need-a-new-sdk-to-use-the-table-api"></a>Måste en ny SDK om du vill använda API: et för tabellen? 
Nej, befintlig lagring som SDK: er ska fungera. Men vi rekommenderar att en alltid hämtar de senaste SDK: er för bästa support och i många fall överlägsen prestanda. Visa en lista över tillgängliga språk i den [introduktion till Azure Cosmos DB tabell API](table-introduction.md).

### <a name="where-is-table-api-not-identical-with-azure-table-storage-behavior"></a>Om tabellen API inte är identiska med Azure Table storage beteende?
Det finns vissa skillnader i som användare som kommer från Azure Table storage och som vill skapa tabeller med Azure Cosmos DB tabell API bör vara medveten om:

* Azure Cosmos DB tabell API använder en modell för reserverad kapacitet för att säkerställa garanterad prestanda, men detta innebär att en betalar för kapacitet när tabellen har skapats, även om kapaciteten som inte används. Med Azure Table storage betalar en endast för kapacitet som faktiskt används. Detta hjälper dig att förklara varför tabell API kan erbjuda en 10 ms läsa och 15 ms skriva SLA vid 99th percentilen medan Azure Table storage erbjuder ett 10 andra SERVICENIVÅAVTAL. Men följaktligen med tabell-API-tabeller, även tomma tabeller utan några förfrågningar pengar kostnaden för att säkerställa att kapaciteten är tillgängliga för att hantera alla begäranden till dem i SLA erbjuds av Azure Cosmos DB.
* Frågeresultatet returneras av API: et för tabellen sorteras inte partition nyckel/key rad. som i Azure Table storage.
* Raden nycklar kan endast vara upp till 255 byte
* Batchar kan endast innehålla upp till 2 MB
* CORS stöds inte för närvarande
* Tabellnamn i Azure Table storage är inte skiftlägeskänsliga, men de finns i Azure Cosmos DB tabell API
* Vissa av Azure Cosmos DB internt format för kodning information, till exempel binära fält är inte så effektivt en kanske gillar. Därför kan det orsaka oväntade begränsningar på datastorlek. Exempelvis använder för närvarande en gick inte fullständig 1 MB på en tabellentiteten för att lagra binära data eftersom den kodningen ökar storleken på data.
* Entitetsnamnet egenskapen ”Id” för närvarande inte stöds

Det finns ett antal slutpunkter/fråga alternativ som inte stöds av Azure Cosmos DB tabell API med avseende på REST API:
| REST-metoder | REST-slutpunkt/frågealternativet | Doc-URL: er | Förklaring |
| ------------| ------------- | ---------- | ----------- |
| HÄMTA OCH PLACERA | /? restype =service@comp= egenskaper| [Ange egenskaper för tabellen](https://docs.microsoft.com/rest/api/storageservices/set-table-service-properties) och [hämta egenskaper för tabellen](https://docs.microsoft.com/rest/api/storageservices/get-table-service-properties) | Den här slutpunkten används för att ange CORS-regler, analytics lagringskonfiguration och loggningsinställningar. CORS stöds inte för närvarande och analyser och loggning hanteras annorlunda i Azure Cosmos DB än Azure Storage-tabeller |
| ALTERNATIV | /<table-resource-name> | [Före svarta CORS tabell begäran](https://docs.microsoft.com/rest/api/storageservices/preflight-table-request) | Detta är en del av CORS som Azure Cosmos DB inte stöder för närvarande. |
| HÄMTA | /? restype =service@comp= statistik | [Hämta Service tabellstatistik](https://docs.microsoft.com/rest/api/storageservices/get-table-service-stats) | Innehåller information hur snabbt data replikeras mellan primära och sekundära. Detta är inte behövs i Cosmos DB eftersom replikering är en del av skrivningar. |
| HÄMTA OCH PLACERA | /mytable? comp = acl | [Hämta tabellen ACL](https://docs.microsoft.com/rest/api/storageservices/get-table-acl) och [ange tabellen ACL](https://docs.microsoft.com/rest/api/storageservices/set-table-acl) | Detta hämtar och anger de lagrade åtkomstprinciper som används för att hantera delade åtkomst signaturer (SAS). Även om SAS stöds de ställs och hanteras annorlunda. |

Dessutom stöder Azure Cosmos DB tabell API endast JSON-format, inte ATOM.

Medan Azure Cosmos DB har stöd för delad åtkomst signaturer (SAS) det finns vissa principer som inte stöds, särskilt de som är relaterade till hanteringsåtgärder, till exempel behörighet att skapa nya tabeller.

För .NET SDK i synnerhet finns vissa klasser och metoder som Azure Cosmos DB inte stöder för närvarande.

| Klass | Metoden stöds inte |
|-------|-------- |
| CloudTableClient | \*ServiceProperties* |
|                  | \*ServiceStats* |
| CloudTable | Behörighetsgruppbehörighet * |
|            | GetPermissions * |
| TableServiceContext | * (är den här klassen föråldrad faktiskt) |
| TableServiceEntity | " " |
| TableServiceExtensions | " " |
| TableServiceQuery | " " |

Om någon av dessa skillnader är ett problem för ditt projekt Kontakta [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com) och berätta för oss.

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Hur jag för att ge feedback om SDK eller buggar?
Du kan dela din feedback i något av följande sätt:

* [Uservoice](https://feedback.azure.com/forums/599062-azure-cosmos-db-table-api)
* [MSDN-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurecosmosdb)
* [Stacken spill](http://stackoverflow.com/questions/tagged/azure-cosmosdb). Stackspill är bäst för programmering frågor. Se till att din fråga [på avsnittet](https://stackoverflow.com/help/on-topic) och [ange så många detaljer som möjligt, göra frågan tydliga och besvaras](https://stackoverflow.com/help/how-to-ask).

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api"></a>Vad är den anslutningssträng som jag behöver använda för att ansluta till tabellen API?
Anslutningssträngen är:
```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountName>.table.cosmosdb.azure.com
```
Du kan hämta anslutningssträngen från sidan anslutningssträngen i Azure-portalen. 

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-net-sdk-for-the-table-api"></a>Hur jag för att åsidosätta konfigurationsinställningarna för begäran-alternativ i .NET SDK för tabell-API?
Information om konfigurationsinställningarna finns [Azure Cosmos DB funktioner](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Vissa inställningar hanteras på CreateCloudTableClient metod och andra via app.config i avsnittet AppSettings i klientprogrammet.

### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-azure-table-storage-sdks"></a>Finns det några ändringar för kunder som använder den befintliga Azure Table storage SDK: er?
Ingen. Det finns inga ändringar för befintliga eller nya kunder som använder den befintliga Azure Table storage SDK: er. 

### <a name="how-do-i-view-table-data-that-is-stored-in-azure-cosmos-db-for-use-with-the-table-api"></a>Hur visar jag tabelldata som lagras i Azure Cosmos DB för användning med tabell-API 
Du kan använda Azure-portalen för att bläddra i data. Du kan också använda tabellen API-koden eller verktyg som nämns i nästa svar. 

### <a name="which-tools-work-with-the-table-api"></a>Vilka verktyg tillsammans med tabell-API: et? 
Du kan använda den [Azure Lagringsutforskaren](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

Verktyg med möjlighet att ta en anslutningssträng i det format som specificerats stöder tidigare tabellen nya API: et. En lista över Tabellverktyg finns på den [Azure Storage-klientverktyg](../storage/common/storage-explorers.md) sidan. 

### <a name="do-powershell-or-azure-cli-work-with-the-table-api"></a>PowerShell eller Azure CLI fungerar med tabell-API: et?
Det finns stöd för [PowerShell](table-powershell.md). Azure CLI-stöd är inte tillgänglig.

### <a name="is-the-concurrency-on-operations-controlled"></a>Är samtidigheten på åtgärder som styrs?
Ja, tillhandahålls Optimistisk samtidighet med hjälp av mekanismen för ETag. 

### <a name="is-the-odata-query-model-supported-for-entities"></a>Stöds modellen för OData-frågan för enheter? 
Ja, tabell-API: et stöder OData-fråge- och LINQ-fråga. 

### <a name="can-i-connect-to-azure-table-storage-and-azure-cosmos-db-table-api-side-by-side-in-the-same-application"></a>Kan ansluta till Azure Table Storage och Azure Cosmos DB tabell API sida vid sida i samma program? 
Ja, du kan ansluta genom att skapa två separata instanser av CloudTableClient, varje pekar på en egen URI via anslutningssträngen.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-offering"></a>Hur jag för att migrera en befintlig Azure Table storage-program till detta erbjudande?
[AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) och [Azure Cosmos DB Datamigreringsverktyg](import-data.md) är båda stöds.

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Hur är expandering av lagringsstorleken Utfärdad för den här tjänsten om du till exempel jag börja med *n* GB data och Mina data kommer att växa till 1 TB över tid? 
Azure Cosmos-DB är utformad att ge obegränsad lagring med hjälp av teckenbredden. Tjänsten kan övervaka och effektivt öka din lagring. 

### <a name="how-do-i-monitor-the-table-api-offering"></a>Hur övervakar tabell API-erbjudande?
Du kan använda tabellen API **mått** fönstret för att övervaka begäranden och lagringskvoten. 

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Hur jag för att beräkna genomflöde I kräver?
Du kan använda kapacitet exteriörbedömning för att beräkna TableThroughput som krävs för åtgärderna. Mer information finns i [uppskattning begära enheter och datalagring](https://www.documentdb.com/capacityplanner). I allmänhet kan du representerar entiteten som JSON och ange siffrorna för din verksamhet. 

### <a name="can-i-use-the-table-api-sdk-locally-with-the-emulator"></a>Kan jag använda tabellen API SDK lokalt med emulatorn?
Inte just nu.

### <a name="can-my-existing-application-work-with-the-table-api"></a>Fungerar mitt befintliga program med tabell-API 
Ja, samma API stöds.

### <a name="do-i-need-to-migrate-my-existing-azure-table-storage-applications-to-the-sdk-if-i-do-not-want-to-use-the-table-api-features"></a>Behöver jag migrera Mina befintliga Azure Table storage-program till SDK om jag inte vill använda tabellen API-funktioner?
Nej, kan du skapa och använda befintliga Azure Table storage tillgångar utan avbrott av något slag. Om du inte använder tabell-API, kan inte du omfattas av automatisk indexet, ytterligare konsekvenskontroll alternativet eller distributionslistor. 

### <a name="how-do-i-add-replication-of-the-data-in-the-table-api-across-multiple-regions-of-azure"></a>Hur lägger jag till replikering av data i tabellen API över flera regioner Azure?
Du kan använda Azure DB som Cosmos-portalen [globala replikeringsinställningarna](tutorial-global-distribution-sql-api.md#portal) du lägger till regioner som är lämpliga för ditt program. För att utveckla ett globalt distribuerat program bör du också lägga till programmet PreferredLocation uppgifterna till den lokala regionen för att tillhandahålla låg latens som skrivskyddade. 

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-table-api"></a>Hur ändrar jag primära write-region för kontot i tabell-API
Du kan använda fönstret Azure Cosmos DB globala replikering portal för att lägga till en region och sedan växla över till den nödvändiga regionen. Instruktioner finns i [utveckling med flera regioner Azure Cosmos DB konton](regional-failover.md). 

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Hur konfigurerar jag min primära skrivskyddade regioner för låg fördröjning när jag fördela mina data? 
Använda PreferredLocation nyckel i filen app.config att läsa från den lokala platsen. Tabell-API genererar ett fel om LocationMode har angetts för befintliga program. Ta bort den koden eftersom tabellen API hämtar informationen från filen app.config. Mer information finns i [Azure Cosmos DB funktioner](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities).

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api"></a>Hur ska tycker om konsekvensnivåer i tabellen API? 
Azure Cosmos-DB tillhandahåller välmotiverat avvägningarna mellan konsekvens, tillgänglighet och svarstid. Azure Cosmos-DB erbjuder fem konsekvensnivåer till tabellen API-utvecklare, så du kan välja rätt konsekvenskontroll modellen på tabellnivå och enskilda begär vid läsning av data. När en klient ansluter ange den en konsekvenskontroll nivå. Du kan ändra nivån via argumentet consistencyLevel för CreateCloudTableClient. 

Tabell-API ger låg latens läser med ”Läs egna skrivningar” med Bounded föråldringskonsekvens som standard. Mer information finns i [konsekvensnivåer](consistency-levels.md). 

Som standard ger Azure Table storage stark konsekvens inom en region och Eventual konsekvens på sekundära platser. 

### <a name="does-azure-cosmos-db-table-api-offer-more-consistency-levels-than-azure-table-storage"></a>Erbjuder Azure Cosmos DB tabell API mer konsekvensnivåer än Azure Table storage?
Ja, finns information om hur du kan utnyttja distribuerade uppbyggnad Azure Cosmos DB [konsekvensnivåer](consistency-levels.md). Eftersom garantier har angetts för nivåerna konsekvens, kan du använda dem med förtroende. Mer information finns i [Azure Cosmos DB funktioner](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities).

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>När globala distribution är aktiverat, hur lång tid tar det för att replikera data?
Azure Cosmos-DB sparar data varaktigt i den lokala regionen och skickar data till andra regioner direkt i en fråga i millisekunder. Replikeringen är beroende av endast de fram och åter tid för datacenter. Läs mer om Azure Cosmos DB global distributionsplatsen kapacitet i [Azure Cosmos DB: en tjänst för globalt distribuerad databas på Azure](distribute-data-globally.md).

### <a name="can-the-read-request-consistency-level-be-changed"></a>Kan konsekvensnivå läsbegäran ändras?
Med Azure Cosmos DB, kan du ange nivån konsekvenskontroll på nivån behållare (på tabellen). Du kan ändra nivån genom att ange värdet för TableConsistencyLevel nyckel i filen app.config med hjälp av .NET SDK. Möjliga värden är: stark, begränsat föråldrad, Session, konsekvent Prefix och Eventual. Mer information finns i [data justerbara konsekvensnivåer i Azure Cosmos DB](consistency-levels.md). Viktiga idé är att du inte kan ange begäran konsekvenskontrollen nivån på mer än inställningen för tabellen. Du kan exempelvis ange konsekvensnivå för tabellen på Eventual och begäran konsekvensnivå på starka. 

### <a name="how-does-the-table-api-handle-failover-if-a-region-goes-down"></a>Hur tabell-API: et hanterar redundans om en region kraschar? 
Tabell-API utnyttjar Azure Cosmos DB globalt distribuerade plattform. Aktivera minst en mer region för kontot i Azure DB som Cosmos-portal för att säkerställa att ditt program kan tolerera datacenter driftstopp, [utveckling med flera regioner Azure Cosmos DB konton](regional-failover.md). Du kan ange prioritet för regionen med hjälp av portalen [utveckling med flera regioner Azure Cosmos DB konton](regional-failover.md). 

Du kan lägga till så många områden som du vill använda för kontot och styra där den kan växla över till genom att tillhandahålla en prioritet för växling vid fel. Naturligtvis för att använda databasen, måste du ge det ett program för. När du gör det får inte driftstopp för dina kunder. Den [senaste .NET klienten SDK](table-sdk-dotnet.md) är automatisk homing men den andra SDK: er inte. Det vill säga kan identifiera den region som är igång och automatiskt växla över till den nya regionen.

### <a name="is-the-table-api-enabled-for-backups"></a>Tabell-API har aktiverats för säkerhetskopiering?
Ja, tabell-API utnyttjar plattformen Azure Cosmos DB för säkerhetskopiering. Säkerhetskopieringar görs automatiskt. Mer information finns i [Online säkerhetskopiering och återställning med Azure Cosmos DB](online-backup-and-restore.md).

 
### <a name="does-the-table-api-index-all-attributes-of-an-entity-by-default"></a>Tabell-API index alla attribut för en entitet som standard?
Ja, alla attribut för en entitet indexeras som standard. Mer information finns i [Azure Cosmos DB: indexering principer](indexing-policies.md). 

### <a name="does-this-mean-i-do-not-have-to-create-multiple-indexes-to-satisfy-the-queries"></a>Detta betyder det inte behöver skapa flera index för frågorna? 
Ja, Azure Cosmos DB tabell API ger automatisk indexering av alla attribut utan någon schemadefinition. Det här automation Frigör utvecklare kan fokusera på programmet istället för på skapandet av index och hantering. Mer information finns i [Azure Cosmos DB: indexering principer](indexing-policies.md).

### <a name="can-i-change-the-indexing-policy"></a>Kan jag ändra indexprincip?
Ja, du kan ändra indexprincip genom att tillhandahålla indexdefinitionen. Mer information finns i [Azure Cosmos DB funktioner](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Du behöver korrekt koda och escape-inställningarna. 

för icke - .NET SDK: erna indexprincip kan bara anges i portalen på **Data Explorer**, navigera till den specifika tabellen som du vill ändra och går sedan till den **skala & inställningar**-> indexering princip Gör önskade ändringen och sedan **spara**.

Från .NET SDK lämnas den i filen app.config:
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

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-sorting-aggregates-hierarchy-and-other-functionality-will-you-be-adding-these-capabilities-to-the-table-api"></a>Azure Cosmos-DB en plattform som verkar ha många funktioner, t.ex sortera, aggregeringar, hierarkin och andra funktioner. Du lägger till dessa funktioner tabell-API: et? 
Tabell-API ger samma fråga funktioner som Azure Table storage. Azure Cosmos DB stöder också sortering, samlingar, geospatiala frågor, hierarki och en mängd inbyggda funktioner. Vi ger ytterligare funktioner i tabellen API i en framtida tjänstuppdatering. Mer information finns i [SQL-frågor](sql-api-sql-query.md).
 
### <a name="when-should-i-change-tablethroughput-for-the-table-api"></a>När bör jag ändra TableThroughput för tabell-API
Du bör ändra TableThroughput när någon av följande villkor gäller:
* Du utför en extrahering, transformering och laddning (ETL) av data, eller om du vill överföra stora mängder data på kort tid. 
* Du behöver mer genomströmning från behållaren på serverdelen. Till exempel visas att använda genomströmning är större än det tillhandahållna dataflödet och du har komma begränsats. Mer information finns i [Set genomströmning för Azure Cosmos DB behållare](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-table"></a>Kan jag skala upp eller ned genomflödet av tabellen API tabellen? 
Ja, kan du använda portalen Azure Cosmos DB skala fönstret för att skala genomflödet. Mer information finns i [Set genomströmning](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Är en standarduppsättning TableThroughput för nyetablerade tabeller?
Ja, om du inte åsidosätter TableThroughput via app.config och Använd inte en förskapad behållare i Azure Cosmos DB tjänsten skapar en tabell med genomströmning på 400.
 
### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-azure-table-storage-service"></a>Finns det några ändringar av priser för befintliga kunder i tjänsten Azure Table storage?
Ingen. Det finns ingen ändring i priset för befintliga Azure Table storage-kunder. 

### <a name="how-is-the-price-calculated-for-the-table-api"></a>Hur beräknas priset för tabell-API: et? 
Priset beror på allokerade TableThroughput. 

### <a name="how-do-i-handle-any-throttling-on-the-tables-in-table-api-offering"></a>Hur hanterar jag en begränsning på tabellerna i tabellen API erbjudande? 
Om frekvensen för begäran överskrider det tillhandahållna dataflödet kapacitet för den underliggande behållaren, du får ett felmeddelande och SDK försöker anropet genom att använda principen försök igen.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-table-api-offering-of-azure-cosmos-db"></a>Varför måste välja en genomströmning förutom PartitionKey och RowKey för att dra nytta av tabellen API-erbjudande på Azure Cosmos DB?
Azure Cosmos-DB anger en standard-genomströmning för din behållaren om du inte anger något i filen app.config eller via portalen. 

Azure Cosmos-DB tillhandahåller garantier för prestanda och fördröjning med övre gränser för åtgärden. Garantin är möjligt när motorn kan tillämpa styrning på klientens åtgärder. Ange TableThroughput garanterar att du får garanterad genomflöde och svarstid, eftersom plattformen reserverar denna kapacitet och garanterar operativa lyckades. 

Med specifikationen dataflöde kan ändra du Elastiskt den om du vill dra nytta av säsongsvärdet för programmet, passar genomflöde och spara kostnader.

### <a name="azure-table-storage-has-been-very-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-azure-cosmos-db-table-api-offering-seems-to-be-charging-me-even-though-i-have-not-performed-a-single-transaction-or-stored-anything-can-you-please-explain"></a>Azure Table storage har så billigt mig, eftersom jag betala endast att lagra data, och jag sällan frågan. Azure Cosmos DB tabell API-erbjudande verkar debitering mig även om jag inte har utfört en enda transaktion eller lagras något. Kan du ange förklara?

Azure Cosmos-DB är avsedd att vara ett globalt distribuerade, SLA-baserade system med garantier för tillgänglighet, svarstid och genomströmning. När du reserverar genomflöde i Azure Cosmos DB garanteras den, till skillnad från genomflödet i andra system. Azure Cosmos-DB tillhandahåller ytterligare funktioner som kunder har begärt, till exempel sekundärindex och distributionslistor.  

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-azure-table-storage-with-the-table-api-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Jag får aldrig en ”full” kvotmeddelanden (som anger att en partition är full) när jag mata in data i Azure Table storage. Jag får det här meddelandet med tabell-API. Detta ger att begränsa mig och tvinga jag ändra Mina befintliga program?

Azure Cosmos-DB är ett SLA-baserat system som ger obegränsad skala garantier för latens, dataflöde, tillgänglighet och konsekvenskontroll. Kontrollera att index och Datastorleken är hanterbar och skalbar för att säkerställa prestanda Garanterad premium. 10 GB-gränsen för antalet enheter eller objekt per Partitionsnyckeln är att säkerställa att vi ger utmärkt prestanda för sökning och fråga. För att säkerställa att programmet ska skalas, även för Azure Storage, rekommenderar vi att du *inte* skapa en varm partition genom att lagra all information i en partition och exempelvärden. 

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-table-api"></a>Så PartitionKey och RowKey krävs fortfarande med tabell-API: et? 
Ja. Eftersom ytan på tabell-API är samma som Azure Table storage SDK innehåller Partitionsnyckeln ett effektivt sätt att fördela data. Radnyckeln är unikt i den aktuella partitionen. Radnyckeln måste finnas och får inte vara null som standard SDK. RowKey längd är 255 byte och längden på PartitionKey är 1 KB. 

### <a name="what-are-the-error-messages-for-the-table-api"></a>Vad är felmeddelanden för tabell-API: et?
Azure Table storage och Azure Cosmos DB tabell API använder samma SDK: erna så att de flesta felen är samma.

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api"></a>Varför jag hämta begränsas när jag försöker skapa många tabeller efter varandra i tabellen API?
Azure Cosmos-DB är ett SLA-baserat system som ger svarstid, dataflöde, tillgänglighet och konsekvens garanterar. Eftersom det är ett etablerade system, reserverar resurser för att garantera att dessa krav. Snabb frekvensen för skapande av tabeller identifieras och begränsas. Vi rekommenderar att du tittar på frekvensen för skapande av tabeller och sänka den till mindre än 5 per minut. Kom ihåg att tabellen API etablerade system. Den tidpunkt då du etablera, börjar du betalar för den. 

## <a name="develop-against-the-graph-api"></a>Utveckla mot Graph-API
### <a name="how-can-i-apply-the-functionality-of-graph-api-to-azure-cosmos-db"></a>Hur kan använda funktionen för Graph API till Azure Cosmos DB?
Du kan använda ett tillägg-bibliotek för att använda funktionen för Graph API. Det här biblioteket kallas Microsoft Azure diagram och det är tillgängligt på NuGet. 

### <a name="it-looks-like-you-support-the-gremlin-graph-traversal-language-do-you-plan-to-add-more-forms-of-query"></a>Det verkar som du stöder Gremlin diagram traversal språk. Du planerar att lägga till flera typer av frågan?
Ja, vi planerar att lägga till andra mekanismer för frågan i framtiden. 

### <a name="how-can-i-use-the-new-graph-api-offering"></a>Hur kan jag använda det nya Graph API-erbjudandet? 
Kom igång genom att slutföra den [Graph API](../cosmos-db/create-graph-dotnet.md) Snabbkurs artikel.

<a id="cassandra"></a> 
## <a name="develop-with-the-apache-cassandra-api-preview"></a>Utveckla med Apache Cassandra API (förhandsgranskning)

### <a name="what-is-the-protocol-version-supported-in-the-private-preview-is-there-a-plan-to-support-other-protocols"></a>Vad är protokollversion som stöds i privat förhandsvisning? Finns det en plan för att stödja andra protokoll?
Apache Cassandra API: et för Azure Cosmos DB stöder idag CQL version 4. Om du har feedback om stöd för andra protokoll berätta för oss [uservoice-feedback](https://feedback.azure.com/forums/263030-azure-cosmos-db) eller skicka ett e-postmeddelande till [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com). 

### <a name="why-is-choosing-a-throughput-for-a-table-a-requirement"></a>Anledningen är att välja en genomströmning för en tabell ett krav?
Azure Cosmos-DB anger standard genomströmning för din behållare baserat på där du skapar en tabell från - portalen eller CQL. Azure Cosmos-DB tillhandahåller garantier för prestanda och fördröjning med övre gränser för åtgärden. Garantin är möjligt när motorn kan tillämpa styrning på klientens åtgärder. Dataflöde för inställningen garanterar att du får garanterad genomflöde och svarstid, eftersom plattformen reserverar denna kapacitet och garanterar att åtgärden lyckades. Elastiskt kan du ändra dataflöde för att dra nytta av säsongsvärdet för programmet och spara kostnader.

Genomströmning konceptet förklaras i den [begära enheter i Azure Cosmos DB](request-units.md) artikel. Genomströmning för en tabell fördelas jämnt över underliggande fysiska partitioner.  

### <a name="what-is-the-default-rus-of-table-when-created-through-cql-what-if-i-need-to-change-it"></a>Vad är standard RU/s för tabellen när skapats via CQL? Vad händer om du vill ändra det?
Azure Cosmos-DB använder frågeenheter per sekund (RU/s) som valuta för att tillhandahålla genomflöde. Tabeller som skapats via CQL har 400 RU. Du kan ändra RU från portalen. 

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

### <a name="what-happens-when-throughput-is-exceeded"></a>Vad händer när genomströmning överskrids? 
Azure Cosmos-DB tillhandahåller garantier för prestanda och fördröjning med övre gränser för åtgärden. Garantin är möjligt när motorn kan tillämpa styrning på klientens åtgärder. Detta är möjligt baserat på inställningen dataflöde, vilket garanterar att du får garanterad genomflöde och svarstid, eftersom plattformen reserverar denna kapacitet och garanterar att åtgärden lyckades. När du överskrider den här kapaciteten visas överbelastad felmeddelande om din kapacitet har överskridits. 0x1001 överbelastad: begäran kan inte bearbetas eftersom ”förfrågningar är stor”. I detta läge är det viktigt att se vilka åtgärder och deras volym orsakar problemet. Du kan få en uppfattning om förbrukade kapacitet som överstiger etablerad kapacitet med mått på portalen. Sedan måste du kontrollera kapacitet används nästan lika för alla underliggande partitioner. Om du ser att de flesta av genomflödet används av en partition, har du skeva av arbetsbelastning. 

Mått är tillgängliga som visar hur genomströmning används över timmar, dagar, och per sju dagar mellan partitioner eller aggregering. Mer information finns i [övervakning och felsökning med mått i Azure Cosmos DB](use-metrics.md).

Diagnostikloggar beskrivs i den [Azure Cosmos DB diagnostikloggning](logging.md) artikel.

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>Stöder primära nyckel kartan partition viktiga konceptet att Azure Cosmos DB?
Partitionsnyckeln är Ja, används för att placera enheten i rätt plats. I Azure Cosmos DB för den att hitta rätt logisk partition som lagras på en fysisk partition. Partitionering konceptet och förklaras i den [Partition och skala i Azure Cosmos DB](partition-data.md) artikel. Väsentliga ta bort det här är att en logisk partition inte får överstiga gränsen på 10 GB idag. 

### <a name="what-happens-when-i-get-a-quota-full-notification-indicating-that-a-partition-is-full"></a>Vad händer när jag får ett ”full” kvotmeddelanden som anger att en partition är full?
Azure Cosmos-DB är ett SLA-baserat system som ger obegränsad skala garantier för latens, dataflöde, tillgänglighet och konsekvenskontroll. Dess Cassandra API kan för obegränsad lagring av data. Den här obegränsad lagring baseras på vågräta scaleout data med hjälp av partitionering som viktiga begrepp. Partitionering konceptet och förklaras i den [Partition och skala i Azure Cosmos DB](partition-data.md) artikel.

10 GB-gränsen för antalet enheter eller objekt per logisk partition bör du följa. För att säkerställa att programmet ska skalas bra, rekommenderar vi att du *inte* skapa en varm partition genom att lagra all information i en partition och exempelvärden. Det här felet kan endast komma om dina data är förvrängd - som är att du har stora mängder data för en partitionsnyckel - d.v.s. mer än 10 GB. Du kan hitta fördelning av data med hjälp av lagring-portalen. Sättet att åtgärda det här felet är att recrete tabellen och välja en detaljerad primär (partitionsnyckel), vilket gör att bättre fördelning av data.

### <a name="is-it-possible-to-use-cassandra-api-as-key-value-store-with-millions-or-billions-of-individual-partition-keys"></a>Är det möjligt att använda Cassandra API som nyckelvärdet store med miljontals eller enskilda partitionsnycklar miljarder?
Azure Cosmos-DB kan lagra obegränsade data genom att skala ut lagring. Detta är oberoende av kapaciteten. Ja kan du alltid bara använda Cassandra API för att lagra och hämta nyckel-värden genom att ange primära/partitionsnyckel. Nycklarna enskilda få sina egna logisk partition och sitta på fysiska partition utan problem. 

### <a name="is-it-possible-to-create-multiple-tables-with-apache-cassandra-api-of-azure-cosmos-db"></a>Är det möjligt att skapa flera tabeller med Apache Cassandra API för Azure Cosmos DB?
Ja, det är möjligt att Kreta flera tabeller med Apache Cassandra API. Var och en av dessa tabeller behandlas som enhet för dataflöden och lagringsutrymmen. 

### <a name="is-it-possible-to-create-multiple-tables-in-succession"></a>Är det möjligt att skapa flera tabeller i följd?
Azure Cosmos-DB är resurser som omfattas system för både data och kontroll plan aktiviteter. Behållare som samlingar, tabeller är runtime-enheter som har etablerats för angivna genomflödeskapaciteten. Skapandet av dessa behållare i snabb följd begränsas är inte förväntade aktivitet. Om du har tester som släpp/skapa tabeller direkt - försök utrymme dem.

### <a name="what-is-maximum-number-of-tables-which-can-be-created"></a>Vad är maximala antalet tabeller som kan skapas?
Det finns ingen fysiska gräns för antalet tabeller, skicka ett e-postmeddelande på [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) om du har ett stort antal tabeller (där den totala storleken för konstant överstiger 10 TB data) som måste skapas från vanliga 10-tal eller 100-tal. 

### <a name="what-is-the-maximum--of-keyspace-which-we-can-create"></a>Vad är det maximala antal keyspace som vi kan skapa? 
Det finns ingen fysiska gräns för antalet keyspaces eftersom de metadata behållare, skicka ett e-postmeddelande på [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) om du har ett stort antal keyspaces av någon anledning. 

### <a name="is-it-possible-to-bring-in-lot-of-data-after-starting-from-normal-table"></a>Är det möjligt att hämta stora mängder data när du har startat från normala tabellen? 
Lagringskapaciteten hanteras automatiskt och ökar när du trycker på mer data. Så kan du vill importera så mycket information som du behöver utan att hantera och etablerar noder, osv. 

### <a name="is-it-possible-to-supply-yaml-file-settings-to-configure-apache-casssandra-api-of-azure-cosmos-db-behavior"></a>Är det möjligt att ange inställningar för att konfigurera beteendet för Apache Casssandra API för Azure Cosmos DB yaml?
Apache Cassandra API för Azure Cosmos DB är en plattform. Det ger protokollet nivån snabbkorrigering för att köra åtgärder. Direkt döljer komplexiteten i hantering, övervakning och konfiguration. Du behöver inte bry dig om tillgänglighet, tombstones, viktiga cache, rad cache, Blom filter och mängd andra inställningar som utvecklare/användare. Azure Cosmos DB Apache Cassandra API fokuserar på att ge läs- och skrivprestanda som du vill ha utan extra kostnader för konfiguration och hantering.

### <a name="will-apache-cassandra-api-for-azure-cosmos-db-support-node-additioncluster-statusnode-status-commands"></a>Apache Cassandra API: et för Azure Cosmos DB stöder nod tillägg/kluster status/nod status kommandon?
Apache Cassandra API är en plattform som gör kapacitetsplanering, svara på elasticitet krav för genomflöde och lagring snabbt. Med Azure Cosmos DB etablera genomströmning som du behöver. Du kan sedan skala den uppåt och nedåt valfritt antal gånger genom dagen utan att bekymra dig om att lägga till/ta bort noder eller hanteras. Detta innebär att du inte behöver använda noden, hanteringsverktyg för kluster för. 

### <a name="what-happens-with-respect-to-various-config-settings-for-keyspace-creation-like-simplenetwork"></a>Vad händer med avseende på olika konfigurationsinställningarna för att skapa en keyspace som enkel/nätverk?
Azure Cosmos-DB tillhandahåller global distributionsplatsen out of box för tillgänglighet och låg latens orsaker. Du behöver inte installationsprogrammet repliker osv. Alla skrivningar är alltid varaktigt kvorum i ett en region där du kan skriva samtidigt som man prestanda garantier.  

### <a name="what-happens-with-respect-to-various-settings-for-table-metadata-like-bloom-filter-caching-read-repair-change-gcgrace-compression-memtableflushperiod-etc"></a>Vad händer med avseende på olika inställningar för tabellmetadata som Blom filter cachelagring, läsa reparera ändringar, gc_grace, komprimering memtable_flush_period osv?
Azure Cosmos-DB tillhandahåller prestanda för läsning/skrivning och genomströmning utan att några av konfigurationsinställningarna och av misstag ändra dem du behöver.  

### <a name="is-time-to-live-ttl-supported-for-cassandra-tables"></a>Time to live (TTL) som stöds för Cassandra tabeller? 
Ja, TTL stöds. 

### <a name="is-it-possible-to-monitor-node-status-replica-status-gc-and-os-parameters-earlier-with-various-tools-what-needs-to-be-monitored-now"></a>Är det möjligt att övervaka nod status, replikens status, gc och OS-parametrar tidigare med olika verktyg? Vad som ska övervakas nu?
Azure Cosmos-DB är en platform-tjänst som hjälper dig att öka produktiviteten och oroa dig inte om hantering och övervakning av infrastruktur. Du behöver bara ta hand om genomströmning som är tillgängligt på portalen mått för att hitta om du har komma begränsats och öka eller minska det genomflödet. Övervakaren [SLA](monitor-accounts.md).
Använd [mått](use-metrics.md) Använd [diagnostikloggar](logging.md).

### <a name="which-client-sdks-can-work-with-apache-cassandra-api-of-azure-cosmos-db"></a>Vilka klient-SDK: er kan arbeta med Apache Cassandra API för Azure Cosmos DB?
Drivrutiner som använder CQLv3 användes i privat förhandsvisning Apache Cassandra SDK: ns-klienten för klientprogram. Om du har andra drivrutiner som du använder eller om du inför problem, skicka e-post till [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com). 

### <a name="is-composite-partition-key-supported"></a>Det finns stöd för sammansatt partitionsnyckel
Ja, kan du använda reguljära syntax för att skapa sammansatta partitionsnyckel. 

### <a name="can-i-use-sstable-loader-for-data-loading"></a>Kan jag använda sstable inläsaren för inläsning av data?
Nej, under förhandsgranskningen sstable inläsaren stöds inte. 

### <a name="can-an-on-premises-cassandra-cluster-be-paired-with-azure-cosmos-dbs-apache-cassandra-api"></a>Kan länkas ett lokalt cassandra kluster med Azure Cosmos DB Apache Cassandra API?
Har en optimerad upplevelse för molnmiljö Slipp åtgärder vid finns Azure Cosmos DB. Om du behöver länkning kan skicka e-post till [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) med en beskrivning av ditt scenario.

### <a name="does-cassandra-api-provide-full-backups"></a>Tillhandahåller Cassandra API och fullständiga säkerhetskopieringar? 
Azure Cosmos-DB tillhandahåller två ledigt och fullständiga säkerhetskopieringar som vidtas på fyra timmar intervall idag över alla API: er. Detta säkerställer att du inte behöver konfigurera ett schema för säkerhetskopiering osv. Om du vill ändra kvarhållning och frekvens skicka ett e-postmeddelande till [ askcosmosdbcassandra@microsoft.com ](mailto:askcosmosdbcassandra@microsoft.com) eller ett supportärende. Information om säkerhetskopiering finns i den [automatisk online säkerhetskopiering och återställning med Azure Cosmos DB](online-backup-and-restore.md) artikel. 

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Hur hanterar redundans Cassandra API-konto om en region kraschar? 
Azure Cosmos DB Cassandra API lånar från Azure Cosmos DB globalt distribuerade plattform. Aktivera minst en mer region för kontot i Azure DB som Cosmos-portal för att säkerställa att ditt program kan tolerera datacenter driftstopp, [utveckling med flera regioner Azure Cosmos DB konton](regional-failover.md). Du kan ange prioritet för regionen med hjälp av portalen [utveckling med flera regioner Azure Cosmos DB konton](regional-failover.md). 

Du kan lägga till så många områden som du vill använda för kontot och styra där den kan växla över till genom att tillhandahålla en prioritet för växling vid fel. Du måste ge det ett program för att använda databasen. När du gör det får inte driftstopp för dina kunder. 

### <a name="does-the-apache-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Apache Cassandra API index alla attribut för en entitet som standard?
Ja, alla attribut för en entitet indexeras som standard av Azure Cosmos DB. Mer information finns i [Azure Cosmos DB: indexering principer](indexing-policies.md). Du får fördelarna med garanterad prestanda med konsekvent indexering och varaktig kvorum allokerat skriver alltid. 

### <a name="does-this-mean-i-do-not-have-to-create-multiple-indexes-to-satisfy-the-queries"></a>Detta betyder det inte behöver skapa flera index för frågorna? 
Ja, Azure Cosmos DB ger automatisk indexering av alla attribut utan någon schemadefinition. Det här automation Frigör utvecklare kan fokusera på programmet istället för på skapandet av index och hantering. Mer information finns i [Azure Cosmos DB: indexering principer](indexing-policies.md).

### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Kan jag använda den nya Cassandra API SDK lokalt med emulatorn?
Vi planerar att stödja den här funktionen i framtiden. 

### <a name="azure-cosmos-db-as-a-platform-seems-to-have-lot-of-capabilities-such-as-changefeed-and-other-functionality-will-these-capabilities-be-added-to-the-cassandra-api"></a>Azure Cosmos-DB en plattform som verkar ha mycket kapacitet, till exempel changefeed och andra funktioner. Dessa funktioner läggs till Cassandra API? 
Apache Cassandra API ger samma CQL funktioner som Apache Cassandra. Vi planerar att studera är möjligt att stödja olika funktioner i framtiden.

### <a name="feature-x-of-regular-cassandra-api-is-not-working-as-today-where-can-the-feedback-be-provided"></a>Funktionen x regelbundna Cassandra API fungerar inte som idag var kan feedback tillhandahållas?
Ge feedback via [uservoice-feedback](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: sql-api-sql-query.md
