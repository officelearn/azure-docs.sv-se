---
title: Aktivera heap dumps för tjänster som Apache Hadoop på HDInsight - Azure
description: Aktivera heap dumps för Apache Hadoop-tjänster från Linux-baserade HDInsight-kluster för felsökning och analys.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: d4245ce35cfc1e3aa0ba9ee9307315c9a999b5ff
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53722054"
---
# <a name="enable-heap-dumps-for-apache-hadoop-services-on-linux-based-hdinsight"></a>Aktivera heap dumps för Apache Hadoop-tjänster på Linux-baserat HDInsight

[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Heapdumpar innehåller en ögonblicksbild av programmets minne, inklusive värdena för variabler vid tidpunkten dumpen skapades. Så att de är användbara för att diagnostisera problem som uppstår vid körning.

> [!IMPORTANT]  
> Stegen i det här dokumentet fungerar bara med HDInsight-kluster som använder Linux. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="whichServices"></a>Tjänster

Du kan aktivera heap dumps för följande tjänster:

* **Apache hcatalog** -tempelton
* **Apache hive** -hiveserver2 och metaarkiv, derbyserver
* **mapreduce** -jobhistoryserver
* **Apache yarn** -resourcemanager nodemanager, timelineserver
* **Apache hdfs** -datanode secondarynamenode, namenode

Du kan också aktivera heap dumps för kartan och minska processer som körts av HDInsight.

## <a name="configuration"></a>Förstå heap dump konfiguration

Heapdumpar aktiveras genom att skicka alternativ (ibland kallas innebär, eller parametrar) till JVM när en tjänst har startats. För de flesta [Apache Hadoop](https://hadoop.apache.org/) tjänster, som du kan ändra shell-skript som används för att starta tjänsten om du vill skicka dessa alternativ.

I varje skript finns en export för  **\* \_OPTS**, som innehåller de alternativ som skickas till i JVM. Till exempel i den **hadoop env.sh** skript och raden som börjar med `export HADOOP_NAMENODE_OPTS=` innehåller alternativ för tjänsten NameNode.

Mappa och minska processer är något annorlunda, eftersom de här åtgärderna är en underordnad process för MapReduce-tjänsten. Var och en mappa eller minska processen körs i en underordnad behållare och det finns två poster som innehåller alternativ för JVM. Både i **mapred site.xml**:

* **mapreduce.Admin.Map.child.Java.opts**
* **mapreduce.Admin.Reduce.child.Java.opts**

> [!NOTE]  
> Vi rekommenderar att du använder [Apache Ambari](https://ambari.apache.org/) om du vill ändra inställningar för både skript och mapred site.xml som Ambari hantera replikering av ändringar över noder i klustret. Se den [med hjälp av Apache Ambari](#using-apache-ambari) för specifika steg.

### <a name="enable-heap-dumps"></a>Aktivera heap dumps

Följande alternativ kan heapdumpar när en OutOfMemoryError inträffar:

    -XX:+HeapDumpOnOutOfMemoryError

Den **+** indikerar att det här alternativet är aktiverat. Standardvärdet är inaktiverad.

> [!WARNING]  
> Heapdumpar har inte aktiverats för Hadoop-tjänster på HDInsight som standard som filer med felsökningsdumpar kan vara stora. Om du aktiverar dem för felsökning kan du komma ihåg att inaktivera dem. när du har problemet och samlat in filer med felsökningsdumpar.

### <a name="dump-location"></a>Dumpa plats

Standardplatsen för dumpfilen är den aktuella arbetskatalogen. Du kan kontrollera om filen lagras med hjälp av följande alternativ:

    -XX:HeapDumpPath=/path

Till exempel `-XX:HeapDumpPath=/tmp` orsakar Dumpar ska lagras i katalogen/tmp.

### <a name="scripts"></a>Skript

Du kan även utlösa ett skript när en **OutOfMemoryError** inträffar. Till exempel aktiverar en avisering så att du vet att felet har inträffat. Använder du följande alternativ för att utlösa ett skript på en __OutOfMemoryError__:

    -XX:OnOutOfMemoryError=/path/to/script

> [!NOTE]  
> Eftersom Apache Hadoop är ett distribuerat system, måste alla skript som används placeras på alla noder i klustret som tjänsten körs på.
> 
> Skriptet måste också vara på en plats som kan nås av det konto som tjänsten körs som och ange behörighet att köra. Du kan till exempel vill lagra skript i `/usr/local/bin` och använda `chmod go+rx /usr/local/bin/filename.sh` får läs-och körbehörighet.

## <a name="using-apache-ambari"></a>Med Apache Ambari

Om du vill ändra konfigurationen för en tjänst, använder du följande steg:

1. Öppna Ambari-webbgränssnittet för klustret. URL: en är https://YOURCLUSTERNAME.azurehdinsight.net.

    När du uppmanas, autentiserar till webbplatsen med hjälp av HTTP-kontonamnet (standard: administratör) och lösenord för klustret.

   > [!NOTE]  
   > Du kan uppmanas en gång med Ambari för användarnamn och lösenord. Då anger du samma kontonamn och lösenord.

2. Använd listan över till vänster och markera serviceområdet som du vill ändra. Till exempel **HDFS**. I området center väljer du den **Peeringkonfigurationer** fliken.

    ![Bild av Ambari web fliken HDFS-konfigurationer](./media/hdinsight-hadoop-heap-dump-linux/serviceconfig.png)

3. Med hjälp av den **Filter...**  posten, ange **innebär**. Endast objekt som innehåller den här texten visas.

    ![Filtrerad lista](./media/hdinsight-hadoop-heap-dump-linux/filter.png)

4. Hitta den  **\* \_OPTS** post för tjänsten som du vill aktivera heap dumps för och lägga till de alternativ som du vill aktivera. I följande bild, har jag lagt till `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` till den **HADOOP\_NAMENODE\_OPTS** post:

    ![HADOOP_NAMENODE_OPTS med - XX: + HeapDumpOnOutOfMemoryError - XX: = HeapDumpPath/tmp /](./media/hdinsight-hadoop-heap-dump-linux/opts.png)

   > [!NOTE]  
   > När Aktivera heap minnesdumpar för kartan eller minska underordnad process, leta upp för fält med namnet **mapreduce.admin.map.child.java.opts** och **mapreduce.admin.reduce.child.java.opts**.

    Använd den **spara** för att spara ändringarna. Du kan ange en kort kommentar som beskriver ändringarna.

5. När ändringarna har tillämpats på **omstart krävs** visas ikonen bredvid en eller flera tjänster.

    ![Starta om nödvändiga ikonen och starta om knappen](./media/hdinsight-hadoop-heap-dump-linux/restartrequiredicon.png)

6. Markera varje tjänst som måste startas om och Använd den **tjänståtgärder** för att **aktivera på underhållsläge**. Underhållsläge förhindrar att aviseringar som genereras från tjänsten när du startar om.

    ![Aktivera läget menyn för underhåll](./media/hdinsight-hadoop-heap-dump-linux/maintenancemode.png)

7. När du har aktiverat underhållsläget kan du använda den **starta om** knappen för att tjänsten ska **starta om alla påverkas**

    ![Starta om alla påverkade post](./media/hdinsight-hadoop-heap-dump-linux/restartbutton.png)

   > [!NOTE]  
   > posterna för den **starta om** knappen kan vara olika för andra tjänster.

8. När tjänsterna har startats om, använda den **tjänståtgärder** för att **stänga av underhållsläge**. Den här Ambari att återuppta övervakning av aviseringar för tjänsten.

