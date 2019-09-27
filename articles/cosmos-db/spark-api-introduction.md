---
title: Introduktion till den inbyggda operativa analysen i Azure Cosmos DB med Apache Spark
description: Lär dig hur du kan använda det inbyggda stödet för Apache Spark i Azure Cosmos DB för att köra operativa analyser och AI
ms.service: cosmos-db
ms.topic: overview
ms.date: 09/26/2019
author: rimman
ms.author: rimman
ms.openlocfilehash: 952dd084bccd67a0a8833002d73d3aaf1d5dfb25
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338612"
---
# <a name="built-in-operational-analytics-in-azure-cosmos-db-with-apache-spark"></a>Inbyggd drift analys i Azure Cosmos DB med Apache Spark

Med Azure Cosmos DB kan du köra globalt distribuerad analys med låg latens och AI på transaktions data. Med inbyggt stöd för Apache Spark-och Jupyter-anteckningsböcker kan Azure Cosmos DB minska tiden till insikter. Eftersom data matas in, betjänas och analys körs mot den lokala databas repliken i en Azure-region. Du kan direkt köra Apache Spark frågor om indexerade data för flera modeller som lagras i dina datapartitioner.

Det inbyggda stödet för Apache Spark i Azure Cosmos DB gör att du kan köra analyser från Apache Spark mot data som lagras i ett Azure Cosmos-konto. Det innehåller inbyggt stöd för Apache Spark jobb som ska köras direkt i dina globalt distribuerade Cosmos-databaser. Med dessa funktioner kan utvecklare, data tekniker och data experter använda Azure Cosmos DB som en flexibel, skalbar och presterande data plattform för att köra både OLTP- **och OLAP/HTAP-** arbetsbelastningar.

Apache Spark support i Azure Cosmos DB ger följande fördelar:

* Du får snabbast tid att insikter om geografiskt distribuerade användare och data.

* Du kan förenkla arkitekturen för din lösning och sänka den [totala ägande kostnaden](total-cost-ownership.md) (TCO). Systemet kommer att ha minst antal data bearbetnings komponenter och undvika onödig data förflyttning mellan dem.

* Skapar en [säkerhets](secure-access-to-data.md)-, [efterlevnads](compliance.md)-och gransknings gränser som omfattar alla data under hantering.

* Tillhandahåller "Always On" eller [hög tillgängliga](high-availability.md) slut användar analys som backas upp av stränga service avtal.

![Apache Spark stöd i Azure Cosmos DB visualisering](./media/spark-api-introduction/spark-api-visualization.png)
 
Med hjälp av Apache Spark support i Azure Cosmos DB kan du bygga och distribuera lösningar som AI-och djup inlärnings modeller, förutsägelse analyser, rekommendationer, IoT, kund 360, bedrägeri identifiering, text sentiment, klick Ströms analys. Dessa lösningar fungerar direkt mot dina Azure Cosmos DB data.

Du kan ställa in batch-och strömmande ETL-jobb i Azure Cosmos DB, utan att behöva gå utanför databas tjänsten eller lägga till ytterligare beräknings tjänster. Du kan skala beräknings miljön elastiskt när du behöver utföra ETL-jobb och skala tillbaka det när jobbet är färdigt.

Apache Spark-stödet i Azure Cosmos DB erbjuder inbyggt Machine Learning stöd i Apache Spark körnings miljön. Körningarna inkluderar Spark MLLib, Microsoft Machine Learning för Spark, Azure Machine Learning och Cognitive Services. Med dessa funktioner kan data experter, data tekniker och data analytiker bygga och operationalisera maskin inlärnings modeller direkt inom Azure Cosmos DB, i en tids period och med låg kostnad.


## <a name="key-benefits"></a>Viktiga fördelar

### <a name="low-latency-operational-analytics-and-ai"></a>Drift analys och AI med låg latens

Med Apache Spark i den globalt distribuerade Azure Cosmos-databasen kan du nu få snabbt tid till insikt över hela världen. Azure Cosmos DB möjliggör **globalt distribuerad drift analys med låg latens** i elastisk skala med tre viktiga tekniker:

* Eftersom din Azure Cosmos-databas distribueras globalt, matas alla data in lokalt där data producenter (till exempel användare) finns. Frågorna betjänas mot de lokala replikerna närmast både producenter och data konsumenter oavsett var de befinner sig i världen.

* Alla analytiska frågor körs direkt på de indexerade data som lagras i datapartitionerna utan att onödig data förflyttning krävs.

* Eftersom Spark är Samplacerat med Azure Cosmos DB, sker färre mellanliggande översättningar och data förflyttning, vilket ger bättre prestanda och skalbarhet.

* Alla Azure Cosmos DB huvud funktioner, till exempel multi-master, automatisk redundans, Tillgänglighetszoner o.s.v., är tillgängliga för inbyggda Apache Spark i Azure Cosmos DB.

### <a name="unified-serverless-experience-for-apache-spark"></a>Enhetlig Server lös upplevelse för Apache Spark

