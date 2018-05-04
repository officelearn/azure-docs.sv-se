---
title: Konfigurera inställningar för Spark - Azure HDInsight | Microsoft Docs
description: Hur du konfigurerar Spark för HDInsight-kluster.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/26/2018
ms.author: maxluk
ms.openlocfilehash: 2ee496eae0767de22d070a0c5689692f0200515b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="configure-spark-settings"></a>Konfigurera Spark-inställningar

Ett HDInsight Spark-kluster innehåller en installation av Apache Spark-biblioteket.  Varje HDInsight-kluster innehåller standard konfigurationsparametrar för alla installerade tjänsterna, inklusive Spark.  En viktig del av hantering av ett HDInsight Hadoop-kluster övervakar arbetsbelastning, inklusive Spark-jobb, kontrollera att jobben körs på ett förutsägbart sätt. Överväg att fysiska klusterkonfigurationen för att köra Spark bäst jobb när du bestämmer hur du optimerar logiska klusterkonfigurationen.

Standard HDInsight Apache Spark-kluster innehåller följande noder: tre ZooKeeper-noder, två huvudnoderna och en eller flera arbetsnoderna:

![Spark HDInsight-arkitektur](./media/apache-spark-settings/spark-hdinsight-arch.png)

Antal virtuella datorer och VM-storlekar för noderna i ditt HDInsight-kluster kan du också påverka Spark-konfiguration. Icke-standard HDInsight konfigurationsvärden kräver ofta inte är standard Spark konfigurationsvärden. När du skapar ett HDInsight Spark-kluster visas föreslagna VM-storlekar för varje komponent. För närvarande den [minnesoptimerade Linux VM-storlekar](../../virtual-machines/linux/sizes-memory.md) för Azure är D12 v2 eller större.

## <a name="spark-versions"></a>Spark-versioner

Använd den bästa Spark-versionen för klustret.  HDInsight-tjänsten innehåller flera versioner av både Spark och HDInsight sig själv.  Varje version av Spark innehåller en uppsättning standardinställningar för klustret.  

När du skapar ett nytt kluster är här de aktuella Spark-versionerna att välja mellan:

![Spark-versioner](./media/apache-spark-settings/spark-version.png)

Spark 2.x kan köra mycket bättre än Väck 1.x. Spark 2.x har ett antal prestandaoptimeringar, till exempel volfram och Catalyst optimeringsnivån.  

> [!NOTE]
> Standardversionen av Apache Spark i HDInsight-tjänst kan ändras utan föregående meddelande. Om du har ett beroende rekommenderar Microsoft att du anger att viss version när du skapar kluster med .NET SDK-/ Azure PowerShell och Azure CLI.

Apache Spark har tre system configuration platser:

* Spark egenskaper som styr parametrar för de flesta program och kan anges med hjälp av en `SparkConf` objektet, eller via Java Systemegenskaper.
* Miljövariabler kan användas för att ange inställningar för per dator, till exempel IP-adress via den `conf/spark-env.sh` skript på varje nod.
* Loggning kan konfigureras via `log4j.properties`.

När du väljer en viss version av Spark innehåller standardinställningarna i klustret.  Du kan ändra standardvärden för konfigurering av Spark genom att använda en anpassad Spark-konfigurationsfil.  Ett exempel visas nedan.

```
    spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
    spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
    spark.hadoop.parquet.block.size 1099511627776
    spark.sql.files.maxPartitionBytes 1099511627776
    spark.sql.files.openCostInBytes 1099511627776
```

I exemplet ovan åsidosätter flera standardvärden för parametrar med fem Spark-konfiguration.  Dessa är komprimerings-codec, Hadoop MapReduce dela minsta storlek och parkettgolv blockstorlekar och även Spar SQL-partition och öppna filstorlekar standardvärdena.  De här konfigurationsändringarna väljs eftersom associerade data och jobb (i det här exemplet genom data) har specifika egenskaper som utför bättre med följande inställningar för anpassad konfiguration.

---

## <a name="view-cluster-configuration-settings"></a>Visa inställningar för klustrets

