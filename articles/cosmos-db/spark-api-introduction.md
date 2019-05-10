---
title: Introduktion till den inbyggda operativ analys i Azure Cosmos DB med Apache Spark
description: Lär dig hur du kan använda det inbyggda stödet för Apache Spark i Azure Cosmos DB för att köra operativ analys och AI
ms.service: cosmos-db
ms.topic: overview
ms.date: 05/06/2019
author: rimman
ms.author: rimman
ms.openlocfilehash: 504f463f55edf1a22f99e1ba02449121e8bf5efc
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472265"
---
# <a name="built-in-operational-analytics-in-azure-cosmos-db-with-apache-spark-preview"></a>Inbyggda operativ analys i Azure Cosmos DB med Apache Spark (förhandsversion) 

Inbyggt stöd för Apache Spark i Azure Cosmos DB kan du köra analyser från Apache Spark mot dina data som lagras i ett Azure Cosmos-konto. Den innehåller inbyggt stöd för Apache Spark-jobb att köra direkt på din globalt distribuerade Cosmos-databaser. Med dessa funktioner kan utvecklare, dataingenjörer och datatekniker kan använda Azure Cosmos DB som en flexibla, skalbara och högpresterande dataplattformen för att köra båda **OLTP och OLAP/HTAP** arbetsbelastningar. 

