---
title: Introduktion till den inbyggda operativa analysen i Azure Cosmos DB med Apache Spark
description: Lär dig hur du kan använda det inbyggda stödet för Apache Spark i Azure Cosmos DB för att köra operativa analyser och AI
ms.service: cosmos-db
ms.topic: overview
ms.date: 08/01/2019
author: rimman
ms.author: rimman
ms.openlocfilehash: 0f070cb9a6e300dad0ec9e0b393b09b7f22d2942
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212591"
---
# <a name="built-in-operational-analytics-in-azure-cosmos-db-with-apache-spark-preview"></a>Inbyggd drift analys i Azure Cosmos DB med Apache Spark (förhands granskning) 

Det inbyggda stödet för Apache Spark i Azure Cosmos DB gör att du kan köra analyser från Apache Spark mot data som lagras i ett Azure Cosmos-konto. Det innehåller inbyggt stöd för Apache Spark jobb som ska köras direkt i dina globalt distribuerade Cosmos-databaser. Med dessa funktioner kan utvecklare, data tekniker och data experter använda Azure Cosmos DB som en flexibel, skalbar och presterande data plattform för att köra både OLTP- **och OLAP/HTAP-** arbetsbelastningar. 

Spark Compute är automatiskt tillgängligt i alla Azure-regioner som är kopplade till ditt Azure Cosmos-konto. Spark-jobb använder Azure Cosmos DBens funktioner för flera huvud servrar och de kan skriva eller fråga mot de lokala replikerna i varje region. 

