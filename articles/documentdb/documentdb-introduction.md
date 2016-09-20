<properties 
    pageTitle="Introduktion till DocumentDB, en JSON-databas | Microsoft Azure" 
    description="Läs mer om Azure DocumentDB, en NoSQL JSON-databas. Den här dokumentdatabasen har skapats för stordata, elastisk skalbarhet och hög tillgänglighet." 
    keywords="json-databas, dokumentdatabas"
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
    ms.date="07/01/2016" 
    ms.author="mimig"/>

# Introduktion till DocumentDB, en NoSQL JSON-databas

Azure DocumentDB är en helt hanterad NoSQL-databas för snabb och förutsägbar prestanda, hög tillgänglighet, automatisk skalning, global distribuering och enkel utveckling. Tack vare den flexibla datamodellen, de genomgående korta svarstiderna och de omfattande frågefunktionerna är den utmärkt för webb-, mobil-, spel- och IoT-appar, liksom många andra appar som kräver smidig skalning.

Du kan snabbt lära dig mer om JSON-databasen och se hur den fungerar med följande tre steg: 

1. Titta på tvåminutersvideon [Vad är DocumentDB?](https://azure.microsoft.com/documentation/videos/what-is-azure-documentdb/), där fördelarna med att använda DocumentDB beskrivs.
2. Titta på treminutersvideon [Skapa DocumentDB på Azure](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/), som visar hur du kommer igång med DocumentDB med hjälp av Azure Portal.
3. Besök [Query Playground](http://www.documentdb.com/sql/demo), där du kan prova olika aktiviteter där du lär dig mer om de omfattande frågefunktionerna i DocumentDB. Öppna sedan fliken Begränsat läge och kör egna anpassade SQL-frågor och experimentera med DocumentDB.

Återvänd sedan till den här artikeln, där vi går djupare och du får svar på följande frågor:  

-   [Vad är DocumentDB och vilket mervärde kan det ge i samband med moderna appar?](#what-is-azure-documentdb)
-   [Hur hanteras mina data i DocumentDB och hur kommer jag åt dem?](#data-management)
-   [Hur utvecklar jag appar med DocumentDB?](#develop)
-   [Vad är nästa steg om jag vill skapa en DocumentDB-app?](#next-steps)  

## Vad är Azure DocumentDB?  

Moderna appar skapar, använder och reagerar snabbt på mycket stora mängder data. Apparna utvecklas snabbt och det gör även det underliggande dataschemat. Därför väljer utvecklare allt oftare schemafria NoSQL-dokumentdatabaser som enkla, snabba och skalbara lösningar för lagring och bearbetning av data medan man behåller möjligheten att snabbt iterera över appdatamodeller och ostrukturerade dataflöden. Många schemafria databaser tillåter däremot inte komplexa frågor och transaktionell bearbetning, vilket gör avancerad datahantering svårt. Det här är här DocumentDB kommer in. Microsoft har utvecklat DocumentDB för att uppfylla dessa datahanteringskrav för dagens appar.

DocumentDB är en verkligt schemafri NoSQL-databastjänst för moderna mobil-, webb-, spel- och IoT-appar. DocumentDB säkerställer att 99 % av dina läsåtgärder behandlas på under 10 millisekunder och 99 % av dina skrivåtgärder behandlas på under 15 millisekunder. Det ger också ett flexibelt schema och möjlighet att enkelt skala upp eller ned en databas på begäran. Den varken antar eller kräver ett schema för de JSON-dokument som indexeras. Som standard indexerar den automatiskt alla dokument i databasen och varken förväntar sig eller kräver något schema eller att sekundärindex skapas. DocumentDB möjliggör avancerade ad hoc-frågor med SQL-språk, stöder väldefinierade konsekvensnivåer och erbjuder språkintegrerad transaktionsbearbetning av flera dokument i JavaScript med den välbekanta programmeringsmiljön med lagrade procedurer, utlösare och UDF:er. 

Som JSON-databas har DocumentDB internt stöd för JSON-dokument vilket möjliggör enkel iterering av appscheman och stöder appar som behöver nyckelvärde, dokument eller tabelldatamodeller. DocumentDB stöder populära JSON och JavaScript, vilket eliminerar matchningsfel mellan programdefinierade objekt och databasscheman. Tack vare djupgående integration av JavaScript kan utvecklarna dessutom köra programlogiken effektivt och direkt, inom databasmotorn i en databastransaktion. 

Azure DocumentDB erbjuder följande viktiga funktioner och fördelar:

-   **Elastiska och skalbara dataflöden och lagringsutrymmen:** Det är enkelt att skala upp eller ned en DocumentDB-JSON-databas så att den passar behoven för ditt program. Dina data lagras på SSD-diskar (Solid State Disk) för korta och förutsägbara svarstider. DocumentDB stöder behållare för lagring av anropade samlingar JSON-data som kan skalas till praktiskt taget obegränsade lagringsstorlekar och etablerade dataflöden. Du kan skala DocumentDB elastiskt och smidigt med förutsägbar prestanda när programmet växer. 

-   **Replikering av flera regioner:** DocumentDB replikerar på ett transparent sätt data till alla regioner som du har kopplat till DocumentDB-kontot, så att du kan utveckla program som kräver global åtkomst till data samtidigt som man kompromissar mellan konsekvens, tillgänglighet och prestanda med motsvarande garantier. DocumentDB ger transparent regional redundans med flera API: er och möjlighet att på ett elastiskt sätt skala dataflöde och lagring i hela världen. Läs mer i [Distribuera data globalt med DocumentDB](documentdb-distribute-data-globally.md).

-   **Ad hoc-frågor med välbekant SQL-syntax:** Lagra heterogena JSON-dokument inom DocumentDB och skicka förfrågningar till dokumenten via en välbekant SQL-syntax. DocumentDB använder en samtidig, låsfri, loggstrukturerad indexeringsteknik som automatiskt indexerar allt dokumentinnehåll. På så vis kan du skicka omfattande förfrågningar i realtid utan att behöva ange schematips, sekundärindex eller vyer. Läs mer i [Förfrågningar i DocumentDB](documentdb-sql-query.md). 

-   **JavaScript-körning i databasen:** Uttryck programlogik som lagrade procedurer, utlösare och användardefinierade funktioner (UFD:er) med standard JavaScript. Då kan applogiken bearbeta data utan bekymmer för matchningsfel mellan appen och databasschemat. DocumentDB ger fullständig transaktionell körning av JavaScript-programlogik direkt i databasmotorn. Med den djupgående integrationen av JavaScript kan åtgärderna INFOGA, ERSÄTT, TA BORT och VÄLJ köras från ett JavaScript-program som en isolerad transaktion. Mer information finns i [Programmering av serversidan i DocumentDB](documentdb-programming.md).

-   **Justerbara konsekvensnivåer:** Välj bland fyra väldefinierade konsekvensnivåer för bästa möjliga balans mellan konsekvens och prestanda. DocumentDB erbjuder fyra olika konsekvensnivåer för frågor och läsåtgärder: stark, begränsat föråldrad, session och slutlig. Med de här detaljerade, väldefinierade konsekvensnivåerna kan du själv avgöra balansen mellan konsekvens, tillgänglighet och svarstid. Läs mer om hur du [maximerar tillgänglighet och prestanda i DocumentDB med hjälp av konsekvensnivåer](documentdb-consistency-levels.md).

-   **Fullständigt hanterad:** Eliminerar behovet av att hantera databasen och datorresurser. Eftersom det är en fullständigt hanterad Microsoft Azure-tjänst behöver du inte hantera virtuella datorer, distribuera och konfigurera programvara, hantera skalning eller hantera komplexa uppgraderingar av datanivåer. Alla databaser säkerhetskopieras och skyddas automatiskt mot regionala fel. Du kan enkelt lägga till ett DocumentDB-konto och etablera kapacitet när du behöver den, så att du kan fokusera på din app i stället för på att använda och hantera din databas. 

-   **Öppen design:** Kom igång snabbt med hjälp av befintliga kunskaper och verktyg. Det är enkelt och användarvänligt att programmera mot DocumentDB och det kräver inte att du skaffar nya verktyg eller följer anpassade tillägg till JSON eller JavaScript. Du kommer åt databasens alla funktioner, som CRUD-, fråge- och JavaScript-bearbetning, över ett enkelt RESTful HTTP-gränssnitt. DocumentDB omfattar befintliga format, språk och standarder och erbjuder dessutom värdefull databaskapacitet.

Du kan använda DocumentDB för att lagra flexibla datauppsättningar som kräver hämtning av förfrågningar och transaktionell bearbetning. Programscenarier kan omfatta användardata för interaktiva webb-, mobil- och spelappar samt lagring, hämtning och bearbetning av JSON-data som genererats för IoT-enheter. En databas kan lagra valfri mängd JSON-dokument eftersom DocumentDB passar bra för program som körs i stor skala på internet.

##<a name="data-management"></a>Resurser i Azure DocumentDB
Azure DocumentDB hanterar data via väldefinierade databasresurser. Dessa resurser replikeras för hög tillgänglighet och är unikt adresserbara genom sina logiska URI:er. DocumentDB erbjuder en enkel HTTP-baserad RESTful-programmeringsmiljö för alla resurser. 

DocumentDB-databaskontot är ett unikt namnområde som ger dig tillgång till Azure DocumentDB. Innan du kan skapa ett databaskonto måste du ha en Azure-prenumeration, som ger dig tillgång till en mängd olika Azure-tjänster. 

Alla resurser inom DocumentDB modelleras och lagras som JSON-dokument. Resurser hanteras som objekt, som är JSON-dokument med metadata, och som flöden, som är samlingar av objekt. Objektuppsättningar ingår i respektive flöde.

Bilden nedan visar relationerna mellan DocumentDB-resurser:

![Hierarkisk relation mellan resurser i DocumentDB, en NoSQL JSON-databas][1] 

Ett databaskonto består av en uppsättning databaser som alla innehåller flera samlingar, som i sin tur kan innehålla lagrade procedurer, utlösare, UDF:er, dokument och relaterade bilagor. En databas har också associerade användare med en uppsättning behörigheter att komma åt andra samlingar, lagrade procedurer, utlösare, UDF:er, dokument eller bilagor. Databaser, användare, behörigheter och samlingar är systemdefinierade resurser med välkända scheman, men dokument, lagrade procedurer, utlösare, UDF:er och bilagor innehåller godtyckligt användardefinierat JSON-innehåll.  

##<a name="develop"></a> Utveckla med Azure DocumentDB
Azure DocumentDB visar resurser via ett REST-API som kan anropas av alla språk som kan göra HTTP/HTTPS-förfrågningar. Dessutom erbjuder DocumentDB programmeringsbibliotek för flera populära språk. Biblioteken förenklar många aspekter av arbetet med Azure DocumentDB eftersom de hanterar information om till exempel cachelagring av adresser, hantering av undantag och automatiska nya försök. Bibliotek finns just nu tillgängliga för följande språk och plattformar:  

Ladda ned | Dokumentation
--- | ---
[.NET SDK](http://go.microsoft.com/fwlink/?LinkID=402989) | [.NET-bibliotek](https://msdn.microsoft.com/library/azure/dn948556.aspx)
[Node.js SDK](http://go.microsoft.com/fwlink/?LinkID=402990) | [Node.js-bibliotek](http://azure.github.io/azure-documentdb-node/)
[Java SDK](http://go.microsoft.com/fwlink/?LinkID=402380) | [Java-bibliotek](http://azure.github.io/azure-documentdb-java/)
[JavaScript SDK](http://go.microsoft.com/fwlink/?LinkID=402991) | [JavaScript-bibliotek](http://azure.github.io/azure-documentdb-js/)
Saknas | [JavaScript SDK för serversidan](http://azure.github.io/azure-documentdb-js-server/)
[Python SDK](https://pypi.python.org/pypi/pydocumentdb) | [Python-bibliotek](http://azure.github.io/azure-documentdb-python/)

DocumentDB erbjuder inte bara grundläggande åtgärder för skapande, läsning, uppdateringar och borttagning, utan också ett omfattande SQL-gränssnitt för hämtning av JSON-dokument och support på serversidan för transaktionell körning av JavaScript-programlogik. Gränssnitten för fråge- och skriptkörning finns tillgängliga via alla plattformsbibliotek samt REST-API:erna. 

### SQL-fråga
Azure DocumentDB stöder förfrågningar till dokument med ett SQL-språk, som grundas på typsystemet i JavaScript, och uttryck med stöd för relations-, hierarki- och rumsfrågor. Frågespråket i DocumentDB är ett enkelt men kraftfullt gränssnitt för förfrågningar till JSON-dokument. Språket har stöd för en delmängd av ANSI SQL-grammatiken och dessutom djupgående integration av objekt, matriser, objektkonstruktion och funktionsanrop i JavaScript. Frågemodellen i DocumentDB erbjuds utan uttryckliga scheman eller indexeringstips från utvecklaren.

Användardefinierade funktioner (UDF:er) kan registreras med DocumentDB och refereras till som en del av en SQL-fråga, vilket innebär att grammatiken även stöder anpassad programlogik. Dessa UDF:er skrivs som JavaScript-program och körs i databasen. 

För .NET-utvecklare erbjuder DocumentDB även en LINQ-frågeleverantör som en del i [.NET SDK](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx). 

### Transaktioner och JavaScript-körning
Med DocumentDB kan du skriva programlogiken som namngivna program helt skrivna i JavaScript. Programmen registreras i en samling och kan utfärda databasåtgärder i dokumenten inom en angiven samling. JavaScript kan registreras för körning som en utlösare, lagrad procedur eller användardefinierad funktion. Utlösare och lagrade procedurer kan skapa, läsa, uppdatera och ta bort dokument medan användardefinierade funktioner körs som en del av frågans körningslogik utan skrivåtkomst till samlingen.

JavaScript-körning inom DocumentDB modelleras efter begrepp som stöds av relationsdatabassystem, med JavaScript som en modern ersättning för Transact-SQL. All JavaScript-logik körs inom en omgivande ACID-transaktion med ögonblicksbildisolering. Om JavaScript genererar ett undantag under körningen avbryts hela transaktionen.

## Nästa steg
Om du redan har ett Azure-konto kan du komma igång med DocumentDB i [Azure Portal](https://portal.azure.com/#gallery/Microsoft.DocumentDB) genom att [skapa ett databaskonto i DocumentDB](documentdb-create-account.md).

Om du inte har något Azure-konto kan du

- Registrera dig för ett [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/free/) som ger dig 30 dagar och 200 USD i rabatt för att prova alla Azure-tjänster. 
- Om du har en prenumeration på MSDN är du berättigad till [150 USD i kostnadsfria Azure-krediter per månad](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) att använda i valfria Azure-tjänster. 

Sedan, när du vill veta mer, finns våra [utbildningsvägar](https://azure.microsoft.com/documentation/learning-paths/documentdb/) där du hittar alla tillgängliga utbildningsresurser. 


[1]: ./media/documentdb-introduction/json-database-resources1.png
 



<!--HONumber=sep16_HO1-->