Kontrollera de aktuella inställningarna för HDInsight-klustret innan du utför prestandaoptimering i klustret. Starta HDInsight instrumentpanelen i Azure Portal genom att klicka på **instrumentpanelen** länk på fönstret Spark-kluster. Logga in med Klusteradministratören användarnamn och lösenord.

Ambari-Webbgränssnittet visas med en instrumentpanelsvy med viktiga klustret resurs användning mått.  Ambari-instrumentpanelen visar Apache Spark-konfigurationen och andra tjänster som du har installerat. Instrumentpanelen innehåller en **Konfigurationshistorik** fliken, där du kan visa konfigurationsinformation för alla installerade tjänster, inklusive Spark.

Om du vill se konfigurationsvärden för Apache Spark, Välj **Konfigurationshistorik**och välj **Spark2**.  Välj den **konfigurationerna** fliken och väljer sedan den `Spark` (eller `Spark2`, beroende på din version) länken i listan.  Du kan se en lista över konfigurationsvärden för klustret:

![Spark-konfigurationer](./media/apache-spark-settings/spark-config.png)

Om du vill se och ändra enskilda Spark konfigurationsvärden, markerar du en länk med ordet ”spark” i rubriken för länken.  Konfigurationer för Spark omfattar både anpassade och avancerade konfigurationsvärden i dessa kategorier:

* Anpassade Spark2 standardvärden
* Anpassade Spark2-mått-egenskaper
* Avancerade Spark2 standardvärden
* Avancerade Spark2 env
* Avancerade spark2-hive-plats-åsidosättande

Om du skapar en uppsättning konfigurationsvärden inte är standard, kan du också se historiken över configuration-uppdateringar.  Konfigurationshistorik för den här kan vara användbart att se vilken konfiguration som inte är standard har optimala prestanda.

> [!NOTE]
> Om du vill se, men inte ändra vanliga Spark-kluster-konfigurationsinställningar, Välj den **miljö** fliken på den översta **Spark jobbet UI** gränssnitt.

## <a name="configuring-spark-executors"></a>Konfigurera Spark executors

Följande diagram visar viktiga Spark-objekt: driver-program och dess associerade Spark-kontext och Klusterhanteraren och dess *n* arbetsnoderna.  Varje arbetsnod innehåller en utförare en cache och *n* uppgift instanser.

![Klusterobjekt](./media/apache-spark-settings/spark-arch.png)

Spark använder worker-resurser, särskilt minne, så det är vanligt att justera Spark konfigurationsvärden för arbetsnoden Executors.

Tre viktiga parametrar som ofta justeras för att finjustera Spark-konfigurationer för att förbättra programkrav är `spark.executor.instances`, `spark.executor.cores`, och `spark.executor.memory`. En utförare är en process startas för ett Spark-program. En utförare körs på arbetsnoden och ansvarar för uppgifter för programmet. För varje kluster beräknas standardantalet executors och utföraren-storlekar baserat på antalet arbetarnoder och nodstorlek worker. Dessa lagras i `spark-defaults.conf` head noderna i klustret.  Du kan redigera dessa värden i ett kluster som körs genom att välja den **anpassad spark-standarder** länken i Ambari-webbgränssnittet.  När du gör ändringar, uppmanas du av UI till **starta om** alla påverkade tjänster.

> [!NOTE]
> Dessa tre konfigurationsparametrar kan konfigureras på klusternivå (för alla program som körs på klustret) och också har angetts för varje enskilt program.

En annan källa för information om de resurser som används av Utförarna för Spark är Gränssnittet Spark-program.  I Användargränssnittet för Spark, välja den **Executors** fliken för att visa sammanfattning och information vyer för konfiguration och resurser som används av executors.  Vyerna kan hjälpa dig att avgöra om du vill ändra standardvärdena för Spark Utförarna för hela klustret eller en viss uppsättning jobb körningar.

![Spark Executors](./media/apache-spark-settings/spark-executors.png)

