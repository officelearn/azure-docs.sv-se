---
title: Vad är Apache Spark i Azure Synapse Analytics?
description: Den här artikeln innehåller en introduktion till Apache Spark i Azure Synapse Analytics och de olika scenarier där du kan använda Spark.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 4155e419bb03613abad808ca09d84bcb6583291f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423651"
---
# <a name="what-is-apache-spark-in-azure-synapse-analytics"></a>Vad är Apache Spark i Azure Synapse Analytics

Apache Spark är ett ramverk för parallellbearbetning som stöder minnesintern bearbetning för att öka prestanda i program för stordataanalys. Apache Spark i Azure Synapse Analytics är en av Microsofts implementeringar av Apache Spark i molnet. Azure Synapse gör det enkelt att skapa och konfigurera en Spark-pool (förhandsversion) i Azure. Spark-pooler i Azure Synapse är kompatibla med Azure Storage och Azure Data Lake Generation 2 Storage. Så du kan använda Azure Spark-pooler för att bearbeta dina data som lagras i Azure.

![Spark: ett enhetligt ramverk](./media/apache-spark-overview/spark-overview.png)

[!INCLUDE [preview](../includes/note-preview.md)]

## <a name="what-is-apache-spark"></a>Vad är Apache Spark

Apache Spark tillhandahåller primitiver för minnesbaserad klusterdator. Ett Spark-jobb kan läsa in och cachelagra data i minnet samt köra frågor på dessa data upprepade gånger. Minnesdatorer är mycket snabbare än diskbaserade program. Spark integrerar också med flera programmeringsspråk så att du kan manipulera distribuerade datauppsättningar som lokala samlingar. Det finns inget behov av att strukturera det hela i mappnings- och reduceringsåtgärder.

![Traditionell MapReduce vs. Spark](./media/apache-spark-overview/map-reduce-vs-spark.png)

Spark-pooler i Azure Synapse erbjuder en fullständigt hanterad Spark-tjänst. Fördelarna med att skapa en Spark-pool i Synapse Analytics visas här.

