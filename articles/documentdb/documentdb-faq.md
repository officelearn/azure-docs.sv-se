<properties 
    pageTitle="Frågor om DocumentDB-databasen – Vanliga frågor och svar | Microsoft Azure" 
    description="Svar på vanliga frågor om Azure DocumentDB, en NoSQL-dokumentdatabastjänst för JSON. Svar på databasfrågor som rör kapacitet, prestandanivåer och skalning." 
    keywords="Database questions, frequently asked questions, documentdb, azure, Microsoft azure"
    services="documentdb" 
    authors="mimig1" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="03/30/2016" 
    ms.author="mimig"/>


#Vanliga frågor om DocumentDB

## Databasfrågor om grunderna i Microsoft Azure DocumentDB

### Vad är Microsoft Azure DocumentDB? 
Microsoft Azure DocumentDB är en mycket skalbar NoSQL-databastjänst för dokument, som erbjuder många frågealternativ över schemafria data, hjälper till att leverera konfigurerbar och tillförlitlig prestanda och möjliggör snabb utveckling. Allt detta via en hanterad plattform med kraften och räckvidden hos Microsoft Azure. DocumentDB är lösningen för webb-, mobil-, spel- och IoT-appar där förutsägbara dataflöden, låg svarstid och en schemafri datamodell är viktiga krav. DocumentDB ger schemaflexibilitet och omfattande indexering via en inbyggd JSON-datamodell och innehåller transaktionellt stöd för flera dokument med integrerat JavaScript.  
  
