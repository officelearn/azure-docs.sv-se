---
title: Skapa en Azure Cosmos DB-app med hjälp av MongoDB-API:er | Microsoft Docs
description: En självstudiekurs som beskriver hur du skapar en onlinedatabas med hjälp av API:erna för MongoDB i Azure Cosmos DB.
keywords: mongodb examples
services: cosmos-db
author: SnehaGunda
manager: kfile
editor: ''
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: sample
ms.date: 03/23/2018
ms.author: sngun
ms.openlocfilehash: bd31656404f11c9676b321e2e40454c33f61e3f5
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34795201"
---
# <a name="build-an-azure-cosmos-db-api-for-mongodb-app-using-nodejs"></a>Skapa en Azure Cosmos DB-app med hjälp av MongoDB-API:er i Node.js
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Node.js för MongoDB](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [C++](sql-api-cpp-get-started.md)
>  
>

Det här exemplet beskriver hur du skapar en Azure Cosmos DB-konsolapp med hjälp av MongoDB-API:er i Node.js.

För att följa med i det här exemplet måste du:

* [Skapa](create-mongodb-dotnet.md#create-account) ett Azure Cosmos DB: API för MongoDB-konto.
* Hämta informationen om MongoDB-[anslutningssträngen](connect-mongodb-account.md).

## <a name="create-the-app"></a>Skapa appen

1. Skapa en *app.js*-fil och kopiera och klistra in koden nedan.

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
    
    **Valfritt**: Ersätt kodfragmentet nedan om du använder **MongoDB Node.js 2.2-drivrutinen**.

    Ursprungligt:

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
    
    Bör ersättas med:

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
    
2. Ändra följande variabler i filen *app.js* baserat på dina kontoinställningar ([så här hittar du din anslutningssträng](connect-mongodb-account.md)):

    > [!IMPORTANT]
    > **MongoDB Node.js 3.0-drivrutinen** kräver kodning av specialtecken i Azure Cosmos DB-lösenordet. Du måste koda ”=”-tecken som %3D
    >
    > Exempel: Lösenordet *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv==* kodas som *jm1HbNdLg5zxEuyD86ajvINRFrFCUX0bIWP15ATK3BvSv%3D%3D*
    >
    > **MongoDB Node.js 2.2-drivrutinen** kräver inte kodning av specialtecken i Azure Cosmos DB-lösenordet.
    >
    >
   
    ```nodejs
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10255/?ssl=true';
    ```
     
3. Öppna valfri terminal, kör **npm install mongodb --save** och kör sedan appen med **node app.js**

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [använder MongoChef](mongodb-mongochef.md) med ditt Azure Cosmos DB: API för MongoDB-konto.
