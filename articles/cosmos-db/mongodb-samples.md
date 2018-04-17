---
title: Använda MongoDB APIs för att skapa en app i Azure Cosmos DB | Microsoft Docs
description: 'En självstudiekurs som skapar en onlinedatabas med hjälp av Azure Cosmos DB-API: er för MongoDB.'
keywords: mongodb-exempel
services: cosmos-db
author: AndrewHoh
manager: kfile
editor: ''
documentationcenter: ''
ms.assetid: fb38bc53-3561-487d-9e03-20f232319a87
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: anhoh
ms.openlocfilehash: 81eff479c94af938918e6a221d45184ca1a84aef
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="build-an-azure-cosmos-db-api-for-mongodb-app-using-nodejs"></a>Skapa en Azure-Cosmos-DB: API: et för MongoDB-app med Node.js
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Node.js för MongoDB](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [C++](sql-api-cpp-get-started.md)
>  
>

Det här exemplet illustrerar hur du skapar en Azure-Cosmos-DB: API: et för MongoDB-konsolapp med Node.js.

Om du vill använda det här exemplet måste du:

* [Skapa](create-mongodb-dotnet.md#create-account) en Azure-Cosmos-DB: API för MongoDB-kontot.
* Hämta din MongoDB [anslutningssträngen](connect-mongodb-account.md) information.

## <a name="create-the-app"></a>Skapa appen

1. Skapa en *app.js* filen och kopiera och klistra in koden nedan.

    ```nodejs
    var MongoClient = require('mongodb').MongoClient;
    var assert = require('assert');
    var ObjectId = require('mongodb').ObjectID;
    var url = 'mongodb://<username>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';

    var insertDocument = function(db, callback) {
    db.collection('families').insertOne( {
            "id": "AndersenFamily",
            "lastName": "Andersen",
            "parents": [
                { "firstName": "Thomas" },
                { "firstName": "Mary Kay" }
            ],
            "children": [
                { "firstName": "John", "gender": "male", "grade": 7 }
            ],
            "pets": [
                { "givenName": "Fluffy" }
            ],
            "address": { "country": "USA", "state": "WA", "city": "Seattle" }
        }, function(err, result) {
        assert.equal(err, null);
        console.log("Inserted a document into the families collection.");
        callback();
    });
    };
    
    var findFamilies = function(db, callback) {
    var cursor =db.collection('families').find( );
    cursor.each(function(err, doc) {
        assert.equal(err, null);
        if (doc != null) {
            console.dir(doc);
        } else {
            callback();
        }
    });
    };
    
    var updateFamilies = function(db, callback) {
    db.collection('families').updateOne(
        { "lastName" : "Andersen" },
        {
            $set: { "pets": [
                { "givenName": "Fluffy" },
                { "givenName": "Rocky"}
            ] },
            $currentDate: { "lastModified": true }
        }, function(err, results) {
        console.log(results);
        callback();
    });
    };
    
    var removeFamilies = function(db, callback) {
    db.collection('families').deleteMany(
        { "lastName": "Andersen" },
        function(err, results) {
            console.log(results);
            callback();
        }
    );
    };
    
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    **Valfria**: Om du använder den **MongoDB Node.js 2.2 drivrutinen**, Ersätt följande kodavsnitt:

    Ursprungliga:

    ```nodejs
    MongoClient.connect(url, function(err, client) {
    assert.equal(null, err);
    var db = client.db('familiesdb');
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                client.close();
            });
        });
        });
    });
    });
    ```
    
    Ska ersättas med:

    ```nodejs
    MongoClient.connect(url, function(err, db) {
    assert.equal(null, err);
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                db.close();
            });
        });
        });
    });
    });
    ```
    
2. Ändra följande variabler i den *app.js* fil per inställningarna för ditt konto (Lär dig att hitta din [anslutningssträngen](connect-mongodb-account.md)):

    > [!IMPORTANT]
    > Den **MongoDB Node.js 3.0-drivrutin** kräver kodning specialtecken i Cosmos-DB-lösenord. Se till att koda '=' tecken som % 3D
    >
    > Exempel: Lösenordet *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv ==* kodar till *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv 3D % 3D*
    >
    > Den **MongoDB Node.js 2.2 drivrutinen** kräver inte att koda specialtecken i Cosmos-DB-lösenord.
    >
    >
   
    ```nodejs
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';
    ```
     
3. Öppna valfri terminal, kör **npm installera mongodb--spara**, kör din app med **noden app.js**

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använder MongoChef](mongodb-mongochef.md) med Azure Cosmos-DB: API för MongoDB-kontot.