Du kan också använda Ambari REST API för att verifiera HDInsight Spark-klusterinställningarna och programmässigt.  Mer information finns på den [Ambari API-referens på GitHub](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Du kan bestämma att en icke-Spark standardkonfiguration innehåller fler optimerade Spark jobbet körningar beroende på din Spark-arbetsbelastning.  Du bör utföra benchmark testning med exempel arbetsbelastningar för att verifiera alla klusterkonfigurationer som inte är standard.  Några av de gemensamma parametrarna kan du justera är:

* `--num-executors` Anger antalet executors.
* `--executor-cores` Anger antalet kärnor för varje utförare. Vi rekommenderar att du använder middle-sized executors som en del av det tillgängliga minnet förbrukar även i andra processer.
* `--executor-memory` kontroller minnesstorlek (heapstorlek) för varje utföraren på YARN, och du behöver lämna minne för körning av arbetet.

Här är ett exempel på två arbetarnoder med olika konfigurationsvärden:

![Två nodkonfigurationer](./media/apache-spark-settings/executor-config.png)

I följande lista visar viktiga Spark utföraren minne parametrar.

* `spark.executor.memory` definierar den totala mängden tillgängligt minne för en utförare.
* `spark.storage.memoryFraction` (standard ~ 60%) definierar mängden ledigt minne för att lagra beständiga RDDs.
* `spark.shuffle.memoryFraction` (standard ~ 20%) definierar hur mycket minne som reserverats för blanda.
* `spark.storage.unrollFraction` och `spark.storage.safetyFraction` (summering ~ 30% av det totala minnet) - dessa värden används internt av Spark och bör inte ändras.

YARN styr maximala summan av minne som används av behållare på varje nod i Spark. Följande diagram visar per nod relationerna mellan YARN konfiguration och Spark-objekt.

![YARN Spark minneshantering](./media/apache-spark-settings/yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>Ändra parametrarna för ett program som körs i Jupyter-anteckningsbok

Spark-kluster i HDInsight innehåller ett antal komponenter som standard. Var och en av dessa komponenter inkluderar standardvärden för konfiguration, som kan åsidosättas vid behov.

* Väck Core - Spark Core, Spark SQL, Väck strömmande API: er, GraphX och MLlib
* Anaconda - paketet en python manager
* Livius - Apache Spark REST-API som används för att skicka remote jobb till ett HDInsight Spark-kluster
* Jupyter och Zeppelin-anteckningsböcker - interaktiva webbläsarbaserade Gränssnittet för att interagera med Spark-kluster
* ODBC-drivrutinen - ansluter Spark-kluster i HDInsight till verktyg för business intelligence (BI), till exempel Microsoft Power BI och Tableau

För program som körs i Jupyter-anteckningsbok kan använda den `%%configure` kommando för att göra konfiguration ändras från inom den bärbara datorn sig själv. Ändringarna tillämpas på Spark-jobb som körs från din bärbara dator-instans. Du bör göra dessa ändringar i början av programmet innan du kör din första kodcellen. Ändrade konfigurationen tillämpas Livius sessionen när den skapas.

> [!NOTE]
> Om du vill ändra konfigurationen senare i programmet i `-f` (force)-parametern. Dock försvinner alla förlopp i programmet.

Koden nedan visar hur du ändrar konfigurationen för ett program som körs i en Jupyter-anteckningsbok.

```
    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>Sammanfattning

Det finns ett antal kärnor konfigurationsinställningar som du behöver för att övervaka och justera för att säkerställa Spark-jobb som körs på ett förutsägbart och performant sätt. De här inställningarna kan fastställa bästa Spark klusterkonfigurationen för dina specifika arbetsbelastningar.  Du måste också att övervaka körning av tidskrävande och/eller resurs förbrukar Spark jobbet körningar.  Den vanligaste utmaningar center runt minnesbelastning på grund av felaktiga konfigurationer (särskilt felaktigt storlek executors), långvariga åtgärder och aktiviteter, vilket resulterar i kartesiska åtgärder.

## <a name="next-steps"></a>Nästa steg

* [Komponenterna i Hadoop och versioner som är tillgängliga med HDInsight?](../hdinsight-component-versioning.md)
* [Hantera resurser för ett Spark-kluster i HDInsight](apache-spark-resource-manager.md)
* [Ställ in kluster i HDInsight Hadoop, Spark, Kafka och mycket mer](../hdinsight-hadoop-provision-linux-clusters.md)
* [Apache Spark-konfiguration](https://spark.apache.org/docs/latest/configuration.html)
* [Köra Spark på YARN](https://spark.apache.org/docs/latest/running-on-yarn.html)
