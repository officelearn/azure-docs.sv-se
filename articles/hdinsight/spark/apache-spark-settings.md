---
title: Konfigurera inställningar för Spark - Azure HDInsight
description: Så här konfigurerar du Spark för ett Azure HDInsight-kluster.
author: maxluk
ms.author: maxluk
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 77f4ec9cce5d02ea4cbcc4968d02773a13edfe5b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098396"
---
# <a name="configure-apache-spark-settings"></a>Konfigurera Apache Spark-inställningar

Ett HDInsight Spark-kluster innehåller en installation av den [Apache Spark](https://spark.apache.org/) biblioteket.  Varje HDInsight-kluster innehåller standard konfigurationsparametrar för alla dess installerade tjänster, inklusive Spark.  En viktig aspekt av hantera HDInsight Apache Hadoop-kluster övervakning av arbetsbelastning, inklusive Spark-jobb att kontrollera att jobb som körs på ett förutsägbart sätt. Överväg att fysiska klusterkonfigurationen för att bäst köra Spark-jobb, när du bestämmer hur du optimerar logiska klusterkonfigurationen.

Standard HDInsight Apache Spark-kluster innehåller följande noder: tre [Apache ZooKeeper](https://zookeeper.apache.org/) noder, två huvudnoder och en eller flera arbetsnoder:

![Spark HDInsight-arkitektur](./media/apache-spark-settings/spark-hdinsight-arch.png)

Hur många virtuella datorer och VM-storlekar för noderna i ditt HDInsight-kluster kan också påverka din Spark-konfiguration. Icke-standard konfigurationsvärden för HDInsight kräver ofta inte är standard Spark-konfigurationsvärden. När du skapar ett HDInsight Spark-kluster, visas föreslagna VM-storlekar för varje komponent. För närvarande den [minnesoptimerade Linux VM-storlekar](../../virtual-machines/linux/sizes-memory.md) för Azure är D12 v2 eller större.

## <a name="apache-spark-versions"></a>Apache Spark-versioner

Använd den bästa Spark-versionen för klustret.  Tjänsten HDInsight innehåller flera versioner av Spark- och HDInsight själva.  Varje version av Spark innehåller en uppsättning standardinställningar för klustret.  

När du skapar ett nytt kluster, finns det flera Spark-versioner kan välja bland. Att se en fullständig lista [HDInsight-komponenter och versioner](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning)


> [!NOTE]  
> Standardversionen av Apache Spark i HDInsight-tjänsten kan ändras utan föregående meddelande. Om du har ett inbyggt beroende rekommenderar Microsoft att du anger den specifika versionen när du skapar kluster med .NET SDK, Azure PowerShell och Azure klassiskt CLI.

Apache Spark har tre system configuration platser:

* Spark egenskaper som styr parametrarna för de flesta program och kan ställas in med hjälp av en `SparkConf` objekt, eller via Java Systemegenskaper.
* Miljövariabler kan användas för att ange per dator-inställningar, till exempel IP-adress via den `conf/spark-env.sh` skript på varje nod.
* Loggning kan konfigureras via `log4j.properties`.

När du väljer en viss version av Spark innehåller standardinställningarna i ditt kluster.  Du kan ändra standardvärden för konfigurering av Spark med hjälp av en anpassad Spark-konfigurationsfil.  Ett exempel visas nedan.

```
    spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
    spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
    spark.hadoop.parquet.block.size 1099511627776
    spark.sql.files.maxPartitionBytes 1099511627776
    spark.sql.files.openCostInBytes 1099511627776
```

Exemplet ovan åsidosätter flera standardvärden för parametrar med fem Spark-konfiguration.  Det här är komprimerings-codec, Apache Hadoop MapReduce dela minsta storlek och parquet block storlekar och även Spar SQL-partition och öppna filstorlekar standardvärdena.  Dessa ändringar i konfigurationen är valt eftersom den associerade data och jobb (i det här exemplet stora uppsättningar genomiska data) har specifika egenskaper som utför bättre med följande inställningar för anpassad konfiguration.

---

## <a name="view-cluster-configuration-settings"></a>Visa inställningar för klustrets

Kontrollera de aktuella inställningarna för HDInsight-klustret innan du utför prestandaoptimering i klustret. Starta instrumentpanelen för HDInsight från Azure portal genom att klicka på den **instrumentpanelen** länken i fönstret Spark-kluster. Logga in med klustret administratörens användarnamn och lösenord.

Apache Ambari-Webbgränssnittet visas med en instrumentpanelsvy med viktiga kluster mätvärden för resursutnyttjande.  Ambari-instrumentpanelen visar konfigurationen av Apache Spark och andra tjänster som du har installerat. Instrumentpanelen innehåller en **Config historik** fliken där du kan visa konfigurationsinformation för alla installerade tjänster, inklusive Spark.

Om du vill se konfigurationsvärden för Apache Spark, **Config historik**och välj sedan **Spark2**.  Välj den **Peeringkonfigurationer** fliken och välj sedan den `Spark` (eller `Spark2`, beroende på din version) länk i listan.  Du kan se en lista över konfigurationsvärden för klustret:

![Spark-konfigurationer](./media/apache-spark-settings/spark-config.png)

Om du vill se och ändra enskilda konfigurationsvärden för Spark, markerar du en länk med ordet ”spark” i rubriken för länken.  Konfigurationer för Spark inkludera båda anpassade och avancerad konfiguration värdena i dessa kategorier:

* Anpassade Spark2-standardinställningar
* Custom Spark2-metrics-properties
* Avancerade Spark2-standardinställningar
* Avancerade Spark2-env
* Avancerade spark2-hive-plats-åsidosättande

Om du skapar en uppsättning konfigurationsvärden avviker från standarden, kan du också se historiken för dina uppdateringar.  Den här konfigurationshistorik kan vara användbart att se vilken konfiguration som inte är standard har optimala prestanda.

> [!NOTE]  
> Om du vill se, men inte ändra gemensamma konfigurationsinställningar för Spark i kluster, Välj den **miljö** fliken på den översta **Spark-jobb Användargränssnittet** gränssnitt.

## <a name="configuring-spark-executors"></a>Konfigurera Spark executors

I följande diagram visas objekt med nycklar Spark: driver-program och dess associerade Spark-kontexten och Klusterhanteraren och dess *n* arbetsnoder.  Varje arbetsnod innehåller en Executor, en cache och *n* uppgift instanser.

![Klusterobjekten](./media/apache-spark-settings/spark-arch.png)

Spark-jobb använder worker-resurser, särskilt minne, så är det vanligt att justera Spark konfigurationsvärden för arbetsnod Executors.

Tre viktiga parametrar som justeras ofta för att justera Spark-konfigurationer för att förbättra programkrav är `spark.executor.instances`, `spark.executor.cores`, och `spark.executor.memory`. En Executor är en process som lanserade för ett Spark-program. En Executor körs på arbetsnoden och ansvarar för uppgifterna för programmet. För varje kluster beräknas standardantalet executors och executor-storlekar baserat på antalet arbetsnoder och nodstorlek worker. Dessa lagras i `spark-defaults.conf` på klustrets huvudnoder.  Du kan redigera dessa värden i ett kluster som körs genom att välja den **anpassade spark-standardvärden** länken i Ambari-webbgränssnittet.  När du gör ändringar, uppmanas du av Gränssnittet för att **starta om** alla påverkade tjänster.

> [!NOTE]  
> Dessa tre konfigurationsparametrar kan konfigureras på klusternivå (för alla program som körs i klustret) och även har angetts för varje enskilt program.

En annan källa till information om de resurser som används av Spark-Executors är Gränssnittet för Spark-programmet.  I Användargränssnittet för Spark, Välj den **Executors** flikarna för att visa sammanfattning och information vyer av konfigurationen och resurser som används av executors.  Vyerna kan hjälpa dig att avgöra om du vill ändra standardvärdena för Spark executors för hela klustret eller en viss uppsättning jobbkörningar.

![Spark Executors](./media/apache-spark-settings/spark-executors.png)

Du kan också använda Ambari REST API för att programmässigt verifiera konfigurationsinställningarna för HDInsight- och Spark-kluster.  Mer information finns på den [Apache Ambari API-referens på GitHub](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Beroende på din Spark-arbetsbelastning kan du bestämma att en icke-standard Spark konfiguration ger mer optimerade Spark jobbkörningar.  Du bör utföra benchmark testning med exempelarbetsbelastningar att verifiera alla klusterkonfigurationer som inte är standard.  Några av de gemensamma parametrarna som du kan justera är:

* `--num-executors` Anger antalet executors.
* `--executor-cores` Anger antalet kärnor för varje executor. Vi rekommenderar att du använder middle-sized körare som förbrukar en del av det tillgängliga minnet även i andra processer.
* `--executor-memory` styr minnesstorleken (stackstorlek) på varje executor på [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), och du kommer att behöva lämna vissa minne för körning kostnader.

Här är ett exempel på två arbetarnoder med olika konfigurationsvärden:

![Två nodkonfigurationer](./media/apache-spark-settings/executor-config.png)

I följande lista visar viktiga Spark-executor minne parametrar.

* `spark.executor.memory` definierar den totala mängden tillgängligt minne för en executor.
* `spark.storage.memoryFraction` (standard ~ 60%) definierar hur mycket minne som är tillgängliga för att lagra beständiga rdd-datauppsättningar.
* `spark.shuffle.memoryFraction` (standard ~ 20%) definierar hur mycket minne som är reserverade för shuffle.
* `spark.storage.unrollFraction` och `spark.storage.safetyFraction` (sammanlagt ~ 30% av det totala minnet) – dessa värden används internt av Spark och bör inte ändras.

YARN styr högsta summan av minne som används av behållare på varje nod i Spark. Följande diagram visar per nod relationerna mellan konfigurationsobjekt för YARN och Spark-objekt.

![YARN Spark minneshantering](./media/apache-spark-settings/yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Ändra parametrarna för ett program som körs i Jupyter-anteckningsbok

Spark-kluster i HDInsight innehåller ett antal komponenter som standard. Var och en av dessa komponenter omfattar standardvärden för konfiguration, som kan åsidosättas efter behov.

* Spark Core - Spark Core, Spark SQL, Spark-strömning API: er, GraphX och Apache Spark MLlib.
* Anaconda - ett python paketera manager.
* [Apache Livy](https://livy.incubator.apache.org/) -Apache Spark REST API, används för att skicka fjärrstyrda jobb till ett HDInsight Spark-kluster.
* [Jupyter](https://jupyter.org/) och [Apache Zeppelin](https://zeppelin.apache.org/) anteckningsböcker - interaktiva webbläsarbaserat användargränssnitt för att interagera med ditt Spark-kluster.
* ODBC-drivrutinen - ansluter Spark-kluster i HDInsight till business intelligence (BI)-verktyg som Microsoft Power BI och Tableau.

För program som körs i Jupyter-anteckningsboken kan använda den `%%configure` kommandot för att göra konfiguration ändras från inom den bärbara datorn sig själv. Ändringarna tillämpas på Spark-jobb som körs från din bärbara dator-instans. Du bör kontrollera ändringarna i början av programmet, innan du kör ditt första kodcellen. Ändrade konfigurationen tillämpas på Livy-sessionen när den skapas.

> [!NOTE]  
> Du kan ändra konfigurationen senare i programmet med den `-f` (force)-parametern. Dock försvinner alla ändringar i programmet.

Koden nedan visar hur du ändrar konfigurationen för ett program som körs i en Jupyter-anteckningsbok.

```
    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Sammanfattning

Det finns ett antal inställningar för kärnor som du behöver för att övervaka och justera för att säkerställa att dina Spark-jobb köras på ett förutsägbart och effektivt sätt. Dessa inställningar att avgöra den bästa Spark-kluster-konfigurationen för din specifika arbetsbelastningar.  Du måste också övervaka körning av långvariga eller resursintensiva Spark jobbkörningar.  Den vanligaste utmaningar center runt minnesbelastning på grund av felaktiga konfigurationer (särskilt felaktigt medelstora executors), långvariga åtgärder och aktiviteter, vilket resulterar i kartesiska åtgärder.

## <a name="next-steps"></a>Nästa steg

* [Apache Hadoop-komponenter och versioner som är tillgängliga med HDInsight?](../hdinsight-component-versioning.md)
* [Hantera resurser för ett Apache Spark-kluster i HDInsight](apache-spark-resource-manager.md)
* [Konfigurera kluster i HDInsight med Apache Hadoop, Apache Spark, Apache Kafka med mera](../hdinsight-hadoop-provision-linux-clusters.md)
* [Apache Spark-konfiguration](https://spark.apache.org/docs/latest/configuration.html)
* [Köra Apache Spark på Apache Hadoop YARN](https://spark.apache.org/docs/latest/running-on-yarn.html)
