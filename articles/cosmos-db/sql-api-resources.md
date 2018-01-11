---
title: Azure DB Cosmos-resursmodell och begrepp | Microsoft Docs
description: "Läs mer om Azure Cosmos DB hierarkiska modell för databaser, samlingar, användardefinierad funktion (UDF), dokument, behörighet att hantera resurser och mycket mer."
keywords: Hierarkisk modellen cosmosdb, azure, Microsoft azure
services: cosmos-db
documentationcenter: 
author: rafats
manager: jhubbard
ms.assetid: ef9d5c0c-0867-4317-bb1b-98e219799fd5
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: rafats
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a88f17a658987e1ff3ae0e0f38d6551c3acee1da
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="azure-cosmos-db-hierarchical-resource-model-and-core-concepts"></a>Hierarkisk Azure Cosmos DB-resursmodell och huvudkoncept

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Databasenheter som hanterar Azure Cosmos DB kallas **resurser**. Varje resurs identifieras unikt med en logisk URI. Du kan interagera med resurser med HTTP-verb som standard, frågor och svar sidhuvuden och statuskoder. 

Den här artikeln innehåller svar på följande frågor:

* Vad är Azure Cosmos DB modell?
* Vad är definierat resurser och användardefinierade resurser?
* Hur åtgärdar en resurs?
* Hur fungerar med samlingar?
* Hur fungerar med lagrade procedurer, utlösare och användardefinierade funktioner (UDF)?

## <a name="hierarchical-resource-model"></a>Hierarkiska resursmodell
Som i följande diagram illustreras Azure Cosmos DB hierarkiska **resursmodell** består av uppsättningar av resurser under ett databaskonto varje adresserbara via en logisk och stabil URI. En uppsättning resurser kallas för en **feed** i den här artikeln. 

> [!NOTE]
> Azure Cosmos-DB erbjuder en högeffektiv TCP-protokollet, vilken är också RESTful i sin kommunikation modellen, tillgängliga via den [SQL .NET klienten API](sql-api-sdk-dotnet.md).
> 
> 

![Azure DB Cosmos hierarkiska resursmodell][1]  
**Hierarkiska resursmodell**   

När du börjar arbeta med resurser, måste du [skapa ett databaskonto](create-sql-api-dotnet.md) med din Azure-prenumeration. Ett databaskonto kan bestå av en uppsättning **databaser**, var och en innehåller flera **samlingar**, varje av som i sin tur innehåller ** lagrade procedurer, utlösare, UDF: er, dokument och relaterade  **bifogade filer**. En databas har också associerade **användare**, var och en med en uppsättning **behörigheter** åtkomst till samlingar, lagrade procedurer, utlösare, UDF: er, dokument eller bilagor. Databaser, användare, behörigheter och samlingar är systemdefinierade resurser med välkända scheman, innehåller dokument och bilagor godtyckligt, användardefinierade JSON-innehåll.  

