---
title: "Vanliga frågor och svar om Azure Cosmos-DB | Microsoft Docs"
description: "Få svar på vanliga frågor och svar om Azure Cosmos DB, ett globalt distribuerade och flera olika modeller database-tjänsten. Läs mer om kapacitet, prestandanivåer och skalning."
keywords: "Databasfrågor, vanliga frågor, documentdb, azure, Microsoft azure"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: b68d1831-35f9-443d-a0ac-dad0c89f245b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.author: mimig
ms.openlocfilehash: 534bf37cc70420dc30fcd5c994ae7d9beb654072
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="azure-cosmos-db-faq"></a>Vanliga frågor om Azure Cosmos DB
## <a name="azure-cosmos-db-fundamentals"></a>Azure DB Cosmos-grunderna
### <a name="what-is-azure-cosmos-db"></a>Vad är Azure Cosmos DB?
Azure Cosmos-databas är en tjänst för flera modell globalt replikerad databas som som erbjuder många frågealternativ över schemafria data, hjälper till att leverera konfigurerbar och tillförlitlig prestanda och möjliggör snabb utveckling. Det är alla uppnås genom en hanterad plattform som backas upp av kraften och räckvidden hos Microsoft Azure. 

Azure Cosmos-DB är den rätta lösningen för webb-, mobil-, spel- och IoT-appar när förutsägbart dataflöde, hög tillgänglighet, låg latens och en schemafri datamodell är viktiga krav. Det ger schemaflexibilitet och omfattande indexering och innehåller transaktionellt stöd för flera dokument med integrerat JavaScript. 

