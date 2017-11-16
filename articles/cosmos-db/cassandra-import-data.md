---
title: Importera Cassandra data till Azure Cosmos DB | Microsoft Docs
description: "Lär dig hur du använder kommandot CQL kopiera för att kopiera Cassandra data till Azure Cosmos DB."
services: cosmos-db
author: govindk
manager: jhubbard
documentationcenter: 
ms.assetid: eced5f6a-3f56-417a-b544-18cf000af33a
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: govindk
ms.custom: mvc
ms.openlocfilehash: bbfcbadcb0010d50e572b335d4cac78b33e974c8
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="azure-cosmos-db-import-cassandra-data"></a>Azure Cosmos DB: Importera Cassandra data

Den här självstudiekursen innehåller instruktioner för att importera Cassandra data till Azure Cosmos DB med hjälp av kommandot Cassandra Query Language (CQL) kopiera. 

Den här kursen ingår följande uppgifter:

> [!div class="checklist"]
> * Hämta din anslutningssträng
> * Importera data med hjälp av kommandot cqlsh kopia
> * Importera med Spark-koppling 

# <a name="prerequisites"></a>Krav

* Installera [Apache Cassandra](http://cassandra.apache.org/download/) och mer specifikt säkerställer *cqlsh* finns.
* Öka genomflödet: din datamigrering varaktighet beror på mängden dataflödet för tabellerna. Se till att öka genomflödet för större migrering av data. När du har slutfört migreringen, minska dataflöde för att spara kostnader. Mer information om ökar genomströmningen i den [Azure-portalen](https://portal.azure.com), finns [Set genomströmning för Azure Cosmos DB behållare](set-throughput.md).
* Aktivera SSL: Azure Cosmos-DB har stränga säkerhetskrav och standarder. Se till att aktivera SSL när du kommunicerar med ditt konto. När du använder CQL med SSH, har ett alternativ för att ange information om SSL. 

## <a name="find-your-connection-string"></a>Hitta anslutningssträngen

1. I den [Azure-portalen](https://portal.azure.com), längst till vänster, klicka på **Azure Cosmos DB**.

2. I den **prenumerationer** rutan, Välj namnet på ditt konto.

3. Klicka på **anslutningssträngen**. Den högra rutan innehåller all information som du behöver för att ansluta till ditt konto.

    ![Anslutningssidan sträng](./media/cassandra-import-data/keys.png)

## <a name="use-cqlsh-copy"></a>Använd cqlsh kopia

Använd följande riktlinjer för att importera data till Cassandra data till Azure Cosmos DB för användning med Cassandra-API:

1. Logga in på cqhsh med hjälp av informationen från portalen.
2. Använd den [kommandot CQL kopiera](http://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) kopiera lokala data till Apache Cassandra API-slutpunkt. Kontrollera att käll- och finns i samma datacenter för att minimera problem med nätverkssvarstiden.

### <a name="guide-for-moving-data-with-cqlsh"></a>Guide för att flytta data med cqlsh

1. Skapa och skala din tabell:
    * Som standard tabellen Azure Cosmos DB tillhandahåller ett nytt Cassandra-API med 1 000 frågeenheter per sekund (RU/s) (CQL-baserade skapa har etablerats med 400 RU/s). Innan du påbörjar migreringen genom att använda cqlsh skapa alla tabeller från den [Azure-portalen](https://portal.azure.com) eller från cqlsh. 

    * Från den [Azure-portalen](https://portal.azure.com), öka genomflödet av tabellerna standard genomströmning (400 eller 1 000 RU/s) och 10 000 RU/s under migreringen. Du kan undvika begränsning och migrera på kortare tid med högre genomströmning. Med varje timme fakturering i Azure Cosmos-databasen, kan du minska genomflödet omedelbart efter migrering för att spara kostnader.

2. Fastställa RU kostnad för en åtgärd. Du kan göra detta med hjälp av Azure Cosmos DB Cassandra API SDK önskat. Det här exemplet visar .NET-version för att få RU avgifter. 

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
            {
                byte[] valueInBytes = customPayload[key];
                string value = Encoding.UTF8.GetString(valueInBytes);
                Console.WriteLine($“CustomPayload:  {key}: {value}”);
            }
 
    ``` 

3. Fastställa svarstid från din dator till Azure DB som Cosmos-tjänsten. Om du är i ett Azure-Datacenter ska fördröjningen vara en låg enda millisekunder siffror. Om du befinner dig utanför Azure-Datacenter, kan du använda psping eller azurespeed.com för att hämta den ungefärliga svarstiden från din plats.   

4. Beräkna rätt värden för parametrar (NUMPROCESS, INGESTRATE, MAXBATCHSIZE eller MINBATCHSIZE) som ger bra prestanda. 

5. Kör kommandot slutliga migrering. Kör detta kommando förutsätter att du har startat cqlsh med hjälp av informationen i anslutningssträngen.

   ```
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="use-spark-to-import-data"></a>Använda Spark att importera data

För data som finns i ett befintligt kluster i Azure-datorer, är importerar data med hjälp av Spark också möjligt alternativ. Detta kräver Spark ställas in som mellanhand för en gång eller regelbundna införandet. 

## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig hur du utför följande uppgifter:

> [!div class="checklist"]
> * Hämta anslutningssträngen
> * Importera data med hjälp av kommandot för cql kopiera
> * Importera med hjälp av Spark-koppling 

Du kan gå vidare till avsnittet begrepp för mer information om Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Data justerbara konsekvensnivåer i Azure Cosmos DB](../cosmos-db/consistency-levels.md)