| Resurs | Beskrivning |
| --- | --- |
| Databaskonto |Ett databaskonto är associerad med en uppsättning databaser och en fast mängd blob-lagring för bifogade filer. Du kan skapa en eller flera databasen konton med hjälp av din Azure-prenumeration. Mer information finns i [sida med priser](https://azure.microsoft.com/pricing/details/cosmos-db/). |
| Databas |En databas är en logisk behållare för dokumentlagring, partitionerad över samlingarna. Det är en användarbehållare. |
| Användare |Det logiska namnområden scope behörigheter. |
| Behörighet |En autentiseringstoken som associeras med en användare för åtkomst till en viss resurs. |
| Samling |En samling är en behållare för JSON-dokument och associerad JavaScript-applogik. En samling är en fakturerbar enhet där [kostnaden](performance-levels.md) bestäms av samlingens prestandanivå. Samlingar kan omfatta en eller flera partitioner/servrar och skalas för att hantera praktiskt taget obegränsade volymer av lagring eller dataflöde. |
| Lagrad procedur |Programlogik skriven i JavaScript, som är registrerade med en samling och transaktionellt köras inom databasmotorn. |
| Utlösare |Programlogik som skrivits i JavaScript utföras före eller efter antingen ett insert-, ersätta eller ta bort igen. |
| UDF |Programlogik skriven i JavaScript. UDF: er kan du utforma en anpassad fråga operator och därmed Utöka grundläggande SQL API-frågespråket. |
| Dokument |Användardefinierat (godtyckligt) JSON-innehåll. Inget schema måste definieras som standard eller behöver sekundärindex måste anges för alla dokument som läggs till en samling. |
| Bifogad fil |En bilaga är ett särskilt dokument som innehåller referenser och associerade metadata för extern blob/media. Utvecklaren kan välja att ha blob som hanteras av Cosmos DB eller lagra med en extern blob-leverantör OneDrive, Dropbox, t.ex. |

## <a name="system-vs-user-defined-resources"></a>System kontra användardefinierade resurser
Resurser, till exempel databasen konton, databaser, samlingar, användare, behörigheter, lagrade procedurer, utlösare och UDF - alla har en fast schema och kallas systemresurser. Däremot har inga begränsningar på schemat resurser, t.ex dokument och bilagor och är exempel på användardefinierade resurser. I Cosmos DB representeras och hanteras som standard-kompatibel JSON både system- och anpassade resurser. Alla resurser, system eller användardefinierade, har du följande gemensamma egenskaper:

> [!NOTE]
> Alla systemgenererade egenskaper i en resurs prefix med ett understreck (_) i sina JSON-representation.
> 
> 

<table>
    <tbody>
        <tr>
            <td valign="top"><p><strong>Egenskap</strong></p></td>
            <td valign="top"><p><strong>Ange användar- eller genereras av systemet?</strong></p></td>
            <td valign="top"><p><strong>Syfte</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>_rid</p></td>
            <td valign="top"><p>Genereras av systemet</p></td>
            <td valign="top"><p>Systemgenererat, unikt och hierarkiska identifierare för resursen</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_etag</p></td>
            <td valign="top"><p>Genereras av systemet</p></td>
            <td valign="top"><p>ETag för resursen som krävs för optimistisk samtidighetskontroll</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_ts</p></td>
            <td valign="top"><p>Genereras av systemet</p></td>
            <td valign="top"><p>Senast uppdaterad tidsstämpeln för resursen</p></td>
        </tr>
        <tr>
            <td valign="top"><p>_self</p></td>
            <td valign="top"><p>Genereras av systemet</p></td>
            <td valign="top"><p>Unikt adresserbara URI för resursen</p></td>
        </tr>
        <tr>
            <td valign="top"><p>id</p></td>
            <td valign="top"><p>Antingen</p></td>
            <td valign="top"><p>Användardefinierade unikt namn på resurs (med den samma partitionsnyckelvärde). Om användaren inte anger ett id, är ett id genereras av systemet</p></td>
        </tr>
    </tbody>
</table>

### <a name="wire-representation-of-resources"></a>Överföring representation av resurser
Cosmos DB tvingade inte egna tillägg till JSON standard eller särskilda kodningar; den fungerar med standard kompatibla JSON-dokument.  

### <a name="addressing-a-resource"></a>En resurs-adressering
Alla resurser är adresserbara URI. Värdet för den **_self** egenskap för en resurs representerar relativ URI för resursen. URI-formatet består av de /\<feed\>/ {_rid} sökvägssegment:  

| Värdet för _self | Beskrivning |
| --- | --- |
| /DBS |Feed med databaser under ett databaskonto |
| /DBS/ {dbName} |Databasen med id matchar värdet {dbName} |
| /DBS/ {dbName} /colls/ |Feeden av samlingar under en-databas |
| /DBS/ {dbName} /colls/ {collName} |Samling med id matchar värdet {collName} |
| /DBS/ {dbName} /colls/ {collName} / docs |Feeden dokument under en samling |
| /DBS/ {dbName} /colls/ {collName} /docs/ {docId} |Dokument med ett id som matchar värdet {doc} |
| /DBS/ {dbName} /users/ |Flöde för användare under en-databas |
| /DBS/ {dbName} /users/ {userId} |Användare med ett id som matchar värdet {user} |
| /DBS/ {dbName} /users/ {userId} / behörigheter |Feeden behörigheter under en användare |
| /DBS/ {dbName} /users/ {userId} /permissions/ {permissionId} |Behörighet med id matchar värdet {behörighet} |

Varje resurs har ett unikt användardefinierat namn som exponeras via id-egenskapen. Obs: för dokument, om användaren inte anger ett id SDK: erna automatiskt generera ett unikt id för dokumentet. ID: t är en användardefinierad sträng med upp till 256 tecken som är unikt inom ramen för en viss överordnade resurs. 

Varje resurs har också en systemgenererad hierarkiska resource identifier (kallas även en RID), som är tillgänglig via egenskapen _rid. RID kodar hela hierarkin för en viss resurs och det är en lämplig interna representation som används för att genomdriva referensintegritet på ett sätt som du har distribuerat. RID är unika inom ett databaskonto och används internt av Cosmos DB för effektiv routning utan mellan partition sökningar. Värdena för _self och _rid egenskaper är både alternativa och kanoniska representationer av en resurs. 

REST-API: er har stöd för adressering av resurser och routning av begäran av både id och _rid egenskaper.

## <a name="database-accounts"></a>Databasen konton
Du kan etablera ett eller flera Cosmos DB databasen konton med din Azure-prenumeration.

Du kan skapa och hantera konton för Cosmos-DB-databasen via Azure portal på [http://portal.azure.com/](https://portal.azure.com/). Skapa och hantera ett databaskonto kräver administratörsbehörighet och kan bara genomföras under din Azure-prenumeration. 

### <a name="database-account-properties"></a>Egenskaper för databas
Som en del av etablera och hantera ett konto kan du konfigurera och läsa följande egenskaper:  

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Egenskapsnamn</strong></p></td>
            <td valign="top"><p><strong>Beskrivning</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>Princip för konsekvenskontroll</p></td>
            <td valign="top"><p>Den här egenskapen att konfigurera standardnivån för konsekvenskontroll för alla samlingar i ditt konto. Du kan åsidosätta konsekvensnivå på grundval av per begäran med begärandehuvudet [x-ms-konsekvens-nivå]. <p><p>Den här egenskapen gäller bara den <i>användardefinierade resurser</i>. Alla system definierade resurser konfigureras för att stödja läsningar/frågor med stark konsekvens.</p></td>
        </tr>
        <tr>
            <td valign="top"><p>Auktorisering nycklar</p></td>
            <td valign="top"><p>De primära och sekundära master och readonly nycklarna som tillhandahåller administrativ åtkomst till alla resurser i databaskontot.</p></td>
        </tr>
    </tbody>
</table>

Förutom etablering, konfigurera och hantera ditt konto i Azure Portal, du kan också programmässigt skapa och hantera Cosmos DB databasen konton med hjälp av den [Azure Cosmos DB REST API: er](/rest/api/documentdb/) samt [client SDK](sql-api-sdk-dotnet.md).  

## <a name="databases"></a>Databaser
En Cosmos-DB-databas är en logisk behållare för en eller flera samlingar och användare, som visas i följande diagram. Du kan skapa valfritt antal databaser under en Cosmos-DB-databaskonto med erbjudandet begränsningar.  

![Konto och samlingar hierarkiska databasmodellen][2]  
**En databas är en logisk behållare för användare och samlingar**

En databas kan innehålla praktiskt taget obegränsade dokumentlagring, partitionerad i samlingar.

### <a name="elastic-scale-of-an-azure-cosmos-db-database"></a>Elastisk skalbarhet av en Azure Cosmos-DB-databas
En Cosmos-DB-databas är elastisk standard-allt från några få GB till petabyte SSD backas upp dokumentlagring och etablerat dataflöde. 

Till skillnad från en databas i traditionella RDBMS begränsas inte en databas i Cosmos-databasen till en enda dator. Med Cosmos DB som skala ditt program behöver växa, kan du skapa flera samlingar, databaser eller båda. Faktiskt har olika första partsprogram i Microsoft använt Azure Cosmos DB på en konsument skala genom att skapa mycket stora databaser i Azure Cosmos DB varje med tusentals samlingar med terabytes dokumentlagring. Du kan öka eller minska en databas genom att lägga till eller ta bort samlingar för att uppfylla skala programkrav. 

Du kan skapa valfritt antal samlingar i en databas, upp till erbjudandet. Varje samling har säkerhetskopieras SSD-lagringen och dataflödet du beroende på vilken valda prestandanivå.

En Azure Cosmos-DB-databas är också en behållare för användare. En användare i sin tur är ett logiskt namnområde för en uppsättning behörigheter som ger detaljerad behörighet och åtkomst till samlingar, dokument och bifogade filer.  

Eftersom andra resurser i Azure Cosmos DB resursmodell databaser kan skapas, ersätts, tas bort, läsa eller räkna upp enkelt med antingen den [REST API: er](/rest/api/documentdb/) eller någon av de [klient-SDK: er](sql-api-sdk-dotnet.md). Azure Cosmos-DB garanterar stark konsekvens för läsning eller en fråga till metadata för en resurs i databasen. Ta bort en databas automatiskt garanterar att du inte kan komma åt någon av de samlingar eller användare som finns i den.   

## <a name="collections"></a>Samlingar
En Cosmos-DB-samling är en behållare för JSON-dokument. 

### <a name="elastic-ssd-backed-document-storage"></a>Elastisk SSD säkerhetskopieras dokumentlagring
En samling är filsystemen elastisk - och den automatiskt växer förminskas när du lägger till eller ta bort dokument. Samlingar är logiska nätverksresurser och kan sträcka sig över en eller flera partitioner fysiska servrar. Antalet partitioner i en samling bestäms av Cosmos DB baserat på lagringsstorleken och etablerat dataflöde för din samling. Varje partition i Cosmos-databasen har en fast mängd SSD-baserad lagring som är kopplad till den och replikeras för hög tillgänglighet. Partitionen management hanteras helt av Azure Cosmos DB och du behöver inte skriva komplex kod eller hantera din partitioner. Cosmos DB samlingar är **praktiskt taget obegränsade** vad gäller lagring och genomflöde. 

### <a name="automatic-indexing-of-collections"></a>Automatisk indexering av samlingar
Azure Cosmos-DB är ett sant schemafria databassystem. Varken antar eller kräver ett schema för JSON-dokument. När du lägger till dokument till en samling Azure Cosmos DB indexerar automatiskt dem och de är tillgängliga för dig att fråga. Automatisk indexering av dokument utan schemat eller sekundärindex är en funktion som är viktiga för Azure Cosmos DB och aktiveras av write-optimerad frigöra Lås och loggstrukturerad index Underhåll tekniker. Azure Cosmos-DB stöder varaktigt mängden mycket snabba skrivningar när betjänar konsekvent frågor. Både dokumentet och index lagring används för att beräkna det lagringsutrymme som förbrukas av varje samling. Du kan styra lagrings- och prestandakrav avvägningarna som är associerade med indexering genom att konfigurera indexprincip för en samling. 

### <a name="configuring-the-indexing-policy-of-a-collection"></a>Konfigurera indexprincip i en samling
Indexprincip för varje samling kan du kompromissa prestanda och lagring som är associerade med indexering. Följande alternativ är tillgängliga för dig som en del av indexerings-konfigurationen:  

* Välj om samlingen indexerar automatiskt alla dokument eller inte. Alla dokument indexeras automatiskt som standard. Du kan välja att inaktivera automatisk indexering och selektivt till endast specifika dokument i index. Du kan däremot selektivt välja att utesluta endast vissa dokument. Du kan åstadkomma detta genom att ange egenskapen automatisk ska vara sant eller FALSKT på indexingPolicy i en samling och använda begärandehuvudet [x-ms-indexingdirective] när lägga till, ersätta eller ta bort ett dokument.  
* Välj om du vill inkludera eller exkludera specifika sökvägar eller mönster i dokument från indexet. Du kan respektive åstadkomma detta genom att inställningen includedPaths och excludedPaths på indexingPolicy i en samling. Du kan också konfigurera lagrings- och prestandakrav avvägningarna för intervall och hash för angiven sökväg mönster. 
* Välj mellan synkron (konsekvent) och uppdateringar av index för asynkron (lazy). Indexet uppdateras synkront som standard på varje insert-, ersätta eller ta bort ett befintligt dokument i samlingen. Detta gör att frågorna ta hänsyn till samma konsekvensnivå som dokumentet läsningar. Du kan konfigurera vissa samlingar för att uppdatera deras index lazy medan Azure Cosmos DB skrivåtgärder optimerad och stöder varaktigt volymer av dokumentet skrivningar tillsammans med synkron index underhåll och betjänar konsekvent frågor. Lazy indexering förstärker skrivprestanda ytterligare och är idealisk för bulk införandet scenarier för främst frekventa Läs samlingar.

Indexprincip kan ändras genom att köra en PUT på samlingen. Detta kan vara uppnås antingen via den [klient-SDK](sql-api-sdk-dotnet.md), [Azure-portalen](https://portal.azure.com) eller [REST API: er](/rest/api/documentdb/).

### <a name="querying-a-collection"></a>Fråga till en samling
Dokumenten inom en samling kan ha godtyckliga scheman och du kan fråga dokument i en samling utan att ange något schema eller sekundärindex förskott. Du kan fråga en samling med hjälp av den [referens för Azure Cosmos-Databasens SQL-syntax](https://msdn.microsoft.com/library/azure/dn782250.aspx), vilket ger omfattande hierarkiska relationella och spatial operatorer och utvidgas via JavaScript-baserade UDF: er. JSON-grammatik gör det möjligt för att modellera JSON-dokument som träd med etiketter som trädnoder. Både SQL API-metoder för automatisk indexering samt Azure Cosmos DB SQL dialect utnyttjas. SQL-frågespråket består av tre huvudsakliga aspekter:   

1. En liten uppsättning frågeåtgärder som naturligt mappas till trädstrukturen inklusive hierarkiska frågor och projektioner. 
2. En delmängd av relationella operations inklusive sammansättning, filter, projektioner, mängder och self kopplingar. 
3. Ren JavaScript-baserade UDF: er som fungerar med (1) och (2).  

Azure Cosmos DB frågemodell försöker göra en avvägning mellan funktionerna, effektivitet och enkelhet. Databasmotorn Azure Cosmos DB internt kompileras och körs SQL-fråga uttryck. Du kan fråga en samling med hjälp av den [REST API: er](/rest/api/documentdb/) eller någon av de [client SDK](sql-api-sdk-dotnet.md). .NET SDK innehåller en LINQ-providern.

> [!TIP]
> Du kan prova att använda SQL-API och köra SQL-frågor mot vår datauppsättning i den [Query Playground](https://www.documentdb.com/sql/demo).
> 
> 

## <a name="multi-document-transactions"></a>Flera dokument transaktioner
Databastransaktioner ger en säker och förutsägbar programmeringsmodell för att hantera samtidiga ändringar av data. I RDBMS, är det traditionella sättet att skriva affärslogik att skriva **lagrade procedurer** och/eller **utlösare** och leverera till databasserver för transaktionell körning. I RDBMS krävs program-programmerare för att hantera två olika programmeringsspråk: 

* Programmet (icke-transaktionell) programmeringsspråket (exempelvis JavaScript, Python, C#, Java, etc.)
* T-SQL, transaktionella programmeringsspråket som körs internt av databasen

Tack vare djupgående förbinder sig att JavaScript och JSON direkt i databasmotorn, Azure Cosmos DB är ett intuitivt programmeringsmodell för verkställande JavaScript programlogik direkt utifrån samlingar som lagrade procedurer och utlösare. Detta möjliggör båda av följande:

* Effektiv implementering av samtidighet styra, återställning av automatisk indexering av JSON-objektdiagram direkt i databasmotorn
* Naturligt uttrycka Kontrollflöde, variabel scope, tilldelning och integration av undantagshantering primitiver med databastransaktioner direkt i JavaScript programmeringsspråket

JavaScript-logik som registrerats på en samlingsnivå kan sedan utfärda databasåtgärder i dokumenten i den angivna samlingen. Azure Cosmos-DB implicit radbryts JavaScript-baserade lagrade procedurer och utlösare inom en omgivande ACID-transaktioner med ögonblicksbildisolering mellan dokument i en samling. Om JavaScript genererar ett undantag under körningen avbryts hela transaktionen. Resulterande programmeringsmiljö är mycket enkelt men ändå kraftfulla. JavaScript-utvecklare får en ”varaktiga” programmeringsmodell när du använder fortfarande sin bekant språkkonstruktioner och biblioteket primitiver.   

Möjligheten att köra JavaScript direkt i databasmotorn i samma adressutrymme som buffertpoolen kan performant och transaktionell körning av databasåtgärder mot dokumenten i en samling. Dessutom Cosmos-databasen databasmotor gör ett djupgående åtagande att JSON och JavaScript åtgärdar eventuella impedans matchningsfel mellan de typ av program och databasen.   

Efter att en samling kan du registrera lagrade procedurer, utlösare och UDF: er med en samling med hjälp av den [REST API: er](/rest/api/documentdb/) eller någon av de [client SDK](sql-api-sdk-dotnet.md). Du kan referera till och köra dem efter registreringen. Överväg följande lagrade procedur helt skrivna i JavaScript, koden nedan tar två argument (namn och författarens namn) och skapar ett nytt dokument, frågar om ett dokument och uppdaterar sin – alla inom en implicit ACID-transaktion. Om en JavaScript-undantagsfel utlöses när som helst under körningen avbryts hela transaktionen.

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

Klienten kan ”levereras” ovan JavaScript-logik i databasen för transaktionell körning via HTTP POST. Mer information om hur du använder HTTP-metoderna finns [RESTful interaktioner med Azure Cosmos DB resurser](https://msdn.microsoft.com/library/azure/mt622086.aspx). 

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


Observera att eftersom databasen förstår internt JSON och JavaScript, det finns inga typmatchningsfel system, ingen ”eller mappning” eller code generation Magiskt tal krävs.   

Lagrade procedurer och utlösare interagera med en samling och dokument i en samling via en väldefinierad objektmodell som visar den aktuella kontexten i samlingen.  

Samlingar i SQL-API kan skapas, tagits bort, läs eller uppräknade enkelt med antingen den [REST API: er](/rest/api/documentdb/) eller någon av de [klient-SDK: er](sql-api-sdk-dotnet.md). SQL-API ger alltid stark konsekvens för läsning eller en fråga till metadata för en samling. Om du tar bort en samling automatiskt säkerställer att du kan inte komma åt dokument, bifogade filer, lagrade procedurer, utlösare och UDF: er som finns i den.   

## <a name="stored-procedures-triggers-and-user-defined-functions-udf"></a>Lagrade procedurer, utlösare och användaren definierat funktioner (UDF)
Enligt beskrivningen i föregående avsnitt, kan du skriva programlogiken till att köras direkt i en transaktion i databasmotorn. Logik för programmet kan vara helt skrivna i JavaScript och kan modelleras som en lagrad procedur, utlösare eller en UDF. JavaScript-kod i en lagrad procedur eller en utlösare kan infoga, Ersätt, ta bort, läs eller fråga dokument i en samling. Å andra sidan JavaScript inom en UDF inte insert-, ersätta eller ta bort dokument. UDF: Räkna upp dokument för en frågeresultatet och skapar en annan resultatuppsättning. Azure Cosmos DB tillämpar en strikt reservation-baserade resurs styrning för flera innehavare. Alla lagrade procedurer, utlösare och en UDF hämtar en fast quantum av systemresurser med sitt arbete. Dessutom lagrade procedurer, utlösare och UDF: er kan inte länka mot externa JavaScript-bibliotek och övriga om de överskrider budgetarna resurs tilldelas. Du kan registrera, avregistrera lagrade procedurer, utlösare och UDF: er med en samling med hjälp av REST-API: er.  Vid registreringen en lagrad procedur, utlösare eller en UDF före kompileras och lagras som bytekod genomförs senare. Följande ssection illustrateshow som du kan använda Azure Cosmos DB JavaScript SDK för att registrera, köra och avregistrera en lagrad procedur, utlösare och en UDF. JavaScript SDK är en enkel omslutning över den [REST API: er](/rest/api/documentdb/). 

### <a name="registering-a-stored-procedure"></a>Registrera en lagrad procedur
Registrering av en lagrad procedur skapar en ny resurs lagrad procedur på en samling via HTTP POST.  

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
Körningen av en lagrad procedur görs genom att utfärda en HTTP POST mot en befintlig resurs lagrade proceduren genom att skicka parametrar till proceduren i begärandetexten.

    var inputDocument = {id : "document1", author: "G. G. Marquez"};
    client.executeStoredProcedureAsync(createdStoredProcedure.resource._self, inputDocument)
        .then(function(executionResult) {
            assert.equal(executionResult, "success - created DOCUMENT1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-stored-procedure"></a>Avregistrerar en lagrad procedur
Avregistrerar en lagrad procedur görs bara genom att utfärda ett HTTP DELETE mot en befintlig resurs lagrad procedur.   

    client.deleteStoredProcedureAsync(createdStoredProcedure.resource._self)
        .then(function (response) {
            return;
        }, function(error) {
            console.log("Error");
        });


### <a name="registering-a-pre-trigger"></a>Registrera en före utlösare
Registrering av en utlösare görs genom att skapa en ny utlösare resurs på en samling via HTTP POST. Du kan ange om utlösaren är en före eller efter utlösare och typ av åtgärd kan den vara associerad med (till exempel skapa, ersätta, ta bort eller alla).   

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

### <a name="executing-a-pre-trigger"></a>Köra en före utlösare
Körningen av en utlösare gör du genom att ange namnet på en befintlig utlösare vid tidpunkten för POST/PUT/ta bort utfärdas av en resurs som dokument via begärandehuvudet.  

    client.createDocumentAsync(collection._self, { id: "doc1", key: "Love in the Time of Cholera" }, { preTriggerInclude: "upperCaseId" })
        .then(function(createdDocument) {
            assert.equal(createdDocument.resource.id, "DOC1");
        }, function(error) {
            console.log("Error");
        });

### <a name="unregistering-a-pre-trigger"></a>Avregistrerar en före utlösare
Avregistrerar en utlösare sker bara via utfärdar en HTTP-ta bort mot en befintlig resurs för utlösare.  

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

### <a name="unregistering-a-udf"></a>Avregistrerar en UDF
Avregistrerar en UDF görs bara genom att utfärda ett HTTP DELETE mot en befintlig UDF-resurs.  

    client.deleteUserDefinedFunctionAsync(createdUdf._self)
        .then(function(response) {
            return;
        }, function(error) {
            console.log("Error");
        });

Även om kodavsnitt ovan visade registrering (POST), avregistreringen (PUT), Läs-/ Liståtgärder (GET) och körningen (POST) via den [JavaScript SDK](https://github.com/Azure/azure-documentdb-js), du kan också använda den [REST API: er](/rest/api/documentdb/) eller andra [client SDK](sql-api-sdk-dotnet.md). 

## <a name="documents"></a>Dokument
Du kan infoga, Ersätt, ta bort, Läs, räkna upp och fråga godtyckliga JSON-dokument i en samling. Azure Cosmos-DB tvingade inte alla scheman och kräver inte sekundärindex för att stödja frågealternativ över dokument i en samling. Den maximala storleken för ett dokument är 2 MB.   

Som en tjänst verkligen öppna databasen Azure Cosmos DB inte hittar några särskilda datatyper (till exempel datum och tid) eller specifika kodningar för JSON-dokument. Azure Cosmos-DB kräver inte några särskilda JSON-konventioner att kodifiera relationerna mellan olika dokument. SQL-syntaxen i Azure Cosmos DB ger mycket kraftfulla hierarkiska relationella frågan och operatorer som fråge- och dokument utan speciella anteckningar eller behöver kodifiera relationerna mellan dokument med unika egenskaper.  

Som med alla andra resurser dokument kan skapas, bytts ut kan tas bort, Läs, räkna upp och frågas enkelt använda REST API: er eller någon av de [client SDK](sql-api-sdk-dotnet.md). Ta bort ett dokument direkt frigör den kvot som motsvarar alla kapslade bifogade filer. Läskonsekvens andelen dokument följer principen konsekvenskontroll på databaskontot. Den här principen kan åsidosättas på grundval av per begäran beroende på kraven för konsekvenskontroll av ditt program. När du frågar dokument följer läskonsekvensen indexering inställd på samlingen. Det följer kontoprincip konsekvens för ”konsekvent”. 

## <a name="attachments-and-media"></a>Bifogade filer och media
Azure Cosmos-DB kan du lagra binära blobbar/media antingen med Azure Cosmos DB (högst 2 GB per konto) eller till en egen remote media store. Du kan också som representerar metadata för ett medium i ett särskilt dokument kallas bifogad fil. Bifogad fil i Azure Cosmos DB är ett särskilt (JSON) dokument som refererar till media/blob som lagras på annan plats. En bilaga är bara ett särskilt dokument som samlar in metadata (till exempel plats, författare o.s.v.) för ett medium som lagras i en fjärransluten media storage. 

Överväg att ett sociala läsning program som använder Azure Cosmos DB för att lagra pennanteckningar och metadata, inklusive kommentarer, detaljer, bokmärken, klassificering, om/favoritsporter etc. som är kopplad till en e-bok för en viss användare.   

* Boken själva innehållet lagras i media storage antingen tillgängligt som en del av Azure Cosmos DB databaskonto eller en fjärransluten mediebutik. 
* Ett program kan lagra metadata för varje användare som distinkta dokument – till exempel Toms metadata för Bok1 lagras i ett dokument som refereras av /colls/joe/docs/book1. 
* Bifogade filer pekar på innehåll sidor i en viss bok för en användare till exempel lagras under motsvarande dokument, /colls/joe/docs/book1/chapter1, /colls/joe/docs/book1/chapter2 osv. 

Exemplen ovan Använd eget-ID: n för att förmedla resurs-hierarkin. Resurser kan nås via REST-API: er via unik resurs-ID: n. 

Egenskapen _media för den bifogade filen refererar till media av dess URI: N för de media som hanteras av Azure Cosmos DB. Azure Cosmos-DB säkerställer att skräp samla in mediet när alla utestående referenser tas bort. Azure Cosmos-DB automatiskt genererar den bifogade filen när du överför det nya mediet och fylls _media så att den pekar till det nyligen tillagda mediet. Om du väljer att lagra media i en fjärransluten blobstore som hanteras av dig (till exempel OneDrive, Azure Storage, DropBox osv), kan du fortfarande använda bilagor för att referera till mediet. I så fall måste du själv skapar den bifogade filen och fylla i egenskapen _media.   

Som med alla andra resurser, bifogade filer skapas, ersätts bort, läsa eller räkna upp enkelt använda REST API: er eller någon av klient-SDK: er. Precis som med dokument, följer läskonsekvens andelen bilagor konsekvenskontroll principen på kontot. Den här principen kan åsidosättas på grundval av per begäran beroende på kraven för konsekvenskontroll av ditt program. När du frågar efter bilagor följer läskonsekvensen indexering inställd på samlingen. Det följer kontoprincip konsekvens för ”konsekvent”. 
 

## <a name="users"></a>Användare
En användare i Azure Cosmos DB representerar ett logiskt namnområde för gruppering av behörigheter. En Azure DB som Cosmos-användare kan motsvara en användare i ett identity-system eller en fördefinierad programrollen. För Azure Cosmos DB representerar en användare helt enkelt en abstraktion att gruppera en uppsättning behörigheter under en databas.   

Du kan skapa användare i Azure Cosmos-databasen, vilket motsvarar faktiska användarna eller innehavare av ditt program för att implementera flera innehavare i ditt program. Du kan sedan skapa behörigheter för en viss användare som motsvarar åtkomstkontroll över olika samlingar, dokument, bilagor och så vidare.   

Som dina program behöver skala med dina användare tillväxt kan anta du olika sätt att fragmentera data. Du kan ange modellen varje användare på följande sätt:   

* Varje användare som mappar till en databas.
* Varje användare som mappar till en samling. 
* Dokument som motsvarar flera användare går du till en särskild samling. 
* Dokument som motsvarar flera användare går du till en uppsättning samlingar.   

Oavsett specifika horisontell partitionering strategin du, kan du modellera faktiska användarna som användare i Azure DB som Cosmos-databasen och koppla detaljerade behörigheter för varje användare.  

![Användarsamlingar][3]  
**Horisontell partitionering strategier och modellering användare**

Precis som alla andra resurser, användare i Azure Cosmos DB kan skapas, ersätts, tas bort, läsa eller räkna upp enkelt använda REST API: er eller någon av klient-SDK:. Azure Cosmos-DB ger alltid stark konsekvens för läsning eller en fråga till metadata för en användarresurs. Det är värt pekar på att ta bort en användare automatiskt garanterar att du inte kan komma åt någon av de behörigheter som finns i den. Även om Azure Cosmos DB återtar kvoten av behörigheter som en del av den borttagna användaren i bakgrunden, borttagna behörigheter är tillgänglig direkt igen som du kan använda.  

## <a name="permissions"></a>Behörigheter
Ur ett access control, resurser, till exempel databasen konton, databaser, användare och behörigheten anses *administrativa* resurser eftersom de kräver administratörsbehörighet. Å andra sidan resurser inklusive samlingarna, dokument, bifogade filer, lagrade procedurer, utlösare och UDF: er omfång under en viss databas och anses vara *programresurser*. Auktoriseringsmodellen som motsvarar de två typerna av resurser och de roller som kan komma åt dem (nämligen administratörer och användare) definierar två typer av *åtkomstnycklar*: *huvudnyckeln* och  *Resursnyckeln*. Huvudnyckeln är en del av kontot och har angetts för utvecklare (eller administratör) som etablerar databaskontot. Den här huvudnyckeln har administratören semantik i att den kan användas för att bevilja åtkomst till både administrativa och program. En Resursnyckeln är däremot ett detaljerade åtkomstnyckel som ger åtkomst till en *specifika* programresursen. Det innebär den samlar in relationen mellan användare av en databas och vilka behörigheter användaren har för en viss resurs (exempelvis samling dokument, bifogad fil, lagrade procedurer, utlösare eller UDF).   

Det enda sättet att hämta en Resursnyckeln är genom att skapa en behörighet resurs under en viss användare. Observera att för att skapa eller hämta behörighet, en huvudnyckel måste vara angiven på authorization-huvud. En behörighet resurs kopplar samman resursen, dess åtkomst och användaren. När du har skapat en behörighet resurs behöver användaren bara finns nyckeln associerad resurs för att få åtkomst till den aktuella resursen. Därför kan en Resursnyckeln ses som en logisk och compact representation av resursen behörighet.  

Som med alla andra resurser, behörigheter i Azure Cosmos DB skapas, ersätts bort, läsa eller räkna upp enkelt använda REST API: er eller någon av klient-SDK: er. Azure Cosmos-DB ger alltid stark konsekvens för läsning eller en fråga till metadata för en behörighet. 

## <a name="next-steps"></a>Nästa steg
Mer information om att arbeta med resurser med hjälp av HTTP-kommandon i [RESTful interaktioner med Azure Cosmos DB resurser](https://msdn.microsoft.com/library/azure/mt622086.aspx).

[1]: media/sql-api-resources/resources1.png
[2]: media/sql-api-resources/resources2.png
[3]: media/sql-api-resources/resources3.png

