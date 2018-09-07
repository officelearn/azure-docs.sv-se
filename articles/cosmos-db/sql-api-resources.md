---
title: Azure Cosmos DB-resursmodell och begrepp | Microsoft Docs
description: Läs mer om Azure Cosmos DB hierarkisk modell för databaser, samlingar, användardefinierad funktion (UDF), dokument, behörigheter för att hantera resurser och mycket mer.
keywords: Hierarkisk modell, cosmosdb, azure, Microsoft azure
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: rafats
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0869881ace689d12272affb38d3689965e107e8f
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44050940"
---
# <a name="azure-cosmos-db-hierarchical-resource-model-and-core-concepts"></a>Hierarkisk Azure Cosmos DB-resursmodell och huvudkoncept

Databasentiteter som hanterar Azure Cosmos DB kallas **resurser**. Varje resurs identifieras unikt genom en logiska URI: er. Du kan interagera med resurser med hjälp av HTTP-verb som standard och begäran/svar-huvuden statuskoder. 

Den här artikeln får du svar på följande frågor:

* Vad är Azure Cosmos DB-resursmodell?
* Vad är definierat resurser till skillnad från en användardefinierad resurser?
* Hur jag för att lösa en resurs?
* Hur jag för att arbeta med samlingar?
* Hur jag för att arbeta med lagrade procedurer, utlösare och användardefinierade funktioner (UDF)?

## <a name="hierarchical-resource-model"></a>Hierarkisk resursmodell
Enligt följande diagram illustrerar hierarkisk Azure Cosmos DB **resursmodell** består av uppsättningar av resurser under ett databaskonto varje adresserbara via en logisk och stabil URI. En uppsättning resurser kallas en **feed** i den här artikeln. 

> [!NOTE]
> Azure Cosmos DB erbjuder ett högeffektiv TCP-protokoll, som också är RESTful i sin modell, tillgängligt via den [SQL .NET API-klient](sql-api-sdk-dotnet.md).
> 
> 

![Azure Cosmos DB hierarkisk resursmodell][1]  
**Hierarkisk resursmodell**   

Om du vill börja arbeta med resurser, måste du [skapa ett databaskonto](create-sql-api-dotnet.md) med din Azure-prenumeration. Ett databaskonto kan bestå av en uppsättning **databaser**, som innehåller flera **samlingar**, och var av vilket i sin tur innehåller **lagrade procedurer, utlösare, UDF: er, dokument och relaterade bifogade filer**. En databas har också associerade **användare**, var och en med en uppsättning **behörigheter** till samlingar, lagrade procedurer, utlösare, UDF: er, dokument eller bilagor. Databaser, användare, behörigheter och samlingar är systemdefinierade resurser med välkända scheman, innehåller dokument och bilagor godtyckligt, användardefinierade JSON-innehåll.  

