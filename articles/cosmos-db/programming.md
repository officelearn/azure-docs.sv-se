---
title: Programmering av serversidan JavaScript för Azure Cosmos DB | Microsoft Docs
description: Lär dig hur du använder Azure Cosmos DB för att skriva lagrade procedurer, databasutlösare och användardefinierade funktioner (UDF) i JavaScript. Hämta databasen programing tips och mycket mer.
keywords: Utlösare, lagrad procedur, lagrad procedur, databasprogram, sproc, azure, Microsoft azure-databas
services: cosmos-db
documentationcenter: ''
author: aliuy
manager: kfile
ms.assetid: 0fba7ebd-a4fc-4253-a786-97f1354fbf17
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2018
ms.author: andrl
ms.openlocfilehash: 25ae6bde2ca89b2f944a8879c746dcedcf798ec2
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="azure-cosmos-db-server-side-programming-stored-procedures-database-triggers-and-udfs"></a>Azure DB Cosmos serversidan programmering: lagrade procedurer, databasutlösare och UDF: er

Lär dig hur Azure Cosmos DB språkintegrerade, transaktionell körning av JavaScript kan utvecklare skriva **lagrade procedurer**, **utlösare**, och **användardefinierade funktioner (UDF)**  internt i en [ECMAScript 2015](http://www.ecma-international.org/ecma-262/6.0/) JavaScript. Den här integreringen av Javascript kan du skriva programlogiken för databasen program kan levereras och köras direkt på partitioner för lagring av databasen. 

Vi rekommenderar att komma igång med att titta på nedanstående video, där Andrew Liu ger en introduktion till Azure Cosmos DB serversidan databasen programmeringsmodell. 

> [!VIDEO https://www.youtube.com/embed/s0cXdHNlVI0]
>
> 

Gå sedan tillbaka till den här artikeln, där du lär dig svaren på följande frågor:  

* Hur jag skriva en lagrad procedur, utlösare eller med hjälp av JavaScript UDF?
* Hur garanterar Cosmos DB av?
* Hur fungerar transaktioner i Cosmos-databasen?
* Vad är utlöser före och efter utlöser och hur skriver jag en?
* Hur registrerar jag och köra lagrade procedurer, utlösare och UDF RESTful sätt med hjälp av HTTP?
* Vad Cosmos DB SDK: er är tillgängliga för att skapa och köra lagrade procedurer, utlösare och UDF: er?

## <a name="introduction-to-stored-procedure-and-udf-programming"></a>Introduktion till lagrade proceduren och UDF-programmering
Den här metoden för *”JavaScript som en modern dag T-SQL”* Frigör programutvecklare från svårigheter av felmatchningar system och Objektrelationer mappning teknik. Det finns också ett antal inbyggda fördelar som kan användas för att skapa omfattande program:  

* **Procedurmässig logik:** JavaScript som ett övergripande programmeringsspråk, ger en omfattande och bekant gränssnitt till express affärslogik. Du kan utföra komplexa sekvenser av åtgärderna närmare data.
* **Atomiska transaktioner:** Cosmos DB garanterar att databasen åtgärder som utförs i en lagrad procedur eller utlösare är atomiska. Funktionen atomiska kan ett program kombinera relaterade åtgärder i en enda grupp, så att alla lyckas eller ingen av dem lyckas. 
* **Prestanda:** att JSON är mappad till Javascript-språket typsystemet och också är den grundläggande enheten för lagring i Cosmos DB möjliggör ett antal prestandaoptimeringar som lazy materialisering av JSON-dokument i buffertpoolen och gör dem tillgängliga på begäran i koden som körs. Det finns flera prestandafördelarna som är associerade med leverans affärslogik till databasen:
  
  * Batchbearbetning – utvecklare kan gruppera åtgärder som infogar och skicka dem gruppvis. Trafik Nätverksfördröjningen kostnad och store-kostnader för att skapa separata transaktioner minskas avsevärt. 
  * Före kompileringen – Cosmos DB precompiles lagrade procedurer, utlösare och användardefinierade funktioner (UDF) att undvika JavaScript kompilering kostnaden för varje anrop. Kostnader för att skapa bytekod för logiken i procedurmässig amorteras till ett minimalt värde.
  * Sekvensering – många åtgärder måste en sidoeffekt (”utlösaren”) som potentiellt omfattar en eller flera sekundära store-operationer. Utöver odelbarhet är det mer performant när de flyttas till servern. 
* **Inkapsling:** lagrade procedurer som kan användas för att gruppera affärslogik på en plats som har två fördelar:
  * Det lägger till ett Abstraktionslager ovanpå rådata, vilket gör att data arkitekter att utveckla sina program oberoende av data. Det här lagret Abstraktionslager är användbar när data är schema-mindre på grund av spröda antaganden som kan behöva vara inbyggd till programmet om de måste behandla data direkt.  
  * Denna framställning kan företag skydda sina data genom att effektivisera åtkomst från skript.  

Skapa och körning av databasutlösare, lagrade procedurer och anpassade frågeoperatorer stöds via den [Azure-portalen](https://portal.azure.com), [REST API](/rest/api/cosmos-db/), [Azure DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases), och [client SDK](sql-api-sdk-dotnet.md) på flera olika plattformar inklusive .NET, Node.js och JavaScript.

Den här kursen använder den [Node.js SDK med Q löftena](http://azure.github.io/azure-documentdb-node-q/) att illustrera syntax och användning av lagrade procedurer, utlösare och UDF: er.   

## <a name="stored-procedures"></a>Lagrade procedurer
### <a name="example-write-a-simple-stored-procedure"></a>Exempel: Skriv en enkel lagrad procedur
Låt oss börja med en lagrad procedur som returnerar svaret ”Hello World”.

    var helloWorldStoredProc = {
        id: "helloWorld",
        serverScript: function () {
            var context = getContext();
            var response = context.getResponse();

            response.setBody("Hello, World");
        }
    }


Lagrade procedurer registreras per samling och kan användas på alla dokument och bifogade filer i samlingen. Följande utdrag visar hur du registrerar helloWorld lagrade proceduren med en samling. 

    // register the stored procedure
    var createdStoredProcedure;
    client.createStoredProcedureAsync('dbs/testdb/colls/testColl', helloWorldStoredProc)
        .then(function (response) {
            createdStoredProcedure = response.resource;
            console.log("Successfully created stored procedure");
        }, function (error) {
            console.log("Error", error);
        });


När den lagrade proceduren har registrerats kan du köra den mot samlingen och läsa resultaten tillbaka till klienten. 

    // execute the stored procedure
    client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/helloWorld')
        .then(function (response) {
            console.log(response.result); // "Hello, World"
        }, function (err) {
            console.log("Error", error);
        });


Context-objektet ger åtkomst till alla åtgärder som kan utföras på Cosmos-databaslagring samt åtkomst till objekt förfrågan och svar. I det här fallet används det response-objektet för att ange brödtexten i svaret som skickades till klienten. Mer information finns i [server Azure Cosmos DB JavaScript SDK-dokumentationen](http://azure.github.io/azure-documentdb-js-server/).  

Låt oss Expandera på det här exemplet och lägga till fler Databasrelaterade funktioner i den lagrade proceduren. Lagrade procedurer kan skapa, uppdatera, läsa, fråga och ta bort dokument och bifogade filer i samlingen.    

### <a name="example-write-a-stored-procedure-to-create-a-document"></a>Exempel: Skriv en lagrad procedur för att skapa ett dokument
Nästa utdrag visar hur du använder context-objektet för att interagera med Cosmos-DB-resurser.

    var createDocumentStoredProc = {
        id: "createMyDocument",
        serverScript: function createMyDocument(documentToCreate) {
            var context = getContext();
            var collection = context.getCollection();

            var accepted = collection.createDocument(collection.getSelfLink(),
                  documentToCreate,
                  function (err, documentCreated) {
                      if (err) throw new Error('Error' + err.message);
                      context.getResponse().setBody(documentCreated.id)
                  });
            if (!accepted) return;
        }
    }


Den här lagrade proceduren tar inkommande documentToCreate, brödtexten i ett dokument som ska skapas i samlingen. Dessa åtgärder är asynkron och beroende av JavaScript-funktionen återanrop. Återanropsfunktionen har två parametrar, ett för felobjektet om åtgärden misslyckas och en för det skapade objektet. I återanropet, kan användare hantera undantaget eller ett fel genereras. Om ett återanrop har angetts och det finns ett fel, genererar ett fel i Azure DB som Cosmos-körningsmiljön.   

I exemplet ovan genererar återanropet ett fel om åtgärden misslyckades. Annars anges id för det skapade dokumentet som en del av svar till klienten. Här är hur den här lagrade proceduren körs med indataparametrar.

    // register the stored procedure
    client.createStoredProcedureAsync('dbs/testdb/colls/testColl', createDocumentStoredProc)
        .then(function (response) {
            var createdStoredProcedure = response.resource;

            // run stored procedure to create a document
            var docToCreate = {
                id: "DocFromSproc",
                book: "The Hitchhiker’s Guide to the Galaxy",
                author: "Douglas Adams"
            };

            return client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/createMyDocument',
                  docToCreate);
        }, function (error) {
            console.log("Error", error);
        })
    .then(function (response) {
        console.log(response); // "DocFromSproc"
    }, function (error) {
        console.log("Error", error);
    });


Observera att den här lagrade proceduren kan ändras för att ta en matris med dokumentet organ som indata och skapar dem i samma lagrade procedurkörningen i stället för flera nätverk förfrågningar till skapa dem individuellt. Detta kan användas för att implementera en effektiv bulk-Importverktyget för Cosmos-DB (beskrivs senare i den här självstudiekursen).   

Det exempel som beskrivs visas hur du använder lagrade procedurer. Senare under kursen tar vi upp utlösare och användardefinierade funktioner (UDF).

## <a name="database-program-transactions"></a>Programmet databastransaktioner
Transaktion i en typisk databas kan definieras som en sekvens med åtgärder som utförs som en logisk enhet på arbetet. Varje transaktion innehåller **ACID garantier**. AV är en välkänd förkortning som står för fyra egenskaper - odelbarhet, konsekvens, isolering och hållbarhet.  

En kort, odelbarhet garanterar att arbetet inuti en transaktion behandlas som en enhet där antingen alla strävar eller none. Konsekvenskontroll ser till att data alltid är i ett internt tillstånd över transaktioner. Isolering garanterar att inga två transaktioner störa varandra – Allmänt, de flesta kommersiella system ger flera isoleringsnivåer som kan användas baserat på programbehov. Hållbarhet säkerställer att alla ändringar genomförs i databasen alltid är tillgänglig.   

I Cosmos DB finns JavaScript i samma minnesutrymme som databas. Därför begäranden som görs i lagrade procedurer och utlösare kör samma omfånget för en databas-session. Detta gör att Cosmos-Databsen ska garantera av för alla åtgärder som ingår i en enda lagrade proceduren/utlösare. Överväg följande lagrade Procedurdefinition:

    // JavaScript source code
    var exchangeItemsSproc = {
        id: "exchangeItems",
        serverScript: function (playerId1, playerId2) {
            var context = getContext();
            var collection = context.getCollection();
            var response = context.getResponse();

            var player1Document, player2Document;

            // query for players
            var filterQuery = 'SELECT * FROM Players p where p.id  = "' + playerId1 + '"';
            var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery, {},
                function (err, documents, responseOptions) {
                    if (err) throw new Error("Error" + err.message);

                    if (documents.length != 1) throw "Unable to find both names";
                    player1Document = documents[0];

                    var filterQuery2 = 'SELECT * FROM Players p where p.id = "' + playerId2 + '"';
                    var accept2 = collection.queryDocuments(collection.getSelfLink(), filterQuery2, {},
                        function (err2, documents2, responseOptions2) {
                            if (err2) throw new Error("Error" + err2.message);
                            if (documents2.length != 1) throw "Unable to find both names";
                            player2Document = documents2[0];
                            swapItems(player1Document, player2Document);
                            return;
                        });
                    if (!accept2) throw "Unable to read player details, abort ";
                });

            if (!accept) throw "Unable to read player details, abort ";

            // swap the two players’ items
            function swapItems(player1, player2) {
                var player1ItemSave = player1.item;
                player1.item = player2.item;
                player2.item = player1ItemSave;

                var accept = collection.replaceDocument(player1._self, player1,
                    function (err, docReplaced) {
                        if (err) throw "Unable to update player 1, abort ";

                        var accept2 = collection.replaceDocument(player2._self, player2,
                            function (err2, docReplaced2) {
                                if (err) throw "Unable to update player 2, abort"
                            });

                        if (!accept2) throw "Unable to update player 2, abort";
                    });

                if (!accept) throw "Unable to update player 1, abort";
            }
        }
    }

    // register the stored procedure in Node.js client
    client.createStoredProcedureAsync(collection._self, exchangeItemsSproc)
        .then(function (response) {
            var createdStoredProcedure = response.resource;
        }
    );

Den här lagrade proceduren använder transaktioner inom spelappar handel objekt mellan två spelare i en enda åtgärd. Den lagrade proceduren försöker läsa två dokument i varje motsvarar player-ID skickas som ett argument. Om båda player dokumenten påträffas, uppdaterar den lagrade proceduren dokument genom att byta sina objekt. Om fel uppstår på vägen utlöser ett JavaScript-undantag som implicit avbryter transaktionen.

Om samlingen den lagrade proceduren har registrerats mot är en enskild partition samling och sedan transaktionen är begränsad till alla dokument i samlingen. Om samlingen är partitionerad utförs lagrade procedurer i transaktionsomfånget för en enskild partitionsnyckel. Varje lagrade proceduren körning innehålla ett värde för partitionen som motsvarar omfånget transaktionen måste köras under. Mer information finns i [Azure Cosmos DB partitionering](partition-data.md).

### <a name="commit-and-rollback"></a>Commit och rollback
Transaktioner är djupt och internt integrerade i Cosmos DB JavaScript-programmeringsmodell. I JavaScript-funktionen radbryts automatiskt alla åtgärder under en enda transaktion. Om JavaScript har slutförts utan några undantag, genomförs åtgärder i databasen. I praktiken är ”BEGIN TRANSACTION” och ”COMMIT TRANSACTION” instruktioner i relationsdatabaser implicit i Cosmos-databasen.  

Om det finns några undantag sprids från skriptet, återställs Cosmos DB JavaScript-körning hela transaktionen. I exemplet ovan visas ett undantagsfel utlöses motsvarar effektivt en ”ROLLBACK TRANSACTION” i Cosmos-databasen.

### <a name="data-consistency"></a>Datakonsekvens
Lagrade procedurer och utlösare körs alltid på den primära repliken på Azure DB som Cosmos-behållaren. Detta säkerställer att läsningar av inuti lagrade procedurer erbjudande stark konsekvens. Frågor med användardefinierade funktioner kan köras på den primära servern eller en sekundär replik, men vi se till att uppfylla de begärda konsekvensnivå genom att välja lämplig replica.

## <a name="bounded-execution"></a>Begränsad körning
Alla Cosmos-DB-åtgärder måste slutföras inom den angivna servern begär timeout-varaktighet. Den här begränsningen gäller även för JavaScript-funktioner (lagrade procedurer, utlösare och användardefinierade funktioner). Om en åtgärd inte slutförs med tidsgränsen återställs transaktionen. JavaScript-funktioner måste slutföras inom tidsgränsen eller implementera en fortsättning-baserade modell för batch/återuppta körning.  

För att förenkla utvecklingen av lagrade procedurer och utlösare för att hantera tidsfrister returnerar alla funktioner under samlingsobjektet (för att skapa, läsa, Ersätt och borttagning av dokument och bifogade filer) ett booleskt värde som representerar om att åtgärden ska slutföras. Om det här värdet är FALSKT, är en indikation på att tidsgränsen upphör snart att gälla och att proceduren måste slutföra körningen.  Åtgärder i kö före den första typen Lagringsåtgärden garanteras att slutföra om den lagrade proceduren har slutförts i tid och inte kö inga fler begäranden.  

JavaScript-funktioner är också avgränsas i resursförbrukning. Cosmos DB reserverar genomströmning per samling baserat på ett databaskonto etablerade storlek. Genomströmning uttrycks som ett normaliserat enhet av CPU, minne och i/o-förbrukning kallas frågeenheter eller RUs. JavaScript-funktioner kan användaren använda ett stort antal RUs inom en kort tid och få hastighet begränsad om mängdens gränsen har nåtts. Resurs-intensiva lagrade procedurer kan även placeras i karantän för att säkerställa tillgängligheten för primitiva databasåtgärder.  

### <a name="example-bulk-importing-data-into-a-database-program"></a>Exempel: Massredigera importera data till ett databasprogram
Nedan visas ett exempel på en lagrad procedur som skrivs till massimport dokument till en samling. Observera hur den lagrade proceduren hanterar avgränsas körning genom att kontrollera booleskt returvärde från createDocument, och använder sedan antal dokument som infogas i varje anrop av den lagrade proceduren för att spåra och återuppta förlopp i batchar.

    function bulkImport(docs) {
        var collection = getContext().getCollection();
        var collectionLink = collection.getSelfLink();

        // The count of imported docs, also used as current doc index.
        var count = 0;

        // Validate input.
        if (!docs) throw new Error("The array is undefined or null.");

        var docsLength = docs.length;
        if (docsLength == 0) {
            getContext().getResponse().setBody(0);
        }

        // Call the create API to create a document.
        tryCreate(docs[count], callback);

        // Note that there are 2 exit conditions:
        // 1) The createDocument request was not accepted. 
        //    In this case the callback will not be called, we just call setBody and we are done.
        // 2) The callback was called docs.length times.
        //    In this case all documents were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
        function tryCreate(doc, callback) {
            var isAccepted = collection.createDocument(collectionLink, doc, callback);

            // If the request was accepted, callback will be called.
            // Otherwise report current count back to the client, 
            // which will call the script again with remaining set of docs.
            if (!isAccepted) getContext().getResponse().setBody(count);
        }

        // This is called when collection.createDocument is done in order to process the result.
        function callback(err, doc, options) {
            if (err) throw err;

            // One more document has been inserted, increment the count.
            count++;

            if (count >= docsLength) {
                // If we created all documents, we are done. Just set the response.
                getContext().getResponse().setBody(count);
            } else {
                // Create next document.
                tryCreate(docs[count], callback);
            }
        }
    }

## <a id="trigger"></a> Databasutlösare
### <a name="database-pre-triggers"></a>Före databasutlösare
Cosmos DB innehåller utlösare som körs eller som utlöses av en åtgärd på ett dokument. Du kan till exempel ange en före utlösare när du skapar ett dokument – före utlösaren ska köras innan dokumentet skapas. I följande exempel visas hur före utlösare kan användas för att verifiera egenskaperna för ett dokument som har skapats:

    var validateDocumentContentsTrigger = {
        id: "validateDocumentContents",
        serverScript: function validate() {
            var context = getContext();
            var request = context.getRequest();

            // document to be created in the current operation
            var documentToCreate = request.getBody();

            // validate properties
            if (!("timestamp" in documentToCreate)) {
                var ts = new Date();
                documentToCreate["my timestamp"] = ts.getTime();
            }

            // update the document that will be created
            request.setBody(documentToCreate);
        },
        triggerType: TriggerType.Pre,
        triggerOperation: TriggerOperation.Create
    }


Och motsvarande Node.js klientsidan Registreringskod för utlösaren:

    // register pre-trigger
    client.createTriggerAsync(collection.self, validateDocumentContentsTrigger)
        .then(function (response) {
            console.log("Created", response.resource);
            var docToCreate = {
                id: "DocWithTrigger",
                event: "Error",
                source: "Network outage"
            };

            // run trigger while creating above document 
            var options = { preTriggerInclude: "validateDocumentContents" };

            return client.createDocumentAsync(collection.self,
                  docToCreate, options);
        }, function (error) {
            console.log("Error", error);
        })
    .then(function (response) {
        console.log(response.resource); // document with timestamp property added
    }, function (error) {
        console.log("Error", error);
    });


Före utlösare kan inte ha indataparametrar. Request-objektet kan användas för att manipulera begärandemeddelandet som associeras med operationen. Här före utlösaren körs med skapandet av ett dokument och brödtext i begäran innehåller ett dokument ska skapas i JSON-format.   

När utlösare är registrerade anger användare vilka åtgärder som inte har. Den här utlösaren har skapats med TriggerOperation.Create, vilket innebär följande inte tillåts.

    var options = { preTriggerInclude: "validateDocumentContents" };

    client.replaceDocumentAsync(docToReplace.self,
                  newDocBody, options)
    .then(function (response) {
        console.log(response.resource);
    }, function (error) {
        console.log("Error", error);
    });

    // Fails, can’t use a create trigger in a replace operation

### <a name="database-post-triggers"></a>Efter databasutlösare
Efter utlösare som före utlösare associeras med en åtgärd på ett dokument och får inte indataparametrar. De körs **när** åtgärden har slutförts och har åtkomst till svarsmeddelandet som skickas till klienten.   

I följande exempel visas efter utlösare i praktiken:

    var updateMetadataTrigger = {
        id: "updateMetadata",
        serverScript: function updateMetadata() {
            var context = getContext();
            var collection = context.getCollection();
            var response = context.getResponse();

            // document that was created
            var createdDocument = response.getBody();

            // query for metadata document
            var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
            var accept = collection.queryDocuments(collection.getSelfLink(), filterQuery,
                updateMetadataCallback);
            if(!accept) throw "Unable to update metadata, abort";

            function updateMetadataCallback(err, documents, responseOptions) {
                if(err) throw new Error("Error" + err.message);
                         if(documents.length != 1) throw 'Unable to find metadata document';

                         var metadataDocument = documents[0];

                         // update metadata
                         metadataDocument.createdDocuments += 1;
                         metadataDocument.createdNames += " " + createdDocument.id;
                         var accept = collection.replaceDocument(metadataDocument._self,
                               metadataDocument, function(err, docReplaced) {
                                      if(err) throw "Unable to update metadata, abort";
                               });
                         if(!accept) throw "Unable to update metadata, abort";
                         return;                    
            }                                                                                            
        },
        triggerType: TriggerType.Post,
        triggerOperation: TriggerOperation.All
    }


Utlösaren kan registreras som visas i följande exempel.

    // register post-trigger
    client.createTriggerAsync('dbs/testdb/colls/testColl', updateMetadataTrigger)
        .then(function(createdTrigger) { 
            var docToCreate = { 
                name: "artist_profile_1023",
                artist: "The Band",
                albums: ["Hellujah", "Rotators", "Spinning Top"]
            };

            // run trigger while creating above document 
            var options = { postTriggerInclude: "updateMetadata" };

            return client.createDocumentAsync(collection.self,
                  docToCreate, options);
        }, function(error) {
            console.log("Error" , error);
        })
    .then(function(response) {
        console.log(response.resource); 
    }, function(error) {
        console.log("Error" , error);
    });


Den här utlösaren frågar för Metadatadokumentet och uppdaterar med information om det nyligen skapade dokumentet.  

En sak som är viktigt att notera är den **transaktionella** körning av utlösare i Cosmos-databasen. Den här efter utlösaren körs som en del av samma transaktion som skapas i det ursprungliga dokumentet. Därför, om vi utlöser ett undantag från efter utlösaren (t.ex. Om det inte att uppdatera metadata dokument) hela transaktionen misslyckas och återställas. Inget dokument kommer att skapas och ett undantag ska returneras.  

## <a id="udf"></a>Användardefinierade funktioner
Användardefinierade funktioner (UDF) används för att utöka Azure Cosmos-Databasens SQL-frågegrammatik för språk och implementera anpassad affärslogik. De kan endast anropas från inuti frågor. De har inte åtkomst till context-objektet och är avsedda att användas som endast beräkning JavaScript. Därför kan du köra UDF: er på sekundära repliker för tjänsten Cosmos DB.  

I följande exempel skapas en användardefinierad funktion för att beräkna skatter baserat på priser för olika intäkter hakparenteser och sedan används i en fråga för att hitta alla personer som betald mer än 20 000 i skatter.

    var taxUdf = {
        id: "tax",
        serverScript: function tax(income) {

            if(income == undefined) 
                throw 'no input';

            if (income < 1000) 
                return income * 0.1;
            else if (income < 10000) 
                return income * 0.2;
            else
                return income * 0.4;
        }
    }


UDF-filen kan därefter användas i frågor som i följande exempel:

    // register UDF
    client.createUserDefinedFunctionAsync('dbs/testdb/colls/testColl', taxUdf)
        .then(function(response) { 
            console.log("Created", response.resource);

            var query = 'SELECT * FROM TaxPayers t WHERE udf.tax(t.income) > 20000'; 
            return client.queryDocuments('dbs/testdb/colls/testColl',
                   query).toArrayAsync();
        }, function(error) {
            console.log("Error" , error);
        })
    .then(function(response) {
        var documents = response.feed;
        console.log(response.resource); 
    }, function(error) {
        console.log("Error" , error);
    });

## <a name="javascript-language-integrated-query-api"></a>JavaScript språkintegrerade frågan API
Förutom att utfärda frågor med Azure Cosmos DB SQL-grammatik kan serversidan SDK du utföra optimerade frågor en flytande JavaScript-gränssnittet utan kännedom om SQL. Frågan JavaScript API kan du skapa frågor via programmering genom att skicka predikat funktioner till chainable funktionen anropas med en bekant för ECMAScript5's matris built-ins och populära JavaScript-bibliotek som lodash syntax. Frågor tolkas av JavaScript-körning för att köra ett effektivt sätt med hjälp av Azure Cosmos DB.

> [!NOTE]
> `__` (double understreck) är ett alias till `getContext().getCollection()`.
> <br/>
> Med andra ord kan du använda `__` eller `getContext().getCollection()` åtkomst till JavaScript-frågan API.
> 
> 

Funktioner som stöds är:

<ul>
<li>
<b>... chain(). värdet ([återanrop] [, alternativ])</b>
<ul>
<li>
Startar en länkad anrop som måste avslutas med value().
</li>
</ul>
</li>
<li>
<b>filter (predicateFunction [, alternativ] [, motringning])</b>
<ul>
<li>
Filtrerar indata med hjälp av ett predikat funktion som returnerar SANT/FALSKT för att filtrera inkommande dokument in/ut till den resulterande uppsättningen. Detta fungerar liknar en WHERE-satsen i SQL.
</li>
</ul>
</li>
<li>
<b>karta (transformationFunction [, alternativ] [, motringning])</b>
<ul>
<li>
Gäller en projektion som anges en transformation-funktion som mappar varje inkommande objekt till en JavaScript-objekt eller ett värde. Detta fungerar liknar en SELECT-satsen i SQL.
</li>
</ul>
</li>
<li>
<b>pluck ([egenskapsnamn] [, alternativ] [, motringning])</b>
<ul>
<li>
Det här är en genväg till en karta som hämtar värdet för en enskild egenskap från varje inkommande objekt.
</li>
</ul>
</li>
<li>
<b>förenkla ([isShallow] [, alternativ] [, motringning])</b>
<ul>
<li>
Kombinerar och förenklas matriser från varje inkommande objekt i till en matris. Detta fungerar liknar SelectMany i LINQ.
</li>
</ul>
</li>
<li>
<b>sortBy ([predicate] [, alternativ] [, motringning])</b>
<ul>
<li>
Skapa en ny uppsättning dokument genom att sortera dokument i Indatadokumentet dataströmmen i stigande ordning med hjälp av angivna predikatet. Detta fungerar liknar en ORDER BY-satsen i SQL.
</li>
</ul>
</li>
<li>
<b>sortByDescending ([predicate] [, alternativ] [, motringning])</b>
<ul>
<li>
Skapa en ny uppsättning dokument genom att sortera dokument i Indatadokumentet dataströmmen i fallande ordning med hjälp av angivna predikatet. Detta fungerar liknar en x DESC ORDER BY-satsen i SQL.
</li>
</ul>
</li>
</ul>


När ingår i predikatet och/eller selector-funktioner, hämta följande JavaScript-konstruktioner automatiskt optimerats för att köras direkt på Azure Cosmos DB index:

* Enkel operatörer: = + - * / % | ^ &amp; == != === !=== &lt; &gt; &lt;= &gt;= || &amp;&amp; &lt;&lt; &gt;&gt; &gt;&gt;&gt;! ~
* Literaler, inklusive objektet literal: {}
* varians returnerade

Inte hämta optimerad följande JavaScript-konstruktioner för Azure Cosmos DB index:

* Åtkomstkontrollflödet (till exempel om, medan)
* Funktionsanrop

Mer information finns i [serversidan JSDocs](http://azure.github.io/azure-documentdb-js-server/).

### <a name="example-write-a-stored-procedure-using-the-javascript-query-api"></a>Exempel: Skriv en lagrad procedur med hjälp av JavaScript-fråga API
Följande kodexempel är ett exempel på hur JavaScript frågan API kan användas i kontexten för en lagrad procedur. Den lagrade proceduren infogar ett dokument som anges av en indataparameter och uppdaterar en metadata dokument, med hjälp av den `__.filter()` metod med minstorlek maxSize och totalSize baserat på inkommande dokumentet egenskapen.

    /**
     * Insert actual doc and update metadata doc: minSize, maxSize, totalSize based on doc.size.
     */
    function insertDocumentAndUpdateMetadata(doc) {
      // HTTP error codes sent to our callback funciton by DocDB server.
      var ErrorCode = {
        RETRY_WITH: 449,
      }

      var isAccepted = __.createDocument(__.getSelfLink(), doc, {}, function(err, doc, options) {
        if (err) throw err;

        // Check the doc (ignore docs with invalid/zero size and metaDoc itself) and call updateMetadata.
        if (!doc.isMetadata && doc.size > 0) {
          // Get the meta document. We keep it in the same collection. it's the only doc that has .isMetadata = true.
          var result = __.filter(function(x) {
            return x.isMetadata === true
          }, function(err, feed, options) {
            if (err) throw err;

            // We assume that metadata doc was pre-created and must exist when this script is called.
            if (!feed || !feed.length) throw new Error("Failed to find the metadata document.");

            // The metadata document.
            var metaDoc = feed[0];

            // Update metaDoc.minSize:
            // for 1st document use doc.Size, for all the rest see if it's less than last min.
            if (metaDoc.minSize == 0) metaDoc.minSize = doc.size;
            else metaDoc.minSize = Math.min(metaDoc.minSize, doc.size);

            // Update metaDoc.maxSize.
            metaDoc.maxSize = Math.max(metaDoc.maxSize, doc.size);

            // Update metaDoc.totalSize.
            metaDoc.totalSize += doc.size;

            // Update/replace the metadata document in the store.
            var isAccepted = __.replaceDocument(metaDoc._self, metaDoc, function(err) {
              if (err) throw err;
              // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again 
              //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
              //       We have to take care of that on the client side.
            });
            if (!isAccepted) throw new Error("replaceDocument(metaDoc) returned false.");
          });
          if (!result.isAccepted) throw new Error("filter for metaDoc returned false.");
        }
      });
      if (!isAccepted) throw new Error("createDocument(actual doc) returned false.");
    }

## <a name="sql-to-javascript-cheat-sheet"></a>SQL till Javascript-fusklapp
I följande tabell visas olika SQL-frågor och motsvarande JavaScript-frågor.

Som med SQL-frågor, dokumentera egenskapen nycklar (exempelvis `doc.id`) är skiftlägeskänsliga.

|SQL| JavaScript-fråga API|Beskrivningen nedan|
|---|---|---|
|VÄLJ *<br>FRÅN dokument| __.Map(Function(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;returnera doc;<br>});|1|
|Välj docs.id, docs.message som ignorerad, docs.actions <br>FRÅN dokument|__.Map(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;returnera {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;meddelande: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Actions:doc.Actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|2|
|VÄLJ *<br>FRÅN dokument<br>VAR docs.id="X998_Y998”|__.filter(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;returnera doc.id === ”X998_Y998”;<br>});|3|
|VÄLJ *<br>FRÅN dokument<br>VAR ARRAY_CONTAINS (dokument. Taggar 123)|__.filter(function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;return x.Tags && x.Tags.indexOf(123) > -1;<br>});|4|
|Välj docs.id, docs.message som ignorerad<br>FRÅN dokument<br>VAR docs.id="X998_Y998”|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;returnera doc.id === ”X998_Y998”;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.Map(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;returnera {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;meddelande: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.value();|5|
|SELECT VALUE-tagg<br>FRÅN dokument<br>Anslut tagg i dokumenten. Taggar<br>ORDER BY docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;returnera dokument. Taggar & & Array.isArray (doc. Taggar).<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;returnera doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.value()|6|

Följande beskrivningar förklarar varje fråga i tabellen ovan.
1. Leder till att alla dokument (sidbrytning med fortsättningstoken) som är.
2. Projekt-id, meddelande (ett alias till ignorerad) och åtgärd från alla dokument.
3. Frågor för dokument med predikatet: id = ”X998_Y998”.
4. Frågor för dokument som har en property-taggar och taggarna är en matris som innehåller värdet 123.
5. Frågor för dokument med ett predikat, id = ”X998_Y998” och sedan projekt-id och meddelanden (ett alias till meddelande).
6. Filter för dokument som har en matrisegenskap, taggar och sorterar dokumenten _ts tidsstämpelsegenskapen system och projekt + förenklas taggar matrisen.


## <a name="runtime-support"></a>Stöd för körning
Azure Cosmos DB [JavaScript server side API](http://azure.github.io/azure-documentdb-js-server/) ger stöd för de flesta av vanlig JavaScript språkfunktioner standardiserade av [ECMA-262](http://www.ecma-international.org/publications/standards/Ecma-262.htm).

### <a name="security"></a>Säkerhet
JavaScript-lagrade procedurer och utlösare är i begränsat läge så att effekterna av ett skript inte läcker till en annan utan att gå via ögonblicksbildisolering för transaktionen på databasnivå. Runtime-miljöer pool men rengöras från kontexten efter varje körning. De är garanterat därför vara säker på alla oavsiktliga sidoeffekter från varandra.

### <a name="pre-compilation"></a>Före kompileringen
Lagrade procedurer, utlösare och UDF: er är implicit förkompilerade till formatet byte kod för att undvika kompilering kostnaden vid tidpunkten för varje skript-anrop. Detta säkerställer att anrop för lagrade procedurer är snabb och har en låg storleken.

## <a name="client-sdk-support"></a>Stöd för klient-SDK
Utöver Azure Cosmos DB [Node.js](sql-api-sdk-node.md) API, Azure Cosmos DB har [.NET](sql-api-sdk-dotnet.md), [.NET Core](sql-api-sdk-dotnet-core.md), [Java](sql-api-sdk-java.md), [JavaScript ](http://azure.github.io/azure-documentdb-js/), och [Python SDK](sql-api-sdk-python.md) för SQL-API. Lagrade procedurer, utlösare och UDF: er kan skapas och köras med hjälp av dessa SDK: er samt. I följande exempel visas hur du skapar och köra en lagrad procedur med hjälp av .NET-klienten. Observera hur .NET-typer som skickas till den lagrade proceduren som JSON och läsa tillbaka.

    var markAntiquesSproc = new StoredProcedure
    {
        Id = "ValidateDocumentAge",
        Body = @"
                function(docToCreate, antiqueYear) {
                    var collection = getContext().getCollection();    
                    var response = getContext().getResponse();    

                    if(docToCreate.Year != undefined && docToCreate.Year < antiqueYear){
                        docToCreate.antique = true;
                    }

                    collection.createDocument(collection.getSelfLink(), docToCreate, {}, 
                        function(err, docCreated, options) { 
                            if(err) throw new Error('Error while creating document: ' + err.message);                              
                            if(options.maxCollectionSizeInMb == 0) throw 'max collection size not found'; 
                            response.setBody(docCreated);
                    });
             }"
    };

    // register stored procedure
    StoredProcedure createdStoredProcedure = await client.CreateStoredProcedureAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), markAntiquesSproc);
    dynamic document = new Document() { Id = "Borges_112" };
    document.Title = "Aleph";
    document.Year = 1949;

    // execute stored procedure
    Document createdDocument = await client.ExecuteStoredProcedureAsync<Document>(UriFactory.CreateStoredProcedureUri("db", "coll", "ValidateDocumentAge"), document, 1920);


Det här exemplet visas hur du använder den [SQL .NET API](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet) att skapa en före utlösare och skapa ett dokument med utlösaren aktiveras. 

    Trigger preTrigger = new Trigger()
    {
        Id = "CapitalizeName",
        Body = @"function() {
            var item = getContext().getRequest().getBody();
            item.id = item.id.toUpperCase();
            getContext().getRequest().setBody(item);
        }",
        TriggerOperation = TriggerOperation.Create,
        TriggerType = TriggerType.Pre
    };

    Document createdItem = await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), new Document { Id = "documentdb" },
        new RequestOptions
        {
            PreTriggerInclude = new List<string> { "CapitalizeName" },
        });


Och följande exempel visar hur du skapar en användardefinierad funktion (UDF) och använda den i en [SQL-frågan](sql-api-sql-query.md).

    UserDefinedFunction function = new UserDefinedFunction()
    {
        Id = "LOWER",
        Body = @"function(input) 
        {
            return input.toLowerCase();
        }"
    };

    foreach (Book book in client.CreateDocumentQuery(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        "SELECT * FROM Books b WHERE udf.LOWER(b.Title) = 'war and peace'"))
    {
        Console.WriteLine("Read {0} from query", book);
    }

## <a name="rest-api"></a>REST-API
Alla Azure DB som Cosmos-åtgärder kan utföras på ett RESTful sätt. Lagrade procedurer, utlösare och användardefinierade funktioner kan registreras i en samling med hjälp av HTTP POST. Följande är ett exempel på hur du registrerar en lagrad procedur:

    POST https://<url>/sprocs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT


    var x = {
      "name": "createAndAddProperty",
      "body": function (docToCreate, addedPropertyName, addedPropertyValue) {
                var collectionManager = getContext().getCollection();
                collectionManager.createDocument(
                    collectionManager.getSelfLink(),
                    docToCreate,
                    function(err, docCreated) {
                      if(err) throw new Error('Error:  ' + err.message);
                      docCreated[addedPropertyName] = addedPropertyValue;
                      getContext().getResponse().setBody(docCreated);
                    });
            }
    }


Den lagrade proceduren registreras genom att köra en POST-begäran mot den URI dbs/testdb/colls/testColl/sprocs med meddelandetexten innehåller den lagrade proceduren att skapa. Utlösare och UDF: er kan registreras på samma sätt genom att utfärda ett INLÄGG mot /triggers och /udfs respektive.
Det här lagrade proceduren kan sedan köras genom att utfärda en POST-begäran mot dess resurslänken:

    POST https://<url>/sprocs/<sproc> HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:20 GMT


    [ { "name": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]


Här kan skickas indata till den lagrade proceduren i begärandetexten. Observera att indata skickas som ett JSON-matris av indataparametrar. Den lagrade proceduren använder den första indata som ett dokument som är en brödtext för svar. Vi tar emot svaret är följande:

    HTTP/1.1 200 OK

    { 
      name: 'TestDocument',
      book: ‘Autumn of the Patriarch’,
      id: ‘V7tQANV3rAkDAAAAAAAAAA==‘,
      ts: 1407830727,
      self: ‘dbs/V7tQAA==/colls/V7tQANV3rAk=/docs/V7tQANV3rAkDAAAAAAAAAA==/’,
      etag: ‘6c006596-0000-0000-0000-53e9cac70000’,
      attachments: ‘attachments/’,
      Price: 200
    }


Utlösare, till skillnad från lagrade procedurer kan inte köras direkt. I stället körs de som en del av en åtgärd på ett dokument. Vi kan ange utlösare kan köras med en förfrågan med en HTTP-huvuden. Följande kod visar begäran om att skapa ett dokument.

    POST https://<url>/docs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
    x-ms-documentdb-pre-trigger-include: validateDocumentContents 
    x-ms-documentdb-post-trigger-include: bookCreationPostTrigger


    {
       "name": "newDocument",
       “title”: “The Wizard of Oz”,
       “author”: “Frank Baum”,
       “pages”: 92
    }


Här anges före utlösaren ska köras med förfrågan i rubriken x-ms-documentdb-pre-trigger-include. På motsvarande sätt anges efter utlösare i rubriken x-ms-documentdb-post-trigger-include. Både före och efter utlösare kan anges för en viss begäran.

## <a name="sample-code"></a>Exempelkod
Du kan hitta mer kodexempel för serversidan (inklusive [massborttagning](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/bulkDelete.js), och [uppdatera](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples/stored-procedures/update.js)) på vår [GitHub-lagringsplatsen](https://github.com/Azure/azure-documentdb-js-server/tree/master/samples).

Om du vill dela dina helt otroligt lagrade proceduren? Skicka oss en pull-begäran! 

## <a name="next-steps"></a>Nästa steg
När du har en eller flera lagrade procedurer, utlösare och användardefinierade funktioner som har skapats kan du läsa in dem och visa dem i Azure-portalen med hjälp av Data Explorer.

Du kan också hitta följande referenser och resurser användbart i din sökväg till mer information om Azure Cosmos dB serversidan programmering:

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases)
* [JSON](http://www.json.org/) 
* [JavaScript ECMA-262](http://www.ecma-international.org/publications/standards/Ecma-262.htm)
* [Säker och bärbar databasen utökningsbarhet](http://dl.acm.org/citation.cfm?id=276339) 
* [Tjänsten inriktade Database-arkitektur](http://dl.acm.org/citation.cfm?id=1066267&coll=Portal&dl=GUIDE) 
* [Värd för .NET-körningsmiljön i Microsoft SQL server](http://dl.acm.org/citation.cfm?id=1007669)