Mer databasfrågor, svar och instruktioner för att distribuera och använda den här tjänsten, finns det [dokumentationssidan för Azure Cosmos DB](https://azure.microsoft.com/documentation/services/cosmos-db/).

### <a name="what-happened-to-documentdb"></a>Vad hände med DocumentDB?
DocumentDB-API: N är en API som stöds och datamodeller för Azure Cosmos DB. Dessutom stöder Azure Cosmos DB du med Graph API (förhandsgranskning), tabell-API (förhandsversion) och MongoDB-API. Mer information finns i [frågor från DocumentDB kunder](#moving-to-cosmos-db).

### <a name="how-do-i-get-to-my-documentdb-account-in-the-azure-portal"></a>Hur får jag till DocumentDB-konto i Azure portal?
Klicka på ikonen Azure Cosmos DB i den vänstra rutan i Azure-portalen. Om du har ett DocumentDB-konto innan har du nu ett Azure DB som Cosmos-konto med faktureringen ändras inte.

### <a name="what-are-the-typical-use-cases-for-azure-cosmos-db"></a>Vad är vanliga användningsområden för Azure Cosmos DB?
Azure Cosmos-DB är ett bra alternativ för nya webb-, mobil-, spel- och IoT-appar där automatisk skalning, förutsägbar prestanda, snabba ordning med svarstider på millisekunder och möjlighet till frågor över schemafria data är viktigt. Azure Cosmos-DB lämpar sig för snabb utveckling och stöder kontinuerlig iteration av appens datamodeller. Program som hanterar innehåll som skapats av användare och data är [vanliga användningsområden för Azure Cosmos DB](use-cases.md). 

### <a name="how-does-azure-cosmos-db-offer-predictable-performance"></a>Hur erbjuder Azure Cosmos DB förutsägbar prestanda?
En [begäran enhet](request-units.md) (RU) är ett mått på dataflödet i Azure Cosmos DB. En genomströmning 1 RU motsvarar dataflödet i GET av ett dokument på 1 KB. Varje åtgärd i Azure Cosmos DB, inklusive läsning, skrivning, SQL-frågor och lagrade procedurkörningar har en deterministisk RU-värde som är baserad på dataflödet som krävs för att slutföra åtgärden. I stället för att fundera på CPU, IO, och minne och hur de påverkar appens dataflöde tänka du på ett enda RU mått.

Du kan reservera varje Azure DB som Cosmos-behållare med etablerat dataflöde räknat RUs genomströmning per sekund. För appar oavsett skala, kan du jämföra enskilda förfrågningar för att mäta deras RU-värden och etablera en behållare för att hantera totalsumman av frågeenheter över alla förfrågningar. Du kan även skala upp eller ned din behållaren genomströmning som behov utvecklas. Mer information om frågeenheter och hjälp att fastställa din behållaren måste, se [uppskatta behov för genomströmning](request-units.md#estimating-throughput-needs) och försök den [genomströmning Kalkylatorn](https://www.documentdb.com/capacityplanner). Termen *behållare* här refererar till refererar till en API för DocumentDB-samling, Graph API diagram, MongoDB API-samling och tabellen API-tabellen. 

### <a name="how-does-azure-cosmos-db-support-various-data-models-such-as-keyvalue-columnar-document-and-graph"></a>Hur stöder Azure Cosmos DB olika datamodeller som nyckel/värde, kolumner, dokument och diagrammet?

Nyckel/värde (tabellen)-kolumner, dokumentera och diagramdata modeller är alla inbyggt stöd på grund av ARS (atomer, poster och sekvenser) design som Azure Cosmos DB bygger på. Atomer, poster och sekvenser kan enkelt mappas och planerad till olika datamodeller. API: er för en delmängd av modeller är tillgängliga just nu (DocumentDB, MongoDB, tabell och Graph API: er) och andra som är specifika för ytterligare datamodeller blir tillgängliga i framtiden.

Azure Cosmos-DB har ett schema oberoende indexering motor kan indexera automatiskt alla data som den en utan att något schema eller sekundärindex från utvecklaren. Motorn är beroende av en uppsättning logiska index layouter (inverterad, kolumner, träd) som frikopplar lagringslayout från index och frågebearbetning undersystem. Cosmos DB har också möjlighet att stödja en uppsättning överföring protokoll och API: er på ett utökningsbart sätt och effektivt översätta dem till datamodellen kärnor (1) och de logiska index layouter (2) vilket gör det unikt kan stödja flera datamodeller internt.

### <a name="is-azure-cosmos-db-hipaa-compliant"></a>Är Azure Cosmos DB HIPAA kompatibel?
Ja, Azure Cosmos DB är HIPAA-kompatibelt. HIPAA fastställer kraven för användning, redovisning, och skydd av individuellt identifierbar hälsoinformation. Mer information finns i [Microsoft Trust Center](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-azure-cosmos-db"></a>Vad är Azure Cosmos DB Lagringsgränser?
Det finns ingen gräns för den totala mängden data som en behållare kan lagra i Azure Cosmos DB.

### <a name="what-are-the-throughput-limits-of-azure-cosmos-db"></a>Vad är Azure Cosmos DB genomströmning gränser?
Det finns ingen gräns för totala genomflödet som en behållare kan stödja i Azure Cosmos DB. Viktiga idé är att distribuera din arbetsbelastning ungefär jämnt mellan ett tillräckligt stort antal partitionsnycklar.

### <a name="how-much-does-azure-cosmos-db-cost"></a>Hur mycket kostar Azure Cosmos DB?
Mer information finns i den [Azure Cosmos DB prisinformation](https://azure.microsoft.com/pricing/details/cosmos-db/) sidan. Avgifterna för användning av Azure DB Cosmos bestäms av antalet etablerade behållare, antalet timmar behållarna var online, och etablerat dataflöde för varje behållare. Termen *behållare* här refererar till DocumentDB API insamling, Graph API diagram, MongoDB API-samling och tabellen API-tabeller. 

### <a name="is-a-free-account-available"></a>Finns det ett kostnadsfritt konto?
Ja, kan du registrera dig för ett konto för tidsbegränsade utan kostnad, utan bindning. Om du vill registrera dig finns [försök Azure Cosmos-DB gratis](https://azure.microsoft.com/try/cosmosdb/) eller Läs mer i den [försök Azure Cosmos DB vanliga frågor och svar](#try-cosmos-db).

Om du är nybörjare på Azure, kan du registrera dig för en [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/), som ger dig 30 dagar och och ett kreditkort för att prova alla Azure-tjänster. Om du har en prenumeration på Visual Studio kan du också är berättigad till [kostnadsfria Azure-krediter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) att använda i valfria Azure-tjänster. 

Du kan också använda den [Azure Cosmos DB emulatorn](local-emulator.md) att utveckla och testa programmet lokalt för gratis, utan att skapa en Azure-prenumeration. När du är nöjd med hur programmet fungerar i Azure Cosmos DB-emulatorn kan växla du till med ett Azure DB som Cosmos-konto i molnet.

### <a name="how-can-i-get-additional-help-with-azure-cosmos-db"></a>Hur kan jag få ytterligare hjälp med Azure Cosmos DB?
Om du behöver hjälp med något kan nå oss på [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-cosmosdb) eller [MSDN-forum](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB), eller schemalägga en one-on-one chatt med teknikteamet Azure Cosmos DB genom att skicka e-post till [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com). 

<a id="try-cosmos-db"></a>
## <a name="try-azure-cosmos-db-subscriptions"></a>Prova Azure Cosmos DB prenumerationer

Nu kan du få en tidsbegränsade Azure Cosmos DB upplevelse utan någon prenumeration gratis och åtaganden. Om du vill registrera dig för en försök Azure DB Cosmos-prenumeration går du till [försök Azure Cosmos-DB gratis](https://azure.microsoft.com/try/cosmosdb/). Den här prenumerationen är separat från den [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/free/), och kan användas utöver en kostnadsfri utvärderingsversion av Azure eller ett Azure betald prenumeration. 

Prova Azure Cosmos DB prenumerationer som visas i Azure portal bredvid andra prenumerationer som är associerade med ditt användar-ID. 

Följande villkor gäller för försök Azure Cosmos DB prenumerationer:

* En behållare per prenumeration för SQL (DocumentDB-API), Gremlin (Graph API) och konton för tabellen.
* Upp till 3 samlingar per prenumeration för MongoDB-konton.
* 10 GB lagringskapacitet.
* Globala replikering är tillgänglig i följande [Azure-regioner](https://azure.microsoft.com/regions/): centrala USA, Norra Europa och Asien/Stillahavsområdet, sydost
* Maximalt dataflöde på 5 K RU/s.
* Prenumerationer upphör efter 24 timmar och kan utökas till maximalt 48 timmar totalt.
* Azure supportärenden kan inte skapas för försök Azure Cosmos DB redovisning. däremot finns stöd för prenumeranter med befintliga supportplaner. 

## <a name="set-up-azure-cosmos-db"></a>Konfigurera Azure Cosmos DB
### <a name="how-do-i-sign-up-for-azure-cosmos-db"></a>Hur loggar jag för Azure Cosmos DB?
Azure Cosmos-DB är tillgängliga i Azure-portalen. Först registrera dig för en Azure-prenumeration. När du har registrerat dig du lägga till ett DocumentDB-API, Graph API (förhandsgranskning), tabell-API (förhandsgranskning) eller MongoDB-API-kontot till din Azure-prenumeration.

### <a name="what-is-a-master-key"></a>Vad är en huvudnyckel?
En huvudnyckel är en säkerhetstoken som ger åtkomst till alla resurser för ett konto. Personer med nyckeln har läs- och skrivbehörighet till alla resurser i databaskontot. Var försiktig när du distribuerar huvudnycklar. Master primärnyckeln och sekundärnyckeln master är tillgängliga på den **nycklar** bladet för den [Azure-portalen][azure-portal]. Mer information om nycklar finns i [Visa, kopiera och generera åtkomstnycklar på nytt](manage-account.md#keys).

### <a name="what-are-the-regions-that-preferredlocations-can-be-set-to"></a>Vilka är de regioner som PreferredLocations kan anges till? 
PreferredLocations-värdet kan anges till någon Azure-regioner där Cosmos DB är tillgänglig. En lista över tillgängliga regioner, se [Azure-regioner](https://azure.microsoft.com/regions/).

### <a name="is-there-anything-i-should-be-aware-of-when-distributing-data-across-the-world-via-the-azure-datacenters"></a>Finns det något jag bör vara medveten om när du distribuerar data över hela världen via Azure-Datacenter? 
Azure Cosmos-DB finns över alla Azure-regioner, som anges på den [Azure-regioner](https://azure.microsoft.com/regions/) sidan. Eftersom den är kärntjänsten har en Azure Cosmos DB förekomst varje ny datacenter. 

Kom ihåg att Azure Cosmos DB respekterar statliga och offentliga moln när du ställer in en region. Som är om du skapar ett konto i en suveräna region, kan du replikera utanför suveräna regionen. På samma sätt kan du aktivera replikering till andra suveräna platser från ett utanför konto. 

## <a name="develop-against-the-documentdb-api"></a>Utveckla mot DocumentDB API

### <a name="how-do-i-start-developing-against-the-documentdb-api"></a>Hur börjar jag utveckla mot DocumentDB-API: et?
Microsoft DocumentDB-API finns i den [Azure-portalen][azure-portal]. Du måste först registrera dig för en Azure-prenumeration. När du registrerar dig för en Azure-prenumeration kan du lägga till DocumentDB API-behållare till din Azure-prenumeration. Anvisningar för att lägga till ett konto i Azure Cosmos DB finns [skapa ett databaskonto Azure Cosmos DB](create-documentdb-dotnet.md#create-account). Om du har ett DocumentDB-konto i förflutna, nu har du ett konto i Azure Cosmos DB. 

[SDK:er](documentdb-sdk-dotnet.md) är tillgängliga för .NET, Python, Node.js, JavaScript och Java. Utvecklare kan också använda den [RESTful http-API: er](/rest/api/documentdb/) att interagera med Azure Cosmos DB resurser från olika plattformar och språk.

### <a name="can-i-access-some-ready-made-samples-to-get-a-head-start"></a>Kan jag använda vissa färdiga prov för att komma igång?
Exempel för DocumentDB-API [.NET](documentdb-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](documentdb-nodejs-samples.md), och [Python](documentdb-python-samples.md) SDK finns på GitHub.


### <a name="does-the-documentdb-api-database-support-schema-free-data"></a>Stöder DocumentDB API databasen schemafria data?
Ja, DocumentDB-API kan appar lagrar godtyckliga JSON-dokument utan schemadefinitioner eller tips. Data är omedelbart tillgängligt för frågor via Azure Cosmos-Databasens SQL-gränssnitt.  

### <a name="does-the-documentdb-api-support-acid-transactions"></a>Stöder DocumentDB API ACID-transaktioner?
Ja, DocumentDB-API: et stöder transaktioner mellan dokument uttryckta som JavaScript-lagrade procedurer och utlösare. Transaktioner är begränsade till en enda partition inom varje samling och utförs med ACID-semantik som ”allt eller inget”, isoleras från andra samtidigt köra kod och begäranden. Om undantag utlöses via server-sida-körning av JavaScript, återställs hela transaktionen. Mer information om transaktioner finns [databasen transaktioner automatiskt](programming.md#database-program-transactions).

### <a name="what-is-a-collection"></a>Vad är en samling?
En samling är en grupp av dokument och deras associerade JavaScript-programlogik. En samling är en fakturerbar enhet där den [kostnaden](performance-levels.md) bestäms av dataflödet och använt lagring. Samlingar kan omfatta en eller flera partitioner eller servrar och kan skalas för att hantera praktiskt taget obegränsade volymer av lagring eller dataflöde.

Samlingar är också faktureringsenheterna för Azure Cosmos DB. Varje samling faktureras timvis baserat på etablerat dataflöde och använt lagringsutrymme. Mer information finns i [priser för Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). 

### <a name="how-do-i-create-a-database"></a>Hur skapar jag en databas?
Du kan skapa databaser med hjälp av den [Azure-portalen](https://portal.azure.com), enligt beskrivningen i [lägger till en samling](create-documentdb-dotnet.md#create-collection), med en av de [Azure Cosmos DB SDK](documentdb-sdk-dotnet.md), eller [REST API: er](/rest/api/documentdb/). 

### <a name="how-do-i-set-up-users-and-permissions"></a>Hur ställer jag in användare och behörigheter?
Du kan skapa användare och behörigheter genom att använda en av de [Cosmos DB API SDK](documentdb-sdk-dotnet.md) eller [REST API: er](/rest/api/documentdb/).  

### <a name="does-the-documentdb-api-support-sql"></a>Stöder API: et DocumentDB SQL?
SQL-frågespråket är en förbättrad underuppsättning av de frågefunktioner som stöds av SQL. Azure Cosmos-Databasens SQL-frågespråket ger omfattande hierarkiska och relationella operatorer och kan utvidgas via JavaScript-baserade, användardefinierade funktioner (UDF). JSON-grammatik gör det möjligt för att modellera JSON-dokument som träd med märkt noder som används av både Azure Cosmos DB tekniker för automatisk indexering och SQL-frågedialekt av Azure Cosmos DB. Information om hur du använder SQL-grammatik finns i [QueryDocumentDB] [ query] artikel.

### <a name="does-the-documentdb-api-support-sql-aggregation-functions"></a>Stöder DocumentDB API SQL aggregeringsfunktioner?
DocumentDB-API: et stöder låg latens aggregering skaländras via mängdfunktioner `COUNT`, `MIN`, `MAX`, `AVG`, och `SUM` via SQL-grammatik. Mer information finns i [mängdfunktionerna](documentdb-sql-query.md#Aggregates).

### <a name="how-does-the-documentdb-api-provide-concurrency"></a>Hur tillhandahåller DocumentDB API samtidighet?
DocumentDB-API: et stöder optimistisk samtidighetskontroll (OCC) via HTTP-entitetstaggar eller ETags. Varje DocumentDB-API-resurs har en ETag och ETag är inställd på servern varje gång ett dokument har uppdaterats. ETag-sidhuvud och det aktuella värdet ingår i alla svarsmeddelanden. ETags kan användas med If-Match-sidhuvudet så att servern för att avgöra om en resurs ska uppdateras. Värdet för If-Match är ETag-värde som ska kontrolleras mot. Om ETag-värde matchar serverns ETag-värde, uppdateras resursen. Om en ETag är inte längre aktuell, avvisar servern igen med ett ”HTTP 412 Förutsättningsfel” svarskoden. Klienten hämtar sedan nytt resurs för att få det aktuella ETag-värdet för resursen. ETags kan dessutom användas med If-None-Match-sidhuvudet för att avgöra om nytt hämta av en resurs som behövs.

Om du vill använda Optimistisk samtidighet i .NET den [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) klass. Ett .NET-exempel finns [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) i DocumentManagement prov på GitHub.

### <a name="how-do-i-perform-transactions-in-the-documentdb-api"></a>Hur gör jag transaktioner i DocumentDB-API
DocumentDB-API: et stöder språkintegrerade transaktioner via JavaScript-lagrade procedurer och utlösare. Alla databasåtgärder i skript körs under ögonblicksbildisolering. Om det är en enskild partition samling är körningen begränsad till samlingen. Om samlingen är partitionerad är körningen begränsad till dokument med samma partitionsnyckel värde i samlingen. En ögonblicksbild av dokumentversionerna (ETags) tas i början av transaktionen och verkställs endast om skriptet lyckas. Om JavaScript genererar ett fel återställs transaktionen. Mer information finns i [programmering av serversidan JavaScript för Azure Cosmos DB](programming.md).

### <a name="how-can-i-bulk-insert-documents-into-cosmos-db"></a>Hur kan jag-massinfogning dokument i Cosmos DB?
Du kan-massinfogning dokument i Azure Cosmos DB på två sätt:

* Datamigreringsverktyget enligt beskrivningen i [verktyg för Azure Cosmos DB](import-data.md).
* Lagrade procedurer som beskrivs i [programmering av serversidan JavaScript för Azure Cosmos DB](programming.md).

### <a name="does-the-documentdb-api-support-resource-link-caching"></a>Stöder den DocumentDB API stöd för cachelagring av resurslänkar?
Ja, eftersom Azure Cosmos DB är en RESTful-tjänst, resurslänkar är oföränderlig och kan cachelagras. DocumentDB-API-klienter kan ange en ”If-None-Match”-rubrik för läsning mot ett resurs-liknande dokument eller en samling och sedan uppdatera sina lokala kopior när serverversionen har ändrats.

### <a name="is-a-local-instance-of-documentdb-api-available"></a>Är en lokal instans av DocumentDB API tillgängligt?
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

## <a name="develop-with-the-table-api-preview"></a>Utveckla med tabell-API (förhandsgranskning)

### <a name="terms"></a>Villkor 
Azure Cosmos DB: Tabell-API (förhandsgranskning) refererar till premium erbjudande med Azure Cosmos DB för tabellen support presenterades vid Build 2017. 

Standardtabell SDK är den befintliga Azure Storage-tabellen SDK. 

### <a name="how-can-i-use-the-new-table-api-preview-offering"></a>Hur kan jag använda nytt erbjudande för tabell-API (förhandsgranskning)? 
Azure Cosmos DB tabell API är tillgängliga i den [Azure-portalen][azure-portal]. Du måste först registrera dig för en Azure-prenumeration. När du har registrerat dig kan du lägga till ett Azure Cosmos DB tabell API-konto till din Azure-prenumeration och sedan lägga till tabeller i ditt konto. 

I förhandsversionen, när [SDK](../cosmos-db/table-sdk-dotnet.md) är tillgänglig för .NET, kan du sätta igång genom att slutföra den [tabell API](../cosmos-db/create-table-dotnet.md) Snabbkurs artikel.

### <a name="do-i-need-a-new-sdk-to-use-the-table-api-preview"></a>Måste en ny SDK använda tabell-API (förhandsgranskning)? 
Ja, den [Windows Azure Storage Premium Table (förhandsgranskning) SDK](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable) är tillgängligt på NuGet. Mer information finns på den [Cosmos-tabellen på Azure DB .NET API: et: hämta och viktig information](https://github.com/Microsoft/azure-docs-pr/cosmos-db/table-sdk-dotnet.md) sidan. 

### <a name="how-do-i-provide-feedback-about-the-sdk-or-bugs"></a>Hur jag för att ge feedback om SDK eller buggar?
Du kan dela din feedback i något av följande sätt:

* [Uservoice](https://feedback.azure.com/forums/599062-azure-cosmos-db-table-api)
* [MSDN-forum](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=AzureDocumentDB)
* [StackOverflow](http://stackoverflow.com/questions/tagged/azure-cosmosdb)

### <a name="what-is-the-connection-string-that-i-need-to-use-to-connect-to-the-table-api-preview"></a>Vad är den anslutningssträng som jag behöver använda för att ansluta till tabellen API (förhandsgranskning)?
Anslutningssträngen är:
```
DefaultEndpointsProtocol=https;AccountName=<AccountNamefromCosmos DB;AccountKey=<FromKeysPaneofCosmosDB>;TableEndpoint=https://<AccountNameFromDocumentDB>.documents.azure.com
```
Du kan hämta anslutningssträngen från sidan nycklar i Azure-portalen. 

### <a name="how-do-i-override-the-config-settings-for-the-request-options-in-the-new-table-api-preview"></a>Hur jag för att åsidosätta konfigurationsinställningarna för begäran-alternativ i nya tabell-API (förhandsgranskning)?
Information om konfigurationsinställningarna finns [Azure Cosmos DB funktioner](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Du kan ändra inställningarna när du lägger till dem i app.config i avsnittet AppSettings i klientprogrammet.

    <appSettings>
        <add key="TableConsistencyLevel" value="Eventual|Strong|Session|BoundedStaleness|ConsistentPrefix"/>
        <add key="TableThroughput" value="<PositiveIntegerValue"/>
        <add key="TableIndexingPolicy" value="<jsonindexdefn>"/>
        <add key="TableUseGatewayMode" value="True|False"/>
        <add key="TablePreferredLocations" value="Location1|Location2|Location3|Location4>"/>....
    </appSettings>


### <a name="are-there-any-changes-for-customers-who-are-using-the-existing-standard-table-sdk"></a>Finns det några ändringar för kunder som använder den befintliga tabellen SDK som standard?
Ingen. Det finns inga ändringar för befintliga eller nya kunder som använder den befintliga tabellen SDK som standard. 

### <a name="how-do-i-view-table-data-that-is-stored-in-azure-cosmos-db-for-use-with-the-table-api-review"></a>Hur visar tabelldata som lagras i Azure Cosmos-databasen för användning med tabell-API (granskning)? 
Du kan använda Azure-portalen för att bläddra i data. Du kan också använda tabellen API (förhandsgranskning)-koden eller verktyg som nämns i nästa svar. 

### <a name="which-tools-work-with-the-table-api-preview"></a>Vilka verktyg tillsammans med tabell-API (förhandsgranskning)? 
Du kan använda den äldre versionen av Azure Explorer (0.8.9).

Verktyg med möjlighet att ta en anslutningssträng i det format som specificerats stöder tidigare ny tabell-API (förhandsversion). En lista över Tabellverktyg finns på den [Azure Storage-klientverktyg](../storage/common/storage-explorers.md) sidan. 

### <a name="do-powershell-or-azure-cli-work-with-the-new-table-api-preview"></a>PowerShell eller Azure CLI fungerar med den nya tabellen API (förhandsgranskning)?
Vi planerar att lägga till stöd för PowerShell och Azure CLI för tabell-API (förhandsversion). 

### <a name="is-the-concurrency-on-operations-controlled"></a>Är samtidigheten på åtgärder som styrs?
Ja, tillhandahålls Optimistisk samtidighet med hjälp av mekanismen för ETag. 

### <a name="is-the-odata-query-model-supported-for-entities"></a>Stöds modellen för OData-frågan för enheter? 
Ja, tabell-API (förhandsversion) stöder OData-fråge- och LINQ-fråga. 

### <a name="can-i-connect-to-the-standard-azure-table-and-the-new-premium-table-api-preview-side-by-side-in-the-same-application"></a>Kan ansluta till standard Azure-tabellen och nya premium tabell API (förhandsgranskning) sida vid sida i samma program? 
Ja, du kan ansluta genom att skapa två separata instanser av CloudTableClient, varje pekar på en egen URI via anslutningssträngen.

### <a name="how-do-i-migrate-an-existing-azure-table-storage-application-to-this-new-offering"></a>Hur jag för att migrera en befintlig Azure Table storage-program till den här nya erbjudande?
Om du vill dra nytta av nya tabell API erbjudandet på din befintliga lagring tabelldata, kontakta [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com). 

### <a name="what-is-the-roadmap-for-this-service-and-when-will-you-offer-other-standard-table-api-functionality"></a>Vad är översikt för den här tjänsten och när du erbjuder andra standard tabell API-funktioner?
Vi planerar att lägga till stöd för SAS-token, ServiceContext, statistik, kryptering, analyser och andra funktioner på klientsidan när vi går vidare mot GA. Du kan ge oss feedback om [Uservoice](https://feedback.azure.com/forums/599062-azure-cosmos-db-table-api). 

### <a name="how-is-expansion-of-the-storage-size-done-for-this-service-if-for-example-i-start-with-n-gb-of-data-and-my-data-will-grow-to-1-tb-over-time"></a>Hur är expandering av lagringsstorleken Utfärdad för den här tjänsten om du till exempel jag börja med  *n*  GB data och Mina data kommer att växa till 1 TB över tid? 
Azure Cosmos-DB är utformad att ge obegränsad lagring med hjälp av teckenbredden. Tjänsten kan övervaka och effektivt öka din lagring. 

### <a name="how-do-i-monitor-the-table-api-preview-offering"></a>Hur övervakar erbjudandet tabell-API (förhandsgranskning)?
Du kan använda tabellen API (förhandsgranskning) **mått** fönstret för att övervaka begäranden och lagringskvoten. 

### <a name="how-do-i-calculate-the-throughput-i-require"></a>Hur jag för att beräkna genomflöde I kräver?
Du kan använda kapacitet exteriörbedömning för att beräkna TableThroughput som krävs för åtgärderna. Mer information finns i [uppskattning begära enheter och datalagring](https://www.documentdb.com/capacityplanner). I allmänhet kan du representerar entiteten som JSON och ange siffrorna för din verksamhet. 

### <a name="can-i-use-the-new-table-api-preview-sdk-locally-with-the-emulator"></a>Kan jag använda den nya tabellen API (förhandsgranskning) SDK lokalt med emulatorn?
Ja, du kan använda tabellen API (förhandsversion) med lokala emulatorn när du använder den nya SDK. Hämta nya emulatorn genom att gå till [Använd Azure Cosmos DB-emulatorn för lokal utveckling och testning](local-emulator.md). Värdet för StorageConnectionString i app.config måste vara:

```
DefaultEndpointsProtocol=https;AccountName=localhost;AccountKey=C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==;TableEndpoint=https://localhost:8081`. 
```

### <a name="can-my-existing-application-work-with-the-table-api-preview"></a>Fungerar mitt befintliga program med tabell-API (förhandsgranskning) 
Ytan på den nya tabellen API (förhandsversion) är kompatibel med den befintliga Azure standard tabellen SDK över skapa, ta bort, uppdatera och fråga åtgärder i .NET-API. Se till att du har en radnyckel eftersom tabellen API (förhandsgranskning) kräver både en partition och en nyckel för raden. Vi planerar också att lägga till flera SDK-stöd när vi går vidare mot GA för det här tjänsterbjudandet.

### <a name="do-i-need-to-migrate-my-existing-azure-table-based-applications-to-the-new-sdk-if-i-do-not-want-to-use-the-table-api-preview-features"></a>Behöver jag migrera Mina befintliga Azure table-baserade program till nya SDK om jag inte vill använda funktioner i tabell-API (förhandsgranskning)?
Nej, kan du skapa och använda den befintliga Standardtabell tillgångar utan avbrott av något slag. Om du inte använder den nya tabellen API (förhandsgranskning) kan inte du omfattas av automatisk indexet, ytterligare konsekvenskontroll alternativet eller distributionslistor. 

### <a name="how-do-i-add-replication-of-the-data-in-the-premium-table-api-preview-across-multiple-regions-of-azure"></a>Hur lägger jag till replikering av data i premium tabell-API (förhandsgranskning) över flera regioner Azure?
Du kan använda Azure DB som Cosmos-portalen [globala replikeringsinställningarna](tutorial-global-distribution-documentdb.md#portal) du lägger till regioner som är lämpliga för ditt program. För att utveckla ett globalt distribuerat program bör du också lägga till programmet PreferredLocation uppgifterna till den lokala regionen för att tillhandahålla låg latens som skrivskyddade. 

### <a name="how-do-i-change-the-primary-write-region-for-the-account-in-the-premium-table-api-preview"></a>Hur ändrar jag primära write-region för kontot i premium tabell-API (förhandsgranskning)
Du kan använda fönstret Azure Cosmos DB globala replikering portal för att lägga till en region och sedan växla över till den nödvändiga regionen. Instruktioner finns i [utveckling med flera regioner Azure Cosmos DB konton](regional-failover.md). 

### <a name="how-do-i-configure-my-preferred-read-regions-for-low-latency-when-i-distribute-my-data"></a>Hur konfigurerar jag min primära skrivskyddade regioner för låg fördröjning när jag fördela mina data? 
Använda PreferredLocation nyckel i filen app.config att läsa från den lokala platsen. Tabell-API (förhandsgranskning) genererar ett fel om LocationMode har angetts för befintliga program. Ta bort den koden eftersom premium tabell-API (förhandsgranskning) hämtar informationen från filen app.config. Mer information finns i [Azure Cosmos DB funktioner](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities).

### <a name="how-should-i-think-about-consistency-levels-in-the-table-api-preview"></a>Hur får jag tänker konsekvensnivåer i tabell-API (förhandsgranskning)? 
Azure Cosmos-DB tillhandahåller välmotiverat avvägningarna mellan konsekvens, tillgänglighet och svarstid. Azure Cosmos-DB erbjuder fem konsekvensnivåer för tabell-API (förhandsgranskning) utvecklare, så kan du välja rätt konsekvenskontroll modellen på tabellnivå och enskilda begär vid läsning av data. När en klient ansluter ange den en konsekvenskontroll nivå. Du kan ändra nivån via app.config inställningen för värdet för nyckeln TableConsistencyLevel. 

Tabell-API (förhandsversion) ger låg latens läser med ”Läs egna skrivningar” med sessionskonsekvens som standard. Mer information finns i [konsekvensnivåer](consistency-levels.md). 

Som standard ger Azure Table storage stark konsekvens inom en region och Eventual konsekvens på sekundära platser. 

### <a name="does-azure-cosmos-db-offer-more-consistency-levels-than-standard-tables"></a>Erbjuder Azure Cosmos DB mer konsekvensnivåer än standardtabeller?
Ja, finns information om hur du kan utnyttja distribuerade uppbyggnad Azure Cosmos DB [konsekvensnivåer](consistency-levels.md). Eftersom garantier har angetts för nivåerna konsekvens, kan du använda dem med förtroende. Mer information finns i [Azure Cosmos DB funktioner](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities).

### <a name="when-global-distribution-is-enabled-how-long-does-it-take-to-replicate-the-data"></a>När globala distribution är aktiverat, hur lång tid tar det för att replikera data?
Vi genomför data varaktigt i den lokala regionen och skicka data till andra regioner direkt i en fråga i millisekunder. Replikeringen är beroende av endast de fram och åter tid för datacenter. Läs mer om Azure Cosmos DB global distributionsplatsen kapacitet i [Azure Cosmos DB: en tjänst för globalt distribuerad databas på Azure](distribute-data-globally.md).

### <a name="can-the-read-request-consistency-level-be-changed"></a>Kan konsekvensnivå läsbegäran ändras?
Med Azure Cosmos DB, kan du ange nivån konsekvenskontroll på nivån behållare (på tabellen). Du kan ändra nivån genom att ange värdet för TableConsistencyLevel nyckel i filen app.config med hjälp av SDK. Möjliga värden är: stark, begränsat föråldrad, Session, konsekvent Prefix och Eventual. Mer information finns i [data justerbara konsekvensnivåer i Azure Cosmos DB](consistency-levels.md). Viktiga idé är att du inte kan ange begäran konsekvenskontrollen nivån på mer än inställningen för tabellen. Du kan exempelvis ange konsekvensnivå för tabellen på Eventual och begäran konsekvensnivå på starka. 

### <a name="how-does-the-premium-table-api-preview-account-handle-failover-if-a-region-goes-down"></a>Hur hanterar redundans kontot premium tabell-API (förhandsgranskning) om en region kraschar? 
Premium API för tabellen (förhandsgranskning) lånar från Azure Cosmos DB globalt distribuerade plattform. Aktivera minst en mer region för kontot i Azure DB som Cosmos-portal för att säkerställa att ditt program kan tolerera datacenter driftstopp, [utveckling med flera regioner Azure Cosmos DB konton](regional-failover.md). Du kan ange prioritet för regionen med hjälp av portalen [utveckling med flera regioner Azure Cosmos DB konton](regional-failover.md). 

Du kan lägga till så många områden som du vill använda för kontot och styra där den kan växla över till genom att tillhandahålla en prioritet för växling vid fel. Naturligtvis för att använda databasen, måste du ge det ett program för. När du gör det får inte driftstopp för dina kunder. Klienten SDK är auto värdar. Det vill säga kan identifiera den region som är igång och automatiskt växla över till den nya regionen.

### <a name="is-the-premium-table-api-preview-enabled-for-backups"></a>Premium tabell-API (förhandsversion) har aktiverats för säkerhetskopiering?
Ja, premium tabell-API (förhandsgranskning) lånar från Azure Cosmos DB plattform för säkerhetskopiering. Säkerhetskopieringar görs automatiskt. Mer information finns i [Online säkerhetskopiering och återställning med Azure Cosmos DB](online-backup-and-restore.md).

 
### <a name="does-the-table-api-preview-index-all-attributes-of-an-entity-by-default"></a>Tabell-API (förhandsversion) index alla attribut för en entitet som standard?
Ja, alla attribut för en entitet indexeras som standard. Mer information finns i [Azure Cosmos DB: indexering principer](indexing-policies.md). 

### <a name="does-this-mean-i-do-not-have-to-create-multiple-indexes-to-satisfy-the-queries"></a>Detta betyder det inte behöver skapa flera index för frågorna? 
Ja, Azure Cosmos DB ger automatisk indexering av alla attribut utan någon schemadefinition. Det här automation Frigör utvecklare kan fokusera på programmet istället för på skapandet av index och hantering. Mer information finns i [Azure Cosmos DB: indexering principer](indexing-policies.md).

### <a name="can-i-change-the-indexing-policy"></a>Kan jag ändra indexprincip?
Ja, du kan ändra indexprincip genom att tillhandahålla indexdefinitionen. Mer information finns i [Azure Cosmos DB funktioner](../cosmos-db/tutorial-develop-table-dotnet.md#azure-cosmos-db-capabilities). Du behöver korrekt koda och escape-inställningarna. 

I strängen json-format i filen app.config:
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
I Förhandsgranska ger tabell-API samma fråga funktioner som Azure Table storage. Azure Cosmos-DB också stöd för sortering, aggregeringar, geospatiala frågan, hierarki och en mängd olika inbyggda funktioner. Vi ger ytterligare funktioner i tabellen API i en framtida tjänstuppdatering. Mer information finns i [SQL-frågor för Azure Cosmos DB DocumentDB API](../documentdb/documentdb-sql-query.md).
 
### <a name="when-should-i-change-tablethroughput-for-the-table-api-preview"></a>När bör jag ändra TableThroughput för tabell-API (förhandsgranskning)
Du bör ändra TableThroughput när någon av följande villkor gäller:
* Du utför en extrahering, transformering och laddning (ETL) av data, eller om du vill överföra stora mängder data på kort tid. 
* Du behöver mer genomströmning från behållaren på serverdelen. Till exempel visas att använda genomströmning är större än det tillhandahållna dataflödet och du har komma begränsats. Mer information finns i [Set genomströmning för Azure Cosmos DB behållare](set-throughput.md).

### <a name="can-i-scale-up-or-scale-down-the-throughput-of-my-table-api-preview-table"></a>Kan jag skala upp eller ned genomflödet av tabellens tabell-API (förhandsgranskning)? 
Ja, kan du använda portalen Azure Cosmos DB skala fönstret för att skala genomflödet. Mer information finns i [Set genomströmning](set-throughput.md).

### <a name="is-a-default-tablethroughput-set-for-newly-provisioned-tables"></a>Är en standarduppsättning TableThroughput för nyetablerade tabeller?
Ja, om du inte åsidosätter TableThroughput via app.config och Använd inte en förskapad behållare i Azure Cosmos DB tjänsten skapar en tabell med genomströmning på 400.
 
### <a name="is-there-any-change-of-pricing-for-existing-customers-of-the-standard-table-api"></a>Finns det några ändringar av priser för befintliga kunder standard tabell API?
Ingen. Det finns ingen ändring i priset för befintliga standard tabell API-kunder. 

### <a name="how-is-the-price-calculated-for-the-table-api-preview"></a>Hur beräknas priset för tabell-API (förhandsgranskning)? 
Priset beror på allokerade TableThroughput. 

### <a name="how-do-i-handle-any-throttling-on-the-tables-in-table-api-preview-offering"></a>Hur hanterar jag en begränsning på tabellerna i tabell-API (förhandsversion) ger? 
Om frekvensen för begäran överskrider det tillhandahållna dataflödet kapacitet för den underliggande behållaren, du får ett fel och SDK kommer att försöka anropet genom att använda principen försök igen.

### <a name="why-do-i-need-to-choose-a-throughput-apart-from-partitionkey-and-rowkey-to-take-advantage-of-the-premium-table-api-preview-offering-of-azure-cosmos-db"></a>Varför måste välja en genomströmning förutom PartitionKey och RowKey för att dra nytta av premium tabell-API (förhandsgranskning) erbjudande på Azure Cosmos DB?
Azure Cosmos-DB anger en standard-genomströmning för din behållaren om du inte anger något i filen app.config. 

Azure Cosmos-DB tillhandahåller garantier för prestanda och fördröjning med övre gränser för åtgärden. Garantin är möjligt när motorn kan tillämpa styrning på klientens åtgärder. Ange TableThroughput garanterar att du får garanterad genomflöde och svarstid, eftersom plattformen reserverar denna kapacitet och garanterar operativa lyckades. 

Med specifikationen dataflöde kan ändra du Elastiskt den om du vill dra nytta av säsongsvärdet för programmet, passar genomflöde och spara kostnader.

### <a name="azure-storage-sdk-has-been-very-inexpensive-for-me-because-i-pay-only-to-store-the-data-and-i-rarely-query-the-new-azure-cosmos-db-offering-seems-to-be-charging-me-even-though-i-have-not-performed-a-single-transaction-or-stored-anything-can-you-please-explain"></a>Azure Storage SDK: N har så billigt mig, eftersom jag betala endast att lagra data, och jag sällan frågan. Nya Azure Cosmos DB erbjudandet verkar debitering mig även om jag inte har utfört en enda transaktion eller lagras något. Kan du ange förklara?

Azure Cosmos-DB är avsedd att vara ett globalt distribuerade, SLA-baserade system med garantier för tillgänglighet, svarstid och genomströmning. När du reserverar genomflöde i Azure Cosmos DB garanteras den, till skillnad från genomflödet i andra system. Azure Cosmos-DB tillhandahåller ytterligare funktioner som kunder har begärt, till exempel sekundärindex och distributionslistor.  

### <a name="i-never-get-a-quota-full-notification-indicating-that-a-partition-is-full-when-i-ingest-data-into-table-storage-with-the-table-api-preview-i-do-get-this-message-is-this-offering-limiting-me-and-forcing-me-to-change-my-existing-application"></a>Jag får aldrig en ”full” kvotmeddelanden (som anger att en partition är full) när jag mata in data i Table storage. Med tabell-API (förhandsgranskning) detta meddelande visas. Detta ger att begränsa mig och tvinga jag ändra Mina befintliga program?

Azure Cosmos-DB är ett SLA-baserat system som ger obegränsad skala garantier för latens, dataflöde, tillgänglighet och konsekvenskontroll. Kontrollera att index och Datastorleken är hanterbar och skalbar för att säkerställa prestanda Garanterad premium. 10 GB-gränsen för antalet enheter eller objekt per Partitionsnyckeln är att säkerställa att vi ger utmärkt prestanda för sökning och fråga. För att säkerställa att programmet ska skalas bra även för Azure Storage, rekommenderar vi att du *inte* skapa en varm partition genom att lagra all information i en partition och exempelvärden. 

### <a name="so-partitionkey-and-rowkey-are-still-required-with-the-new-table-api-preview"></a>Så PartitionKey och RowKey krävs fortfarande med den nya tabellen API (förhandsgranskning)? 
Ja. Eftersom ytan på tabell-API (förhandsversion) är samma som Table storage SDK innehåller Partitionsnyckeln ett effektivt sätt att fördela data. Radnyckeln är unikt i den aktuella partitionen. Radnyckeln måste finnas och får inte vara null som standard SDK. RowKey längd är 255 byte och längden på PartitionKey är 100 byte (snart ska ökas till 1 KB). 

### <a name="what-are-the-error-messages-for-the-table-api-preview"></a>Vad är felmeddelanden för tabell-API (förhandsgranskning)?
Eftersom den här förhandsgranskningen är kompatibel med tabellen standard mappar de flesta av fel som till felen från tabellen standard. 

### <a name="why-do-i-get-throttled-when-i-try-to-create-lot-of-tables-one-after-another-in-the-table-api-preview"></a>Varför jag hämta begränsas när jag försöker skapa många tabeller efter varandra i tabell-API (förhandsgranskning)?
Azure Cosmos-DB är ett SLA-baserat system som tillhandahåller svarstid, dataflöde, tillgänglighet och konsekvens garanterar. Eftersom det är ett etablerade system, reserverar resurser för att garantera att dessa krav. Snabb frekvensen för skapande av tabeller identifieras och begränsas. Vi rekommenderar att du tittar på frekvensen för skapande av tabeller och sänka den till mindre än 5 per minut. Kom ihåg att tabellen API (förhandsversion) är ett etablerade system. Den tidpunkt då du etablera, börjar du betalar för den. 

## <a name="develop-against-the-graph-api-preview"></a>Utveckla mot Graph-API (förhandsgranskning)
### <a name="how-can-i-apply-the-functionality-of-graph-api-preview-to-azure-cosmos-db"></a>Hur kan använda funktionen för Graph API (förhandsgranskning) till Azure Cosmos DB?
Du kan använda ett tillägg-bibliotek för att använda funktionen för Graph API (förhandsversion). Det här biblioteket kallas Microsoft Azure diagram och det är tillgängligt på NuGet. 

### <a name="it-looks-like-you-support-the-gremlin-graph-traversal-language-do-you-plan-to-add-more-forms-of-query"></a>Det verkar som du stöder Gremlin diagram traversal språk. Du planerar att lägga till flera typer av frågan?
Ja, vi planerar att lägga till andra mekanismer för frågan i framtiden. 

### <a name="how-can-i-use-the-new-graph-api-preview-offering"></a>Hur kan jag använda nytt erbjudande för Graph API (förhandsgranskning)? 
Kom igång genom att slutföra den [Graph API](../cosmos-db/create-graph-dotnet.md) Snabbkurs artikel.

<a id="moving-to-cosmos-db"></a>
## <a name="questions-from-documentdb-customers"></a>Frågor från DocumentDB kunder
### <a name="why-are-you-moving-to-azure-cosmos-db"></a>Varför flyttar du till Azure Cosmos DB? 

Azure Cosmos-DB är nästa stora steget i globalt distribuerade med skalning molnet databaser. Som en DocumentDB-kund nu har du åtkomst till banbrytande system och funktioner som erbjuds av Azure Cosmos DB.

Azure Cosmos-DB starta som en ”projekt Florens” 2010 för att åtgärda de eventuella stötestenar utvecklare bygga storskaliga program i Microsoft. Utmaningar för att skapa globalt distribuerade appar är inte unikt till Microsoft, så vi gjort första generationen av den här tekniken finns i 2015 Azure utvecklare i form av Azure DocumentDB. 

Sedan dess har vi lagt till nya funktioner och introduceras viktiga nya funktioner. Azure Cosmos-DB är resultatet. Som en del av den här versionen blir DocumentDB kunder med sina data automatiskt och sömlöst du Azure DB som Cosmos-kunder. Dessa funktioner finns i områden som core databasmotorn, samt global distributionsplatsen, elastisk skalbarhet och branschledande, omfattande SLA: er. Vi har särskilt utvecklats Azure Cosmos DB databasmotorn effektivt att mappa alla populära datamodeller typen System och API: er till den underliggande datamodellen av Azure Cosmos DB. 

Den aktuella developer-riktade uttryck för att arbetet är nytt stöd för [Gremlin](../cosmos-db/graph-introduction.md) och [Table storage API: er](../cosmos-db/table-introduction.md). Detta är bara början. Vi planerar att lägga till andra populära API: er och nyare datamodeller över tid, med mer utvecklingen av prestanda och lagringsutrymme på global nivå. 

Det är viktigt att påpeka att av DocumentDB [SQL dialect](../documentdb/documentdb-sql-query.md) har alltid varit en av de många API: er som har stöd för den underliggande Azure Cosmos DB. För utvecklare som använder en helt hanterad tjänst, till exempel Azure Cosmos DB, är det enda gränssnittet till tjänsten API: er som exponeras av tjänsten. Inget verkligen ändringar för befintliga DocumentDB-kunder. I Azure Cosmos DB få exakt samma SQL API som erbjuder DocumentDB. Och nu (och senare) du kan komma åt andra funktioner som tidigare inte tillgänglig 

Ett annat uttryck för våra fortsatt arbete är utökade grunden för global och elastisk skalbarhet dataflöden och lagringsutrymmen. Vi har gjort några grundläggande förbättringar för undersystemet global distributionsplatsen. En av de många sådana developer-riktade funktionerna är konsekvent prefixet konsekvent modell, som gör en totala fem väldefinierade konsekvenskontroll modeller. Vi kommer att släppa många mer intressant funktioner som de förfaller. 

### <a name="what-do-i-need-to-do-to-ensure-that-my-documentdb-resources-continue-to-run-on-azure-cosmos-db"></a>Vad behöver jag för att säkerställa att DocumentDB-resurser fortsätter att köras på Azure Cosmos DB?

Du behöver göra några ändringar alls. DocumentDB-resurser är nu Azure Cosmos DB resurser och uppstod utan avbrott i tjänsten när flyttningen inträffade.

### <a name="what-changes-do-i-need-to-make-for-my-app-to-work-with-azure-cosmos-db"></a>Vad behöver jag göra för min app att fungera med Azure Cosmos DB?

Det finns inga ändringar att göra. Namn för klasser, namnområden och NuGet-paketet har inte ändrats. Som alltid rekommenderar vi att du behåller din SDK: er uppdaterade för att dra nytta av de senaste funktionerna och förbättringarna. 

### <a name="whats-changed-in-the-azure-portal"></a>Nyheter i Azure portal?

DocumentDB visas inte längre i portalen som en Azure-tjänst. Är en ny Azure DB som Cosmos-ikon som visas i följande bild i dess ställe. Alla samlingar är tillgängliga, som de fanns innan, och du kan fortfarande skala dataflöde, ändra konsekvensnivåer och övervaka SLA: er. Funktionerna i Data Explorer (förhandsversion) har förbättrats. Du kan nu visa och redigera dokument, skapa och köra frågor och arbeta med lagrade procedurer, utlösare och UDF från en sida som visas i följande bild: 

![Bladet Azure Cosmos DB samlingar](./media/faq/cosmos-db-data-explorer.png)

### <a name="are-there-changes-to-pricing"></a>Finns det ändringar av priser?

Nej, kostnaden för att köra din app på Azure Cosmos DB är samma som förut.

### <a name="are-there-changes-to-the-slas"></a>Finns det ändringar av SLA: er?

Nej, serviceavtal för tillgänglighet, konsekvens, svarstid och genomströmning ändras inte och visas fortfarande i portalen. Mer information finns i [SLA för Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/).
   
![Att göra-app med exempeldata](./media/faq/azure-cosmosdb-portal-metrics-slas.png)


[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md
