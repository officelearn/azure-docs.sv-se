---
title: Konfigurera Spark-inställningar – Azure HDInsight
description: Konfigurera Spark för ett Azure HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: eb948aa2b683f426831e1b0d34b44f814eab6b9f
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68441920"
---
# <a name="configure-apache-spark-settings"></a>Konfigurera Apache Spark-inställningar

An-HDInsight Spark-kluster innehåller en installation av [Apache Sparks](https://spark.apache.org/) biblioteket.  Varje HDInsight-kluster innehåller standard konfigurations parametrar för alla installerade tjänster, inklusive Spark.  En viktig aspekt i hanteringen av ett HDInsight Apache Hadoop-kluster är övervakning av arbets belastning, inklusive Spark-jobb, för att se till att jobben körs på ett förutsägbart sätt. Om du vill köra Spark-jobb på bästa sätt bör du tänka på den fysiska kluster konfigurationen när du bestämmer hur klustrets logiska konfiguration ska optimeras.

Standard-HDInsight Apache Spark-klustret innehåller följande noder: tre [Apache ZooKeeper](https://zookeeper.apache.org/) noder, två huvudnoder och en eller flera arbetsnoder:

![Spark HDInsight-arkitektur](./media/apache-spark-settings/spark-hdinsight-arch.png)

Antalet virtuella datorer och VM-storlekarna för noderna i HDInsight-klustret kan också påverka Spark-konfigurationen. Konfigurations värden som inte är standard för HDInsight kräver ofta konfigurations värden som inte är standard. När du skapar ett HDInsight Spark-kluster visas föreslagna VM-storlekar för varje komponent. För närvarande är de [minnesoptimerade virtuella Linux-VM](../../virtual-machines/linux/sizes-memory.md) -storlekarna för Azure D12 v2 eller senare.

## <a name="apache-spark-versions"></a>Apache Spark versioner

Använd den bästa Spark-versionen för klustret.  HDInsight-tjänsten innehåller flera versioner av både Spark och HDInsight.  Varje version av Spark innehåller en uppsättning standard kluster inställningar.  

När du skapar ett nytt kluster finns det flera Spark-versioner att välja mellan. Så här visar du en fullständig lista, [HDInsight-komponenter och-versioner](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)


> [!NOTE]  
> Standard versionen av Apache Spark i HDInsight-tjänsten kan ändras utan föregående meddelande. Om du har ett versions beroende rekommenderar Microsoft att du anger den specifika versionen när du skapar kluster med .NET SDK, Azure PowerShell och klassisk Azure-CLI.

Apache Spark har tre platser för system konfiguration:

* Spark-egenskaperna styr de flesta program parametrarna och kan anges med hjälp `SparkConf` av ett objekt, eller genom Java system egenskaper.
* Miljövariabler kan användas för att ange inställningar per dator, till exempel IP-adress, via `conf/spark-env.sh` skriptet på varje nod.
* Loggning kan konfigureras via `log4j.properties`.

När du väljer en viss version av Spark innehåller klustret standard konfigurations inställningarna.  Du kan ändra standard konfigurations värden för Spark genom att använda en anpassad Spark-konfigurationsfil.  Ett exempel visas nedan.

```
    spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
    spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
    spark.hadoop.parquet.block.size 1099511627776
    spark.sql.files.maxPartitionBytes 1099511627776
    spark.sql.files.openCostInBytes 1099511627776
```

Exemplet som visas ovan åsidosätter flera standardvärden för fem Spark-konfigurationsparametrar.  Detta är komprimerings-codecen, Apache Hadoop MapReduce delade minimi storlek och Parquet block storlekar, samt standardvärden för Spara SQL-partitionen och Open File-storlek.  De här konfigurations ändringarna väljs eftersom tillhör ande data och jobb (i det här exemplet genomiks data) har särskilda egenskaper, som kommer att fungera bättre med hjälp av dessa anpassade konfigurations inställningar.

---

## <a name="view-cluster-configuration-settings"></a>Visa kluster konfigurations inställningar

Verifiera de aktuella konfigurations inställningarna för HDInsight-klustret innan du utför prestanda optimering i klustret. Starta HDInsight-instrumentpanelen från Azure Portal genom att klicka på länken för **instrument panelen** i fönstret Spark-kluster. Logga in med kluster administratörens användar namn och lösen ord.

Apache Ambari Web UI visas med en instrument panel med användnings mått för nyckel kluster resurser.  Ambari-instrumentpanelen visar Apache Spark konfiguration och andra tjänster som du har installerat. På instrument panelen finns fliken **konfigurations historik** där du kan visa konfigurations information för alla installerade tjänster, inklusive Spark.

Om du vill se konfigurations värden för Apache Spark väljer du **konfigurations historik**och väljer sedan **Spark2**.  Välj fliken **konfigurationer** och välj `Spark` sedan länken (eller `Spark2`, beroende på din version) i tjänst listan.  Du ser en lista över konfigurations värden för klustret:

![Spark-konfigurationer](./media/apache-spark-settings/spark-config.png)

Om du vill se och ändra enskilda konfigurations värden för Spark väljer du en länk med ordet "Spark" i länk rubriken.  Konfigurationer för Spark inkluderar både anpassade och avancerade konfigurations värden i följande kategorier:

* Anpassade Spark2 – standardvärden
* Anpassade Spark2-mått – egenskaper
* Avancerade Spark2 – standardvärden
* Avancerad Spark2 – kuvert
* Avancerad spark2-Hive-plats-åsidosättande

Om du skapar en uppsättning konfigurations värden som inte är standard kan du även se historiken för dina konfigurations uppdateringar.  Den här konfigurations historiken kan vara till hjälp för att se vilken konfiguration som inte är standard som har optimala prestanda.

> [!NOTE]  
> Om du vill se, men inte ändra, vanliga konfigurations inställningar för Spark-klustret väljer du fliken **miljö** på den högsta nivån **Spark-jobbets gränssnitts** gränssnitt.

## <a name="configuring-spark-executors"></a>Konfigurera Spark-körningar

Följande diagram visar viktiga Spark-objekt: driv rutins programmet och dess tillhör ande Spark-kontext samt kluster hanteraren och dess *n* Worker-noder.  Varje arbetsnoden innehåller ett utförar, ett cache-och *n* -aktivitets instanser.

![Kluster objekt](./media/apache-spark-settings/spark-arch.png)

Spark-jobb använder arbets resurser, särskilt minne, så det är vanligt att justera Spark-konfigurationsinställningar för körning av arbetsnoder.

Tre nyckel parametrar som ofta justeras för att justera Spark-konfigurationer för att förbättra `spark.executor.instances`program `spark.executor.cores`kraven är `spark.executor.memory`, och. En utförar är en process som startas för ett Spark-program. En utförar körs på Worker-noden och ansvarar för aktiviteterna för programmet. För varje kluster beräknas standard antalet körningar och utförar storlekarna utifrån antalet arbetsnoder och storleken på arbetsnoden. De lagras i `spark-defaults.conf` kluster huvudnoderna.  Du kan redigera dessa värden i ett kluster som körs genom att välja länken **anpassad Spark-standard** i Ambari-webbgränssnittet.  När du har gjort ändringar uppmanas du att ange användar gränssnittet för att **starta om** alla berörda tjänster.

> [!NOTE]  
> Dessa tre konfigurations parametrar kan konfigureras på kluster nivå (för alla program som körs i klustret) och också anges för varje enskilt program.

En annan informations källa om resurserna som används av Spark-körningarna är Spark-programgränssnittet.  I Spark-ANVÄNDARGRÄNSSNITTET väljer du fliken **körningar** för att Visa sammanfattnings-och detalj visningar av konfigurationen och resurserna som används av körningarna.  Dessa vyer kan hjälpa dig att avgöra om du vill ändra standardvärdena för Spark-körningar för hela klustret eller en viss uppsättning jobb körningar.

![Spark-körningar](./media/apache-spark-settings/spark-executors.png)

Du kan också använda Ambari-REST API för att program mässigt verifiera HDInsight-och Spark-klusterresursernas kluster konfigurations inställningar.  Mer information finns på [Apache AMBARI API Reference på GitHub](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Beroende på din spark-arbetsbelastning kan du bestämma att en icke-standard-Spark-konfiguration ger mer optimerade Spark-jobb.  Du bör utföra benchmark-testning med exempel arbets belastningar för att verifiera alla klusterkonfigurationer som inte är standard.  Några av de vanliga parametrarna som du kan överväga att justera är:

* `--num-executors`anger antalet körningar.
* `--executor-cores`anger antalet kärnor för varje utförar. Vi rekommenderar att du använder mellanstora körningar, eftersom andra processer också använder en del av det tillgängliga minnet.
* `--executor-memory`styr minnes storleken (Heap-storlek) för varje utförar på [Apache HADOOP garn](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), och du måste lämna lite minne för att utföra omkostnader.

Här är ett exempel på två arbetsnoder med olika konfigurations värden:

![Två nodkonfigurationer](./media/apache-spark-settings/executor-config.png)

I följande lista visas minnes parametrarna för utförar i Key Spark.

* `spark.executor.memory`definierar den totala mängden minne som är tillgängligt för en utförar.
* `spark.storage.memoryFraction`(standard ~ 60%) definierar mängden minne som är tillgängligt för lagring av beständiga RDD.
* `spark.shuffle.memoryFraction`(standard ~ 20%) definierar mängden minne som är reserverat för blandad.
* `spark.storage.unrollFraction`och `spark.storage.safetyFraction` (totalt ~ 30% av det totala minnet) – dessa värden används internt av Spark och bör inte ändras.

GARN styr den maximala mängd minne som används av behållarna på varje spark-nod. Följande diagram visar relationer per nod mellan garn konfigurations objekt och Spark-objekt.

![GARN Spark minnes hantering](./media/apache-spark-settings/yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Ändra parametrar för ett program som körs i Jupyter Notebook

Spark-kluster i HDInsight innehåller ett antal komponenter som standard. Var och en av dessa komponenter innehåller standard konfigurations värden som kan åsidosättas vid behov.

* Spark Core – Spark Core, Spark SQL, Spark streaming-API: er, GraphX och Apache Spark MLlib.
* Anaconda – en python-paket hanterare.
* [Apache livy](https://livy.incubator.apache.org/) – Apache Spark REST API som används för att skicka Fjärrjobb till ett HDInsight Spark-kluster.
* [Jupyter](https://jupyter.org/) och [Apache Zeppelin](https://zeppelin.apache.org/) Notebooks – interaktivt webbläsarbaserat användar gränssnitt för att interagera med ditt Spark-kluster.
* ODBC-drivrutin – ansluter Spark-kluster i HDInsight till Business Intelligence (BI) verktyg som Microsoft Power BI och Tableau.

För program som körs i Jupyter Notebook använder `%%configure` du kommandot för att göra konfigurations ändringar inifrån själva antecknings boken. Dessa konfigurations ändringar kommer att tillämpas på Spark-jobben som körs från din antecknings bok instans. Du bör göra sådana ändringar i början av programmet innan du kör den första kod cellen. Den ändrade konfigurationen tillämpas på livy-sessionen när den skapas.

> [!NOTE]  
> Om du vill ändra konfigurationen i ett senare skede i programmet använder du `-f` parametern (Force). Men all förloppet i programmet går förlorad.

Koden nedan visar hur du ändrar konfigurationen för ett program som körs i en Jupyter Notebook.

```
    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Sammanfattning

Det finns ett antal grundläggande konfigurations inställningar som du behöver övervaka och justera för att se till att dina Spark-jobb körs på ett förutsägbart och effektivt sätt. De här inställningarna hjälper dig att avgöra den bästa Spark-klustrets konfiguration för dina specifika arbets belastningar.  Du måste också övervaka körningen av körning av långvariga och/eller resurs krävande Spark-jobb.  De vanligaste utmaningarna för utmaningarna när det gäller minnes belastning på grund av felaktiga konfigurationer (särskilt maskin storleks körningar), långvariga åtgärder och uppgifter som resulterar i kartesiska-åtgärder.

## <a name="next-steps"></a>Nästa steg

* [Apache Hadoop komponenter och versioner som är tillgängliga med HDInsight?](../hdinsight-component-versioning.md)
* [Hantera resurser för ett Apache Spark kluster i HDInsight](apache-spark-resource-manager.md)
* [Konfigurera kluster i HDInsight med Apache Hadoop, Apache Spark, Apache Kafka med mera](../hdinsight-hadoop-provision-linux-clusters.md)
* [Apache Spark konfiguration](https://spark.apache.org/docs/latest/configuration.html)
* [Köra Apache Spark på Apache Hadoop garn](https://spark.apache.org/docs/latest/running-on-yarn.html)
