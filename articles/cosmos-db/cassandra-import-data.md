---
title: Importera Cassandra-data till Azure Cosmos DB | Microsoft Docs
description: Lär dig hur du kopierar Cassandra-data till Azure Cosmos DB med hjälp av Copy-kommandot i CQL.
services: cosmos-db
author: kanshiG
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: govindk
ms.custom: mvc
ms.openlocfilehash: f8c84cc501ea6a979d90d254abeceea8fcc6bddf
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44053027"
---
# <a name="migrate-your-data-to-azure-cosmos-db-cassandra-api-account"></a>Migrera data till Azure Cosmos DB Cassandra API-konto

I den här självstudiekursen beskriver vi hur du importerar Cassandra-data till Azure Cosmos DB med hjälp av kommandot COPY (kopiera) i CQL (Cassandra Query Language). 

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Hämta anslutningssträngen
> * Importera data med hjälp av COPY-kommandot i cqlsh
> * Importera med Apache Spark-anslutningsappen 

# <a name="prerequisites"></a>Nödvändiga komponenter

* Installera [Apache Cassandra](http://cassandra.apache.org/download/) och kontrollera särskilt att du ser *cqlsh*.  

* Öka dataflödet: Hur lång tid migreringen tar beror på hur stort dataflöde du har etablerat för dina tabeller. Vi rekommenderar att du ökar dataflödet för större datamigreringar. När du har slutfört migreringen minskar du dataflödet för att spara kostnader. Mer information om hur du ökar dataflödet på [Azure Portal](https://portal.azure.com) finns i [Set throughput for Azure Cosmos DB containers](set-throughput.md) (Ange dataflöde för Azure Cosmos DB-behållare).  

* Aktivera SSL: Azure Cosmos DB har stränga säkerhetskrav och säkerhetsstandarder. Det är viktigt att du aktiverar SSL när du kommunicerar med ditt konto. Du kan ange SSL-information när du använder CQL med SSH. 

## <a name="get-your-connection-string"></a>Hämta anslutningssträngen

1. Klicka på **Azure Cosmos DB** längst till vänster på [Azure Portal](https://portal.azure.com).

2. Välj namnet på ditt konto i fönstret **Prenumerationer**.

3. Klicka på **Anslutningssträng**. Den högra rutan innehåller all information som du behöver för att ansluta till ditt konto.

    ![Sidan Anslutningssträng](./media/cassandra-import-data/keys.png)

## <a name="migrate-data-by-using-cqlsh-copy"></a>Migrera data med hjälp av COPY (kopiera) i cqlsh

Importera Cassandra-data till Azure Cosmos DB för användning med API:et för Apache Cassandra genom att följa riktlinjerna nedan:

1. Logga in i cqhsh med hjälp av informationen från portalen.
2. Använd [COPY-kommandot i CQL](http://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh) för att kopiera lokala data till slutpunkten för API:et för Apache Cassandra. Kontrollera att källan och målet finns i samma datacenter för att minimera problem med långa svarstider.

### <a name="steps-to-move-data-with-cqlsh"></a>Anvisningar för att flytta data med cqlsh

1. Skapa tabellen i förväg och skala den:
    * Som standard etablerar Azure Cosmos DB en ny tabell för Apache Cassandra-API:et med 1 000 enheter för programbegäran per sekund (RU/s) (med CQL etableras en ny tabell med 400 RU/s). Innan du påbörjar migreringen med hjälp av cqlsh skapar du alla tabeller i förväg på [Azure Portal](https://portal.azure.com) eller från cqlsh. 

    * Öka dataflödet för dina tabeller på [Azure Portal](https://portal.azure.com) från standarddataflödet (400 eller 1 000 RU/s) till 10 000 RU/s under migreringen. Med ett högre dataflöde kan du undvika begränsningar och migrera snabbare. Med timbaserad fakturering i Azure Cosmos DB kan du minska dataflödet direkt efter migreringen för att spara kostnader.

2. Beräkna RU-kostnaden för en åtgärd. Du kan göra detta med valfritt SDK för Apache Cassandra-API:et för Azure Cosmos DB. I det här exemplet används .NET-versionen för att hämta RU-kostnaden. 

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
            {
                byte[] valueInBytes = customPayload[key];
                string value = Encoding.UTF8.GetString(valueInBytes);
                Console.WriteLine($"CustomPayload:  {key}: {value}");
            }
 
    ``` 

3. Kontrollera svarstiden från din dator till Azure Cosmos DB-tjänsten. Inom ett Azure-datacenter bör svarstiderna vara låga ensiffriga millisekunder. Utanför ett Azure-datacenter kan du använda psping eller azurespeed.com för att hämta den ungefärliga svarstiden från din plats.   

4. Beräkna lämpliga värden för parametrar (NUMPROCESS, INGESTRATE, MAXBATCHSIZE eller MINBATCHSIZE) som ger bra prestanda. 

5. Kör migreringskommandot. Körningen av det här kommandot förutsätter att du har startat cqlsh baserat på informationen för anslutningssträngen.

   ```bash
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="migrate-data-by-using-spark"></a>Migrera data med Spark

Data som finns i ett befintligt kluster på virtuella datorer i Azure kan även importeras med hjälp av Apache Spark. I så fall måste Apache Spark konfigureras som mellanhand för en engångsinmatning eller för regelbunden datainmatning. 

## <a name="next-steps"></a>Nästa steg

I den här kursen har du lärt dig hur du:

> [!div class="checklist"]
> * Hämtar anslutningssträngen
> * Importerar data med hjälp av copy-kommandot i cql
> * Importerar med hjälp av Apache Spark-anslutningsappen 

Om du vill lära dig mer om Azure Cosmos DB fortsätter du till avsnittet Begrepp. 

> [!div class="nextstepaction"]
>[Justerbara datakonsekvensnivåer i Azure Cosmos DB](../cosmos-db/consistency-levels.md)
