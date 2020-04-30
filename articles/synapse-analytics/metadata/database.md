---
title: Delad Azure Synapse Analytics-databas
description: Azure Synapse Analytics tillhandahåller en delad metadata modell där du skapar en databas i Apache Spark, vilket gör den tillgänglig från SQL on-demand (för hands version) och SQL-poolens motorer.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: e3651467de86d3b026ab348675249f93ebf3a86a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424148"
---
# <a name="azure-synapse-analytics-shared-database"></a>Delad Azure Synapse Analytics-databas

Med Azure Synapse Analytics kan olika data behandlings arbets Ytors motorer dela databaser och tabeller mellan dess Spark-pooler (för hands version), motorn SQL på begäran (för hands version) och SQL-pooler.

[!INCLUDE [synapse-analytics-preview-terms](../../../includes/synapse-analytics-preview-terms.md)]

En databas som skapats med ett Spark-jobb blir synlig med samma namn på alla aktuella och framtida Spark-pooler (för hands version) i arbets ytan samt SQL-motorn på begäran.

Om det finns SQL-pooler i arbets ytan där metadata-synkronisering är aktiverat, eller om du skapar en ny SQL-pool där metadata-synkronisering är aktiverat, mappas de Spark-skapade databaserna automatiskt till särskilda scheman i SQL-adresspoolen. 

Varje schema har namnet efter Spark-databasen med ett ytterligare `$` prefix. Både de externa och hanterade tabellerna i den Spark-genererade databasen visas som externa tabeller i motsvarande särskilda schema.

Spark-standarddatabasen, `default`som anropas, visas också i SQL-kontexten på begäran som en databas `default`som kallas, och i någon av SQL-poolens databaser där metadata-synkronisering är `$default`aktiverat som schema.

Eftersom databaserna synkroniseras till SQL på begäran och SQL-pooler asynkront, kommer det att finnas en fördröjning tills de visas.

## <a name="manage-a-spark-created-database"></a>Hantera en spark-skapad databas

Använd Spark för att hantera Spark-skapade databaser. Du kan t. ex. ta bort den via ett Spark-jobb och skapa tabeller i det från Spark.

Om du skapar objekt i en spark-databas som skapats med SQL på begäran, eller om du försöker släppa databasen, kommer åtgärden att lyckas. Men den ursprungliga Spark-databasen ändras inte.

Om du försöker ta bort det synkroniserade schemat i en SQL-pool eller försöker skapa en tabell i den, returnerar Azure ett fel.

## <a name="handling-of-name-conflicts"></a>Hantering av namn konflikter

Om namnet på en spark-databas står i konflikt med namnet på en befintlig SQL-databas på begäran, läggs ett suffix till i SQL på begäran till Spark-databasen. Suffixet i SQL på begäran är `_<workspace name>-ondemand-DefaultSparkConnector`.

Om t. ex. en spark- `mydb` databas som heter skapas i Azure dataSynapses-arbetsytan `myws` och det redan finns en SQL-databas på begäran med det namnet, måste Spark-databasen i SQL på begäran refereras till med `mydb_myws-ondemand-DefaultSparkConnector`namnet.

> [!CAUTION]
> Varning: du bör inte ta ett beroende på detta beteende.

## <a name="security-model"></a>Säkerhetsmodell

Spark-databaser och-tabeller, tillsammans med deras synkroniserade representationer i SQL-motorerna, kommer att skyddas på den underliggande lagrings nivån.

Det säkerhets objekt som skapar en databas anses vara ägare till databasen och har alla rättigheter till databasen och dess objekt.

Om du vill ge ett säkerhets objekt, till exempel en användare eller en säkerhets grupp, åtkomst till en databas, anger du rätt POSIX-mapp och fil behörigheter för de underliggande mapparna `warehouse` och filerna i katalogen. 

För att ett säkerhets objekt ska kunna läsa en tabell i en databas måste till exempel alla mappar som börjar på databasmappen i `warehouse` katalogen ha `X` och `R` behörighet som tilldelas till säkerhets objekt. Dessutom kräver `R` filer (till exempel tabellens underliggande datafiler) behörigheter. 

Om ett säkerhets objekt kräver möjlighet att skapa objekt eller släppa objekt i en databas, krävs ytterligare `W` behörigheter för mapparna och filerna i `warehouse` mappen.

## <a name="examples"></a>Exempel

### <a name="create--connect-to-spark-database---sql-on-demand"></a>Skapa & ansluta till Spark Database-SQL på begäran

Skapa först en ny Spark-databas `mytestdb` som heter med ett Spark-kluster som du redan har skapat i din arbets yta. Du kan till exempel använda en spark C#-anteckningsbok med följande .NET for Spark-instruktion:

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Efter en kort fördröjning kan du se databasen från SQL på begäran. Kör till exempel följande uttryck från SQL på begäran.

```sql
SELECT * FROM sys.databases;
```

Kontrol lera `mytestdb` att ingår i resultaten.

### <a name="exposing-a-spark-database-in-a-sql-pool"></a>Exponera en spark-databas i en SQL-pool

Med databasen som skapades i föregående exempel skapar du nu en SQL-pool på arbets ytan med `mysqlpool` namnet som aktiverar synkronisering av metadata.

Kör följande instruktion mot `mysqlpool` SQL-poolen:

```sql
SELECT * FROM sys.schema;
```

Verifiera schemat för den nyligen skapade databasen i resultaten.

## <a name="next-steps"></a>Nästa steg

- [Läs mer om delade metadata för Azure Synapse Analytics](overview.md)
- [Läs mer om delade metadata-tabeller för Azure Synapse Analytics](table.md)

<!-- - [Learn more about the Synchronization with SQL Analytics on-demand](overview.md)
- [Learn more about the Synchronization with SQL Analytics pools](overview.md)-->
