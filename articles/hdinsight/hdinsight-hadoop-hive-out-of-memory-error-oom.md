---
title: "Åtgärda registreringsdata slut på minne i Azure HDInsight | Microsoft Docs"
description: "Åtgärda registreringsdata slut på minne i HDInsight. Kund-scenario är en fråga i många stora tabeller."
keywords: "slut på minne fel, OOM, Hive inställningar"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 7bce3dff-9825-4fa0-a568-c52a9f7d1dad
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/22/2018
ms.author: jgao
ms.openlocfilehash: ea83a6b4fa3399ae6a30157c720005683d445d8a
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2018
---
# <a name="fix-a-hive-out-of-memory-error-in-azure-hdinsight"></a>Åtgärda registreringsdata slut på minne i Azure HDInsight

Lär dig hur du löser en Hive slut på minne när bearbeta stora tabeller genom att konfigurera inställningar för Hive-minne.

## <a name="run-hive-query-against-large-tables"></a>Köra Hive-fråga mot stora tabeller

En kund körde en Hive-fråga:

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

Vissa olika delarna i den här frågan:

* T1 är ett alias till en stor tabell tabell1 som har många typer av STRÄNGEN.
* Andra tabeller är inte som stora men har många kolumner.
* Alla tabeller som ansluter till varandra, i vissa fall med flera kolumner i tabell 1 och andra.

Hive-frågan tog 26 minuter på en 24-nod A3 HDInsight-kluster. Kunden har upptäckt följande varning visas:

    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

Med hjälp av Tez-Körningsmotor. Samma fråga kördes i 15 minuter och sedan returnerade följande fel:

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

Felet fortfarande när du använder en större virtuell dator (till exempel D12).


## <a name="debug-the-out-of-memory-error"></a>Felsöka out-of-minne

Vår support och teknikerna snappa tillsammans var ett av de problem som orsakar out-of-minnesfel en [kända problem som beskrivs i Apache JIRA](https://issues.apache.org/jira/browse/HIVE-8306):

    When hive.auto.convert.join.noconditionaltask = true we check noconditionaltask.size and if the sum  of tables sizes in the map join is less than noconditionaltask.size the plan would generate a Map join, the issue with this is that the calculation doesnt take into account the overhead introduced by different HashTable implementation as results if the sum of input sizes is smaller than the noconditionaltask size by a small margin queries will hit OOM.

Den **hive.auto.convert.join.noconditionaltask** i hive-site.xml filen har ställts in **SANT**:

    <property>
        <name>hive.auto.convert.join.noconditionaltask</name>
        <value>true</value>
        <description>
              Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
              If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
              specified size, the join is directly converted to a mapjoin (there is no conditional task).
        </description>
      </property>

Det är troligt kartan koppling var orsaken till området Java Heap våra av minnesfel. Enligt beskrivningen i blogginlägget [Hadoop Yarn minnesinställningarna i HDInsight](http://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx)när Tez-Körningsmotor är används heap utrymmet faktiskt tillhör Tez-behållare. Se följande bild som beskriver Tez behållare minne.

![Tez behållaren minne diagram: Hive slut på minne](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)

Blogginlägget föreslår följande två minnesinställningarna definiera behållaren minne för heap: **hive.tez.container.size** och **hive.tez.java.opts**. Av erfarenhet innebär inte out-of-minne undantag behållarens storlek är för liten. Det innebär att Java heap-storlek (hive.tez.java.opts) är för liten. Så när du ser slut på minne kan du försöka att öka **hive.tez.java.opts**. Om det behövs kan du behöva öka **hive.tez.container.size**. Den **java.opts** inställningen ska vara runt 80% av **container.size**.

> [!NOTE]
> Inställningen **hive.tez.java.opts** måste alltid vara mindre än **hive.tez.container.size**.
> 
> 

Eftersom en D12 dator har 28GB minne, beslutat vi att använda en behållare storlek på 10GB (10240MB) och tilldela java.opts 80%.

    SET hive.tez.container.size=10240
    SET hive.tez.java.opts=-Xmx8192m

Med de nya inställningarna körde frågan under 10 minuter.

## <a name="next-steps"></a>Nästa steg

Hämta ett OOM fel innebär inte nödvändigtvis behållarens storlek är för liten. I stället bör du konfigurera minnesinställningarna så att heap-storlek ökas och minst 80% av minnesstorlek för behållaren. För att optimera Hive-frågor finns [optimera Hive-frågor för Hadoop i HDInsight](hdinsight-hadoop-optimize-hive-query.md).