| Resurs | Beskrivning |
| --- | --- |
| Databaskonto |Ett databaskonto är associerad med en uppsättning databaser och en fast mängd blob storage för bifogade filer. Du kan skapa en eller flera databaskonton med din Azure-prenumeration. Mer information finns i [prissättningssidan](https://azure.microsoft.com/pricing/details/cosmos-db/). |
| Databas |En databas är en logisk behållare för dokumentlagring, partitionerad över samlingarna. Det är också en användarbehållaren. |
| Användare |Logiska namnområdet för att samla behörigheter. |
| Behörighet |En autentiseringstoken som är associerade med en användare med åtkomst till en viss resurs. |
| Samling |En samling är en container för JSON-dokument och associerad JavaScript-applogik. Samlingar kan omfatta en eller flera partitioner/servrar och skalas för att hantera praktiskt taget obegränsade volymer av lagring eller dataflöde. |
| Lagrad procedur |Programlogiken skriven i JavaScript, som är registrerade med en samling och Verkställ inom databasmotorn. |
| Utlösare |Programlogiken skriven i JavaScript som körs före eller efter antingen en insert-, ersätta eller ta bort åtgärden. |
| UDF |Programlogiken skriven i JavaScript. UDF: er kan du modellera en anpassad fråga operator och därmed Utöka grundläggande SQL API-frågespråket. |
| Dokument |Användardefinierat (godtyckligt) JSON-innehåll. Inget schema måste definieras som standard eller sekundära index måste anges för alla dokument som läggs till en samling. |
| Bifogad fil |Bifogad fil är ett särskilt dokument som innehåller referenser och associerade metadata för extern blob/media. Utvecklaren kan välja att ha den blob som hanteras av Cosmos DB eller lagra den med en extern blob-leverantör, till exempel OneDrive, Dropbox, osv. |

## <a name="system-vs-user-defined-resources"></a>System jämfört med användardefinierade resurser
Resurser, till exempel databaskonton, databaser, samlingar, användare, behörigheter, lagrade procedurer, utlösare och UDF: er – alla har en fast schema och kallas systemresurser. Däremot resurser, till exempel dokument och bifogade filer har inga begränsningar på schemat och är exempel på användardefinierade resurser. I Cosmos DB kan visas och hanteras som standard-kompatibla JSON både system och anpassade resurser. Alla resurser, system eller användardefinierade, har följande gemensamma egenskaper:

> [!NOTE]
> Alla systemgenererade egenskaper i en resurs är föregås av ett understreck (_) i sina JSON-representation.
> 
> 

<table>
    <tbody>
        <tr>
            <td valign="top"><p><strong>Egenskap</strong></p></td>
            <td valign="top"><p><strong>Inställbar användar- eller systemgenererade?</strong></p></td>
            <td valign="top"><p><strong>Syfte</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>_rid</p></td>
            <td valign="top"><p>Systemgenererad</p></td>
            <td valign="top"><p>Systemgenererad, unikt och hierarkiska ID för resursen</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_etag</p></td>
            <td valign="top"><p>Systemgenererad</p></td>
            <td valign="top"><p>ETag för resursen som krävs för optimistisk samtidighetskontroll</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_ts</p></td>
            <td valign="top"><p>Systemgenererad</p></td>
            <td valign="top"><p>Senast uppdaterade tidsstämpeln för resursen</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_self</p></td>
            <td valign="top"><p>Systemgenererad</p></td>
            <td valign="top"><p>Unikt adresserbara URI för resursen</p></td>
        </tr>
        <tr>
            <td valign="top"><p>id</p></td>
            <td valign="top"><p>Antingen</p></td>
            <td valign="top"><p>Användardefinierade unika namnet på resursen (med samma partitionsnyckelvärdet). Om användaren inte anger ett id, är ett id systemgenererad</p></td>
        </tr>
    </tbody>
</table>

### <a name="wire-representation-of-resources"></a>Wire-representation av resurser
Cosmos DB tvingade inte några egna tillägg till JSON standard eller särskilda kodningar; Det fungerar med standard kompatibla JSON-dokument.  

### <a name="addressing-a-resource"></a>En resurs-adressering
Alla resurser är URI: N adresserbara. Värdet för den **_self** egenskap för en resurs representerar den relativa URI för resursen. Formatet på URI: N består av den /\<feed\>/ {_rid} segment för resurssökväg:  

| Värdet för _self | Beskrivning |
| --- | --- |
| /DBS |Feed med databaser under ett databaskonto |
| /DBS/ {%{dbname/} |Databasen med id matchar värdet {%{dbname/} |
| /DBS/ {%{dbname/} /colls/ |Feed med samlingar under en databas |
| /DBS/ {%{dbname/} /colls/ {collName} |Samling med id matchar värdet {collName} |
| /DBS/ {%{dbname/} /colls/ {collName} / docs |Feed med dokument under en samling |
| /DBS/ {%{dbname/} /colls/ {collName} /docs/ {docId} |Dokument med ett id som matchar värdet {doc} |
| /users/ /DBS/ {%{dbname/} |Feed med användare under en databas |
| /users/ /DBS/ {%{dbname/} {userId} |Användaren med id matchar värdet {user} |
| /users/ /DBS/ {%{dbname/} {userId} / behörigheter |Flödet av behörigheter under en användare |
| /dbs/{dbName}/users/{userId}/permissions/{permissionId} |Behörigheten med id matchar värdet {behörighet} |

Varje resurs har ett unikt egna namn som exponeras via id-egenskapen. Obs: dokument om användaren inte anger ett id, SDK: er automatiskt generera ett unikt id för dokumentet. ID: t är en användardefinierad sträng med upp till 256 tecken som är unik inom ramen för en specifik överordnad resurs. 

Varje resurs har också en systemgenererad hierarkisk resursidentifierare (kallas även en RID), som är tillgängligt via egenskapen _rid. RID kodar hela hierarkin för en viss resurs och det är en praktisk intern representation som används för att framtvinga referensintegritet i ett distribuerat sätt. RID är unika inom ett databaskonto och används internt av Cosmos DB för effektiv routning utan mellan partition sökningar. Värdena för _self och egenskaper för _rid är både alternativa och canonical representationer av en resurs. 

REST-API: er stöd för adressering av resurser och routning av begäranden av både ID: t och _rid egenskaper.

## <a name="database-accounts"></a>Databaskonton
Du kan etablera en eller flera Cosmos DB-databaskonton med hjälp av din Azure-prenumeration.

Du kan skapa och hantera konton för Cosmos DB-databas via Azure-portalen på [ http://portal.azure.com/ ](https://portal.azure.com/). Skapa och hantera ett databaskonto kräver administratörsbehörighet och kan bara genomföras under din Azure-prenumeration. 

### <a name="database-account-properties"></a>Egenskaper för databas-konto
Som en del av etablera och hantera ett databaskonto kan du konfigurera och Läs följande egenskaper:  

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Egenskapsnamn</strong></p></td>
            <td valign="top"><p><strong>Beskrivning</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Princip för konsekvens</p></td>
            <td valign="top"><p>Ange den här egenskapen för att konfigurera standardkonsekvensnivå för alla samlingar under ditt databaskonto. Du kan åsidosätta konsekvensnivå på basis av per begäran med hjälp av rubriken [x-ms--konsekvensnivå]. <p><p>Den här egenskapen gäller bara den <i>användardefinierade resurser</i>. Alla system som är definierade resurser är konfigurerad för att stödja läsningar/frågor med stark konsekvens.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Auktoriseringsregel för nycklar</p></td>
            <td valign="top"><p>De primära och sekundära master och readonly nycklarna som ger administrativ åtkomst till alla resurser under databaskontot.</p></td>
        </tr>
    </tbody>
</table>

Förutom att etablera, konfigurera och hantera ditt databaskonto med Azure portal kan du kan också programmässigt skapa och hantera Cosmos DB-databaskonton med hjälp av den [Azure Cosmos DB REST API: er](/rest/api/cosmos-db/) samt [klient-SDK: er](sql-api-sdk-dotnet.md).  

## <a name="databases"></a>Databaser
En Cosmos DB-databas är en logisk behållare för en eller flera samlingar och användare, enligt följande diagram. Du kan skapa valfritt antal databaser under ett Cosmos DB-databaskonto omfattas av erbjudandet gränser.  

![Konto och samlingar hierarkisk databasmodell][2]  
**En databas är en logisk behållare för användare och samlingar**

En databas kan innehålla obegränsad dokumentlagring, partitionerad i samlingar.

### <a name="elastic-scale-of-an-azure-cosmos-db-database"></a>Elastisk skalning av en Azure Cosmos DB-databas
En Cosmos DB-databas är elastisk standard – allt från några få GB till petabyte med SSD-uppbackad dokumentlagring och dataflöde. 

Till skillnad från en databas i traditionella RDBMS är en databas i Cosmos DB inte begränsad till en enda dator. Med Cosmos DB som ditt program skalas behov växer och kan du skapa flera samlingar, databaser eller båda. Faktiskt har olika första partsprogram inom Microsoft använt Azure Cosmos DB i konsument skala genom att skapa mycket stora Azure Cosmos DB-databaser varje som innehåller tusentals samlingar med terabyte dokumentlagring. Du kan öka eller minska en databas genom att lägga till eller ta bort samlingar för att uppfylla ditt programs skalningskrav. 

Du kan skapa valfritt antal samlingar i databasen omfattas av erbjudandet. Varje samling, eller en uppsättning samlingar (inom en databas), har SSD uppbackad lagring och dataflöde etablerat för dig beroende på det valda erbjudandet.

En Azure Cosmos DB-databas är också en behållare för användare. En användare i sin tur, är ett logiskt namnområde för en uppsättning behörigheter som ger detaljerad auktorisering och åtkomst till samlingar, dokument och bifogade filer.  

Som med andra resurser i Azure Cosmos DB-resursmodell databaser kan skapas och byts ut kommer tas bort, läsa eller räkna upp enkelt med antingen den [REST API: er](/rest/api/cosmos-db/) eller någon av de [klient-SDK: er](sql-api-sdk-dotnet.md). Azure Cosmos DB garanterar stark konsekvens för läsning eller skicka en fråga metadata för en databasresurs. Tar bort en databas automatiskt säkerställer att du inte åtkomst till någon av de samlingar eller användare som ingår i detta.   

## <a name="collections"></a>Samlingar
En Cosmos DB-samling är en behållare för JSON-dokument. 

### <a name="elastic-ssd-backed-document-storage"></a>Elastisk SSD-uppbackad dokumentlagring
En samling är tack elastiska – det automatiskt ökar och minskar du lägger till eller ta bort dokument. Samlingar är logiska resurser och kan sträcka sig över en eller flera fysiska partitioner eller servrar. Antalet partitioner som är tilldelade till en samling bestäms av Cosmos DB baserat på lagringsutrymmet och dataflödet som tillhandahållits för samlingen eller en uppsättning samlingar. Varje partition i Cosmos DB har en fast mängd SSD-uppbackad lagring som är associerade med det och replikeras för hög tillgänglighet. Hantering av partitionen är fullständigt hanterad av Azure Cosmos DB och du behöver inte skriva komplex kod eller hantera din partitioner. Cosmos DB-samlingar är **obegränsad** vad gäller lagring och dataflöde. 

### <a name="automatic-indexing-of-collections"></a>Automatisk indexering av samlingar
Azure Cosmos DB är ett sant schemafria databassystem. Den varken antar eller kräver något schema för JSON-dokument. När du lägger till dokument till en samling Azure Cosmos DB indexerar automatiskt dem och de är tillgängliga för dig att fråga. Automatisk indexering av dokument utan schema eller sekundära index är en viktig funktion i Azure Cosmos DB och aktiveras av skrivoptimerad Lås är kostnadsfria och logg-strukturerad index Underhåll tekniker. Azure Cosmos DB stöder fast volym av mycket snabba skrivningar samtidigt som man betjänar konsekventa frågor fortfarande. Både dokument och index används för att beräkna lagringsutrymmet som förbrukas av varje samling. Du kan styra de lagring och prestanda med de som är associerade med indexering genom att konfigurera indexeringsprincip för en samling. 

### <a name="configuring-the-indexing-policy-of-a-collection"></a>Konfigurera indexeringsprincip i en samling
Indexeringsprincip för varje samling kan du se prestanda- och nackdelarna som är associerade med indexering. Följande alternativ är tillgängliga för dig som en del av indexering konfiguration:  

* Välj om samlingen indexerar automatiskt alla dokument eller inte. Som standard indexeras automatiskt alla dokument. Du kan välja att inaktivera automatisk indexering och selektivt lägga till endast specifika dokument i indexet. Däremot kan du selektivt du exkludera endast vissa dokument. Du kan åstadkomma detta genom att ange egenskapen automatisk ska vara sant eller FALSKT på indexingPolicy i en samling och med hjälp av rubriken [x-ms-indexingdirective] samtidigt lägga till, ersätta eller ta bort ett dokument.  
* Välj om du vill inkludera eller exkludera specifika sökvägar eller mönster i dina dokument från indexet. Du kan respektive åstadkomma detta genom att inställningen includedPaths och excludedPaths på indexingPolicy i en samling. Du kan också konfigurera de lagring och prestanda med tanke på de intervall och hash-frågor för specifika sökvägsmönster. 
* Välj mellan synkron (på samma sätt) och asynkrona (lazy) index uppdateringar. Som standard uppdateras indexet synkront på varje infoga, Ersätt eller borttagning av ett dokument i samlingen. På så sätt kan frågor för att respektera samma konsekvensnivå som dokumentet läsningar. Azure Cosmos DB är skrivning optimerade och stöder varaktigt volymer av dokumentet skrivningar tillsammans med synkron indexunderhåll och betjänar konsekventa frågor, kan du konfigurera vissa samlingar för att uppdatera deras index lazily. Lazy indexering förstärker skrivprestanda ytterligare och är perfekt för bulk inmatning scenarier för främst inriktad Läs samlingar.

Indexprincip kan ändras genom att köra en placering på samlingen. Detta kan vara uppnås genom den [klient-SDK](sql-api-sdk-dotnet.md), [Azure-portalen](https://portal.azure.com), eller [REST API: er](/rest/api/cosmos-db/).

### <a name="querying-a-collection"></a>Frågar efter en samling
Dokumenten i en samling kan ha valfri scheman och du kan fråga dokument i en samling utan att ange något schema eller sekundära index från början. Du kan fråga efter en samling med hjälp av den [referens för Azure Cosmos DB SQL-syntax](https://msdn.microsoft.com/library/azure/dn782250.aspx), som ger omfattande hierarki-, Relations- och rumsfrågor operatörer och kan utvidgas via JavaScript-baserade UDF: er. JSON-grammatik gör det möjligt för att modellera JSON-dokument som träd med etiketter som trädnoder. Detta utnyttjas både av SQL-API: s automatiska indexeringstekniker samt Azure Cosmos DB SQL-språket. SQL-frågespråket består av tre huvudsakliga delar:   

1. En liten uppsättning frågeåtgärder som mappar naturligt till trädstrukturen inklusive hierarkisk frågor och projektioner. 
2. En delmängd av relationella åtgärder inklusive sammansättning, filter, projektioner, samlingar och självsignerat kopplingar. 
3. Ren JavaScript baserade användardefinierade funktioner som fungerar med (1) och (2).  

Azure Cosmos DB-frågemodell försöker göra en avvägning mellan funktionerna, effektivitet och enkelhet. Azure Cosmos DB database engine internt kompilerar och kör SQL-uttryck för fråga. Du kan fråga efter en samling med hjälp av den [REST API: er](/rest/api/cosmos-db/) eller någon av de [klient-SDK: er](sql-api-sdk-dotnet.md). .NET SDK levereras med en LINQ-provider.

> [!TIP]
> Du kan prova att använda SQL-API och köra SQL-frågor mot vår datauppsättning i det [Frågespelplan](https://www.documentdb.com/sql/demo).
> 
> 

## <a name="multi-document-transactions"></a>Flera dokument transaktioner
Databastransaktioner ger en säker och förutsägbar programmeringsmodell för att hantera samtidiga ändringar av data. I RDBMS, är det traditionella sättet att skriva affärslogik på att skriva **lagrade procedurer** och/eller **utlösare** och skickar den till databasservern för transaktionell körning. I RDBMS krävs program-programmerare för att hantera två olika programmeringsspråk: 

* (Icke-transaktionell) programmet programmeringsspråket (till exempel JavaScript, Python, C#, Java, osv.)
* T-SQL, det transaktionella programmeringsspråk som körs internt av databasen

Tack vare dess stort engagemang i JavaScript och JSON direkt i databasmotorn, Azure Cosmos DB tillhandahåller en intuitiv programmeringsmodell för verkställande JavaScript baserat programlogik direkt på samlingarna som lagrade procedurer och utlösare. Detta möjliggör följande:

* Effektiv implementering av samtidighet styra, återställning, automatisk indexering av JSON-objektdiagram direkt i databasmotorn
* Naturligt uttrycka Kontrollflöde, variabel omfång, tilldelning och integrering av primitiver med databastransaktioner direkt när det gäller JavaScript-programmeringsspråket för undantagshantering

JavaScript-logik som registrerats på en samlingsnivå kan sedan utfärda databasåtgärder i dokumenten i den angivna samlingen. Azure Cosmos DB implicit omsluter den JavaScript-baserade lagrade procedurer och utlösare inom en omgivande ACID-transaktion med ögonblicksbildisolering över dokumenten i en samling. Om JavaScript genererar ett undantag under körningen avbryts hela transaktionen. Den resulterande programmeringsmodellen är enkla men ändå kraftfull. JavaScript-utvecklare får en ”varaktiga” programmeringsmodell när du använder fortfarande sin välbekanta språkkonstruktioner och biblioteket primitiver.   

Möjligheten att köra JavaScript direkt i databasmotorn i samma adressutrymmet som buffertpoolen gör det möjligt för högpresterande och transaktionell körning av databasåtgärder mot dokumenten i en samling. Dessutom Cosmos DB databasmotor gör en stort engagemang i JSON och JavaScript eliminerar alla impedans matchningsfel mellan system som typ av program och databasen.   

När du har skapat en samling, du kan registrera lagrade procedurer, utlösare och UDF: er med en samling med hjälp av den [REST API: er](/rest/api/cosmos-db/) eller någon av de [klient-SDK: er](sql-api-sdk-dotnet.md). Du kan referera till och köra dem efter registreringen. Överväg följande lagrade procedur som helt skrivna i JavaScript, koden nedan tar två argument (boknamn och författarens namn) och skapar ett nytt dokument, frågar efter ett dokument och uppdaterar sedan den – allt inom en implicit ACID-transaktion. Om en JavaScript-undantagsfel utlöses när som helst under körningen avbryts hela transaktionen.

    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();        
        var collectionLink = collectionManager.getSelfLink()

        // create a new document.
        collectionManager.createDocument(collectionLink,
            {id: name, author: author},
            function(err, documentCreated) {
                if(err) throw new Error(err.message);

                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function(err, matchingDocuments) {
                        if(err) throw new Error(err.message);

                        context.getResponse().setBody(matchingDocuments.length);

                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don’t need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);   
                        }
                    })
            })
    };

Klienten skicka ”” ovan JavaScript-logik i databasen för transaktionell körning via HTTP POST. Läs mer om hur du använder HTTP-metoder, [RESTful interaktioner med Azure Cosmos DB-resurserna](https://msdn.microsoft.com/library/azure/mt622086.aspx). 

    client.createStoredProcedureAsync(collection._self, {id: "CRUDProc", body: businessLogic})
       .then(function(createdStoredProcedure) {
            return client.executeStoredProcedureAsync(createdStoredProcedure.resource._self,
                "NoSQL Distilled",
                "Martin Fowler");
        })
        .then(function(result) {
            console.log(result);
        },
        function(error) {
            console.log(error);
        });


Observera att eftersom databasen tolka JSON och JavaScript, det finns inga typmatchningsfel för system, ingen ”OR mappning” eller kod generation magic krävs.   

Lagrade procedurer och utlösare interagera med en samling och dokument i en samling via en väldefinierad objektmodell som visar den aktuella kontexten i samlingen.  

Samlingar i SQL-API kan skapas har tagits bort, läs eller uppräknade enkelt med antingen den [REST API: er](/rest/api/cosmos-db/) eller någon av de [klient-SDK: er](sql-api-sdk-dotnet.md). SQL-API ger alltid stark konsekvens för läsning eller skicka en fråga metadata för en samling. Tar bort en samling automatiskt säkerställer att du inte åtkomst till någon av dokument, bifogade filer, lagrade procedurer, utlösare och UDF: er som ingår i detta.   

## <a name="stored-procedures-triggers-and-user-defined-functions-udf"></a>Lagrade procedurer, utlösare och användardefinierade funktioner (UDF, User)
Enligt beskrivningen i föregående avsnitt, kan du skriva programlogiken till att köra direkt i en transaktion inuti databasmotorn. Programlogiken kan vara helt skrivna i JavaScript och kan modelleras som en lagrad procedur, utlösare eller en UDF. JavaScript-koden i en lagrad procedur eller utlösare kan infoga, Ersätt, ta bort, läsa eller fråga dokument i en samling. JavaScript i en UDF kan inte å andra sidan, infoga, ersätta eller ta bort dokument. Användardefinierade funktioner räkna upp dokument resultatuppsättningen för en fråga och skapa en annan resultatuppsättning. Azure Cosmos DB tillämpar en strikt reserverade-baserad resurs-styrning för flera innehavare. Var och en lagrad procedur, utlösare eller en UDF hämtar en fast quantum av operativsystemresurser med sitt arbete. Dessutom lagrade procedurer, utlösare och UDF: er kan inte länka mot externa JavaScript-bibliotek och övriga om de överskrider resurs budgetar som tilldelats dem. Du kan registrera, avregistrera lagrade procedurer, utlösare och UDF: er med en samling med hjälp av REST-API: er.  En lagrad procedur, utlösare eller en UDF är förväg kompilerad och lagras som bytekod, som genomförs senare vid registreringen. Följande avsnitt visar hur du kan använda Azure Cosmos DB JavaScript SDK för att registrera, köra och avregistrera en lagrad procedur, utlösare och en UDF. JavaScript SDK är en enkel omslutning över den [REST API: er](/rest/api/cosmos-db/). 

### <a name="registering-a-stored-procedure"></a>Registrera en lagrad procedur
Registrering av en lagrad procedur skapar en ny lagrad procedur-resurs på en samling via HTTP POST.  

    var storedProc = {
        id: "validateAndCreate",
        body: function (documentToCreate) {
            documentToCreate.id = documentToCreate.id.toUpperCase();

            var collectionManager = getContext().getCollection();
            collectionManager.createDocument(collectionManager.getSelfLink(),
                documentToCreate,
                function(err, documentCreated) {
                    if(err) throw new Error('Error while creating document: ' + err.message;
                    getContext().getResponse().setBody('success - created ' + 
                            documentCreated.name);
                });
        }
    };

    client.createStoredProcedureAsync(collection._self, storedProc)
        .then(function (createdStoredProcedure) {
            console.log("Successfully created stored procedure");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-stored-procedure"></a>Kör en lagrad procedur
Körningen av en lagrad procedur görs genom att utfärda en HTTP POST mot en befintlig resurs för lagrad procedur genom att skicka parametrar till proceduren i begärandetexten.

    var inputDocument = {id : "document1", author: "G. G. Marquez"};
    client.executeStoredProcedureAsync(createdStoredProcedure.resource._self, inputDocument)
        .then(function(executionResult) {
            assert.equal(executionResult, "success - created DOCUMENT1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-stored-procedure"></a>Avregistrera en lagrad procedur
Avregistrera en lagrad procedur görs genom att utfärda en HTTP DELETE mot en befintlig resurs för lagrad procedur.   

    client.deleteStoredProcedureAsync(createdStoredProcedure.resource._self)
        .then(function (response) {
            return;
        }, function(error) {
            console.log("Error");
        });


### <a name="registering-a-pre-trigger"></a>Registrera en före utlösare
Registrering av en utlösare gör du genom att skapa en ny utlösarresurs på en samling via HTTP POST. Du kan ange om utlösaren är en i förväg eller en post-utlösare och vilken typ av åtgärd kan det vara associerad med (till exempel skapa, ersätta, ta bort, eller alla).   

    var preTrigger = {
        id: "upperCaseId",
        body: function() {
                var item = getContext().getRequest().getBody();
                item.id = item.id.toUpperCase();
                getContext().getRequest().setBody(item);
        },
        triggerType: TriggerType.Pre,
        triggerOperation: TriggerOperation.All
    }

    client.createTriggerAsync(collection._self, preTrigger)
        .then(function (createdPreTrigger) {
            console.log("Successfully created trigger");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-pre-trigger"></a>Kör en tidigare utlösare
Körningen av en utlösare gör du genom att ange namnet på en befintlig utlösare vid tidpunkten för efter/PUT/ta bort utfärdas av en resurs för dokumentet via huvudet för begäran.  

    client.createDocumentAsync(collection._self, { id: "doc1", key: "Love in the Time of Cholera" }, { preTriggerInclude: "upperCaseId" })
        .then(function(createdDocument) {
            assert.equal(createdDocument.resource.id, "DOC1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-pre-trigger"></a>Avregistrera en före utlösare
Avregistrera en utlösare görs via utfärda en HTTP DELETE mot en befintlig utlösarresurs.  

    client.deleteTriggerAsync(createdPreTrigger._self);
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

### <a name="registering-a-udf"></a>Registrera en UDF
Registrering av en UDF görs genom att skapa en ny UDF-resurs på en samling via HTTP POST.  

    var udf = { 
        id: "mathSqrt",
        body: function(number) {
                return Math.sqrt(number);
        },
    };
    client.createUserDefinedFunctionAsync(collection._self, udf)
        .then(function (createdUdf) {
            console.log("Successfully created stored procedure");
        }, function(error) {
            console.log("Error");
        });

### <a name="executing-a-udf-as-part-of-the-query"></a>Köra en UDF som en del av frågan
En UDF kan anges som en del av SQL-frågan och används som ett sätt att utöka grundläggande [SQL-frågespråket](sql-api-sql-query-reference.md).

    var filterQuery = "SELECT udf.mathSqrt(r.Age) AS sqrtAge FROM root r WHERE r.FirstName='John'";
    client.queryDocuments(collection._self, filterQuery).toArrayAsync();
        .then(function(queryResponse) {
            var queryResponseDocuments = queryResponse.feed;
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-udf"></a>Avregistrera en UDF
Avregistrera en UDF görs bara genom att utfärda en HTTP DELETE mot en befintlig UDF-resurs.  

    client.deleteUserDefinedFunctionAsync(createdUdf._self)
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

Även om kodfragment ovan visade registrering (POST), avregistrera (PUT), Läs/lista (GET) och körningen (POST) via den [JavaScript SDK](https://github.com/Azure/azure-documentdb-js), du kan också använda den [REST API: er](/rest/api/cosmos-db/) eller andra [klient-SDK: er](sql-api-sdk-dotnet.md). 

## <a name="documents"></a>Dokument
Du kan infoga, Ersätt, ta bort, läsa, räkna upp och fråga godtyckliga JSON-dokument i en samling. Azure Cosmos DB tvingade inte något schema och kräver inte sekundära index för att stödja frågealternativ över dokument i en samling. Den maximala storleken för ett dokument är 2 MB.   

Är en verkligt öppen databastjänst, Azure Cosmos DB inte göra några särskilda datatyper (till exempel datum tid) eller specifika kodningar för JSON-dokument. Azure Cosmos DB kräver inte någon särskild JSON-konventioner att kodifiera relationerna mellan olika dokument. SQL-syntaxen för Azure Cosmos DB ger kraftfulla hierarkisk och relationsdata fråga operatorer för att fråga och projekt dokument utan särskild anteckningar eller behöva kodifiera relationerna mellan dokument med hjälp av unika egenskaper.  

Som med alla andra resurser, dokument kan skapas och byts ut kommer tas bort, läsa, räknas upp och efterfrågas enkelt med hjälp av REST API: er eller någon av de [klient-SDK: er](sql-api-sdk-dotnet.md). Ta bort ett dokument omedelbart frigör den kvot som motsvarar alla kapslade bifogade filer. Läs konsekvensnivå dokument följer principen konsekvens på databaskontot. Den här principen kan åsidosättas på basis av per begäran beroende på krav på datakonsekvens för ditt program. När du frågar efter dokument, följer läsningskontinuitet indexering inställd på samlingen. För ”konsekvent” följer detta kontoprincip konsekvens. 

## <a name="attachments-and-media"></a>Bifogade filer och media
Azure Cosmos DB kan du lagra binär blobar/media antingen med Azure Cosmos DB (högst 2 GB per konto) eller till ditt eget fjärranslutna mediebutik. Du kan också som representerar metadata för en media när det gäller ett specialdokument som kallas bifogad fil. En bifogad fil i Azure Cosmos DB är ett särskilt (JSON)-dokument som refererar till den media/blobben som lagras på annan plats. Bifogad fil är bara ett specialdokument som samlar in metadata (till exempel plats, författare osv) för en media som lagras i en fjärransluten medielagringsplats. 

Överväg att ett sociala läsning-program som använder Azure Cosmos DB för att lagra pennanteckningar och metadata, inklusive kommentarer, visar bokmärken, klassificeringar, gilla/kritik etc. som är associerade för en e-bok av en viss användare.   

* Innehållet i en bok själva lagras i Medialagring antingen tillgängligt som en del av Azure Cosmos DB-databaskontot eller en fjärransluten mediebutik. 
* Ett program kan lagra metadata för varje användare som distinkta dokument – till exempel Josefs metadata för Bok1 lagras i ett dokument som refereras av /colls/joe/docs/book1. 
* Bifogade filer som pekar på innehållssidor av en viss bok för en användare lagras under motsvarande dokumentet, till exempel /colls/joe/docs/book1/chapter1, /colls/joe/docs/book1/chapter2 osv. 

I exemplen ovan används eget ID: n för att förmedla resurs-hierarkin. Resurser kan nås via REST-API: er via unik resurs-ID: n. 

Refererar till Median av dess URI för media som hanteras av Azure Cosmos DB, _medier-egenskapen för den bifogade filen. Azure Cosmos DB garanterar att skräpinsamling samlar du in mediet när alla utestående referenser ignoreras. Azure Cosmos DB automatiskt genererar den bifogade filen när du överför ny media och fylls _medier så att den pekar till det nyligen tillagda mediet. Om du väljer att lagra mediet i en fjärransluten blob store som hanteras av dig (till exempel OneDrive, Azure Storage, DropBox, etc.), kan du fortfarande använda bilagor för att referera till mediet. Då du skapar den bifogade filen själv och fylla i egenskapen _medier.   

Som med alla andra resurser, kan du skapa bifogade filer, ersatts, tas bort, läsa eller räkna upp enkelt med hjälp av REST API: er eller någon av klient-SDK: er. Precis som med dokument, följer läsningskontinuitet andelen bilagor konsekvens principen på databaskontot. Den här principen kan åsidosättas på basis av per begäran beroende på krav på datakonsekvens för ditt program. När du frågar efter bifogade filer, följer läsningskontinuitet indexering inställd på samlingen. För ”konsekvent” följer detta kontoprincip konsekvens. 
 

## <a name="users"></a>Användare
En Azure Cosmos DB-användare representerar ett logiskt namnområde för gruppering av behörigheter. En Azure Cosmos DB-användare kan motsvara en användare i ett identitetshanteringssystem eller en fördefinierad programrollen. Azure Cosmos DB representerar en användare bara en abstraktion att gruppera en uppsättning behörigheter under en databas.   

Du kan skapa användare i Azure Cosmos DB, vilket motsvarar dina faktiska användare eller klienter för ditt program för att implementera flera innehavare i ditt program. Du kan sedan skapa behörigheter för en viss användare som motsvarar åtkomstkontroll över olika samlingar, dokument, bifogade filer, osv.   

Dina program behöver att skala med dina användare möjlighet att växa, kan du använda olika sätt att dela dina data. Du kan utforma alla användare på följande sätt:   

* Varje användare mappas till en databas.
* Varje användare mappas till en samling. 
* Dokument som motsvarar flera användare går du till en särskild samling. 
* Dokument som motsvarar flera användare går du till en uppsättning samlingar.   

Oavsett strategi för specifika horisontell partitionering väljer du, kan du modellera dina faktiska användare som användare i Azure Cosmos DB-databas och koppla de detaljerade behörigheter för varje användare.  

![Användarsamlingar][3]  
**Strategier för horisontell partitionering och modellering användare**

Som alla andra resurser, användare i Azure Cosmos DB kan skapas, ersatts, tas bort, läsa eller räkna upp enkelt med hjälp av REST API: er eller någon av klient-SDK. Azure Cosmos DB tillhandahåller alltid stark konsekvens för läsning eller skicka en fråga metadata för en användarresurs. Det är värt att påpeka att ta bort en användare automatiskt garanterar att du inte kan komma åt någon av de behörigheter som ingår i detta. Även om Azure Cosmos DB återtar kvoten av behörigheter som en del av den borttagna användaren i bakgrunden, behörigheterna som har tagits bort är tillgänglig direkt igen som du kan använda.  

## <a name="permissions"></a>Behörigheter
Ur en access control, resurser, till exempel databaskonton, databaser, användare och behörigheten anses *administrativa* resurser eftersom de kräver administratörsbehörighet. Å andra sidan resurser inklusive samlingar, dokument, bifogade filer, lagrade procedurer, utlösare och UDF: er är begränsade under en viss databas och anses vara *programresurser*. Auktoriseringsmodellen som motsvarar de två typerna av resurser och de roller som kommer åt dem (nämligen administratör och användare) definierar två typer av *åtkomstnycklar*: *huvudnyckeln* och  *Resursnyckeln*. Huvudnyckeln är en del av kontot och har angetts för utvecklare (eller administratör) som etablerar databaskontot. Den här huvudnyckeln måste administratören semantik, eftersom den kan användas för att auktorisera åtkomst till både administrativa och program. Däremot har en resursnyckel är en detaljerad åtkomstnyckel som ger åtkomst till en *specifika* programresurs. Därför den samlar in relation mellan användaren av en databas och de behörigheter som användaren har för en specifik resurs (till exempel samling, dokument, bifogad fil, lagrade procedurer, utlösare eller UDF).   

Det enda sättet att få en resursnyckel är genom att skapa en behörighet resurs under en viss användare. För att kunna skapa eller hämta en behörighet, visas en huvudnyckel i auktoriseringshuvudet. En behörighet resurs binda resursen, dess åtkomst och användaren. När du har skapat en behörighet resurs, behöver användaren bara presentera den associerade Resursnyckeln för att få åtkomst till önskad resurs. Därför kan en Resursnyckeln ses som en logisk och compact återgivning av resursen behörighet.  

Som med alla andra resurser, kan du skapa behörigheter i Azure Cosmos DB, ersatts, tas bort, läsa eller räkna upp enkelt med hjälp av REST API: er eller någon av klient-SDK: er. Azure Cosmos DB tillhandahåller alltid stark konsekvens för läsning eller skicka en fråga metadata för en behörighet. 

## <a name="next-steps"></a>Nästa steg
Läs mer om att arbeta med resurser med hjälp av HTTP-kommandon i [RESTful interaktioner med Azure Cosmos DB-resurserna](https://msdn.microsoft.com/library/azure/mt622086.aspx).

[1]: media/sql-api-resources/resources1.png
[2]: media/sql-api-resources/resources2.png
[3]: media/sql-api-resources/resources3.png

