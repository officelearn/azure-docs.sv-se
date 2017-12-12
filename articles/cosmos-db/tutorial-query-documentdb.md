---
title: "Hur man frågan med SQL i Azure Cosmos DB? | Microsoft Docs"
description: "Lär dig att fråga med SQL i Azure Cosmos DB"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: cosmos-db
ms.custom: tutorial-develop, mvc
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/10/2017
ms.author: mimig
ms.openlocfilehash: 6e159ba7988801b274feb0d7c15a292b3b3b3126
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="azure-cosmos-db-how-to-query-using-sql"></a>Azure Cosmos DB: Hur man frågan med hjälp av SQL?

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Azure Cosmos DB [SQL API](documentdb-introduction.md) stöder frågar dokument med hjälp av SQL. Den här artikeln innehåller ett exempel på dokument och två exempel SQL-frågor och resultat.

Den här artikeln omfattar följande aktiviteter: 

> [!div class="checklist"]
> * Hämtning av data med SQL

## <a name="sample-document"></a>Exempel på ett dokument

SQL-frågor i den här artikeln använder följande exempeldokumentet.

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
## <a name="where-can-i-run-sql-queries"></a>Var kan jag köra SQL-frågor?

Du kan köra frågor med Data Explorer i Azure-portalen den [REST-API och SDK](documentdb-sdk-dotnet.md), och även [Query playground](https://www.documentdb.com/sql/demo), som körs frågor på en befintlig uppsättning exempeldata.

Mer information om SQL-frågor finns:
* [SQL-fråga och SQL-syntax](documentdb-sql-query.md)

## <a name="prerequisites"></a>Krav

Den här kursen förutsätter att du har ett konto för Azure Cosmos DB och samling. Har inte något av de? Slutför den [5 minuter quickstart](create-mongodb-nodejs.md) eller [developer kursen](tutorial-develop-mongodb.md) att skapa ett konto och samling.

## <a name="example-query-1"></a>Exempelfråga 1

Exempel family dokumentet ovan får följande SQL-frågan returnerar dokument där fältet id matchar `WakefieldFamily`. Eftersom det är en `SELECT *` -instruktionen utdata från frågan är klar JSON-dokumentet:

**Fråga**

    SELECT * 
    FROM Families f 
    WHERE f.id = "WakefieldFamily"

**Resultat**

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

## <a name="example-query-2"></a>Exempelfråga 2

Nästa fråga returnerar alla angivna namnen på underordnade i familjen vars id matchar `WakefieldFamily` sorterade efter deras klass.

**Fråga**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.children.grade ASC

**Resultat**

    [
      { "givenName": "Jesse" }, 
      { "givenName": "Lisa"}
    ]


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen kommer du har gjort följande:

> [!div class="checklist"]
> * Lärt dig hur man frågan med SQL  

Du kan nu fortsätta till nästa kurs att lära dig hur du distribuerar dina data globalt.

> [!div class="nextstepaction"]
> [Distribuera dina data globalt](tutorial-global-distribution-documentdb.md)

