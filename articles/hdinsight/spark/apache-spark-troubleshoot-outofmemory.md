---
title: Undantag för OutOfMemoryError för Apache Spark i Azure HDInsight
description: Olika OutOfMemoryError undantag för Apache Spark kluster i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/15/2019
ms.openlocfilehash: 31cdef281b1cb26d01a4690c815e3d3621e2c053
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271972"
---
# <a name="outofmemoryerror-exceptions-for-apache-spark-in-azure-hdinsight"></a>Undantag för OutOfMemoryError för Apache Spark i Azure HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du använder Apache Spark-komponenter i Azure HDInsight-kluster.

## <a name="scenario-outofmemoryerror-exception-for-apache-spark"></a>Scenario: OutOfMemoryError undantag för Apache Spark

### <a name="issue"></a>Problem

Ditt Apache Spark-program misslyckades med ett oföränderliga undantag. Ett felmeddelande kan visas som liknar:

```error
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439)

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.grow(Unknown Source)
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.write(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source)
    at java.io.ObjectOutputStream.writeObject0(Unknown Source)
    at java.io.ObjectOutputStream.writeObject(Unknown Source)
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44)
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101)
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source)
    at java.lang.Thread.run(Unknown Source)
```

```error
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main]

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    ...
```

### <a name="cause"></a>Orsak

Den mest sannolika orsaken till detta undantag är att inte tillräckligt med heapminne allokeras till virtuella Java-datorer (JVMs). Dessa JVMs startas som executors eller drivrutiner som en del av Apache Spark-programmet.

### <a name="resolution"></a>Lösning

1. Avgör maximal storlek för de data som Spark-programmet ska hantera. Gör en uppskattning av storleken baserat på den maximala storleken på indata, mellanliggande data som produceras genom att omvandla indata och de utdata som produceras ytterligare omvandla mellanliggande data. Om den ursprungliga uppskattningen inte är tillräcklig, öka storleken något och iterera tills minnesfelen avtar.

1. Se till att HDInsight-klustret som ska användas har tillräckligt med resurser när det gäller minne och tillräckligt med kärnor för Spark-programmet. Detta kan bestämmas genom att visa avsnittet Klustermått i YARN-gränssnittet i klustret för värdena **minne som används** jämfört med totalt **minne** och **virtuella kärnor som används** jämfört med **VCores Total**.

    ![garn kärna minne visa](./media/apache-spark-ts-outofmemory/yarn-core-memory-view.png)

1. Ställ in följande Spark-konfigurationer till lämpliga värden. Balansera programkraven med de tillgängliga resurserna i klustret. Dessa värden bör inte överstiga 90% av det tillgängliga minnet och kärnorna enligt YARN, och bör också uppfylla minimikravet för Spark-programmet:

    ```
    spark.executor.instances (Example: 8 for 8 executor count)
    spark.executor.memory (Example: 4g for 4 GB)
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB)
    spark.executor.cores (Example: 2 for 2 cores per executor)
    spark.driver.memory (Example: 8g for 8GB)
    spark.driver.cores (Example: 4 for 4 cores)
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB)
    ```

    Totalt minne som används av alla utförare =

    ```
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```

    Totalt minne som används av drivrutinen =

    ```
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

---

## <a name="scenario-java-heap-space-error-when-trying-to-open-apache-spark-history-server"></a>Scenario: Java heap utrymme fel när man försöker öppna Apache Spark historia server

### <a name="issue"></a>Problem

Följande felmeddelande visas när du öppnar händelser på Spark History-servern:

```
scala.MatchError: java.lang.OutOfMemoryError: Java heap space (of class java.lang.OutOfMemoryError)
```

### <a name="cause"></a>Orsak

Det här problemet orsakas ofta av brist på resurser när stora spark-event-filer öppnas. Spark-högstorleken är inställd på 1 GB som standard, men stora Spark-händelsefiler kan kräva mer än så.

Om du vill kontrollera storleken på de filer som du försöker läsa in kan du utföra följande kommandon:

```bash
hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0274_1/
**576.5 M**  wasb:///hdp/spark2-events/application_1503957839788_0274_1

hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0264_1/
**2.1 G**  wasb:///hdp/spark2-events/application_1503957839788_0264_1
```

### <a name="resolution"></a>Lösning

Du kan öka Spark History Server-minnet genom att redigera egenskapen `SPARK_DAEMON_MEMORY` i Spark-konfigurationen och starta om alla tjänster.

Du kan göra detta inifrån webbläsaren Ambari genom att välja avsnittet Spark2/Config/Advanced spark2-env.

![Avancerad spark2-env avsnitt](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image01.png)

Lägg till följande egenskap för att ändra Spark History Server-minnet från 1g till 4g: `SPARK_DAEMON_MEMORY=4g`.

![Egenskapen Spark](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image02.png)

Se till att starta om alla berörda tjänster från Ambari.

---

## <a name="scenario-livy-server-fails-to-start-on-apache-spark-cluster"></a>Scenario: Livy Server kan inte starta på Apache Spark kluster

### <a name="issue"></a>Problem

Livy Server kan inte startas på en Apache Spark [(Spark 2.1 på Linux (HDI 3.6)]. Om du försöker starta om resulterar du i följande felstapel, från Livy-loggarna:

```log
17/07/27 17:52:50 INFO CuratorFrameworkImpl: Starting
17/07/27 17:52:50 INFO ZooKeeper: Client environment:zookeeper.version=3.4.6-29--1, built on 05/15/2017 17:55 GMT
17/07/27 17:52:50 INFO ZooKeeper: Client environment:host.name=10.0.0.66
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.version=1.8.0_131
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.vendor=Oracle Corporation
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.home=/usr/lib/jvm/java-8-openjdk-amd64/jre
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.class.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.library.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.io.tmpdir=/tmp
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.compiler=<NA>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.name=Linux
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.arch=amd64
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.version=4.4.0-81-generic
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.name=livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.home=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.dir=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Initiating client connection, connectString=zk2-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk3-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk6-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181 sessionTimeout=60000 watcher=org.apache.curator.ConnectionState@25fb8912
17/07/27 17:52:50 INFO StateStore$: Using ZooKeeperStateStore for recovery.
17/07/27 17:52:50 INFO ClientCnxn: Opening socket connection to server 10.0.0.61/10.0.0.61:2181. Will not attempt to authenticate using SASL (unknown error)
17/07/27 17:52:50 INFO ClientCnxn: Socket connection established to 10.0.0.61/10.0.0.61:2181, initiating session
17/07/27 17:52:50 INFO ClientCnxn: Session establishment complete on server 10.0.0.61/10.0.0.61:2181, sessionid = 0x25d666f311d00b3, negotiated timeout = 60000
17/07/27 17:52:50 INFO ConnectionStateManager: State change: CONNECTED
17/07/27 17:52:50 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
17/07/27 17:52:50 INFO AHSProxy: Connecting to Application History server at headnodehost/10.0.0.67:10200
Exception in thread "main" java.lang.OutOfMemoryError: unable to create new native thread
  at java.lang.Thread.start0(Native Method)
  at java.lang.Thread.start(Thread.java:717)
  at com.cloudera.livy.Utils$.startDaemonThread(Utils.scala:98)
  at com.cloudera.livy.utils.SparkYarnApp.<init>(SparkYarnApp.scala:232)
  at com.cloudera.livy.utils.SparkApp$.create(SparkApp.scala:93)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:117)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:116)
  at com.cloudera.livy.server.batch.BatchSession.<init>(BatchSession.scala:137)
  at com.cloudera.livy.server.batch.BatchSession$.recover(BatchSession.scala:108)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.mutable.ResizableArray$class.foreach(ResizableArray.scala:59)
  at scala.collection.mutable.ArrayBuffer.foreach(ArrayBuffer.scala:47)
  at scala.collection.TraversableLike$class.map(TraversableLike.scala:244)
  at scala.collection.AbstractTraversable.map(Traversable.scala:105)
  at com.cloudera.livy.sessions.SessionManager.com$cloudera$livy$sessions$SessionManager$$recover(SessionManager.scala:150)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at scala.Option.getOrElse(Option.scala:120)
  at com.cloudera.livy.sessions.SessionManager.<init>(SessionManager.scala:82)
  at com.cloudera.livy.sessions.BatchSessionManager.<init>(SessionManager.scala:42)
  at com.cloudera.livy.server.LivyServer.start(LivyServer.scala:99)
  at com.cloudera.livy.server.LivyServer$.main(LivyServer.scala:302)
  at com.cloudera.livy.server.LivyServer.main(LivyServer.scala)
  
  ## using "vmstat" found  we had enough free memory