| Funktion | Beskrivning |
| --- | --- |
| Snabbhet och effektivitet |Spark-instanser börjar om cirka 2 minuter i färre än 60 noder och cirka 5 minuter i mer än 60 noder. Instansen stängs av som standard 5 minuter efter det senaste jobbet som kördes om den inte hålls vid liv av en anteckningsboksanslutning. |
| Enkelt att skapa |Du kan skapa en ny Spark-pool i Azure Synapse på några minuter med Azure-portalen, Azure PowerShell eller Synapse Analytics .NET SDK. Se [Komma igång med Spark-pooler i Synapse Analytics](apache-spark-notebook-create-spark-use-sql.md). |
| Användbarhet |Synapse Analytics innehåller en anpassad anteckningsbok som härleds från [Nteract](https://nteract.io/). Du kan de här anteckningsböckerna för interaktiv databehandling och visualisering.|
| REST API:er |Spark i Synapse Analytics innehåller [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), en REST API-baserad Spark-jobbserver för att fjärrstyra och övervaka jobb. |
| Stöd för Generation för Azure Data Lake Storage Generation 2| Spark-pooler i Azure Synapse kan använda Azure Data Lake Storage Generation 2 samt BLOB-lagring. Mer information om Data Lake Storage finns i [Översikt över Azure Data Lake Storage](../../data-lake-store/data-lake-store-overview.md). |
| Integrering med tredje parts IDEs | Azure Synapse tillhandahåller en IDE-plugin för [Jetbrains IntelliJ IDEA](https://www.jetbrains.com/idea/) som är användbar för att skapa och skicka program till en Spark-pool. |
| Förinstallerade Anaconda-bibliotek |Spark-pooler i Azure Synapse levereras med Anaconda-bibliotek förinstallerade. [Anaconda](https://docs.continuum.io/anaconda/) innehåller närmare 200 bibliotek för Machine Learning, dataanalys, visualisering med mera. |
| Skalbarhet | Apache Spark i Azure Synapse-pooler kan ha automatisk skalning aktiverat, så att poolerna skalas upp och ned efter behov. Spark-pooler kan också stängas av utan förlust av data eftersom alla data lagras i Azure Storage eller Data Lake Storage. |

Spark-pooler i Azure Synapse innehåller följande komponenter som är tillgängliga i poolerna som standard.

- [Spark Core](https://spark.apache.org/docs/latest/). Innehåller Spark Core, Spark SQL, GraphX och MLlib.
- [Anaconda](https://docs.continuum.io/anaconda/)
- [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
- [Nteract bärbar dator](https://nteract.io/)

## <a name="spark-pool-architecture"></a>Spark pool arkitektur

Det är lätt att förstå komponenterna i Spark genom att förstå hur Spark körs på Synapse Analytics.

Spark-program körs som oberoende uppsättningar processer på en pool, som koordineras av SparkContext-objektet i huvudprogrammet (kallas drivrutinsprogrammet).

SparkContext kan ansluta till klusterhanteraren, som allokerar resurser mellan program. Klusterhanteraren är [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html). När Spark har anslutits hämtas utförare på noder i poolen, som är processer som kör beräkningar och lagrar data för ditt program. Därefter skickar den din programkod (definieras av JAR- eller Python-filer som överförts till SparkContext) till exekverarna. Slutligen skickar SparkContext uppgifter till de exekverare som ska köras.

SparkContext kör användarens huvudfunktion och kör de olika parallella åtgärderna på noderna. Drivrutinen samlar sedan in åtgärdernas resultat. Noderna läser och skriver data från och till filsystemet. Noderna cachelagrade också data i minnet som RDDs (Resilient Distributed Datasets).

SparkContext ansluter till Spark-poolen och ansvarar för att konvertera ett program till ett riktat diagram (DAG). Diagrammet består av enskilda uppgifter som körs i en executor-process på noderna. Varje applikation får en egen körningsprocess som förblir aktiv under applikationens livslängd och kör uppgifter i flera trådar.

## <a name="apache-spark-in-synapse-analytics-use-cases"></a>Apache Spark i Synapse Analytics användningsfall

Spark-pooler i Synapse Analytics aktiverar följande huvudscenarier:

### <a name="data-engineeringdata-preparation"></a>Datateknik/dataförberedelse

Apache Spark innehåller många språkfunktioner för att stödja förberedelse och bearbetning av stora mängder data så att den kan göras mer värdefull och sedan förbrukas av andra tjänster inom Synapse Analytics. Detta aktiveras via flera språk (C#, Scala, PySpark, Spark SQL) och angivna bibliotek för bearbetning och anslutning.

### <a name="machine-learning"></a>Machine Learning

Apache Spark levereras med [MLlib](https://spark.apache.org/mllib/), ett maskininlärningsbibliotek byggt ovanpå Spark som du kan använda från en Spark-pool i Synapse Analytics. Spark pooler i Synapse Analytics inkluderar också Anaconda, en Python-distribution med en mängd olika paket för datavetenskap, inklusive maskininlärning. I kombination med inbyggt stöd för bärbara datorer har du en miljö för att skapa maskininlärningsprogram.

## <a name="where-do-i-start"></a>Var börjar jag

Använd följande artiklar om du vill veta mer om Apache Spark i Synapse Analytics:

- [Snabbstart: Skapa en Spark-pool i Azure Synapse](./apache-spark-notebook-create-spark-use-sql.md)
- [Självstudiekurs: Maskininlärning med Apache Spark](./apache-spark-machine-learning-mllib-notebook.md)
- [Apache Spark officiell dokumentation](https://spark.apache.org/docs/latest/)

> [!NOTE]
> En del av den officiella Apache Spark-dokumentationen förlitar sig på att använda spark-konsolen, detta är inte tillgängligt på Azure Synapse Spark, använder den bärbara datorn eller IntelliJ-upplevelserna istället

## <a name="next-steps"></a>Nästa steg

I den här översikten får du en grundläggande förståelse för Apache Spark i Azure Synapse Analytics. Gå vidare till nästa artikel om du vill lära dig hur du skapar en Spark-pool i Azure Synapse Analytics:

- [Skapa en Spark-pool i Azure Synapse](./apache-spark-notebook-create-spark-use-sql.md)
