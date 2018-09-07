---
title: Programmering av serversidan JavaScript för Azure Cosmos DB | Microsoft Docs
description: Lär dig hur du använder Azure Cosmos DB för att skriva lagrade procedurer, databasutlösare och användardefinierade funktioner (UDF) i JavaScript. Hämta databasen programmering tips och mycket mer.
keywords: Databas utlösare, lagrad procedur, lagrad procedur, databasprogram, sproc, azure, Microsoft azure
services: cosmos-db
author: aliuy
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: andrl
ms.openlocfilehash: 3c18478fb2996178ee0b75870ce63dfc79ad4c4c
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44054731"
---
# <a name="azure-cosmos-db-server-side-programming-stored-procedures-database-triggers-and-udfs"></a>Azure Cosmos DB från serversidan programmering: lagrade procedurer, databasutlösare och UDF: er

Lär dig hur Azure Cosmos DB språkintegrerade, transaktionell körning av JavaScript kan utvecklare skriva **lagrade procedurer**, **utlösare**, och **användardefinierade funktioner (UDF)**  internt i en [ECMAScript 2015](http://www.ecma-international.org/ecma-262/6.0/) JavaScript. JavaScript-integrering kan du skriva program logik som kan levereras och köras direkt i partitioner för lagring av databasen. 

I den här artikeln får du lära dig besvara följande frågor:  

* Hur jag skriva lagrade procedurer, utlösare och UDF med hjälp av JavaScript?
* Hur garanterar Cosmos DB ACID?
* Hur fungerar transaktioner i Cosmos DB?
* Vad är utlöser före och efter utlöser och hur skriver jag en?
* Hur jag för att registrera och köra lagrade procedurer, utlösare och UDF i ett RESTful sätt genom att använda HTTP?
* Vad Cosmos DB SDK: er är tillgängliga för att skapa och köra lagrade procedurer, utlösare och UDF: er?

## <a name="introduction-to-stored-procedure-and-udf-programming"></a>Introduktion till lagrade proceduren och UDF-programmering
Den här metoden för *”JavaScript som en modern dag T-SQL”* Frigör utvecklare av företagsprogram från komplexiteten i inmatningsfel system och tekniker för objektrelationell mappning. Det finns också ett antal inbyggda fördelar som kan användas för att skapa omfattande program:  

* **Procedurmässig Logic:** JavaScript som en övergripande programmeringsspråk, tillhandahåller en omfattande och välbekanta gränssnitt för express affärslogik. Du kan utföra komplexa sekvenser av åtgärder närmare till data.
* **Atomiska transaktioner:** Cosmos DB garanterar att databasen åtgärder som utförs i en lagrad procedur eller utlösare är atomiska. Atomiska funktionen att ett program kan kombinera relaterade åtgärder i en enskild batch så att alla lyckas eller ingen av dem lyckas. 
* **Prestanda:** det faktum att JSON är mappad till Javascript-typsystemet för språk och också är den grundläggande enheten för lagring i Cosmos DB möjliggör ett antal optimeringar som lazy materialisering av JSON-dokument i buffertpoolen och gör dem tillgängliga på begäran i koden som körs. Det finns flera prestandafördelarna som är associerade med endash affärslogik till databasen:
  
  * Batchbearbetning – utvecklare kan gruppera åtgärder som infogar och skicka dem gruppvis. Trafik Nätverksfördröjningen kostnad och store-omkostnader för att skapa separata transaktioner minskas avsevärt. 
  * Före kompilering – Cosmos DB precompiles lagrade procedurer, utlösare och användardefinierade funktioner (UDF) att undvika JavaScript-kompilering kostnaden för varje anrop. Arbetet med att skapa byte-koden för procedurmässig logiken amorteras till ett minimal värde.
  * Sekvensering – många åtgärder behöver en sidoeffekter (”utlösare”) som potentiellt omfattar att göra en eller flera sekundära store-åtgärder. Förutom Atomicitet är detta bättre när flyttas till servern. 
* **Inkapsling:** lagrade procedurer som kan användas för att gruppera affärslogik på samma plats, som har två fördelar:
  * Det lägger till ett Abstraktionslager ovanpå rådata, vilket gör att dataarkitekter att utveckla sina program oberoende av data. Det här lagret Abstraktionslager är användbar när data är utan schema, på grund av riskfyllt antaganden som kan behöva vara inbyggd i programmet om de har utan data direkt.  
  * Denna framställning kan företag skydda sina data genom att effektivisera åtkomst från skripten.  

Skapa och köra databasutlösare, lagrade procedurer och anpassade frågeoperatorer stöds via den [Azure-portalen](https://portal.azure.com), [REST API](/rest/api/cosmos-db/), [Azure DocumentDB Studio](https://github.com/mingaliu/DocumentDBStudio/releases), och [klient-SDK: er](sql-api-sdk-dotnet.md) i många plattformar, inklusive .NET, Node.js och JavaScript.

Den här självstudien används den [Node.js-SDK med frågor och löften](http://azure.github.io/azure-documentdb-node-q/) att visa syntax och användning av lagrade procedurer, utlösare och UDF: er.   

## <a name="stored-procedures"></a>Lagrade procedurer
### <a name="example-write-a-stored-procedure"></a>Exempel: Skriva en lagrad procedur
Låt oss börja med en enkel lagrad procedur som returnerar svaret ”Hello World”.

```javascript
var helloWorldStoredProc = {
    id: "helloWorld",
    serverScript: function () {
        var context = getContext();
        var response = context.getResponse();

        response.setBody("Hello, World");
    }
}
```

Lagrade procedurer har registrerats per samling och kan användas i alla dokument och bifogad fil i samlingen. Följande kodfragment visar hur du registrerar helloWorld lagrade proceduren med en samling. 


```javascript
// register the stored procedure
var createdStoredProcedure;
client.createStoredProcedureAsync('dbs/testdb/colls/testColl', helloWorldStoredProc)
    .then(function (response) {
        createdStoredProcedure = response.resource;
        console.log("Successfully created stored procedure");
    }, function (error) {
        console.log("Error", error);
    });
```


När den lagrade proceduren är registrerad kan du köra den mot samlingen och läsa resultaten tillbaka på klienten. 

```javascript
// execute the stored procedure
client.executeStoredProcedureAsync('dbs/testdb/colls/testColl/sprocs/helloWorld')
    .then(function (response) {
        console.log(response.result); // "Hello, World"
    }, function (err) {
        console.log("Error", error);
    });
```

Context-objektet ger åtkomst till alla åtgärder som kan utföras på Cosmos DB-lagring, samt åtkomst till begäranden och svar-objekt. I det här fallet kan du använda svarsobjekt för att ange brödtexten i svaret som skickades tillbaka till klienten. Mer information finns i den [Azure Cosmos DB JavaScript server SDK-dokumentation](https://azure.github.io/azure-cosmosdb-js-server/).  

Låt oss Expandera på det här exemplet och lägga till fler funktioner för databas-relaterade till den lagrade proceduren. Lagrade procedurer kan skapa, uppdatera, läsa, fråga och ta bort dokument och bifogade filer i samlingen.    

### <a name="example-write-a-stored-procedure-to-create-a-document"></a>Exempel: Skriva en lagrad procedur för att skapa ett dokument
Nästa kodfragmentet visar hur du använder context-objektet för att interagera med Cosmos DB-resurser.

```javascript
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
```


Den här lagrade proceduren tar som indata documentToCreate, brödtexten i ett dokument som ska skapas i samlingen. Alla sådana åtgärder är asynkrona och är beroende av återanrop för JavaScript-funktion. Återanropsfunktionen har två parametrar, en för felobjektet om åtgärden misslyckas och ett för objektet. I återanropet kan användare hantera undantaget eller fel. Om ett återanrop inte har angetts och det finns ett fel, genererar ett fel i Azure Cosmos DB-runtime.   

I exemplet ovan är genererar återanropet som ett fel om åtgärden misslyckades. I annat fall anger ID för det skapade dokumentet som en del av svaret till klienten. Här är hur den här lagrade proceduren körs med indataparametrar.

```javascript
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
```

Den här lagrade proceduren kan ändras för att ta en matris med dokumentet organ som indata och skapa alla i samma lagrad procedur-körning i stället för flera begäranden om att skapa dem individuellt. Den här lagrade proceduren kan användas för att implementera en effektiv bulk-Importverktyget för Cosmos DB (beskrivs senare i den här självstudien).   

Exemplet som beskrivs lärt dig att använda lagrade procedurer. Därefter lär du dig att utlösare och användardefinierade funktioner (UDF) senare under kursen.

### <a name="known-issues"></a>Kända problem

När du definierar en lagrad procedur med hjälp av Azure-portalen, skickas alltid indataparametrar som en sträng till den lagrade proceduren. Även om du skickar en matris med strängar som indata, konverteras till sträng matrisen och skickas till den lagrade proceduren. Lösa det här problemet kan du definiera en funktion i din lagrad procedur som en matris strängen ska parsas. Följande kod är ett exempel som en matris strängen ska parsas: 

```javascript
function sample(arr) {
    if (typeof arr === "string") arr = JSON.parse(arr);
    
    arr.forEach(function(a) {
        // do something here
        console.log(a);
    });
}
```

## <a name="database-program-transactions"></a>Programmet databastransaktioner
Transaktionen i en typisk databasen kan definieras som en sekvens med åtgärder som utförs som en enda logisk enhet för arbete. Varje transaktion innehåller **ACID-garantier**. ACID är en välkänd förkortning för fyra egenskaper - Atomicitet, konsekvens, isolering och tillförlitlighet.  

Kort, Atomicitet garanterar att alla arbete i en transaktion behandlas som en enda enhet där antingen allt det strävar eller ingen. Konsekvens ser till att data alltid är i interna fullgott över transaktioner. Isolering garanterar att inga två transaktioner störa varandra – Allmänt, mest kommersiella system ger flera isoleringsnivåer som kan användas baserat på programmets behov. Hållbarhet säkerställer att alla ändringar genomförs i databasen alltid kommer att finnas.   

I Cosmos DB finns JavaScript i samma minnesutrymme som databas. Därför begäranden som görs i lagrade procedurer och utlösare som körs i samma definitionsområde till en databas-session. Den här funktionen gör det möjligt för Cosmos DB för att garantera ACID för alla åtgärder som ingår i en lagrad procedur/utlösare. Överväg följande lagrade Procedurdefinition:

```javascript
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
```

Den här lagrade proceduren använder transaktioner inom en spelappen trade objekt mellan två spelare i en enda åtgärd. Den lagrade proceduren försöker läsa två dokument varje motsvarar player-ID: N skickas som ett argument. Om båda player-dokumenten finns, uppdateras den lagrade proceduren dokumenten genom att byta sina objekt. Om fel uppstår på vägen, utlöser en JavaScript-undantag som implicit avbryter transaktionen.

Om samlingen den lagrade proceduren har registrerats mot är en samling med en partition och transaktionen är begränsad till alla dokument i samlingen. Om samlingen är partitionerad utförs lagrade procedurer i transaktionsomfånget för en enda partitionsnyckel. Varje lagrade procedurkörning innehålla ett partitionsnyckelvärde som motsvarar omfånget transaktionen måste köras under. Mer information finns i [Azure Cosmos DB partitionering](partition-data.md).

### <a name="commit-and-rollback"></a>Commit och rollback
Transaktioner är djupt och internt integrerade i Cosmos DB: s JavaScript-programmeringsmodell. I en JavaScript-funktion omsluts alla åtgärder automatiskt under en enda transaktion. Om JavaScript har slutförts utan några undantag, genomförs åtgärderna i databasen. I praktiken är ”BEGIN TRANSACTION” och ”COMMIT TRANSACTION”-instruktioner i relationsdatabaser implicit i Cosmos DB.  

Om det finns några undantag som sprids från skriptet, återställs Cosmos DB: s JavaScript-körning hela transaktionen. I det tidigare exemplet visas undantagsfel motsvarar effektivt en ”ROLLBACK TRANSACTION” i Cosmos DB.

### <a name="data-consistency"></a>Datakonsekvens
Lagrade procedurer och utlösare körs alltid på den primära repliken av Azure Cosmos DB-behållare. Detta garanterar att läsningar från inuti lagrade procedurer erbjudandet stark konsekvens. Frågor med användardefinierade funktioner kan utföras på primärt eller en sekundär replik, men du se till att uppfylla den begärda konsekvensnivån genom att välja lämpliga repliken.

## <a name="bounded-execution"></a>Begränsad körning
Alla Cosmos DB-åtgärder måste slutföras inom den angivna servern timeout varaktighet för begäran. Den här begränsningen gäller även för JavaScript-funktioner (lagrade procedurer, utlösare och användardefinierade funktioner). Om en åtgärd inte slutförs med tidsgränsen, återställs transaktionen. JavaScript-funktioner måste slutförs inom tidsgränsen eller implementera en fortsättning-baserad modell för att batch/återuppta körningen.  

För att förenkla utvecklingen av lagrade procedurer och utlösare för att hantera tidsfrister, alla funktioner under samlingsobjektet (för att skapa, läsa, ersätta och ta bort dokument och bifogade filer) returnerar ett booleskt värde som representerar om åtgärden Slutför. Om det här värdet är FALSKT, är det en indikation på att tidsgränsen är upphör snart att gälla och att proceduren måste avsluta körningen.  Åtgärder i kö före den första värde store-åtgärden garanterat slutföra om den lagrade proceduren har slutförts i tid och inte köa inga fler begäranden.  

JavaScript-funktioner är också bundet resursförbrukning. Cosmos DB reserverar dataflöde per samling eller en uppsättning behållare. Dataflöde uttrycks som en normaliserad enhet för processor, minne och IO-förbrukning kallas begäransenheter eller ru: er. JavaScript-funktioner kan användaren använda ett stort antal ru: er inom en kort tid och kan få rate-limited om samlingens gränsen har nåtts. Kan också vara i karantän resurskrävande lagrade procedurer för att säkerställa tillgängligheten för primitiva databasåtgärder.  

### <a name="example-bulk-importing-data-into-a-database-program"></a>Exempel: Bulk import av data till ett databasprogram
Nedan visas ett exempel på en lagrad procedur som skrivs till massimport dokument i en samling. Observera hur den lagrade proceduren hanterar begränsad körning genom att markera Boolean returvärde från createDocument, och använder sedan antalet dokument som infogades i varje anrop av den lagrade proceduren för att spåra och återuppta förlopp i batchar.

```javascript
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
```

## <a id="trigger"></a> Databasutlösare
### <a name="database-pre-triggers"></a>Före databasutlösare
Cosmos DB innehåller utlösare som körs eller som utlöses av en åtgärd på ett dokument. Du kan till exempel ange en före utlösare när du skapar ett dokument – den här före utlösaren ska köras innan dokumentet har skapats. I följande exempel visas hur före utlösare kan användas för att verifiera egenskaperna för ett dokument som håller på att skapas:

```javascript
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
```

Och den motsvarande Node.js på klientsidan registreringskoden för utlösaren:

```javascript
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
```

Före utlösare kan inte ha någon indataparametrar. Begäranobjektet kan användas för att manipulera meddelandet med begäran som är associerade med åtgärden. Här kan före utlösaren körs med att skapa ett dokument, och meddelandetexten begäran innehåller dokumentet som ska skapas i JSON-format.   

När utlösare är registrerade ange användare vilka åtgärder som den kan köras med. Den här utlösaren har skapats med TriggerOperation.Create, vilket innebär att använda utlösaren i en ersättningsåtgärden som visas i följande kod inte är tillåtet.

```javascript
var options = { preTriggerInclude: "validateDocumentContents" };

client.replaceDocumentAsync(docToReplace.self,
              newDocBody, options)
.then(function (response) {
    console.log(response.resource);
}, function (error) {
    console.log("Error", error);
});

// Fails, can’t use a create trigger in a replace operation
```
### <a name="database-post-triggers"></a>Efter databasutlösare
Efter utlösare, som tidigare utlösare associeras med en åtgärd på ett dokument och tar inte några indataparametrar. De körs **när** åtgärden har slutförts och har åtkomst till svarsmeddelandet som skickas till klienten.   

I följande exempel visas efter utlösare i praktiken:
```javascript
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

```
Utlösaren kan registreras som visas i följande exempel.
```javascript
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
```

Den här utlösaren frågar efter Metadatadokumentet och uppdaterar den med information om det nyligen skapade dokumentet.  

En sak som är viktigt att notera är den **transaktionell** körning av utlösare i Cosmos DB. Den här efter utlösaren körs som en del av samma transaktion för att skapa det ursprungliga dokumentet. Därför, om du utlöser ett undantag från efter utlösaren (till exempel om det inte går att uppdatera metadatadokument) hela transaktionen misslyckas och återställas. Inget dokument kommer att skapas och ett undantag returneras.  

## <a id="udf"></a>Användardefinierade funktioner
Användardefinierade funktioner (UDF) används för att utöka Azure Cosmos DB SQL-frågegrammatik för språk och implementera anpassad affärslogik. De kan bara anropas från inuti frågor. De har inte åtkomst till context-objektet och är avsedda att användas som endast beräkning JavaScript. Användardefinierade funktioner kan därför köras på sekundära repliker för Cosmos DB-tjänsten.  

I följande exempel skapar en UDF för att beräkna inkomstskatt utifrån för olika inkomst hakparenteser och sedan använder den i en fråga för att hitta alla personer som betalats mer än 20 000 i skatter.

```javascript
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
```

En användardefinierad funktion kan därefter användas i frågor som i följande exempel:

```javascript
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
```

## <a name="javascript-language-integrated-query-api"></a>JavaScript språkintegrerade frågorna API
Förutom att utfärda frågor med Azure Cosmos DB SQL-grammatik kan serversidan SDK du utföra optimerade frågor med en fluent JavaScript-gränssnitt utan att SQL. Frågan JavaScript API kan du programmässigt skapa frågor genom att skicka predikat funktioner i chainable funktionen anropas med en syntax som är bekanta dig av ECMAScript5's matris built-ins och populära JavaScript-bibliotek som Lodash. Frågor parsas av JavaScript-körning som ska köras effektivt med hjälp av Azure Cosmos DB.

> [!NOTE]
> `__` (double-understreck) är ett alias till `getContext().getCollection()`.
> <br/>
> Med andra ord kan du använda `__` eller `getContext().getCollection()` till frågan JavaScript API.
> 
> 

Funktioner som stöds är:

<ul>
<li>
<b>... chain(). värde ([återanrop] [, alternativ])</b>
<ul>
<li>
Startar ett länkat anrop som måste avslutas med value().
</li>
</ul>
</li>
<li>
<b>filter (predicateFunction [, alternativ] [, återanrop])</b>
<ul>
<li>
Filtrerar indata med hjälp av en predikat funktion som returnerar SANT/FALSKT för att filtrera inkommande dokument in/ut till den resulterande uppsättningen. Den här funktionen fungerar ungefär som en WHERE-satsen i SQL.
</li>
</ul>
</li>
<li>
<b>karta (transformationFunction [, alternativ] [, återanrop])</b>
<ul>
<li>
Gäller en projektion får en transformation-funktion som mappar varje inkommande objekt till ett JavaScript-objekt eller ett värde. Den här funktionen fungerar ungefär som en SELECT-satsen i SQL.
</li>
</ul>
</li>
<li>
<b>pluck ([egenskapsnamn] [, alternativ] [, återanrop])</b>
<ul>
<li>
Det här är en genväg till en karta som extraherar värdet för en enskild egenskap från varje inkommande objekt.
</li>
</ul>
</li>
<li>
<b>förenkla ([isShallow] [, alternativ] [, återanrop])</b>
<ul>
<li>
Kombinerar och plattar ut matriser från varje inkommande objekt i att en enskild matris. Den här funktionen fungerar ungefär som SelectMany i LINQ.
</li>
</ul>
</li>
<li>
<b>sortBy ([predikat] [, alternativ] [, återanrop])</b>
<ul>
<li>
Skapa en ny uppsättning dokument genom att sortera dokument i strömmen indata-dokument i stigande ordning med hjälp av angivna predikatet. Den här funktionen fungerar ungefär som en ORDER BY-satsen i SQL.
</li>
</ul>
</li>
<li>
<b>sortByDescending ([predikat] [, alternativ] [, återanrop])</b>
<ul>
<li>
Skapa en ny uppsättning dokument genom att sortera dokument i strömmen indata-dokument i fallande ordning med hjälp av angivna predikatet. Den här funktionen fungerar ungefär som en x DESC ORDER BY-satsen i SQL.
</li>
</ul>
</li>
</ul>


När ingår i predikatet och/eller väljare funktioner, hämta följande JavaScript-konstruktioner automatiskt optimerade för att köra direkt i Azure Cosmos DB index:

* Enkel operatorer: = + - * / % | ^ &amp; == != === !=== &lt; &gt; &lt;= &gt;= || &amp;&amp; &lt;&lt; &gt;&gt; &gt;&gt;&gt;! ~
* Literaler, inklusive literal objektet: {}
* var, gå tillbaka

Följande JavaScript-konstruktioner inte hämta har optimerats för Azure Cosmos DB index:

* Kontrollera flödet (till exempel om efter, medan)
* Funktionsanrop

Mer information finns i den [serversidan JSDocs](https://azure.github.io/azure-cosmosdb-js-server/).

### <a name="example-write-a-stored-procedure-using-the-javascript-query-api"></a>Exempel: Skriva en lagrad procedur med fråga JavaScript API
Följande kodexempel är ett exempel på hur JavaScript fråge-API kan användas i kontexten för en lagrad procedur. Den lagrade proceduren infogar ett dokument som anges av en indataparameter och en programuppdateringsmetadata dokument, med hjälp av den `__.filter()` metoden med minstorlek, maxSize och totalSize baserat på egenskapen för inkommande dokumentet.

```javascript
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
```

## <a name="sql-to-javascript-cheat-sheet"></a>SQL till facit för Javascript
I följande tabell visas olika SQL-frågor och motsvarande JavaScript-frågor.

Som med SQL-frågor, dokumentera egenskapsnycklarna (till exempel `doc.id`) är skiftlägeskänsliga.

|SQL| Fråga JavaScript API|Beskrivning nedan|
|---|---|---|
|VÄLJ *<br>FRÅN docs| __.Map(Function(doc) { <br>&nbsp;&nbsp;&nbsp;&nbsp;returnera doc;<br>});|1|
|Välj docs.id, docs.message som msg, docs.actions <br>FRÅN docs|__.Map(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;returnera {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Actions:doc.Actions<br>&nbsp;&nbsp;&nbsp;&nbsp;};<br>});|2|
|VÄLJ *<br>FRÅN docs<br>VAR docs.id="X998_Y998”|__.filter(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;returnera doc.id === ”X998_Y998”;<br>});|3|
|VÄLJ *<br>FRÅN docs<br>VAR ARRAY_CONTAINS (docs. Taggar 123)|__.filter(Function(x) {<br>&nbsp;&nbsp;&nbsp;&nbsp;returnera x.Tags & & x.Tags.indexOf(123) > -1;<br>});|4|
|Välj docs.id, docs.message som msg<br>FRÅN docs<br>VAR docs.id="X998_Y998”|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;returnera doc.id === ”X998_Y998”;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.Map(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;returnera {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ID: doc.id,<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;msg: doc.message<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;};<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>.Value();|5|
|SELECT VALUE-tagg<br>FRÅN docs<br>Anslut taggen i docs. Taggar<br>ORDER BY docs._ts|__.chain()<br>&nbsp;&nbsp;&nbsp;&nbsp;.filter(Function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;returnera dokument. Taggar & & Array.isArray (doc. Taggar);<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.sortBy(function(doc) {<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;returnera doc._ts;<br>&nbsp;&nbsp;&nbsp;&nbsp;})<br>&nbsp;&nbsp;&nbsp;&nbsp;.pluck("Tags")<br>&nbsp;&nbsp;&nbsp;&nbsp;.flatten()<br>&nbsp;&nbsp;&nbsp;&nbsp;.Value()|6|

Med följande förklarar varje fråga i tabellen ovan.
1. Resultat i alla dokument (sidnumrerade med fortsättningstoken) som är.
2. Projekt-id, meddelandet (ett alias till msg) och åtgärden från alla dokument.
3. Frågor för dokument med predikatet: id = ”X998_Y998”.
4. Frågor för dokument som har en property-taggar och taggarna är en matris som innehåller värdet 123.
5. Frågor för dokument med ett predikat, id = ”X998_Y998” och sedan projekt-id och meddelanden (ett alias till msg).
6. Filter för dokument som innehåller en matrisegenskap, taggar, och sorterar dokumenten av _ts tidsstämpelsegenskapen system och projekt + plattar ut taggar matrisen.


## <a name="runtime-support"></a>Runtime-stöd
Azure Cosmos DB [JavaScript server sida API](https://azure.github.io/azure-cosmosdb-js-server/) har stöd för de flesta av vanlig JavaScript-språkfunktioner som standardiserad av [ECMA-262](http://www.ecma-international.org/publications/standards/Ecma-262.htm).

### <a name="security"></a>Säkerhet
JavaScript-lagrade procedurer och utlösare är i begränsat läge så att effekterna av ett skript inte läckts till en annan utan att gå via transaktionsisoleringen ögonblicksbild på databasnivå. Runtime-miljöer i pooler men rensas av kontexten efter varje körning. Därför garanterat de är säker för eventuella oväntade sidoeffekter från varandra.

### <a name="pre-compilation"></a>Före kompilering
Lagrade procedurer, utlösare och UDF: er är implicit förkompilerade till formatet byte-kod för att undvika kompilering kostnaden vid tidpunkten för varje skript-anrop. Före kompileringen anrop av lagrade procedurer går snabbt och har en små utrymmeskrav.

## <a name="client-sdk-support"></a>Stöd för klient-SDK
Förutom att Azure Cosmos DB [Node.js](sql-api-sdk-node.md) API, Azure Cosmos DB har [.NET](sql-api-sdk-dotnet.md), [.NET Core](sql-api-sdk-dotnet-core.md), [Java](sql-api-sdk-java.md), [JavaScript ](sql-api-sdk-node.md), och [Python SDK: er](sql-api-sdk-python.md) för SQL-API. Lagrade procedurer, utlösare och UDF: er kan skapas och körs med någon av dessa SDK: er samt. I följande exempel visas hur du skapar och kör en lagrad procedur med hjälp av .NET-klienten. Observera hur .NET-typerna skickas till den lagrade proceduren som JSON och spela upp.

```javascript
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
```

Det här exemplet visar hur du använder den [SQL .NET API](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet) att skapa en utlösare för före och skapa ett dokument med utlösaren aktiveras. 

```javascript
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
```

Och i följande exempel visas hur du skapar en användardefinierad funktion (UDF) och använda det i en [SQL-fråga](sql-api-sql-query.md).

```javascript
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
```

## <a name="rest-api"></a>REST-API
Alla Azure Cosmos DB-åtgärder kan utföras på ett RESTful sätt. Lagrade procedurer, utlösare och användardefinierade funktioner kan registreras under en samling med hjälp av HTTP POST. I följande exempel visar hur du registrerar en lagrad procedur:

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


Den lagrade proceduren har registrerats genom att köra en POST-begäran mot den URI dbs/testdb/colls/testColl/sprocs där meddelandetexten innehåller den lagrade proceduren att skapa. Utlösare och UDF: er kan registreras på samma sätt genom att utfärda ett INLÄGG mot /triggers och /udfs respektive.
Det här lagrade proceduren kan sedan utföras genom att utfärda en POST-begäran mot dess resurslänk:

    POST https://<url>/sprocs/<sproc> HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:20 GMT


    [ { "name": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]


Här kan skickas indata till den lagrade proceduren i begärandetexten. Indata skickas som en JSON-matris av indataparametrar. Den lagrade proceduren tar första indata som ett dokument som är en svarstexten. Du får svaret är följande:

    HTTP/1.1 200 OK

    { 
      name: 'TestDocument',
      book: 'Autumn of the Patriarch',
      id: 'V7tQANV3rAkDAAAAAAAAAA==',
      ts: 1407830727,
      self: 'dbs/V7tQAA==/colls/V7tQANV3rAk=/docs/V7tQANV3rAkDAAAAAAAAAA==/',
      etag: '6c006596-0000-0000-0000-53e9cac70000',
      attachments: 'attachments/',
      Price: 200
    }


Utlösare, till skillnad från lagrade procedurer kan inte köras direkt. I stället utförs de som en del av en åtgärd på ett dokument. Du kan ange utlösare ska köras med en begäran med hjälp av HTTP-huvuden. Följande kod visar begäran om att skapa ett dokument.

    POST https://<url>/docs/ HTTP/1.1
    authorization: <<auth>>
    x-ms-date: Thu, 07 Aug 2014 03:43:10 GMT
    x-ms-documentdb-pre-trigger-include: validateDocumentContents 
    x-ms-documentdb-post-trigger-include: bookCreationPostTrigger

    {
       "name": "newDocument",
       "title": "The Wizard of Oz",
       "author": "Frank Baum",
       "pages": 92
    }


Här anges före utlösaren ska köras med förfrågan i rubriken x-ms-documentdb-pre-trigger-include. På motsvarande sätt ges efter utlösare i rubriken x-ms-documentdb-post-trigger-include. Både före och efter utlösare kan anges för en viss begäran.

## <a name="sample-code"></a>Exempelkod
Du hittar fler serverkod exempel (inklusive [massborttagning](https://github.com/Azure/azure-cosmosdb-js-server/blob/master/samples/stored-procedures/bulkDelete.js), och [uppdatera](https://github.com/Azure/azure-cosmosdb-js-server/blob/master/samples/stored-procedures/update.js)) i den [GitHub-lagringsplatsen](https://github.com/Azure/azure-cosmosdb-js-server/tree/master/samples).

Om du vill dela dina fantastiska lagrad procedur? bidra till databasen och skapa en pull-begäran! 

## <a name="next-steps"></a>Nästa steg
När du har en eller flera lagrade procedurer, utlösare och användardefinierade funktioner som har skapats kan du läsa in dem och visa dem i Azure-portalen med Datautforskaren.

Du kan också vara användbara följande referenser och resurser i din sökväg till mer information om programmering av serversidan för Azure Cosmos dB:

* [Azure Cosmos DB SDK: er](sql-api-sdk-dotnet.md)
* [DocumentDB-Studio](https://github.com/mingaliu/DocumentDBStudio/releases)
* [JSON](http://www.json.org/) 
* [JavaScript ECMA-262](http://www.ecma-international.org/publications/standards/Ecma-262.htm)
* [Säker och bärbar databasen utökningsbarhet](http://dl.acm.org/citation.cfm?id=276339) 
* [Tjänst-orienterade Database-arkitektur](http://dl.acm.org/citation.cfm?id=1066267&coll=Portal&dl=GUIDE) 
* [Som är värd för .NET-Runtime i Microsoft SQL server](http://dl.acm.org/citation.cfm?id=1007669)
