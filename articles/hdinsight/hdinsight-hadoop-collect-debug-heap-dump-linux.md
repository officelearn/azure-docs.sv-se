---
title: Aktivera heap Dumpar för Hadoop på HDInsight - Azure-tjänster | Microsoft Docs
description: Aktivera heap Dumpar Hadoop från Linux-baserade HDInsight-kluster för felsökning och analys-tjänster.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 8f151adb-f687-41e4-aca0-82b551953725
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: cd906736f2642d764c2b72a0572f63d675613c81
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="enable-heap-dumps-for-hadoop-services-on-linux-based-hdinsight"></a>Aktivera heap Dumpar för Hadoop-tjänster på Linux-baserat HDInsight

[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Heap Dumpar innehåller en ögonblicksbild av programmets minne, inklusive värdena för variabler vid tiden för dumpen skapades. Så att de är användbara för att diagnostisera problem som uppstår vid körning.

> [!IMPORTANT]
> Stegen i det här dokumentet fungerar endast med HDInsight-kluster som använder Linux. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="whichServices"></a>Tjänster

Du kan aktivera heap Dumpar för följande tjänster:

* **hcatalog** -tempelton
* **hive** -hiveserver2 metastore, derbyserver
* **mapreduce** -jobhistoryserver
* **yarn** -resurshanteraren, nodemanager, timelineserver
* **hdfs** -datanode secondarynamenode, namenode

Du kan också aktivera heap Dumpar för kartan och minska processer kördes av HDInsight.

## <a name="configuration"></a>Förstå heap dump konfiguration

Heap Dumpar aktiveras genom att skicka alternativ (ibland kallas bort, eller parametrar) till JVM när en tjänst har startats. Du kan ändra shell-skript som används för att starta tjänsten om du vill skicka dessa alternativ för de flesta Hadoop-tjänster.

I varje skript är en export för  **\* \_OPTS**, som innehåller de alternativ som angavs för JVM. Till exempel i den **hadoop env.sh** skript rad som börjar med `export HADOOP_NAMENODE_OPTS=` innehåller alternativ för tjänsten NameNode.

Mappa och minska processer är något annorlunda, eftersom dessa åtgärder är en underordnad process för MapReduce-tjänsten. Varje mappa eller minska processen körs i en underordnad behållare och det finns två poster som innehåller JVM-alternativ. Både i **mapred site.xml**:

* **mapreduce.Admin.Map.child.Java.opts**
* **mapreduce.Admin.Reduce.child.Java.opts**

> [!NOTE]
> Vi rekommenderar att du använder Ambari för att ändra inställningar för både skript och mapred site.xml, som Ambari referens replikera ändringar på noder i klustret. Finns det [med Ambari](#using-ambari) avsnittet specifika anvisningar.

### <a name="enable-heap-dumps"></a>Aktivera heap dumps

Följande alternativ kan heap minnesdumpar när en OutOfMemoryError inträffar:

    -XX:+HeapDumpOnOutOfMemoryError

Den **+** anger att det här alternativet är aktiverat. Standardvärdet är inaktiverad.

> [!WARNING]
> Heap Dumpar har inte aktiverats för Hadoop-tjänster på HDInsight standard dumpfiler kan vara stora. Om du aktiverar dem för felsökning kan du komma ihåg att inaktivera dem när du har försökt återskapa problemet och samlat in dumpfiler.

### <a name="dump-location"></a>Dump plats

Standardplatsen för dumpfilen är den aktuella arbetskatalogen. Du kan kontrollera om filen sparas med hjälp av följande alternativ:

    -XX:HeapDumpPath=/path

Till exempel använder `-XX:HeapDumpPath=/tmp` orsakar minnesdumpar lagras i tmp.

### <a name="scripts"></a>Skript

Du kan även utlösa ett skript när en **OutOfMemoryError** inträffar. Till exempel utlösa en avisering så att du vet att felet har uppstått. Använd följande alternativ för att utlösa ett skript på en __OutOfMemoryError__:

    -XX:OnOutOfMemoryError=/path/to/script

> [!NOTE]
> Eftersom Hadoop är ett distribuerat system, måste alla skript som används placeras på alla noder i klustret som tjänsten körs på.
> 
> Skriptet måste också vara på en plats som är tillgänglig för det konto som tjänsten körs som och måste ange behörighet att köra. Exempelvis kanske du vill lagra skript i `/usr/local/bin` och använda `chmod go+rx /usr/local/bin/filename.sh` att bevilja Läs- och körningsbehörighet.

## <a name="using-ambari"></a>Med Ambari

Om du vill ändra konfigurationen för en tjänst använder du följande steg:

1. Öppna Ambari webbgränssnittet för klustret. URL-Adressen är https://YOURCLUSTERNAME.azurehdinsight.net.

    När du uppmanas, autentisera till platsen med HTTP-kontonamnet (standard: admin) och lösenord för klustret.

   > [!NOTE]
   > Du kan uppmanas att en gång av Ambari användarnamn och lösenord. I så fall, ange samma kontonamn och lösenord

2. Använd listan över till vänster och markera området tjänst som du vill ändra. Till exempel **HDFS**. I området center väljer du den **konfigurationerna** fliken.

    ![Bild av Ambari web HDFS konfigurationerna fliken](./media/hdinsight-hadoop-heap-dump-linux/serviceconfig.png)

3. Med den **Filter...**  posten, ange **bort**. Endast objekt som innehåller den här texten visas.

    ![Filtrerad lista](./media/hdinsight-hadoop-heap-dump-linux/filter.png)

4. Hitta de  **\* \_OPTS** post för tjänsten som du vill aktivera heap Dumpar för och Lägg till de alternativ som du vill aktivera. I följande bild, jag har lagt till `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` till den **HADOOP\_NAMENODE\_OPTS** post:

    ![HADOOP_NAMENODE_OPTS med - XX: + HeapDumpOnOutOfMemoryError - XX: = HeapDumpPath/tmp /](./media/hdinsight-hadoop-heap-dump-linux/opts.png)

   > [!NOTE]
   > När Aktivera heap Dumpar för kartan eller minska underordnad process titta för fält med namnet **mapreduce.admin.map.child.java.opts** och **mapreduce.admin.reduce.child.java.opts**.

    Använd den **spara** för att spara ändringarna. Du kan ange en kort anteckning om ändringarna.

5. När ändringarna har tillämpats på **omstart krävs** ikon visas bredvid en eller flera tjänster.

    ![Starta om nödvändiga ikon och starta om knappen](./media/hdinsight-hadoop-heap-dump-linux/restartrequiredicon.png)

6. Markera varje tjänst som måste startas om och Använd den **tjänståtgärder** för att **aktivera på underhållsläge**. Underhållsläge förhindrar att aviseringar som genereras från tjänsten när du startar om.

    ![Aktivera menyn för underhåll läge](./media/hdinsight-hadoop-heap-dump-linux/maintenancemode.png)

7. När du har aktiverat underhållsläget kan använda den **starta om** knappen för tjänsten **starta om alla påverkas**

    ![Starta om alla berörda post](./media/hdinsight-hadoop-heap-dump-linux/restartbutton.png)

   > [!NOTE]
   > posterna för den **starta om** knappen kan vara olika för andra tjänster.

8. När tjänsten har startats om, använda den **tjänståtgärder** för att **aktivera inaktivera underhållsläge**. Den här Ambari att återuppta övervakning för aviseringar för tjänsten.

