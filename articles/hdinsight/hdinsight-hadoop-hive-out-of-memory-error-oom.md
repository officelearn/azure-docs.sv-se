---
title: Åtgärda ett hive-fel i minnet i Azure HDInsight
description: Åtgärda ett Hive-fel i minnet i HDInsight. Kundscenariot är en fråga i många stora tabeller.
keywords: på minnesfel, OOM, Hive-inställningar
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: hdinsightactive
ms.date: 11/28/2019
ms.openlocfilehash: add55c29bb93d8dce9ad69bd9850a1db02ea5afe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687772"
---
# <a name="fix-an-apache-hive-out-of-memory-error-in-azure-hdinsight"></a>Åtgärda ett Apache Hive-fel i minnet i Azure HDInsight

Lär dig hur du åtgärdar ett Apache Hive-fel (OOM) när du bearbetar stora tabeller genom att konfigurera Hive-minnesinställningar.

## <a name="run-apache-hive-query-against-large-tables"></a>Kör Apache Hive-fråga mot stora tabeller

En kund körde en Hive-fråga:

```sql
SELECT
    COUNT (T1.COLUMN1) as DisplayColumn1,
    …
    …
    ….
FROM
    TABLE1 T1,
    TABLE2 T2,
    TABLE3 T3,
    TABLE5 T4,
    TABLE6 T5,
    TABLE7 T6
where (T1.KEY1 = T2.KEY1….
    …
    …
```

Några nyanser i den här frågan:

* T1 är ett alias till en stor tabell, TABLE1, som har många STRING-kolumntyper.
* Andra tabeller är inte så stora men har många kolumner.
* Alla tabeller sammanfogar varandra, i vissa fall med flera kolumner i TABLE1 och andra.

Hive-frågan tog 26 minuter att slutföra på ett 24 nod A3 HDInsight-kluster. Kunden lade märke till följande varningsmeddelanden:

    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

Genom att använda Apache Tez-utförandemotorn. Samma fråga kördes i 15 minuter och kastade sedan följande fel:

    Status: Failed
    Vertex failed, vertexName=Map 5, vertexId=vertex_1443634917922_0008_1_05, diagnostics=[Task failed, taskId=task_1443634917922_0008_1_05_000006, diagnostics=[TaskAttempt 0 failed, info=[Error: Failure while running task:java.lang.RuntimeException: java.lang.OutOfMemoryError: Java heap space
        at
    org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:172)
        at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.run(TezProcessor.java:138)
        at
    org.apache.tez.runtime.LogicalIOProcessorRuntimeTask.run(LogicalIOProcessorRuntimeTask.java:324)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:176)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:168)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:415)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1628)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:168)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:163)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
    Caused by: java.lang.OutOfMemoryError: Java heap space

Felet kvarstår när du använder en större virtuell dator (till exempel D12).

## <a name="debug-the-out-of-memory-error"></a>Felsöka fel i minnet

Vår support och ingenjörsteam tillsammans hittade en av de problem som orsakar ut ur minnet fel var ett [känt problem som beskrivs i Apache JIRA:](https://issues.apache.org/jira/browse/HIVE-8306)

"När hive.auto.convert.join.noconditionaltask = sant vi kontrollerar noconditionaltask.size och om summan av tabeller storlekar i kartan gå är mindre än noconditionaltask.size planen skulle generera en Karta gå, problemet med detta är att beräkningen inte tar hänsyn till de omkostnader som införts av olika HashTable-implementering som resultat om summan av indatastorlekar är mindre än noconditionaltask-storleken med en liten marginal frågor kommer att drabba OOM."

Filen **hive.auto.convert.join.noconditionaltask** i filen hive-site.xml angavs till **true:**

```xml
<property>
    <name>hive.auto.convert.join.noconditionaltask</name>
    <value>true</value>
    <description>
            Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
            If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
            specified size, the join is directly converted to a mapjoin (there is no conditional task).
    </description>
</property>
```

Det är troligt att kartkopplingen var orsaken till Java Heap Space out of memory error. Som förklaras i blogginlägget [Hadoop Yarn minnesinställningar i HDInsight](https://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx), när Tez utförande motor används högen utrymme som används faktiskt tillhör Tez behållaren. Se följande bild som beskriver Tez-behållarminnet.

![Minnesdiagram för Tez-behållare: Hive-fel i minnet](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)

Som blogginlägget antyder definierar följande två minnesinställningar behållarminnet för högen: **hive.tez.container.size** och **hive.tez.java.opts**. Från vår erfarenhet betyder undantaget för slutminnet inte att behållarstorleken är för liten. Det betyder att Java högstorlek (hive.tez.java.opts) är för liten. Så när du ser ur minnet, kan du försöka öka **hive.tez.java.opts**. Om det behövs kan du behöva öka **hive.tez.container.size**. Inställningen **java.opts** bör vara cirka 80 % av **container.size.**

> [!NOTE]  
> Inställningen **hive.tez.java.opts** måste alltid vara mindre än **hive.tez.container.size**.

Eftersom en D12-maskin har 28 GB minne bestämde vi oss för att använda en behållarstorlek på 10 GB (10240 MB) och tilldelar 80 % till java.opts:

    SET hive.tez.container.size=10240
    SET hive.tez.java.opts=-Xmx8192m

Med de nya inställningarna kördes frågan på under 10 minuter.

## <a name="next-steps"></a>Nästa steg

Att få ett OOM-fel betyder inte nödvändigtvis att behållarstorleken är för liten. I stället bör du konfigurera minnesinställningarna så att heap-storleken ökas och är minst 80 % av behållarens minnesstorlek. För att optimera Hive-frågor, se [Optimera Apache Hive-frågor för Apache Hadoop i HDInsight](hdinsight-hadoop-optimize-hive-query.md).
