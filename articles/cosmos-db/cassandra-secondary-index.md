---
title: Indexering i Azure Cosmos DB Cassandra API-konto
description: Lär dig hur sekundär indexering fungerar i Azure Azure Cosmos DB Cassandra API-konto.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/04/2020
ms.author: thvankra
ms.reviewer: sngun
ms.openlocfilehash: 7de38097acdbfa1f9c9b90f3051c68dec5465b32
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758032"
---
# <a name="secondary-indexing-in-azure-cosmos-db-cassandra-api"></a>Sekundär indexering i Azure Cosmos DB Cassandra API

Cassandra-API:et i Azure Cosmos DB utnyttjar den underliggande indexeringsinfrastrukturen för att exponera indexeringsstyrkan som är inbyggd i plattformen. Till skillnad från sql-huvud-API:et indexerar Cassandra API i Azure Cosmos DB dock inte alla attribut som standard. I stället stöder den sekundär indexering för att skapa ett index på vissa attribut, som fungerar på samma sätt som Apache Cassandra.  

I allmänhet rekommenderas det inte att köra filterfrågor på kolumnerna som inte är partitionerade. Du måste använda syntaxen TILLÅT FILTRERING explicit, vilket resulterar i en åtgärd som kanske inte fungerar bra. I Azure Cosmos DB kan du köra sådana frågor på låga kardinalitetsattribut eftersom de fläktar ut över partitioner för att hämta resultaten.

Det rekommenderas inte att skapa ett index i en ofta uppdaterad kolumn. Det är klokt att skapa ett index när du definierar tabellen. Detta säkerställer att data och index är i ett konsekvent tillstånd. Om du skapar ett nytt index för befintliga data kan du för närvarande inte spåra indexstatusändringen för tabellen. Om du behöver spåra förloppet för den här åtgärden måste du begära förloppsändring via en [supportbiljett.]( https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)


> [!NOTE]
> Sekundärt index stöds inte på följande objekt:
> - datatyper som frysta insamlingstyper, decimaler och varianttyper.
> - Statiska kolumner
> - Klusternycklar

## <a name="indexing-example"></a>Exempel på indexering

Skapa först ett exempel på nyckelutrymme och tabell genom att köra följande kommandon i CQL-skalprompten:

```shell
CREATE KEYSPACE sampleks WITH REPLICATION = {'class' : 'SimpleStrategy'};
CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=400;
``` 

Infoga sedan exempelanvändardata med följande kommandon:

```shell
insert into sampleks.t1(user_id,lastname) values (1, 'nishu');
insert into sampleks.t1(user_id,lastname) values (2, 'vinod');
insert into sampleks.t1(user_id,lastname) values (3, 'bat');
insert into sampleks.t1(user_id,lastname) values (5, 'vivek');
insert into sampleks.t1(user_id,lastname) values (6, 'siddhesh');
insert into sampleks.t1(user_id,lastname) values (7, 'akash');
insert into sampleks.t1(user_id,lastname) values (8, 'Theo');
insert into sampleks.t1(user_id,lastname) values (9, 'jagan');
```

Om du försöker köra följande utdrag kommer du att stöta `ALLOW FILTERING`på ett fel som ber dig att använda: 

```shell
select user_id, lastname from sampleks.t1 where lastname='nishu';
``` 

Även om Cassandra API stöder TILLÅT FILTRERING, som nämns i föregående avsnitt, rekommenderas det inte. Du bör i stället skapa ett index i följande exempel:

```shell
CREATE INDEX ON sampleks.t1 (lastname);
```
När du har skapat ett index i fältet "efternamn" kan du nu köra den föregående frågan. Med Cassandra API i Azure Cosmos DB behöver du inte ange ett indexnamn. Ett standardindex `tablename_columnname_idx` med format används. Är till ` t1_lastname_idx` exempel indexnamnet för föregående tabell.

## <a name="dropping-the-index"></a>Släppa indexet 
Du måste veta vad indexnamnet är för att släppa indexet. Kör `desc schema` kommandot för att få en beskrivning av tabellen. Utdata för det här kommandot innehåller `CREATE INDEX tablename_columnname_idx ON keyspacename.tablename(columnname)`indexnamnet i formatet . Du kan sedan använda indexnamnet för att släppa indexet enligt följande exempel:

```shell
drop index sampleks.t1_lastname_idx;
```

## <a name="next-steps"></a>Nästa steg
* Lär dig hur [automatisk indexering](index-overview.md) fungerar i Azure Cosmos DB
* [Apache Cassandra-funktioner som stöds av Azure Cosmos DB Cassandra-API](cassandra-support.md)
