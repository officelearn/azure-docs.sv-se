---
title: "Självstudie om NoSQL Node.js för DocumentDB | Microsoft Docs"
description: "En självstudie om NoSQL Node.js där du skapar en NoSQL-databas och ett konsolprogram med DocumentDB Node.js SDK. DocumentDB är en NoSQL-databas för JSON."
keywords: "självstudier för node.js, noddatabas"
services: documentdb
documentationcenter: node.js
author: AndrewHoh
manager: jhubbard
editor: monicar
ms.assetid: 14d52110-1dce-4ac0-9dd9-f936afccd550
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: node
ms.topic: hero-article
ms.date: 12/25/2016
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 2b8ac838e9387b04467f03d0608da05b3edfdd26
ms.lasthandoff: 03/28/2017


---
# <a name="nosql-nodejs-tutorial-documentdb-nodejs-console-application"></a>Självstudie om NoSQL Node.js: DocumentDB Node.js-konsolprogram
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Node.js för MongoDB](documentdb-mongodb-samples.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 

Välkommen till självstudien om Node.js för Azure DocumentDB Node.js SDK! När du har genomfört den här självstudiekursen har du en konsolapp som skapar och skickar frågor till DocumentDB-resurser.

Vi tar upp följande:

* Skapa och ansluta till ett DocumentDB-konto
* Konfigurera ditt program
* Skapa en Node-databas
* Skapa en samling
* Skapa JSON-dokument
* Skicka frågor till samlingen
* Ersätta ett dokument
* Ta bort ett dokument
* Ta bort Node-databasen

Har du inte tid? Oroa dig inte! Den kompletta lösningen finns på [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started). En snabbguide finns i [Hämta den kompletta lösningen](#GetSolution).

När du har genomfört självstudien om Node.js får du gärna ge oss feedback med röstningsknapparna högst upp och längst ned på den här sidan. Om du vill att vi ska kontakta dig direkt kan du skriva din e-postadress i kommentaren.

Nu sätter vi igång!

## <a name="prerequisites-for-the-nodejs-tutorial"></a>Förutsättningar för självstudien om Node.js
Se till att du har följande:

* Ett aktivt Azure-konto. Om du inte har ett kan du registrera dig för en [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
    * Du kan också använda [Azure DocumentDB-emulatorn](documentdb-nosql-local-emulator.md) för den här självstudien.
* [Node.js](https://nodejs.org/) version 0.10.29 eller högre.

## <a name="step-1-create-a-documentdb-account"></a>Steg 1: Skapa ett DocumentDB-konto
Börja med att skapa ett DocumentDB-konto. Om du redan har ett konto som du vill använda kan du gå vidare till [Konfigurera Node.js-programmet](#SetupNode). Om du använder DocumentDB-emulatorn följer du stegen i artikeln om [Azure DocumentDB-emulatorn](documentdb-nosql-local-emulator.md) för att konfigurera emulatorn och gå vidare med att [konfigurera Node.js-programmet](#SetupNode).

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a id="SetupNode"></a>Steg 2: Konfigurera Node.js-programmet
1. Öppna valfri terminal.
2. Leta reda på mappen eller katalogen där du vill spara Node.js-programmet.
3. Skapa två tomma JavaScript-filer med följande kommandon:
   * Windows:
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```
   * Linux/OS X:
     * ```touch app.js```
     * ```touch config.js```
4. Installera modulen documentdb via npm. Ange följande kommando:
   * ```npm install documentdb --save```

Bra! Nu när vi har slutfört installationen kan vi börja skriva kod.

## <a id="Config"></a>Steg 3: Ange appkonfigurationer
Öppna ```config.js``` i valfri textredigerare.

Kopiera och klistra sedan in kodfragmentet nedan och ange egenskaper för ```config.endpoint``` och ```config.primaryKey``` till slutpunkts-URI och primärnyckel i DocumentDB. Båda dessa konfigurationer finns i [Azure Portal](https://portal.azure.com).

![Självstudie om Node.js – Skärmdump av Azure Portal som visar ett DocumentDB-konto med hubben AKTIV markerad, knappen NYCKLAR markerad i bladet DocumentDB-konto och värdena URI, PRIMÄRNYCKEL och SEKUNDÄRNYCKEL markerade i bladet Nycklar – Node-databas][keys]

    // ADD THIS PART TO YOUR CODE
    var config = {}

    config.endpoint = "~your DocumentDB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

Kopiera och klistra in ```database id```, ```collection id``` och ```JSON documents``` till ditt ```config```-objekt nedan där du anger ```config.endpoint```- och ```config.authKey```-egenskaper. Om du redan har data som du vill lagra i databasen kan du använda [datamigreringsverktyget](documentdb-import-data.md) för DocumentDB istället för att lägga till dokumentdefinitionerna.

    config.endpoint = "~your DocumentDB endpoint uri here~";
    config.primaryKey = "~your primary key here~";

    // ADD THIS PART TO YOUR CODE
    config.database = {
        "id": "FamilyDB"
    };

    config.collection = {
        "id": "FamilyColl"
    };

    config.documents = {
        "Andersen": {
            "id": "Anderson.1",
            "lastName": "Andersen",
            "parents": [{
                "firstName": "Thomas"
            }, {
                    "firstName": "Mary Kay"
                }],
            "children": [{
                "firstName": "Henriette Thaulow",
                "gender": "female",
                "grade": 5,
                "pets": [{
                    "givenName": "Fluffy"
                }]
            }],
            "address": {
                "state": "WA",
                "county": "King",
                "city": "Seattle"
            }
        },
        "Wakefield": {
            "id": "Wakefield.7",
            "parents": [{
                "familyName": "Wakefield",
                "firstName": "Robin"
            }, {
                    "familyName": "Miller",
                    "firstName": "Ben"
                }],
            "children": [{
                "familyName": "Merriam",
                "firstName": "Jesse",
                "gender": "female",
                "grade": 8,
                "pets": [{
                    "givenName": "Goofy"
                }, {
                        "givenName": "Shadow"
                    }]
            }, {
                    "familyName": "Miller",
                    "firstName": "Lisa",
                    "gender": "female",
                    "grade": 1
                }],
            "address": {
                "state": "NY",
                "county": "Manhattan",
                "city": "NY"
            },
            "isRegistered": false
        }
    };


Databasen, samlingen och dokumentdefinitionerna kommer att fungera som DocumentDB-```database id```, -```collection id``` och dokumentens data.

Exportera slutligen ```config```-objektet, så att du kan referera till det i filen ```app.js```.

            },
            "isRegistered": false
        }
    };

    // ADD THIS PART TO YOUR CODE
    module.exports = config;

## <a id="Connect"></a>Steg 4: Anslut till ett DocumentDB-konto
Öppna den tomma filen ```app.js``` i textredigeraren. Kopiera och klistra in koden nedan för att importera modulen ```documentdb``` och modulen ```config``` som du just skapade.

    // ADD THIS PART TO YOUR CODE
    "use strict";

    var documentClient = require("documentdb").DocumentClient;
    var config = require("./config");
    var url = require('url');

Kopiera och klistra in koden för att använda ```config.endpoint``` och ```config.primaryKey``` som du tidigare skapade och för att skapa en ny dokumentklient.

    var config = require("./config");
    var url = require('url');

    // ADD THIS PART TO YOUR CODE
    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

Nu har du koden för att initiera DocumentDB-klienten och det är dags att gå vidare till arbete med DocumentDB-resurser.

## <a name="step-5-create-a-node-database"></a>Steg 5: Skapa en Node-databas
Kopiera och klistra in koden nedan för att ange HTTP-status för Kunde inte hittas, databasens URL och samlingens URL. Det är via URL:erna som DocumentDB-klienten hittar rätt databas och samling.

    var client = new documentClient(config.endpoint, { "masterKey": config.primaryKey });

    // ADD THIS PART TO YOUR CODE
    var HttpStatusCodes = { NOTFOUND: 404 };
    var databaseUrl = `dbs/${config.database.id}`;
    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

Du kan skapa en [databas](documentdb-resources.md#databases) med funktionen [createDatabase](https://azure.github.io/azure-documentdb-node/DocumentClient.html) för klassen **DocumentClient**. En databas är en logisk behållare för dokumentlagring, partitionerad över samlingarna.

Kopiera och klistra in funktionen **getDatabase** för att skapa den nya databasen i filen app.js med ```id``` som anges i objektet ```config```. Funktionen kommer att kontrollera om en databas med samma ```FamilyRegistry```-id redan finns. Om den finns returnerar vi den databasen istället för att skapa en ny.

    var collectionUrl = `${databaseUrl}/colls/${config.collection.id}`;

    // ADD THIS PART TO YOUR CODE
    function getDatabase() {
        console.log(`Getting database:\n${config.database.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDatabase(databaseUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDatabase(config.database, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

Kopiera och klistra in koden nedan där du konfigurerar funktionen **getDatabase** att lägga till hjälpfunktionen **exit** som skriver stängningsmeddelandet och anropet till funktionen **getDatabase**.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function exit(message) {
        console.log(message);
        console.log('Press any key to exit');
        process.stdin.setRawMode(true);
        process.stdin.resume();
        process.stdin.on('data', process.exit.bind(process, 0));
    }

    getDatabase()
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Leta upp filen ```app.js``` i terminalen och kör kommandot: ```node app.js```

Grattis! Du har skapat en DocumentDB-databas.

## <a id="CreateColl"></a>Steg 6: Skapa en samling
> [!WARNING]
> **CreateDocumentCollectionAsync** skapar en ny samling, vilket får konsekvenser för priset. Mer information finns på vår [sida med priser](https://azure.microsoft.com/pricing/details/documentdb/).
> 
> 

En [samling](documentdb-resources.md#collections) kan skapas med funktionen [createCollection](https://azure.github.io/azure-documentdb-node/DocumentClient.html) för klassen **DocumentClient**. En samling är en behållare för JSON-dokument och associerad JavaScript-applogik.

Kopiera och klistra in funktionen **getCollection** under funktionen **getDatabase** i app.js-filen för att skapa den nya samlingen med ```id``` som anges i objektet ```config```. Vi kontrollerar igen att det inte redan finns en samling med samma ```FamilyCollection```-id. Om det gör det returnerar vi den samlingen istället för att skapa en ny.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function getCollection() {
        console.log(`Getting collection:\n${config.collection.id}\n`);

        return new Promise((resolve, reject) => {
            client.readCollection(collectionUrl, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createCollection(databaseUrl, config.collection, { offerThroughput: 400 }, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    }

Kopiera och klistra in koden under anropet till **getDatabase** för att köra funktionen **getCollection**.

    getDatabase()

    // ADD THIS PART TO YOUR CODE
    .then(() => getCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Leta upp filen ```app.js``` i terminalen och kör kommandot: ```node app.js```

Grattis! Du har skapat en DocumentDB-samling.

## <a id="CreateDoc"></a>Steg 7: Skapa ett dokument
Du kan skapa ett [dokument](documentdb-resources.md#documents) med metoden [createDocument](https://azure.github.io/azure-documentdb-node/DocumentClient.html) för klassen **DocumentClient**. Dokument är användardefinierat (godtyckligt) JSON-innehåll. Du kan nu infoga ett dokument i DocumentDB.

Kopiera och klistra in funktionen **getFamilyDocument** under funktionen **getCollection** för att skapa dokumenten med de JSON-data som sparas i objektet ```config```. Vi kontrollerar igen att det inte redan finns ett dokument med samma id.

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function getFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Getting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.readDocument(documentUrl, { partitionKey: document.district }, (err, result) => {
                if (err) {
                    if (err.code == HttpStatusCodes.NOTFOUND) {
                        client.createDocument(collectionUrl, document, (err, created) => {
                            if (err) reject(err)
                            else resolve(created);
                        });
                    } else {
                        reject(err);
                    }
                } else {
                    resolve(result);
                }
            });
        });
    };

Kopiera och klistra in koden under anropet till **getCollection** för att köra funktionen **getFamilyDocument**.

    getDatabase()
    .then(() => getCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Leta upp filen ```app.js``` i terminalen och kör kommandot: ```node app.js```

Grattis! Du har skapat ett DocumentDB-dokument.

![Självstudie om Node.js – Diagram som illustrerar den hierarkiska relationen mellan kontot, databasen, samlingen och dokumenten – Node-databas](./media/documentdb-nodejs-get-started/node-js-tutorial-account-database.png)

## <a id="Query"></a>Steg 8: Skicka frågor till DocumentDB-resurser
DocumentDB stöder [komplexa frågor](documentdb-sql-query.md) mot JSON-dokument som lagras i varje samling. Följande exempelkod visar en fråga som du kan köra mot dokumenten i samlingen.

Kopiera och klistra in funktionen **queryCollection** under funktionen **getFamilyDocument** i app.js-filen. DocumentDB stöder SQL-liknande frågor som visas nedan. Mer information om hur du skapar komplexa frågor finns i [Query Playground](https://www.documentdb.com/sql/demo) och [frågedokumentationen](documentdb-sql-query.md).

                } else {
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function queryCollection() {
        console.log(`Querying collection through index:\n${config.collection.id}`);

        return new Promise((resolve, reject) => {
            client.queryDocuments(
                collectionUrl,
                'SELECT VALUE r.children FROM root r WHERE r.lastName = "Andersen"'
            ).toArray((err, results) => {
                if (err) reject(err)
                else {
                    for (var queryResult of results) {
                        let resultString = JSON.stringify(queryResult);
                        console.log(`\tQuery returned ${resultString}`);
                    }
                    console.log();
                    resolve(results);
                }
            });
        });
    };


Följande diagram illustrerar hur DocumentDB SQL-frågesyntaxen anropas mot samlingen som du skapade.

![Självstudie om Node.js – Diagram som illustrerar frågans omfång och innebörd – Node-databas](./media/documentdb-nodejs-get-started/node-js-tutorial-collection-documents.png)

Nyckelordet [FROM](documentdb-sql-query.md#FromClause) är valfritt i frågan eftersom DocumentDB-frågor redan är begränsade till en enda samling. ”FROM Families f” kan därför bytas mot ”FROM root r” eller annat valfritt variabelnamn som du väljer. DocumentDB drar slutsatsen att familjer, roten eller variabelnamnet som du har valt som standard refererar till den aktuella samlingen.

Kopiera och klistra in koden under anropet till **getFamilyDocument** för att köra funktionen **queryCollection**.

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))

    // ADD THIS PART TO YOUR CODE
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Leta upp filen ```app.js``` i terminalen och kör kommandot: ```node app.js```

Grattis! Du har skickat en fråga till DocumentDB-dokument.

## <a id="ReplaceDocument"></a>Steg 9: Ersätta ett dokument
DocumentDB har stöd för att ersätta JSON-dokument.

Kopiera och klistra in funktionen **replaceFamilyDocument** under funktionen **queryCollection** i app.js-filen.

                    }
                    console.log();
                    resolve(result);
                }
            });
        });
    }

    // ADD THIS PART TO YOUR CODE
    function replaceFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Replacing document:\n${document.id}\n`);
        document.children[0].grade = 6;

        return new Promise((resolve, reject) => {
            client.replaceDocument(documentUrl, document, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

Kopiera och klistra in koden under anropet till **queryCollection** för att köra funktionen **replaceDocument**. Lägg även till kod för att anropa **queryCollection** på nytt för att kontrollera att dokumentet har ändrats.

    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Leta upp filen ```app.js``` i terminalen och kör kommandot: ```node app.js```

Grattis! Du har ersatt ett DocumentDB-dokument.

## <a id="DeleteDocument"></a>Steg 10: Ta bort ett dokument
DocumentDB har stöd för att ta bort JSON-dokument.

Kopiera och klistra in funktionen **deleteFamilyDocument** under funktionen **replaceFamilyDocument**.

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function deleteFamilyDocument(document) {
        let documentUrl = `${collectionUrl}/docs/${document.id}`;
        console.log(`Deleting document:\n${document.id}\n`);

        return new Promise((resolve, reject) => {
            client.deleteDocument(documentUrl, (err, result) => {
                if (err) reject(err);
                else {
                    resolve(result);
                }
            });
        });
    };

Kopiera och klistra in koden under anropet till den andra **queryCollection** för att köra funktionen **deleteDocument**.

    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())

    // ADD THIS PART TO YOUR CODE
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Leta upp filen ```app.js``` i terminalen och kör kommandot: ```node app.js```

Grattis! Du har tagit bort ett DocumentDB-dokument.

## <a id="DeleteDatabase"></a>Steg 11: Ta bort Node-databasen
Om du tar bort databasen du skapade försvinner databasen och alla underordnade resurser (t.ex. samlingar och dokument).

Kopiera och klistra in funktionen **cleanup** under funktionen **deleteFamilyDocument** för att ta bort databasen och alla underordnade resurser.

                else {
                    resolve(result);
                }
            });
        });
    };

    // ADD THIS PART TO YOUR CODE
    function cleanup() {
        console.log(`Cleaning up by deleting database ${config.database.id}`);

        return new Promise((resolve, reject) => {
            client.deleteDatabase(databaseUrl, (err) => {
                if (err) reject(err)
                else resolve(null);
            });
        });
    }

Kopiera och klistra in koden under anropet till **deleteFamilyDocument** för att köra funktionen **cleanup**.

    .then(() => deleteFamilyDocument(config.documents.Andersen))

    // ADD THIS PART TO YOUR CODE
    .then(() => cleanup())
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

## <a id="Run"></a>Steg 12: Kör Node.js-programmet i sin helhet!
Hela sekvensen för att anropa dina funktioner bör se ut så här:

    getDatabase()
    .then(() => getCollection())
    .then(() => getFamilyDocument(config.documents.Andersen))
    .then(() => getFamilyDocument(config.documents.Wakefield))
    .then(() => queryCollection())
    .then(() => replaceFamilyDocument(config.documents.Andersen))
    .then(() => queryCollection())
    .then(() => deleteFamilyDocument(config.documents.Andersen))
    .then(() => cleanup())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });

Leta upp filen ```app.js``` i terminalen och kör kommandot: ```node app.js```

Du bör nu se utdata från din kom-igång-app. Dina utdata bör motsvara exempeltexten nedan.

    Getting database:
    FamilyDB

    Getting collection:
    FamilyColl

    Getting document:
    Anderson.1

    Getting document:
    Wakefield.7

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing document:
    Anderson.1

    Querying collection through index:
    FamilyColl
        Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleting document:
    Anderson.1

    Cleaning up by deleting database FamilyDB
    Completed successfully
    Press any key to exit

Grattis! Du har slutfört självstudien om Node.js och skapat ditt första DocumentDB-konsolprogram!

## <a id="GetSolution"></a>Hämta den fullständiga lösningen till Node.js-självstudien
Om du inte har tid att slutföra stegen i den här självstudien eller bara vill ladda ned koden kan du hämta den från [GitHub](https://github.com/Azure-Samples/documentdb-node-getting-started).

För att köra GetStarted-lösningen med alla exempel i den här artikeln behöver du följande:

* [DocumentDB-konto][documentdb-create-account].
* [GetStarted](https://github.com/Azure-Samples/documentdb-node-getting-started)-lösningen som finns på GitHub.

Installera modulen **documentdb** via npm. Ange följande kommando:

* ```npm install documentdb --save```

Sedan uppdaterar du värdena config.endpoint och config.authKey i filen ```config.js``` enligt beskrivningen i [Steg 3: Ange appkonfigurationer](#Config). 

Leta sedan upp filen ```app.js``` i terminalen och kör kommandot: ```node app.js```.

Då är det bara att bygga den, så är du på väg! 

## <a name="next-steps"></a>Nästa steg
* Vill du ha ett mer komplext Node.js-exempel? Se [Skapa en Node.js-webbapp med DocumentDB](documentdb-nodejs-application.md).
* Mer information om hur du [övervakar ett DocumentDB-konto](documentdb-monitor-accounts.md).
* Kör frågor mot vår exempeldatauppsättning i [Query Playground](https://www.documentdb.com/sql/demo).
* Mer information om programmeringsmiljön finns i avsnittet Utveckla på [dokumentationssidan för DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

[documentdb-create-account]: documentdb-create-account.md
[keys]: media/documentdb-nodejs-get-started/node-js-tutorial-keys.png

