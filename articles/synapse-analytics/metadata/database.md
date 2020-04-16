---
title: Delad databas med Azure Synapse Analytics
description: Azure Synapse Analytics tillhandahåller en delad metadatamodell där skapandet av en databas i Apache Spark gör den tillgänglig från sina SQL on-demand (förhandsversion) och SQL-poolmotorer.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: e3651467de86d3b026ab348675249f93ebf3a86a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424148"
---
# <a name="azure-synapse-analytics-shared-database"></a>Delad databas med Azure Synapse Analytics

Azure Synapse Analytics gör det möjligt för de olika beräkningsarbetsytamotorerna att dela databaser och tabeller mellan dess Spark-pooler (förhandsversion), SQL on-demand-motor (förhandsversion) och SQL-pooler.

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

En databas som skapas med ett Spark-jobb blir synlig med samma namn för alla aktuella och framtida Spark-pooler (förhandsversion) på arbetsytan samt SQL on-demand-motorn.

Om det finns SQL-pooler på arbetsytan som har metadatasynkronisering aktiverat, eller om du skapar en ny SQL-pool med metadatasynkroniseringen aktiverad, mappas dessa Spark-skapade databaser automatiskt till särskilda scheman i SQL-pooldatabasen. 

Varje schema är uppkallat efter `$` Spark-databasnamnet med ytterligare ett prefix. Både externa och hanterade tabeller i den Spark-genererade databasen visas som externa tabeller i motsvarande specialschema.

Spark-standarddatabasen, `default`som kallas , visas också i SQL on-demand-kontexten som en databas som kallas `default`, och `$default`i någon av SQL-pooldatabaserna med metadatasynkronisering aktiverad som schemat .

Eftersom databaserna synkroniseras med SQL on-demand och SQL-poolerna asynkront, kommer det att finnas en fördröjning tills de visas.

## <a name="manage-a-spark-created-database"></a>Hantera en skapad spark-databas

Använd Spark för att hantera Spark-skapade databaser. Ta till exempel bort det via ett Spark-pooljobb och skapa tabeller i det från Spark.

Om du skapar objekt i en Spark-skapad databas med SQL på begäran, eller försöker släppa databasen, lyckas åtgärden. Men den ursprungliga Spark-databasen ändras inte.

Om du försöker släppa det synkroniserade schemat i en SQL-pool eller försöker skapa en tabell i den returnerar Azure ett fel.

## <a name="handling-of-name-conflicts"></a>Hantering av namnkonflikter

Om namnet på en Spark-databas står i konflikt med namnet på en befintlig SQL-databas på begäran läggs ett suffix till i SQL on-demand till Spark-databasen. Suffixet i SQL on-demand är `_<workspace name>-ondemand-DefaultSparkConnector`.

Om till exempel en `mydb` Spark-databas som heter skapas `myws` i Azure Synapse-arbetsytan och en SQL-databas på begäran med det namnet redan `mydb_myws-ondemand-DefaultSparkConnector`finns, måste Spark-databasen i SQL on-demand refereras med namnet .

> [!CAUTION]
> Varning: Du bör inte vara beroende av det här beteendet.

## <a name="security-model"></a>Säkerhetsmodell

Spark-databaserna och tabellerna, tillsammans med deras synkroniserade representationer i SQL-motorerna, kommer att skyddas på den underliggande lagringsnivån.

Säkerhetsobjektet som skapar en databas anses vara ägare till databasen och har alla rättigheter till databasen och dess objekt.

Om du vill ge ett säkerhetsobjekt, till exempel en användare eller en säkerhetsgrupp, åtkomst till en databas, ange `warehouse` lämpliga POSIX-mappar och filbehörigheter till de underliggande mapparna och filerna i katalogen. 

För att ett säkerhetsobjekt till exempel ska kunna läsa en tabell i en databas `warehouse` måste alla `X` `R` mappar som börjar vid databasmappen i katalogen ha och behörigheter som tilldelats säkerhetsobjektet. Dessutom kräver `R` filer (till exempel tabellens underliggande datafiler) behörigheter. 

Om ett säkerhetsobjekt kräver möjligheten att skapa objekt `W` eller släppa objekt i en databas `warehouse` krävs ytterligare behörigheter för mapparna och filerna i mappen.

## <a name="examples"></a>Exempel

### <a name="create--connect-to-spark-database---sql-on-demand"></a>Skapa & ansluta till Spark-databasen - SQL on-demand

Skapa först en ny `mytestdb` Spark-databas med namnet Spark-kluster som du redan har skapat på arbetsytan. Du kan till exempel uppnå detta med hjälp av en Spark C#-anteckningsbok med följande .NET för Spark-sats:

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Efter en kort fördröjning kan du se databasen från SQL on-demand. Kör till exempel följande uttryck från SQL on-demand.

```sql
SELECT * FROM sys.databases;
```

Kontrollera `mytestdb` att det ingår i resultatet.

### <a name="exposing-a-spark-database-in-a-sql-pool"></a>Exponera en Spark-databas i en SQL-pool

När databasen har skapats i föregående exempel skapar du `mysqlpool` nu en SQL-pool på arbetsytan som möjliggör metadatasynkronisering.

Kör följande uttryck `mysqlpool` mot SQL-poolen:

```sql
SELECT * FROM sys.schema;
```

Verifiera schemat för den nyskapade databasen i resultatet.

## <a name="next-steps"></a>Nästa steg

- [Läs mer om Azure Synapse Analytics delade metadata](overview.md)
- [Läs mer om Azure Synapse Analytics delade metadatatabeller](table.md)

<!-- - [Learn more about the Synchronization with SQL Analytics on-demand](overview.md)
- [Learn more about the Synchronization with SQL Analytics pools](overview.md)-->
