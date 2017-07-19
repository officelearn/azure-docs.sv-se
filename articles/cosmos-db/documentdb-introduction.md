---
title: "DocumentDB-API:et för Azure Cosmos DB | Microsoft Docs"
description: "Lär dig hur du kan använda Azure Cosmos DB för att lagra och skicka frågor mot enorma mängder JSON-dokument med korta svarstider med hjälp av SQL och JavaScript."
keywords: json-databas, dokumentdatabas
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 686cdd2b-704a-4488-921e-8eefb70d5c63
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/22/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 79156c0b511dafcb43ed91800f01338dbb7ee5f3
ms.contentlocale: sv-se
ms.lasthandoff: 06/20/2017


---
# <a name="introduction-to-azure-cosmos-db-documentdb-api"></a>Introduktion till DocumentDB-API:et för Azure Cosmos DB

[Azure Cosmos DB](introduction.md) är Microsofts globalt distribuerade databastjänst för flera datamodeller för verksamhetskritiska program. Azure Cosmos DB erbjuder [nyckelfärdig global distribution](distribute-data-globally.md), [elastisk skalning av dataflöde och lagring](partition-data.md) världen över, ensiffrig svarstid som den 99:e percentilen, [fem väldefinierade konsekvensnivåer](consistency-levels.md) och garanterat hög tillgänglighet, och allt understöds av [branschledande serviceavtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [indexerar alla data automatiskt](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) utan att du behöver bry dig om schema- eller indexhantering. Det stöder flera modeller och dokument, nyckelvärde graf och kolumndatamodeller. 

![Azure DocumentDB-API:et](./media/documentdb-introduction/cosmosdb-documentdb.png) 

Genom DocumentDB-API:et tillhandahåller Azure Cosmos DB omfattande och välbekanta [SQL-frågefunktioner](documentdb-sql-query.md) med genomgående korta svarstider för schemalösa JSON-data. Den här artikeln innehåller en översikt över DocumentDB-API:et för Azure Cosmos DB och förklarar hur du kan använda det för att lagra enorma mängder JSON-data och skicka frågor mot dem med svarstider på bara några millisekunder. Artikeln beskriver också hur du enkelt kan utveckla schemat. 

## <a name="what-capabilities-and-key-features-does-azure-cosmos-db-offer"></a>Vilka är de viktigaste funktionerna i Azure Cosmos DB?
Azure Cosmos DB erbjuder, via DocumentDB-API:et, följande funktioner och fördelar:

* **Elastiska och skalbara dataflöden och lagringsutrymmen:** Skala enkelt upp eller ned din JSON-databas så att den passar dina programbehov. Dina data lagras på SSD-diskar (Solid State Disk) för korta och förutsägbara svarstider. Azure Cosmos DB stöder behållare för lagring av JSON-data kallade samlingar som kan skalas till praktiskt taget obegränsade lagringsstorlekar och etablerade dataflöden. Du kan skala Azure Cosmos DB elastiskt och smidigt med förutsägbara prestanda allteftersom programmet växer. 


* **Replikering mellan flera regioner:** Azure Cosmos DB replikerar data transparent till alla regioner som du har associerat med ditt Azure Cosmos DB-konto, så att du kan utveckla program som kräver global åtkomst till data med rätt balans mellan konsekvens, tillgänglighet och prestanda – allt med motsvarande garantier. Azure Cosmos DB tillhandahåller transparent regional redundans med flera API:er, och möjligheten att elastiskt skala dataflöde och lagring i hela världen. Mer information finns i [Distribute data globally with Azure Cosmos DB](distribute-data-globally.md) (Distribuera data globalt med Azure Cosmos DB).

* **Ad hoc-frågor med välbekant SQL-syntax:** Lagra heterogena JSON-dokument och skicka frågor mot dokumenten med hjälp av en välbekant SQL-syntax. Azure Cosmos DB använder en samtidig, låsfri, loggstrukturerad indexeringsteknik som automatiskt indexerar allt dokumentinnehåll. På så vis kan du skicka omfattande förfrågningar i realtid utan att behöva ange schematips, sekundärindex eller vyer. Mer information finns i [Query Azure Cosmos DB](documentdb-sql-query.md) (Skicka frågor mot Azure Cosmos DB). 
* **JavaScript-körning i databasen:** Uttryck programlogik som lagrade procedurer, utlösare och användardefinierade funktioner (UFD:er) med standard JavaScript. Då kan applogiken bearbeta data utan bekymmer för matchningsfel mellan appen och databasschemat. DocumentDB-API:et erbjuder fullständig transaktionell körning av JavaScript-programlogik direkt i databasmotorn. Med den djupgående integrationen av JavaScript kan åtgärderna INFOGA, ERSÄTT, TA BORT och VÄLJ köras från ett JavaScript-program som en isolerad transaktion. Mer information finns i [Programmering av serversidan i DocumentDB](programming.md).

* **Justerbara konsekvensnivåer:** Välj mellan fem väldefinierade konsekvensnivåer för bästa möjliga balans mellan konsekvens och prestanda. Azure Cosmos DB erbjuder fem olika konsekvensnivåer för frågor och läsåtgärder: stark, bunden utgång, session, enhetligt prefix och slutlig. Med de här detaljerade, väldefinierade konsekvensnivåerna kan du själv avgöra balansen mellan konsekvens, tillgänglighet och svarstid. Läs mer om hur du [maximerar tillgänglighet och prestanda med hjälp av konsekvensnivåer](consistency-levels.md).

* **Fullständigt hanterad:** Eliminerar behovet av att hantera databasen och datorresurser. Eftersom det är en fullständigt hanterad Microsoft Azure-tjänst behöver du inte hantera virtuella datorer, distribuera och konfigurera programvara, hantera skalning eller hantera komplexa uppgraderingar av datanivåer. Alla databaser säkerhetskopieras och skyddas automatiskt mot regionala fel. Du kan enkelt lägga till ett Azure Cosmos DB-konto och etablera kapacitet när du behöver den, så att du kan fokusera på din app i stället för på driften och hanteringen av databasen. 

* **Öppen design:** Kom igång snabbt med hjälp av befintliga kunskaper och verktyg. Det är enkelt att programmera mot DocumentDB-API:et och du behöver inte skaffa nya verktyg eller använda de vanliga tilläggen för JSON eller JavaScript. Du kommer åt databasens alla funktioner, som CRUD-, fråge- och JavaScript-bearbetning, över ett enkelt RESTful HTTP-gränssnitt. DocumentDB-API:et omfattar befintliga format, språk och standarder och erbjuder dessutom värdefull databaskapacitet.

* **Automatisk indexering:** Som standard indexerar Azure Cosmos DB automatiskt alla dokument i databasen och varken förväntar sig eller kräver något schema eller att sekundära index skapas. Vill du inte indexera allt? Oroa dig inte, du kan även [välja bort sökvägar i JSON-filer](indexing-policies.md).

## <a name="data-management"></a>Hur hanteras data med DocumentDB-API?
Med DocumentDB-API:et hanteras JSON-data med hjälp av väldefinierade databasresurser. Dessa resurser replikeras för hög tillgänglighet och är unikt adresserbara genom sina logiska URI:er. DocumentDB erbjuder en enkel HTTP-baserad RESTful-programmeringsmiljö för alla resurser. 


Azure Cosmos DB-databaskontot är ett unikt namnområde som ger dig åtkomst till Azure Cosmos DB. Innan du kan skapa ett databaskonto måste du ha en Azure-prenumeration, som ger dig tillgång till en mängd olika Azure-tjänster. 

Alla resurser i Azure Cosmos DB modelleras och lagras som JSON-dokument. Resurser hanteras som objekt, som är JSON-dokument med metadata, och som flöden, som är samlingar av objekt. Objektuppsättningar ingår i respektive flöde.

Bilden nedan visar relationerna mellan Azure DB Cosmos-resurserna:

![Den hierarkiska relationen mellan resurser i Azure Cosmos DB][1] 

Ett databaskonto består av en uppsättning databaser som alla innehåller flera samlingar, som i sin tur kan innehålla lagrade procedurer, utlösare, UDF:er, dokument och relaterade bilagor. En databas har också associerade användare med en uppsättning behörigheter att komma åt andra samlingar, lagrade procedurer, utlösare, UDF:er, dokument eller bilagor. Databaser, användare, behörigheter och samlingar är systemdefinierade resurser med välkända scheman, men dokument, lagrade procedurer, utlösare, UDF:er och bilagor innehåller godtyckligt användardefinierat JSON-innehåll.  

> [!NOTE]
> Eftersom DocumentDB-API:et tidigare var tillgängligt som Azure DocumentDB-tjänsten kan du fortsätta att etablera, övervaka och hantera konton som skapats med REST-API:et eller verktygen för Azure Resource Management genom att använda antingen Azure DocumentDB- eller Azure Cosmos DB-resursnamn. Vi använder namnen utan åtskillnad när vi refererar till Azure DocumentDB-API:erna. 

## <a name="develop"></a>Hur kan jag utveckla appar med DocumentDB-API:et?

Azure Cosmos DB exponerar resurser via ett REST-API som kan anropas av alla språk som stöder HTTP/HTTPS-förfrågningar. Vi erbjuder också programmeringsbibliotek för flera populära språk för DocumentDB-API:et. Klientbiblioteken förenklar många aspekter av arbetet med API:et eftersom de hanterar information om till exempel cachelagring av adresser, hantering av undantag och automatiska nya försök. Bibliotek finns just nu tillgängliga för följande språk och plattformar:  

| Ladda ned | Dokumentation |
| --- | --- |
| [.NET SDK](http://go.microsoft.com/fwlink/?LinkID=402989) |[.NET-bibliotek](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet) |
| [Node.js SDK](http://go.microsoft.com/fwlink/?LinkID=402990) |[Node.js-bibliotek](http://azure.github.io/azure-documentdb-node/) |
| [Java SDK](http://go.microsoft.com/fwlink/?LinkID=402380) |[Java-bibliotek](/java/api/com.microsoft.azure.documentdb) |
| [JavaScript SDK](http://go.microsoft.com/fwlink/?LinkID=402991) |[JavaScript-bibliotek](http://azure.github.io/azure-documentdb-js/) |
| Saknas |[JavaScript SDK för serversidan](http://azure.github.io/azure-documentdb-js-server/) |
| [Python SDK](https://pypi.python.org/pypi/pydocumentdb) |[Python-bibliotek](http://azure.github.io/azure-documentdb-python/) |
| Saknas | [API för MongoDB](mongodb-introduction.md)


Med [Azure Cosmos DB-emulatorn](local-emulator.md) kan du utveckla och testa ditt program lokalt med DocumentDB-API:et, utan att skapa en Azure-prenumeration och utan kostnad. När du är nöjd med hur programmet fungerar i emulatorn kan du växla till ett Azure Cosmos DB-konto i molnet.

DocumentDB-API:et tillhandahåller inte bara grundläggande åtgärder för generering, läsning, uppdatering och borttagning, utan även ett omfattande SQL-gränssnitt för hämtning av JSON-dokument och stöd på serversidan för transaktionell körning av JavaScript-programlogik. Gränssnitten för fråge- och skriptkörning finns tillgängliga via alla plattformsbibliotek samt REST-API:erna. 

### <a name="sql-query"></a>SQL-fråga
DocumentDB-API:et stöder frågekörning mot dokument med ett SQL-språk, som bygger på JavaScript-typsystemet, och uttryck med stöd för relations-, hierarki- och spatialfrågor. Frågespråket i DocumentDB är ett enkelt men kraftfullt gränssnitt för förfrågningar till JSON-dokument. Språket har stöd för en delmängd av ANSI SQL-grammatiken och dessutom djupgående integration av objekt, matriser, objektkonstruktion och funktionsanrop i JavaScript. Frågemodellen i DocumentDB erbjuds utan uttryckliga scheman eller indexeringstips från utvecklaren.

Användardefinierade funktioner (UDF:er) kan registreras med DocumentDB-API:et och refereras till som en del av en SQL-fråga, vilket innebär att grammatiken även stöder anpassad programlogik. Dessa UDF:er skrivs som JavaScript-program och körs i databasen. 

För .NET-utvecklare erbjuder DocumentDB [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx) även en LINQ-frågeprovider. 

### <a name="transactions-and-javascript-execution"></a>Transaktioner och JavaScript-körning
Med DocumentDB-API:et kan du skriva programlogiken som namngivna program helt skrivna i JavaScript. Programmen registreras i en samling och kan utfärda databasåtgärder i dokumenten inom en angiven samling. JavaScript kan registreras för körning som en utlösare, lagrad procedur eller användardefinierad funktion. Utlösare och lagrade procedurer kan skapa, läsa, uppdatera och ta bort dokument medan användardefinierade funktioner körs som en del av frågans körningslogik utan skrivåtkomst till samlingen.

JavaScript-körning inom DocumentDB-API:et modelleras efter begrepp som stöds av relationsdatabassystem, med JavaScript som en modern ersättning för Transact-SQL. All JavaScript-logik körs inom en omgivande ACID-transaktion med ögonblicksbildisolering. Om JavaScript genererar ett undantag under körningen avbryts hela transaktionen.

## <a name="are-there-any-online-courses-on-azure-cosmos-db"></a>Finns det några onlinekurser om Azure Cosmos DB?

Ja, det finns en kurs om [Microsoft Virtual Academy](https://mva.microsoft.com/en-US/training-courses/azure-documentdb-planetscale-nosql-16847) på Azure DocumentDB. 

>[!VIDEO https://mva.microsoft.com/en-US/training-courses-embed/azure-documentdb-planetscale-nosql-16847]
>
>

## <a name="next-steps"></a>Nästa steg
Har du redan ett Azure-konto? Sedan kan du sätta igång med Azure Cosmos DB genom att följa våra [snabbstarter](../cosmos-db/create-documentdb-dotnet.md), som hjälper dig att skapa ett konto och att komma igång med Cosmos DB.

[1]: ./media/documentdb-introduction/json-database-resources1.png