```

### <a name="cause"></a>Orsak

`java.lang.OutOfMemoryError: unable to create new native thread`markerar os kan inte tilldela fler inbyggda trådar till JVMs. Bekräftat att det här undantaget orsakas av brott mot gränsen för per process trådantal.

När Livy Server avslutas oväntat avslutas även alla anslutningar till Spark-kluster, vilket innebär att alla jobb och relaterade data går förlorade. I HDP 2.6 session återvinnande mekanismen var introducerat, Livy lagren session detaljerna i Zookeeper till vara återvinna efter den Livy Servaren är rygg.

När ett stort antal jobb skickas via Livy lagras dessa sessionstillstånd i ZK (på HDInsight-kluster) som en del av Hög tillgänglighet för Livy Server i ZK (på HDInsight-kluster) och återställer dessa sessioner när Livy-tjänsten startas om. Vid omstart efter oväntad avslutning skapar Livy en tråd per session och detta ackumulerar ett visst antal att återställa sessioner som orsakar för många trådar som skapas.

### <a name="resolution"></a>Lösning

Ta bort alla poster med hjälp av stegen nedan.

1. Hämta IP-adressen för zookeeper noder med

    ```bash
    grep -R zk /etc/hadoop/conf  
    ```

1. Ovanstående kommando listade alla zookeepers för mitt kluster

    ```bash
    /etc/hadoop/conf/core-site.xml:      <value>zk1-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk2-      hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk4-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181</value>
    ```

1. Få alla IP-adressen för zookeeper noder med ping Eller så kan du också ansluta till zookeeper från headnode med zk namn

    ```bash
    /usr/hdp/current/zookeeper-client/bin/zkCli.sh -server zk2-hwxspa:2181
    ```

1. När du är ansluten till zookeeper köra följande kommando för att lista alla sessioner som försöker starta om.

    1. De flesta fall kan detta vara en lista över mer än 8000 sessioner ####

        ```bash
        ls /livy/v1/batch
        ```

    1. Följande kommando är att ta bort alla att-vara-återvunna sessioner. #####

        ```bash
        rmr /livy/v1/batch
        ```

1. Vänta tills kommandot ovan har slutförts och markören returnerar prompten och startar sedan om Livy-tjänsten från Ambari, vilket bör lyckas.

> [!NOTE]
> `DELETE`den liviga sessionen när den är avslutad sin avrättning. Livy-batchsessionerna tas inte bort automatiskt så snart spark-appen är klar, vilket är avsiktligt. En Livy-session är en entitet som skapas av en POST-begäran mot Livy Rest-server. Ett `DELETE` samtal behövs för att ta bort den entiteten. Eller så väntar vi på att GC ska sparka in.

---

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* [Översikt över hantering av sparkminne](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Felsökning Spark-programmet på HDInsight-kluster](https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/).

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen. Ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i [Så här skapar du en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
