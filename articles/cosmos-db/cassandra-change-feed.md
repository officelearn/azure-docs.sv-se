---
title: Ändra feed i Azure Cosmos DB API för Cassandra
description: Lär dig hur du använder ändringsflödet i Azure Cosmos DB API för Cassandra för att få ändringarna gjorda i dina data.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: thvankra
ms.openlocfilehash: c2c695608653130b97bf29cc9ce48e2fbb429209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74694628"
---
# <a name="change-feed-in-the-azure-cosmos-db-api-for-cassandra"></a>Ändra feed i Azure Cosmos DB API för Cassandra

[Ändra feedstöd](change-feed.md) i Azure Cosmos DB API för Cassandra är tillgängligt via frågepredikaterna i Cassandra Query Language (CQL). Med hjälp av dessa predikatvillkor kan du fråga om ändringsfeed-API:et. Program kan hämta ändringarna i en tabell med primärnyckeln (kallas även partitionsnyckeln) som krävs i CQL. Du kan sedan vidta ytterligare åtgärder baserat på resultaten. Ändringar av raderna i tabellen fångas i ordningen för deras ändringstid och sorteringsordningen garanteras per partitionsnyckel.

I följande exempel visas hur du får en ändringsfeed på alla rader i en Cassandra API Keyspace-tabell med .NET. Predikatet COSMOS_CHANGEFEED_START_TIME() används direkt inom CQL för att fråga objekt i ändringsflödet från en angiven starttid (i det här fallet aktuell datetime). Du kan ladda ner hela provet [här](https://docs.microsoft.com/samples/azure-samples/azure-cosmos-db-cassandra-change-feed/cassandra-change-feed/).

I varje iteration återupptas frågan vid den sista punkten som ändringarna lästes med hjälp av växlingstillstånd. Vi kan se en kontinuerlig ström av nya ändringar i tabellen i Keyspace. Vi ser ändringar i rader som infogas eller uppdateras. Det går inte att söka efter borttagningsåtgärder med ändringsflödet i Cassandra API. 

```C#
    //set initial start time for pulling the change feed
     DateTime timeBegin = DateTime.UtcNow;

    //initialise variable to store the continuation token
    byte[] pageState = null;
    while (true)
    {
        try
        {

            //Return the latest change for all rows in 'user' table    
            IStatement changeFeedQueryStatement = new SimpleStatement(
            $"SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");
            if (pageState != null)
            {
                changeFeedQueryStatement = changeFeedQueryStatement.SetPagingState(pageState);
            }
            Console.WriteLine("getting records from change feed at last page state....");
            RowSet rowSet = session.Execute(changeFeedQueryStatement);

            //store the continuation token here
            pageState = rowSet.PagingState;

            List<Row> rowList = rowSet.ToList();
            if (rowList.Count != 0)
            {
                for (int i = 0; i < rowList.Count; i++)
                {
                    string value = rowList[i].GetValue<string>("user_name");
                    int key = rowList[i].GetValue<int>("user_id");
                    // do something with the data - e.g. compute, forward to another event, function, etc.
                    // here, we just print the user name field
                    Console.WriteLine("user_name: " + value);
                }
            }
            else
            {
                Console.WriteLine("zero documents read");
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Exception " + e);
        }
    }

```

Om du vill hämta ändringarna på en enda rad efter primärnyckel kan du lägga till primärnyckeln i frågan. I följande exempel visas hur du spårar ändringar för raden där "user_id = 1"

```C#
    //Return the latest change for all row in 'user' table where user_id = 1
    IStatement changeFeedQueryStatement = new SimpleStatement(
    $"SELECT * FROM uprofile.user where user_id = 1 AND COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");

```

## <a name="current-limitations"></a>Aktuella begränsningar

Följande begränsningar gäller när du använder ändringsflöde med Cassandra API:

* Skär och uppdateringar stöds för närvarande. Borttagningsåtgärd stöds ännu inte. Som en lösning kan du lägga till en mjuk markör på rader som tas bort. Lägg till exempel till ett fält på raden som heter "borttagen" och ställ in det på "true".
* Senaste uppdatering sparas som i centrala SQL API och mellanliggande uppdateringar till entiteten är inte tillgängliga.


## <a name="error-handling"></a>Felhantering

Följande felkoder och meddelanden stöds när du använder ändringsflödet i Cassandra API:

* **HTTP-felkod 429** - När ändringsflödet är begränsat returneras en tom sida.

## <a name="next-steps"></a>Nästa steg

* [Hantera Azure Cosmos DB Cassandra API-resurser med Azure Resource Manager-mallar](manage-cassandra-with-resource-manager.md)