> [!NOTE]
> Inbyggt stöd för Apache Spark i Azure Cosmos DB är för närvarande i en begränsad förhandsversion. Gå till att registrera dig för förhandsversionen [registrera dig för förhandsversionen av](https://portal.azure.com/?feature.customportal=false#create/Microsoft.DocumentDB) sidan. 

Stöd för Apache Spark i Azure Cosmos DB erbjuder följande fördelar:

* Du kan hämta snabbaste tiden till insikter för geografiskt distribuerade användare och data.

* Du kan förenkla arkitekturen för din lösning och lägre den [totalkostnad](total-cost-ownership.md) (TCO). Systemet har minsta möjliga antal komponenter för databearbetning och på så sätt undviker eventuella onödiga dataförflyttning mellan dem.

* Skapar en [security](secure-access-to-data.md), [efterlevnad](compliance.md), och granskning gräns som omfattar alla data under hantering.

* Innehåller ”alltid på” eller [med hög tillgänglighet](high-availability.md) slutanvändarens analytics som backas upp av strikta serviceavtal.

![Stöd för Apache Spark i Azure Cosmos DB-visualisering](./media/spark-api-introduction/spark-api-visualization.png)
 
Med stöd för Apache Spark i Azure Cosmos DB kan du skapa och distribuera lösningar, till exempel AI och djupinlärning modeller, förutsägande analys, rekommendationer, IoT, customer 360, bedrägerier, text sentiment, klickströmsanalys. Dessa lösningar fungerar direkt mot ditt Azure Cosmos DB-data.

Du kan konfigurera batch och strömning ETL-jobb i Azure Cosmos DB utan att behöva gå utanför databastjänsten eller lägga till ytterligare tjänster för compute. Du kan Elastiskt skala compute-miljö när du behöver utföra ETL-jobb och skalar tillbaka ned när jobbet är klart.

Stöd för Apache Spark i Azure Cosmos DB erbjuder inbyggt stöd för Machine Learning i Apache Spark-körningar. Körningar omfattar Spark MLLib, Microsoft Machine Learning för Spark, Azure Machine Learning och Cognitive Services. Med dessa funktioner, kan datatekniker, dataingenjörer och dataanalytiker skapa och operationalisera maskininlärningsmodeller direkt i Azure Cosmos DB, på en bråkdel med låg kostnad.


## <a name="key-benefits"></a>Viktiga fördelar

### <a name="globally-distributed-low-latency-operational-analytics-and-ai"></a>Globalt distribuerad, Låg fördröjning operativ analys och AI

Med Apache Spark för globalt distribuerade Azure Cosmos-databasen får du nu alla snabb tid förståelse runt om i världen. Azure Cosmos DB kan **globalt distribuerade, Låg fördröjning verksamhetsanalyser** med elastisk skala med tre viktiga tekniker:

* Eftersom Azure Cosmos-databasen distribueras globalt, alla data som matas in lokalt där producenter av data (till exempel användare) finns. Frågorna hanteras mot de lokala replikerna som är närmast både producenterna och konsumenter av data, oavsett var de befinner sig i världen. 

* Analytiska frågor körs direkt på indexerade data som lagras i data-partitionerna utan alla onödiga dataförflyttning. 

* Eftersom Spark är samordnat med Azure Cosmos DB, färre mellanliggande översättningar och dataflyttningar ske, vilket ger en bättre prestanda och skalbarhet.

### <a name="unified-serverless-experience-for-apache-spark"></a>Enhetlig serverlös miljö för Apache Spark

Som en databas för flera modeller, Azure Cosmos DB nu utökar stödet för OSS API: er genom att tillhandahålla en **enhetlig serverlös miljö för Apache Spark** med nyckel / värde, dokument, diagram, kolumnen family datamodeller. Olika datamodeller stöds med MongoDB, Cassandra, Gremlin, Etcd och SQL API: er – alla fungerar på samma underliggande data. 

Med stöd för Apache Spark i Azure Cosmos DB kan du internt stöd för program som skrivits i Scala, Python, Java och använder flera tätt integrerad bibliotek för SQL. Inkludera dessa bibliotek ([Spark SQL](https://spark.apache.org/sql/)), machine learning-(Spark [MLlib](https://spark.apache.org/mllib/)), strömbearbetning ([Spark Structured Streaming](https://spark.apache.org/streaming/)), och bearbeta diagram (Spark [GraphFrames]( https://docs.databricks.com/spark/latest/graph-analysis/graphframes/user-guide-python.html)). Dessa verktyg gör det enklare att använda Apache Spark för en mängd olika användningsområden. Du behöver hantera hantera Spark eller Spark-kluster. Du kan använda välbekanta Apache Spark API och **Jupyter-anteckningsböcker** för analys och SQL API eller någon av OSS NoSQL APIs som Cassandra för transaktionsbearbetning på samma underliggande data på samma gång.

### <a name="no-schema-or-index-management"></a>Ingen hantering av schema eller index

Till skillnad från traditionella analytiska databaser med Azure Cosmos DB, behöver dataingenjörer och datatekniker inte längre behöver bry dig om besvärlig schema- och indexhantering. Databasmotorn i Azure Cosmos DB kräver inte någon explicit schema eller index hantering och är kapabel att automatiskt indexera alla data den tar in för att leverera Apache Spark-frågor snabbt. 

### <a name="consistency-choices"></a>Konsekvensval

Eftersom Apache Spark-jobb körs i data-partitionerna i din Azure Cosmos-databas, frågorna får den [fem väldefinierade konsekvensval](consistency-levels.md). Dessa konsekvensmodeller ger flexibilitet att välja strikt konsekvens att tillhandahålla bästa resultat för machine learning-algoritmer utan att kompromissa med fördröjning och hög tillgänglighet. 

### <a name="slas"></a>Serviceavtal

Apache Spark-jobb har Azure Cosmos DB-fördelar som branschledande, omfattande [serviceavtal](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) (99,999) utan någon arbetet med att hantera separata Apache Spark-kluster... Dessa serviceavtal omfatta genomflöde, svarstid på 99: e percentilen, konsekvens och hög tillgänglighet. 

### <a name="mixed-workloads"></a>Blandade arbetsbelastningar

Integration av Apache Spark i Azure Cosmos DB bryggor transaktions- och analytiska-åtskillnad, som har en större kunden stötestenar när du bygger molnprogram i global skala. 

## <a name="next-steps"></a>Nästa steg

* Läs om fördelarna med Azure Cosmos DB i den [översikt](introduction.md) artikeln.
* [Kom igång med Azure Cosmos DB-API:n för Azure MongoDB](mongodb-introduction.md)
* [Kom igång med Cassandra-API för Azure Cosmos DB](cassandra-introduction.md)
* [Komma igång med Gremlin API för Azure Cosmos DB](graph-introduction.md)
* [Komma igång med Tabell-API för Azure Cosmos DB](table-introduction.md)




