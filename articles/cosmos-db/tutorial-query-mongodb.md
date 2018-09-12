---
title: 'Azure Cosmos DB: Hur används MongoDB API för att ställa frågor? | Microsoft Docs'
description: Lär dig att fråga med MongoDB-API:et för Azure Cosmos DB
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2018
ms.author: sngun
ms.custom: mvc
ms.openlocfilehash: efb59a73b3c9b0ab06fae2e7b4fe5b97d85249eb
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44052824"
---
# <a name="tutorial-query-azure-cosmos-db-by-using-the-mongodb-api"></a>Självstudie: Fråga mot Azure Cosmos DB med hjälp av MongoDB API

Azure Cosmos DB-[API:et för MongoDB](mongodb-introduction.md) stöder [frågor mot MongoDB-gränssnittet](https://docs.mongodb.com/manual/tutorial/query-documents/). 

I den här artikeln beskrivs följande uppgifter: 

> [!div class="checklist"]
> * Fråga efter data med MongoDB

Du kan sätta igång med hjälp av exemplen i det här dokumentet och titta på videon [Query Azure Cosmos DB with MongoDB shell](https://azure.microsoft.com/resources/videos/query-azure-cosmos-db-data-by-using-the-mongodb-shell/) (Hämta data från Azure Cosmos DB med MongoDB-gränssnittet).

## <a name="sample-document"></a>Exempeldokument

Frågorna i den här artikeln använder följande exempeldokument.

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```
## <a id="examplequery1"></a> Exempelfråga 1 

Med exemplet på familjedokumentet ovan returnerar följande fråga dokument där ID-fältet matchar `WakefieldFamily`.

**Fråga**
    
    db.families.find({ id: "WakefieldFamily"})

**Results**

    {
    "_id": "ObjectId(\"58f65e1198f3a12c7090e68c\")",
    "id": "WakefieldFamily",
    "parents": [
      {
        "familyName": "Wakefield",
        "givenName": "Robin"
      },
      {
        "familyName": "Miller",
        "givenName": "Ben"
      }
    ],
    "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
          { "givenName": "Goofy" },
          { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
      }
    ],
    "address": {
      "state": "NY",
      "county": "Manhattan",
      "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
    }

## <a id="examplequery2"></a>Exempelfråga 2 

Nästa fråga returnerar alla underordnade i familjen. 

**Fråga**
    
    db.families.find( { id: "WakefieldFamily" }, { children: true } )

**Results**

    {
    "_id": "ObjectId("58f65e1198f3a12c7090e68c")",
    "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
          { "givenName": "Goofy" },
          { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
      }
    ]
    }


## <a id="examplequery3"></a>Exempelfråga 3 

Nästa fråga returnerar alla familjer som är registrerade. 

**Fråga**
    
    db.families.find( { "isRegistered" : true })
**Resultat** Inget dokument returneras. 

## <a id="examplequery4"></a>Exempelfråga 4

Nästa fråga returnerar alla familjer som inte är registrerade. 

**Fråga**
    
    db.families.find( { "isRegistered" : false })
**Results**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}

## <a id="examplequery5"></a>Exempelfråga 5

Nästa fråga returnerar alla familjer med statusen NY som inte är registrerade. 

**Fråga**
    
     db.families.find( { "isRegistered" : false, "address.state" : "NY" })

**Results**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}


## <a id="examplequery6"></a>Exempelfråga 6

Nästa fråga returnerar alla familjer där värdet för underordnade klasser är 8.

**Fråga**
  
     db.families.find( { children : { $elemMatch: { grade : 8 }} } )

**Results**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}

## <a id="examplequery7"></a>Exempelfråga 7

Nästa fråga returnerar alla familjer där den underordnade matrisens storlek är 3.

**Fråga**
  
      db.Family.find( {children: { $size:3} } )

**Results**

Inga resultat returneras eftersom det inte finns några familjer med fler än två underordnade. Endast när parametern är 2 kommer frågan att lyckas och returnera det fullständiga dokumentet.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du gjort följande:

> [!div class="checklist"]
> * Lärt dig hur du frågar med MongoDB 

Du kan nu fortsätta till nästa självstudie för att lära dig hur du distribuerar dina data globalt.

> [!div class="nextstepaction"]
> [Distribuera dina data globalt](tutorial-global-distribution-sql-api.md)

