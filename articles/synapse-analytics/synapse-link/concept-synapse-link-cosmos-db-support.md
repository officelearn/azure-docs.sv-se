---
title: Azure Synapse-länk (för hands version) för Azure Cosmos DB funktioner som stöds
description: Förstå den aktuella listan med åtgärder som stöds av Azure Synapse-länken för Azure Cosmos DB
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: synapse-link
ms.date: 09/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 3d12e60e2aeee667b89d56589870d53366772014
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459047"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-supported-features"></a>Funktioner som stöds för Azure Synapse Link för Azure Cosmos DB

I den här artikeln beskrivs funktionerna som för närvarande stöds i Azure Synapse Link för Azure Cosmos DB.

## <a name="azure-synapse-support"></a>Stöd för Azure Synapse

Det finns två typer av behållare i Azure Cosmos DB:
* HTAP container – en behållare med Synapse-länk aktive rad. Den här behållaren har både transaktions lager och analys lager. 
* OLTP-behållare – en behållare med Synaspe-länk är inte aktive rad. Den här behållaren har endast transaktions lager och inget analytiskt lager.

> [!IMPORTANT]
> Azure Synapse-länken för Azure Cosmos DB stöds för närvarande i Synapse-arbetsytor som inte har hanterat virtuellt nätverk aktiverat. 

Du kan ansluta till en Azure Cosmos DB-behållare utan att aktivera Synapse-länken. I det här scenariot kan du bara läsa/skriva till transaktions arkivet. Nedan visas en lista över de funktioner som stöds för närvarande i Synapse-länken för Azure Cosmos DB. 

| Kategori              | Beskrivning |[Apache Spark pool](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) | [Serverlös SQL-pool](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- |
| **Stöd för körning** |Azure Synapse runtime som stöds för att få åtkomst till Azure Cosmos DB| ✓ | Förhandsgranskning |
| **Stöd för Azure Cosmos DB-API** | Azure Cosmos DB API-typ som stöds | SQL/MongoDB | SQL/MongoDB |
| **Jobbobjektet**  |Objekt som en tabell som kan skapas, peka direkt på Azure Cosmos DB behållare| Dataframe, Visa, tabell | Visa |
| **Läs**    | Typ av Azure Cosmos DB behållare som kan läsas | OLTP/HTAP | HTAP  |
| **Skriva**   | Kan Azure Synapse-körningsmiljön användas för att skriva data till en Azure Cosmos DB behållare | Ja | Inga |

* Om du skriver data till en Azure Cosmos DB behållare från Spark sker den här processen genom transaktions arkivet för Azure Cosmos DB. Den påverkar transaktions prestandan för Azure Cosmos DB genom att förbruka enheter för programbegäran.
* Dedikerad integrering av SQL-pooler via externa tabeller stöds inte för närvarande.
 
## <a name="supported-code-generated-actions-for-spark"></a>Kod genererade åtgärder som stöds för Spark

| Gest              | Description |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Läs in till DataFrame** |Läsa in och läsa data i en spark-DataFrame |✓| ✓ |
| **Skapa Spark-tabell** |Skapa en tabell som pekar på en Azure Cosmos DB behållare|✓| ✓ |
| **Skriv DataFrame till container** |Skriva data till en behållare|✓| ✓ |
| **Läs in strömmande DataFrame från behållare** |Strömma data med Azure Cosmos DB ändra feed|✓| ✓ |
| **Skriv strömmande DataFrame till behållare** |Strömma data med Azure Cosmos DB ändra feed|✓| ✓ |


## <a name="supported-code-generated-actions-for-serverless-sql-pool"></a>Kod genererade åtgärder för SQL-poolen utan Server

| Gest              | Description |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Utforska data** |Utforska data från en behållare med välbekant T-SQL-syntax och automatisk schema härledning|X| ✓ |
| **Skapa vyer och skapa BI-rapporter** |Skapa en SQL-vy för att få direkt åtkomst till en behållare för BI via server lös SQL-pool |X| ✓ |
| **Koppla ihop olika data källor tillsammans med Cosmos DB data** | Lagra resultat från fråga läsa data från Cosmos DB behållare tillsammans med data i Azure Blob Storage eller Azure Data Lake Storage med CETAS |X| ✓ |

## <a name="next-steps"></a>Nästa steg

* Se hur du [ansluter till Synapse-länken för Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
* [Lär dig hur du frågar Cosmos DB Analytical Store med Spark](how-to-query-analytical-store-spark.md)