> [!NOTE]
> Det inbyggda stödet för Apache Spark i Azure Cosmos DB är för närvarande en begränsad för hands version. Om du vill registrera dig för för hands versionen navigerar du till [registrering för sidan för förhands granskning](https://portal.azure.com/?feature.customportal=false#create/Microsoft.DocumentDB). 

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

### <a name="globally-distributed-low-latency-operational-analytics-and-ai"></a>Globalt distribuerad drift analys och AI med låg latens

Med Apache Spark i den globalt distribuerade Azure Cosmos-databasen kan du nu få snabbt tid till insikt över hela världen. Azure Cosmos DB möjliggör **globalt distribuerad drift analys med låg latens** i elastisk skala med tre viktiga tekniker:

* Eftersom din Azure Cosmos-databas distribueras globalt, matas alla data in lokalt där data producenter (till exempel användare) finns. Frågorna betjänas mot de lokala replikerna närmast både producenter och data konsumenter oavsett var de befinner sig i världen. 

* Alla analytiska frågor körs direkt på de indexerade data som lagras i datapartitionerna utan att onödig data förflyttning krävs. 

* Eftersom Spark är Samplacerat med Azure Cosmos DB, sker färre mellanliggande översättningar och data förflyttning, vilket ger bättre prestanda och skalbarhet.

### <a name="unified-serverless-experience-for-apache-spark"></a>Enhetlig Server lös upplevelse för Apache Spark

Som en databas med flera modeller expanderar Azure Cosmos DB nu stödet för OSS API: er genom att tillhandahålla en **enhetlig Server lös upplevelse för Apache Spark** med nyckel värdes-, dokument-, graf-och kolumn serie data modeller. Olika data modeller stöds med MongoDB, Cassandra, Gremlin, Etcd och SQL-API: er – allt körs på samma underliggande data. 

Med Apache Spark stöd i Azure Cosmos DB kan du internt stödja program som skrivits i Scala, python, Java och använda flera nära integrerade bibliotek för SQL. Dessa bibliotek inkluderar ([Spark SQL](https://spark.apache.org/sql/)), Machine Learning (Spark- [MLlib](https://spark.apache.org/mllib/)), Stream-bearbetning ([Spark-strukturerad strömning](https://spark.apache.org/streaming/)) och diagram bearbetning (Spark [GraphFrames]( https://docs.databricks.com/spark/latest/graph-analysis/graphframes/user-guide-python.html)). De här verktygen gör det enklare att använda Apache Spark i en mängd olika användnings fall. Du behöver inte hantera Spark-eller Spark-kluster. Du kan använda välkända Apache Spark-API: er och **Jupyter-anteckningsböcker** för analys-och SQL API eller någon av dina oss NoSQL-API: er som Cassandra för transaktions bearbetning på samma underliggande data på samma tid.

### <a name="no-schema-or-index-management"></a>Ingen hantering av schema eller index

Till skillnad från traditionella analys databaser, med Azure Cosmos DB, data tekniker och data experter, behöver inte längre hantera komplicerade schema-och index hantering. Databas motorn i Azure Cosmos DB kräver inte någon explicit schema-eller index hantering, och den kan automatiskt indexera alla data som matas in för att snabbt hantera Apache Sparks frågor. 

### <a name="consistency-choices"></a>Konsekvens alternativ

Eftersom Apache Spark jobb körs i datapartitionerna i din Azure Cosmos-databas, får frågorna de [fem väldefinierade konsekvens alternativen](consistency-levels.md). Dessa konsekvens modeller ger flexibiliteten att välja strikt konsekvens för att tillhandahålla de mest exakta resultaten för Machine Learning-algoritmer utan att kompromissa med svars tiden och hög tillgänglighet. 

### <a name="comprehensive-slas"></a>Omfattande serviceavtal

De Apache Spark jobben har de Azure Cosmos DB fördelarna, till exempel branschledande omfattande [service avtal](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) (99,999) utan att det går att hantera separata Apache Spark-kluster. Dessa service avtal omfattar data flöde, svars tid vid 99 percentil, konsekvens och hög tillgänglighet. 

### <a name="mixed-workloads"></a>Blandade arbets belastningar

Integreringen av Apache Spark i Azure Cosmos DB bryggor av transaktions-och analys separationen, som har varit en av de stora kunderna som är en av de viktigaste kunderna när de skapar molnbaserade program i global skala. 

## <a name="scenarios-for-azure-cosmos-db-spark-support"></a>Scenarier för Azure Cosmos DB Spark-support

### <a name="retail-and-consumer-goods"></a>Butiks-och konsument varor

Du kan använda Spark-support i Azure Cosmos DB för att tillhandahålla rekommendationer och erbjudanden i real tid. Du kan hjälpa kunderna att identifiera de objekt de behöver med anpassningar och produkt rekommendationer i real tid.

* Du kan använda det inbyggda Machine Learning support från Apache Spark runtime för att generera rekommendationer i real tid i alla produkt kataloger.

* Du kan klicka på strömma data, köpa data och kunddata för att tillhandahålla riktade rekommendationer som bedriver livs längd värde.

* Med hjälp av den globala distributions funktionen i Azure Cosmos DB kan stora mängder produkt data som sprids mellan regioner analyseras i millisekunder.

* Du kan snabbt få insikter om geografiskt distribuerade användare och data. Du kan förbättra konverterings takten för befordran genom att betjäna rätt AD till rätt användare vid rätt tidpunkt.

* Du kan utnyttja den inbyggda funktionen Spark streaming för att utöka real tids data genom att kombinera dem med statiska kunddata. På så sätt kan du leverera mer personliga och riktade annonser i real tid och i samband med vad kunderna gör.

Följande bild visar hur Azure Cosmos DB Spark-support används för att optimera priser och kampanjer:

![Azure Cosmos DB Spark-support för att optimera priser och kampanjer](./media/spark-api-introduction/optimize-pricing-and-promotions.png)


Följande bild visar hur Azure Cosmos DB Spark-stöd används i real tids rekommendations motor:

![Stöd för Azure Cosmos DB spark i real tids rekommendations motor](./media/spark-api-introduction/real-time-recommendation-engine.png)

### <a name="manufacturing-and-iot"></a>Tillverkning och IoT

Med Azure Cosmos DB inbyggda analys plattformen kan du aktivera real tids analys av IoT-data från miljon tals enheter i global skala. Du kan göra moderna innovationer som förutsäger väder mönster, förutsägelse analyser och energi optimeringar.

* Med hjälp av Azure Cosmos DB kan du mina data, till exempel real tids till gångs mått och väder faktorer, använda Smart Grid Analytics för att optimera prestanda för anslutna enheter i fältet. Smart Grid Analytics är nyckeln för att kontrol lera drifts kostnaderna, för att förbättra rutnätets tillförlitlighet och leverera anpassade energi tjänster till konsumenter.

Följande bild visar hur Azure Cosmos DB Spark-support används för att läsa mått från IoT-enheter och använda Smart Grid-analys:

![Azure Cosmos DB Spark-support för att läsa mått från IoT-enheter](./media/spark-api-introduction/read-metrics-from-iot-devices.png)


### <a name="predictive-maintenance"></a>Förutsägande underhåll

* Att underhålla till gångar som kompressorer som används i små borrnings Rigs till plattformar med djup vatten är en komplex Endeavor. Dessa till gångar finns i hela världen och genererar petabyte av data. Med hjälp av Azure Cosmos DB kan du skapa en pipeline för förutsägelse data från slut punkt till slut punkt som använder Spark-direktuppspelning för att bearbeta stora mängder sensor telemetri, lagra till gångs delar och sensor mappnings data.

* Du kan skapa och distribuera maskin inlärnings modeller för att förutse till gångs fel innan de inträffar och utfärda underhålls arbets order innan felet uppstår.

Följande bild visar hur Azure Cosmos DB Spark-support används för att bygga ett förutsägande underhålls system:

![Azure Cosmos DB Spark-support för att bygga ett förutsägande underhålls system](./media/spark-api-introduction/predictive-maintenance-system.png)

Följande bild visar hur Azure Cosmos DB Spark-stödet används för att bygga ett real tids motor diagnos system:

![Azure Cosmos DB Spark-stöd för att bygga ett system för diagnostik av motor i real tid](./media/spark-api-introduction/real-time-vehicle-diagnostic-system.png)

### <a name="gaming"></a>Spel

* Med inbyggd Spark-support gör Azure Cosmos DB att du enkelt kan bygga, skala och distribuera avancerade analyser och maskin inlärnings modeller på bara några minuter för att skapa bästa möjliga spel upplevelse.

* Du kan analysera spelare, Köp och beteende data för att skapa relevanta anpassade erbjudanden för att uppnå höga växelkurser.

* Med hjälp av Spark Machine Learning kan du analysera och få insikter om spel telemetridata. Du kan diagnostisera och förhindra långsamma inläsnings tider och problem med spelet.

Följande bild visar hur Azure Cosmos DB Spark-stödet används i spel analys:

![Azure Cosmos DB Spark-stöd i spel analys](./media/spark-api-introduction/gaming-analytics.png)

## <a name="next-steps"></a>Nästa steg

* Mer information om fördelarna med Azure Cosmos DB finns i [översikts](introduction.md) artikeln.
* [Kom igång med Azure Cosmos DB API för MongoDB](mongodb-introduction.md)
* [Kom igång med Azure Cosmos DB API för Cassandra](cassandra-introduction.md)
* [Kom igång med API: et för Azure Cosmos DB Gremlin](graph-introduction.md)
* [Kom igång med Azure Cosmos DB Tabell-API](table-introduction.md)




