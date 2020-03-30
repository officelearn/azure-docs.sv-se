---
title: Konfigurera Spark-inställningar - Azure HDInsight
description: Visa och konfigurera Apache Spark-inställningar för ett Azure HDInsight-kluster
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: 48f19e5da8c7703cc597518246c2f62ebce3ae17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272024"
---
# <a name="configure-apache-spark-settings"></a>Konfigurera Apache Spark-inställningar

Ett HDInsight Spark-kluster innehåller en installation av [Apache Spark-biblioteket.](https://spark.apache.org/)  Varje HDInsight-kluster innehåller standardkonfigurationsparametrar för alla dess installerade tjänster, inklusive Spark.  En viktig aspekt av hanteringen av ett HDInsight Apache Hadoop-kluster är att övervaka arbetsbelastningen, inklusive Spark-jobb, för att se till att jobben körs på ett förutsägbart sätt. Om du vill köra Spark-jobb på bästa sätt bör du tänka på den fysiska klusterkonfigurationen när du bestämmer hur klustrets logiska konfiguration ska optimeras.

Standardklustret HDInsight Apache Spark innehåller följande noder: tre [Apache ZooKeeper-noder,](https://zookeeper.apache.org/) två huvudnoder och en eller flera arbetsnoder:

![Spark HDInsight-arkitektur](./media/apache-spark-settings/spark-hdinsight-arch.png)

Antalet virtuella datorer och vm-storlekarna för noderna i HDInsight-klustret kan också påverka spark-konfigurationen. Konfigurationsvärden som inte är standard kräver ofta konfigurationsvärden som inte är standard. När du skapar ett HDInsight Spark-kluster visas föreslagna VM-storlekar för var och en av komponenterna. För närvarande är de [minnesoptimerade Linux VM-storlekarna](../../virtual-machines/linux/sizes-memory.md) för Azure D12 v2 eller senare.

## <a name="apache-spark-versions"></a>Apache Spark-versioner

Använd den bästa Spark-versionen för ditt kluster.  HDInsight-tjänsten innehåller flera versioner av både Spark och HDInsight själv.  Varje version av Spark innehåller en uppsättning standardklusterinställningar.  

När du skapar ett nytt kluster finns det flera Spark-versioner att välja mellan. För att se hela listan, [HDInsight-komponenter och versioner](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)


> [!NOTE]  
> Standardversionen av Apache Spark i HDInsight-tjänsten kan ändras utan föregående meddelande. Om du har ett versionsberoende rekommenderar Microsoft att du anger den versionen när du skapar kluster med .NET SDK, Azure PowerShell och Azure Classic CLI.

Apache Spark har tre platser för systemkonfiguration:

* Spark-egenskaper styr de flesta programparametrar och kan ställas in med hjälp av ett `SparkConf` objekt eller via Java-systemegenskaper.
* Miljövariabler kan användas för att ange inställningar per dator, `conf/spark-env.sh` till exempel IP-adressen, via skriptet på varje nod.
* Loggning kan konfigureras `log4j.properties`via .

När du väljer en viss version av Spark innehåller klustret standardkonfigurationsinställningarna.  Du kan ändra standardkonfigurationsvärdena för Spark med hjälp av en anpassad Spark-konfigurationsfil.  Ett exempel på detta visas nedan.

```
spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
spark.hadoop.parquet.block.size 1099511627776
spark.sql.files.maxPartitionBytes 1099511627776
spark.sql.files.openCostInBytes 1099511627776
```

Exemplet som visas ovan åsidosätter flera standardvärden för fem Spark-konfigurationsparametrar.  Dessa är komprimering codec, Apache Hadoop MapReduce split minsta storlek och parkett block storlekar, och även Spar SQL-partitionen och öppna filstorlekar standardvärden.  Dessa konfigurationsändringar väljs eftersom associerade data och jobb (i det här exemplet genomiska data) har särskilda egenskaper, vilket kommer att fungera bättre med hjälp av dessa anpassade konfigurationsinställningar.

---

## <a name="view-cluster-configuration-settings"></a>Visa inställningar för klusterkonfiguration

Verifiera de aktuella hdinsight-klusterkonfigurationsinställningarna innan du utför prestandaoptimering i klustret. Starta HDInsight-instrumentpanelen från Azure-portalen genom att klicka på **länken Instrumentpanel** i snabbklusterfönstret. Logga in med klusteradministratörens användarnamn och lösenord.

Apache Ambari Webbgränssnitt visas med en instrumentpanelsvy över viktiga mått för klusterresursanvändning.  Ambari-instrumentpanelen visar Apache Spark-konfigurationen och andra tjänster som du har installerat. Instrumentpanelen innehåller fliken **Konfigurationshistorik,** där du kan visa konfigurationsinformation för alla installerade tjänster, inklusive Spark.

Om du vill visa konfigurationsvärden för Apache Spark väljer du **Config-historik**och väljer **spark2**.  Välj fliken **Configs** och `Spark` välj `Spark2`sedan länken (eller , beroende på din version) i tjänstlistan.  Du ser en lista över konfigurationsvärden för klustret:

![Spark-konfigurationer](./media/apache-spark-settings/spark-configurations.png)

Om du vill visa och ändra enskilda Spark-konfigurationsvärden markerar du en länk med ordet "gnista" i länkrubriken.  Konfigurationer för Spark innehåller både anpassade och avancerade konfigurationsvärden i följande kategorier:

* Anpassade Spark2-standardvärden
* Anpassade Spark2-mått-egenskaper
* Avancerade Spark2-standardvärden
* Avancerad Spark2-env
* Avancerad spark2-hive-site-override

Om du skapar en icke-standarduppsättning konfigurationsvärden kan du också se historiken för dina konfigurationsuppdateringar.  Den här konfigurationshistoriken kan vara användbar för att se vilken konfiguration som inte är standard har optimal prestanda.

> [!NOTE]  
> Om du vill se, men inte ändra, vanliga konfigurationsinställningar för Spark-kluster väljer du fliken **Miljö** i det översta **sparkjobbgränssnittsgränssnittet.**

## <a name="configuring-spark-executors"></a>Konfigurera Spark-körskydd

I följande diagram visas viktiga Spark-objekt: drivrutinsprogrammet och dess *n* associerade Spark-kontext och klusterhanteraren och dess n-arbetsnoder.  Varje arbetsnod innehåller en Executor, *n* en cache och n-aktivitetsinstanser.

![Klusterobjekt](./media/apache-spark-settings/hdi-spark-architecture.png)

Spark-jobb använder arbetsresurser, särskilt minne, så det är vanligt att justera Spark-konfigurationsvärden för arbetsnodutredare.

Tre nyckelparametrar som ofta justeras för att `spark.executor.instances`justera `spark.executor.cores`Spark-konfigurationer för att förbättra programkraven är , och `spark.executor.memory`. En Executor är en process som startas för ett Spark-program. En Executor körs på arbetarnoden och är ansvarig för aktiviteterna för programmet. För varje kluster beräknas standardantalet executors och executor-storlekar baserat på antalet arbetsnoder och arbetarnodstorleken. Dessa lagras `spark-defaults.conf` i på klusterhuvudnoderna.  Du kan redigera dessa värden i ett kluster som körs genom att välja länken **Anpassad spark-standard** i webbgränssnittet i Ambari.  När du har gjort ändringar uppmanas du av användargränssnittet att **starta om** alla berörda tjänster.

> [!NOTE]  
> Dessa tre konfigurationsparametrar kan konfigureras på klusternivå (för alla program som körs i klustret) och även anges för varje enskilt program.

En annan källa till information om de resurser som används av Spark-utförarna är Spark-programgränssnittet.  I spark-användargränssnittet väljer du fliken **Körskydd för** att visa sammanfattnings- och detaljvyer av konfigurationen och resurser som förbrukas av utförarna.  Dessa vyer kan hjälpa dig att avgöra huruvida du vill ändra standardvärdena för Spark-utförare för hela klustret eller en viss uppsättning jobbkörningar.

![Spark Executors](./media/apache-spark-settings/apache-spark-executors.png)

Alternativt kan du använda Ambari REST API för att programmässigt verifiera hdinsight- och spark-klusterkonfigurationsinställningar.  Mer information finns på [Apache Ambari API-referensen på GitHub](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Beroende på din Spark-arbetsbelastning kan du bestämma att en icke-standardmässig Spark-konfiguration ger mer optimerade Spark-jobbkörningar.  Du bör utföra prestandatestning med exempelarbetsbelastningar för att validera eventuella icke-standardmässiga klusterkonfigurationer.  Några av de vanliga parametrar som du kan överväga att justera är:

* `--num-executors`anger antalet utförare.
* `--executor-cores`anger antalet kärnor för varje utförare. Vi rekommenderar att du använder medelstora utförare, eftersom andra processer också förbrukar en del av det tillgängliga minnet.
* `--executor-memory`styr minnesstorleken (heap-storleken) för varje executor på [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), och du måste lämna lite minne för körning overhead.

Här är ett exempel på två arbetsnoder med olika konfigurationsvärden:

![Konfigurationer av två noder](./media/apache-spark-settings/executor-configuration.png)

Följande lista visar parametrarna för nyckel spark executor minne.

* `spark.executor.memory`definierar den totala mängden minne som är tillgängligt för en utförare.
* `spark.storage.memoryFraction`(standard ~60%) definierar mängden minne som är tillgängligt för lagring av beständiga RDD:er.
* `spark.shuffle.memoryFraction`(standard ~20%) definierar mängden minne som reserverats för blandning.
* `spark.storage.unrollFraction`och `spark.storage.safetyFraction` (totalt ~ 30% av det totala minnet) - dessa värden används internt av Spark och bör inte ändras.

YARN styr den maximala summan minne som används av behållarna på varje Spark-nod. Följande diagram visar per nodrelationer mellan YARN-konfigurationsobjekt och Spark-objekt.

![HANTERING AV YARN Spark-minne](./media/apache-spark-settings/hdi-yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Ändra parametrar för ett program som körs i Jupyter-anteckningsbok

Spark-kluster i HDInsight innehåller som standard ett antal komponenter. Var och en av dessa komponenter innehåller standardkonfigurationsvärden som kan åsidosättas efter behov.

* Spark Core - Spark Core, Spark SQL, Spark streaming API: er, GraphX och Apache Spark MLlib.
* Anaconda - en python pakethanterare.
* [Apache Livy](https://livy.incubator.apache.org/) - Apache Spark REST API, som används för att skicka fjärrjobb till ett HDInsight Spark-kluster.
* [Jupyter](https://jupyter.org/) och [Apache Zeppelin-anteckningsböcker](https://zeppelin.apache.org/) – interaktivt webbläsarbaserat användargränssnitt för att interagera med Spark-klustret.
* ODBC-drivrutin – ansluter Spark-kluster i HDInsight till BI-verktyg (Business Intelligence) som Microsoft Power BI och Tableau.

För program som körs i jupyter-anteckningsboken `%%configure` använder du kommandot för att göra konfigurationsändringar från själva anteckningsboken. Dessa konfigurationsändringar tillämpas på Spark-jobben som körs från din anteckningsboksinstans. Du bör göra sådana ändringar i början av programmet innan du kör din första kodcell. Den ändrade konfigurationen tillämpas på Livy-sessionen när den skapas.

> [!NOTE]  
> Om du vill ändra konfigurationen i ett `-f` senare skede av programmet använder du parametern (force). Alla framsteg i programmet kommer dock att gå förlorade.

Koden nedan visar hur du ändrar konfigurationen för ett program som körs i en Jupyter-anteckningsbok.

```
%%configure
{"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Slutsats

Det finns ett antal grundläggande konfigurationsinställningar som du behöver övervaka och justera för att säkerställa att dina Spark-jobb körs på ett förutsägbart och högpresterande sätt. De här inställningarna hjälper dig att avgöra den bästa Spark-klusterkonfigurationen för just dina arbetsbelastningar.  Du måste också övervaka körningen av tidskrävande och/eller resurskrävande Spark-jobbkörningar.  De vanligaste utmaningarna kretsar kring minnestryck på grund av felaktiga konfigurationer (särskilt felaktigt stora utförare), långvariga åtgärder och uppgifter, vilket resulterar i kartesiska åtgärder.

## <a name="next-steps"></a>Nästa steg

* [Apache Hadoop komponenter och versioner som finns med HDInsight?](../hdinsight-component-versioning.md)
* [Hantera resurser för ett Apache Spark-kluster på HDInsight](apache-spark-resource-manager.md)
* [Konfigurera kluster i HDInsight med Apache Hadoop, Apache Spark, Apache Kafka med mera](../hdinsight-hadoop-provision-linux-clusters.md)
* [Konfiguration av Apache-gnista](https://spark.apache.org/docs/latest/configuration.html)
* [Köra Apache Spark på Apache Hadoop YARN](https://spark.apache.org/docs/latest/running-on-yarn.html)