Som en databas med flera modeller expanderar Azure Cosmos DB nu stödet för OSS API: er genom att tillhandahålla en **enhetlig Server lös upplevelse för Apache Spark** med nyckel värdes-, dokument-, graf-och kolumn serie data modeller. Olika data modeller stöds med MongoDB, Cassandra, Gremlin, Etcd och SQL-API: er – allt körs på samma underliggande data. 

Med Apache Spark stöd i Azure Cosmos DB kan du internt stödja program som skrivits i Scala, python, Java och använda flera nära integrerade bibliotek för SQL. Dessa bibliotek inkluderar ([Spark SQL](https://spark.apache.org/sql/)), Machine Learning (Spark- [MLlib](https://spark.apache.org/mllib/)), Stream-bearbetning ([Spark-strukturerad strömning](https://spark.apache.org/streaming/)) och diagram bearbetning (Spark [GraphFrames]( https://docs.databricks.com/spark/latest/graph-analysis/graphframes/user-guide-python.html)). De här verktygen gör det enklare att använda Apache Spark i en mängd olika användnings fall. Du behöver inte hantera Spark-eller Spark-kluster. Du kan använda välkända Apache Spark-API: er och **Jupyter-anteckningsböcker** för analys-och SQL API eller någon av dina oss NoSQL-API: er som Cassandra för transaktions bearbetning på samma underliggande data på samma tid.

### <a name="no-schema-or-index-management"></a>Ingen hantering av schema eller index

Till skillnad från traditionella analys databaser, med Azure Cosmos DB, data tekniker och data experter, behöver inte längre hantera komplicerade schema-och index hantering. Databas motorn i Azure Cosmos DB kräver inte någon explicit schema-eller index hantering, och den kan automatiskt indexera alla data som matas in för att snabbt hantera Apache Sparks frågor.

### <a name="consistency-choices"></a>Konsekvens alternativ

Eftersom Apache Spark jobb körs i datapartitionerna i din Azure Cosmos-databas, får frågorna de [fem väldefinierade konsekvens alternativen](consistency-levels.md). Dessa konsekvens modeller ger flexibiliteten att välja strikt konsekvens för att tillhandahålla de mest exakta resultaten för Machine Learning-algoritmer utan att kompromissa med svars tiden och hög tillgänglighet.

### <a name="comprehensive-slas"></a>Omfattande serviceavtal

De Apache Spark jobben har de Azure Cosmos DB fördelarna, till exempel branschledande omfattande [service avtal](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) (99,999) utan att det går att hantera separata Apache Spark-kluster. Dessa service avtal omfattar data flöde, svars tid vid 99 percentil, konsekvens och hög tillgänglighet. 

### <a name="mixed-operational-and-analytical-workloads-with-complete-isolation"></a>Blandad drift och analytiska arbets belastningar med fullständig isolering

Integreringen av Apache Spark i Azure Cosmos DB bryggor av transaktions-och analys separationen, som har varit en av de stora kunderna som är en av de viktigaste kunderna när de skapar molnbaserade program i global skala. OLTP-och OLAP-arbetsbelastningarna körs sida vid sida och stör inte varandra.

### <a name="low-latency-analytical-and-transactional-storage"></a>Analytisk och transaktionell lagring med låg latens

Azure Cosmos DB lagrar data internt i två distinkta lagrings nivåer: transaktionell (rad orienterad) och analytisk lagring (kolumn-orienterad i Apache Parquet-filformat). Den replikerar data på varje nivå globalt och gör det möjligt för användare att självständigt hantera data i dessa nivåer baserat på bevarande principer.

Den här lagrings arkitekturen möjliggör både verksamhets kritiska transaktions-och analys arbets belastningar på samma globalt distribuerade data. Med Cosmos DB behöver du inte några ETL-åtgärder eller utföra onödig data förflyttning. Du kan helt enkelt köra både transaktions-och analys arbets belastningar på samma underliggande data. Transaktions arbets belastningar kan använda välbekanta API: er för transaktionella åtkomst, inklusive SQL, Cassandra, MongoDB, Gremlin och Etcd. Analys-och AI-arbetsbelastningar kan använda inbyggda Apache Spark SQL, ML-ramverk och hela Apache Spark verktygskedjan.

### <a name="snapshots-and-historical-analytical-queries"></a>Ögonblicks bilder och historiska analys frågor

Du kan skapa ögonblicks bilder på begäran eller schemalagda ögonblicks bilder av kolumnerna som är lagrade på analys nivån för att utföra analytiska frågor direkt mot en angiven ögonblicks bild. Med den här funktionen kan du använda analytiska frågor för Flashback eller tids resa, återställningar, fullständiga historiska spårnings spårningar och återanvända Machine Learning och AI-experiment.

## <a name="next-steps"></a>Nästa steg

* Mer information om fördelarna med Azure Cosmos DB finns i [översikts](introduction.md) artikeln.
* [Kom igång med Azure Cosmos DB API för MongoDB](mongodb-introduction.md)
* [Kom igång med Azure Cosmos DB API för Cassandra](cassandra-introduction.md)
* [Kom igång med API: et för Azure Cosmos DB Gremlin](graph-introduction.md)
* [Kom igång med Azure Cosmos DB Tabell-API](table-introduction.md)