Fler frågor, svar och instruktioner om distribution och användning av tjänsten finns i [dokumentationssidan för DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

### Vilken typ av databas är DocumentDB?
DocumentDB är en dokumentorienterad NoSQL-databas som lagrar data i JSON-format.  DocumentDB stöder kapslade, oberoende datastrukturer där förfrågningar kan skickas via en omfattande DocumentDB [SQL-frågegrammatik](documentdb-sql-query.md). DocumentDB ger transaktionell bearbetning med hög prestanda av JavaScript på serversidan via [lagrade procedurer, utlösare och användardefinierade funktioner](documentdb-programming.md). Databasen stöder också utvecklarjusterade konsekvensnivåer med associerade [prestandanivåer](documentdb-performance-levels.md).
 
### Har DocumentDB-databaser tabeller som relationsdatabaser (RDBMS)?
Nej, DocumentDB lagrar data i samlingar av JSON-dokument.  Information om DocumentDB-resurser finns i [Modell och koncept för DocumentDB-resurser](documentdb-resources.md). 

### Har DocumentDB-databaser stöd för schemafria data?
Ja, DocumentDB tillåter att appar lagrar godtyckliga JSON-dokument utan schemadefinition eller tips. Data är omedelbart tillgängliga för frågor via DocumentDB SQL-frågegränssnittet.   

### Stöder DocumentDB ACID-transaktioner?
Ja, DocumentDB stöder transaktioner mellan dokument uttryckta som JavaScript-lagrade procedurer och utlösare. Transaktioner begränsas till en enskild partition inom varje samling och utförs med ACID-semantik som allt eller inget isolerat från annan kod och andra användarförfrågningar som körs samtidigt.  Om undantag utlöses via serversidans körning av JavaScript-appkoden återställs hela transaktionen. 

### Vad är vanliga användningsområden för DocumentDB?  
DocumentDB är ett bra alternativ för nya webb-, mobil-, spel- och IoT-appar där automatisk skalning, förutsägbar prestanda, snabba beställningar med svarstider på millisekunder och möjlighet till frågor i schemafria data är viktigt. DocumentDB lämpar sig för snabb utveckling och stöder kontinuerlig iteration av appens datamodeller. Appar som hanterar användargenererat innehåll och data är [vanliga användningsområden för DocumentDB](documentdb-use-cases.md).  

### Hur erbjuder DocumentDB förutsägbar prestanda?
En frågeenhet (Request unit eller RU) är ett mått på dataflödet i DocumentDB. 1 RU motsvarar dataflödet i GET av ett dokument på 1 kB. Varje åtgärd i DocumentDB, inklusive läsning, skrivning, SQL-frågor och lagrade procedurkörningar har en deterministisk frågeenhet baserad på dataflödet som krävs för att slutföra åtgärden. I stället för att fundera på CPU, IO och minne och hur de påverkar appens dataflöde kan du tänka på en enskild åtgärd för en frågeenhet.

Varje DocumentDB-samling kan reserveras med etablerat dataflöde räknat som dataflöde av frågeenheter per sekund. Du kan jämföra enskilda förfrågningar för att mäta värdet av deras frågeenheter och etablera samlingar som hanterar totalsumman av frågeenheter över alla förfrågningar i appar oavsett skala. Du kan även skala upp eller skala ned dataflödet i din samling allteftersom appens behov utvecklas. Mer information om frågeenheter och hjälp att fastställa samlingsbehov finns i [Hantera prestanda och kapacitet](documentdb-manage.md).

### Är DocumentDB HIPAA-kompatibelt?
Ja, DocumentDB är HIPAA-kompatibelt. HIPAA fastställer kraven för användning, redovisning, och skydd av individuellt identifierbar hälsoinformation. Mer information finns i [Microsoft Trust Center](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### Vilka lagringsgränser har DocumentDB? 
Det finns ingen teoretisk gräns för den totala mängden data som en samling kan lagra i DocumentDB. Om du vill lagra över 250 GB data i en enda samling ska du [kontakta kundtjänst](documentdb-increase-limits.md) för att öka din kontokvot. 

### Vilka dataflödesgränser har DocumentDB? 
Det finns ingen teoretisk gräns för totala dataflödet som en samling kan stödja i DocumentDB om din arbetsbelastning kan fördelas någorlunda jämnt mellan ett tillräckligt stort antal partitionsnycklar. Om du vill överskrida 250 000 frågeenheter/sekund per samling eller kontakt ska du [kontakta kundtjänst](documentdb-increase-limits.md) för att öka din kontokvot. 

### Hur mycket kostar Microsoft Azure DocumentDB?
Mer information finns på sidan [Priser för DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/). Avgifterna för användning av DocumentDB bestäms av antalet samlingar som används, antalet timmar samlingarna varit online samt förbrukad lagring och etablerat dataflöde för varje samling. 

### Finns det ett kostnadsfritt konto?
Om du inte har använts Azure tidigare kan du registrera dig för ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/pricing/free-trial/) som ger dig 30 dagar och 200 USD i rabatt för att prova alla Azure-tjänster. Eller, om du har en prenumeration på Visual Studio, är du berättigad till [150 USD i kostnadsfria Azure-krediter per månad](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) att använda i valfria Azure-tjänster.  

### Hur kan jag få ytterligare hjälp med DocumentDB?
Om du behöver hjälp med något kan du nå oss via [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-documentdb) eller [Azure DocumentDB MSDN-utvecklingsforumen](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureDocumentDB), eller boka en [enskild chatt med teknikteamet för DocumentDB](http://www.askdocdb.com/). Om du vill veta det senaste om nyheter och funktioner i DocumentDB ska du följa oss på [Twitter](https://twitter.com/DocumentDB).

## Konfigurera Microsoft Azure DocumentDB

### Hur registrerar jag mig för Microsoft Azure DocumentDB?
Microsoft Azure DocumentDB är tillgängligt i [Azure Portal][azure-portal].  Först måste du registrera dig för en Microsoft Azure-prenumeration.  När du registrerar dig för en Microsoft Azure-prenumeration kan du lägga till DocumentDB-konto till din Azure-prenumeration. Anvisningar för hur du lägger till ett DocumentDB-konto finns i [Skapa ett databaskonto i DocumentDB](documentdb-create-account.md).   

### Vad är en huvudnyckel?
En huvudnyckel är en säkerhetstoken som ger åtkomst till alla resurser för ett konto. Personer med nyckeln har läs- och skrivbehörighet för alla resurser i databaskontot. Var försiktig när du distribuerar huvudnycklar. De primära och sekundära huvudnycklarna finns tillgängliga i bladet **Nycklar **i [Azure Portal][azure-portal]. Mer information om nycklar finns i [Visa, kopiera och generera åtkomstnycklar på nytt](documentdb-manage-account.md#keys).

### Hur skapar jag en databas?
Du kan skapa databaser i [Azure Portal]() enligt beskrivningen i [Skapa en DocumentDB-databas](documentdb-create-database.md), med en [DocumentDB-SDK](documentdb-sdk-dotnet.md) eller via [REST-API:er](https://msdn.microsoft.com/library/azure/dn781481.aspx).  

### Vad är en samling?
En samling är en behållare för JSON-dokument och associerad JavaScript-applogik. En samling är en fakturerbar enhet där [kostnaden](documentdb-performance-levels.md) bestäms av samlingens prestandanivå. Samlingar kan omfatta en eller flera partitioner/servrar och skalas för att hantera praktiskt taget obegränsade volymer av lagring eller dataflöde.

Samlingar är också faktureringsenheterna för DocumentDB. Varje samling faktureras timvis baserat på etablerat dataflöde och använt lagringsutrymme. Mer information finns i [priser för DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/).  

### Hur ställer jag in användare och behörigheter?
Du kan skapa användare och behörigheter via ett [DocumentDB-SDK](documentdb-sdk-dotnet.md) eller via [REST-API:er](https://msdn.microsoft.com/library/azure/dn781481.aspx).   

## Databasfrågor om utveckling mot Microsoft Azure DocumentDB

### Hur börjar jag utveckla mot DocumentDB?
[SDK:er](documentdb-sdk-dotnet.md) är tillgängliga för .NET, Python, Node.js, JavaScript och Java.  Utvecklare kan även utnyttja [RESTful HTTP-API:er](https://msdn.microsoft.com/library/azure/dn781481.aspx) för att samverka med DocumentDB-resurser från flera olika plattformar och språk. 

Exempel på [.NET](documentdb-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](documentdb-nodejs-samples.md), och [Python](documentdb-python-samples.md) SDK:er för DocumentDB finns på GitHub.

### Stöder DocumentDB SQL?
SQL-frågespråket i DocumentDB är en förbättrad underuppsättning av de frågefunktioner som stöds av SQL. SQL-frågespråket i DocumentDB ger omfattande hierarki- och relationsoperatorer och kan utvidgas via JavaScript-baserade användardefinierade funktioner (UDF:er). JSON-grammatik gör det möjligt att modellera JSON-dokument som träd med etiketter som trädnoder, vilket används av både DocumentDB:s automatiska indexeringstekniker och DocumentDB:s SQL-frågedialekt.  Mer information om hur du använder SQL-grammatik finns i artikeln [Förfrågningar][DocumentDB].

### Vilka datatyper stöds av DocumentDB?
De primitiva datatyper som stöds i DocumentDB är desamma som i JSON. JSON har ett enkelt typsystem som består av strängar, siffror (IEEE754 dubbel precision) och booleska värden – SANT, FALSKT och null-värden.  Mer komplexa datatyper som DateTime, Guid, Int64 och Geometry kan representeras både i JSON och DocumentDB genom att skapa kapslade objekt med operatorn { } och matriser med operatorn [ ]. 

### Hur tillhandahåller DocumentDB samtidighet?
DocumentDB stöder optimistisk samtidighetskontroll (OCC) via HTTP-entitetstaggar eller ETags. Varje DocumentDB-resurs har en ETag, och DocumentDB-klienter inkluderar sin senaste läsversion i skrivförfrågningar. Om en ETag är aktuell verkställs ändringen. Om värdet har ändrats externt avvisar servern skrivningen med en svarskod av typen ”HTTP 412 förutsättningsfel”. Klienter måste läsa den senaste versionen av resursen och skicka förfrågan på nytt. 

### Hur gör jag transaktioner i DocumentDB?
DocumentDB stöder språkintegrerade transaktioner via JavaScript-lagrade procedurer och utlösare. Alla databasåtgärder i skript körs under ögonblicksbildisolering begränsad till samlingen om det är en samling med en enskild partition, eller dokument med samma partitionsnyckelvärde inom en samling om samlingen är partitionerad. En ögonblicksbild av dokumentversionerna (ETags) tas i början av transaktionen och verkställs endast om skriptet lyckas. Om JavaScript genererar ett fel återställs transaktionen. Mer information finns i [Programmering av serversidan i DocumentDB](documentdb-programming.md).

### Hur massinfogar jag dokument i DocumentDB? 
Det finns tre sätt att massinfoga dokument i DocumentDB:

- Med datamigreringsverktyget som beskrivs i [Importera data till DocumentDB](documentdb-import-data.md).
- Med dokumentutforskaren i Azure Portal som beskrivs i [Massinfoga dokument med dokumentutforskaren](documentdb-view-json-document-explorer.md#BulkAdd).
- Med lagrade procedurer enligt beskrivningen i [Programmering av serversidan i DocumentDB](documentdb-programming.md).

### Stöder DocumentDB cachelagring av resurslänkar?
Ja, eftersom DocumentDB är en RESTful-tjänst ändras resurslänkar inte och kan cachelagras. DocumentDB-klienter kan ange en ”om-ingen-träff”-rubrik för läsning mot valfri resurs som dokument eller samlingar och uppdatera sina lokala kopior först när serverversionen har ändrats. 




[azure-portal]: https://portal.azure.com
[DocumentDB]: documentdb-sql-query.md
 


<!----HONumber=Jun16_HO2-->


