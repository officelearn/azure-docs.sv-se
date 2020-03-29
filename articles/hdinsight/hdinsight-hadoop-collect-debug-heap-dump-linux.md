---
title: Aktivera heap-dumpar för Apache Hadoop-tjänster på HDInsight - Azure
description: Aktivera heap-dumpar för Apache Hadoop-tjänster från Linux-baserade HDInsight-kluster för felsökning och analys.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 9134eb6922b0ed37bbe6051b138da2c7c082b175
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75658805"
---
# <a name="enable-heap-dumps-for-apache-hadoop-services-on-linux-based-hdinsight"></a>Aktivera heap dumpar för Apache Hadoop tjänster på Linux-baserade HDInsight

[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Heap-dumpar innehåller en ögonblicksbild av programmets minne, inklusive värdena för variabler när dumpen skapades. Så de är användbara för att diagnostisera problem som uppstår vid körning.

## <a name="services"></a>Tjänster

Du kan aktivera heap-dumpar för följande tjänster:

* **Apache hcatalog** - tempelton
* **Apache hive** - hiveserver2, metastore, derbyserver
* **mapreduce** - jobhistoryserver
* **Apache garn** - resourcemanager, nodemanager, tidslinjeserver
* **Apache hdfs** - datanod, secondarynamenode, namenode

Du kan också aktivera heap dumpar för kartan och minska processer som kördes av HDInsight.

## <a name="understanding-heap-dump-configuration"></a>Förstå heap dump konfiguration

Heap-dumpar aktiveras genom att skicka alternativ (kallas ibland opts eller parametrar) till JVM när en tjänst startas. För de flesta [Apache Hadoop-tjänster](https://hadoop.apache.org/) kan du ändra skalskriptet som används för att starta tjänsten för att skicka dessa alternativ.

I varje skript finns det en export för ** \* \_OPTS**, som innehåller de alternativ som skickas till JVM. I **hadoop-env.sh** skriptet innehåller till exempel raden som `export HADOOP_NAMENODE_OPTS=` börjar med alternativen för NameNode-tjänsten.

Kart- och reducesprocesser är något annorlunda, eftersom dessa åtgärder är en underordnad process för MapReduce-tjänsten. Varje mappning eller förminska process körs i en underordnad behållare och det finns två poster som innehåller JVM-alternativen. Båda finns i **mapred-site.xml**:

* **mapreduce.admin.map.child.java.opts mapreduce.admin.map.child.java.opts mapreduce.admin.map.child.java.opts mapr**
* **mapreduce.admin.reduce.child.java.opts mapreduce.admin.reduce.child.java.opts mapreduce.admin.reduce.child.java.opts mapr**

> [!NOTE]  
> Vi rekommenderar att du använder [Apache Ambari](https://ambari.apache.org/) för att ändra både skript och mapred-site.xml-inställningar, eftersom Ambari hanterar replikera ändringar över noder i klustret. Mer information finns i avsnittet [Använda Apache Ambari](#using-apache-ambari) för specifika steg.

### <a name="enable-heap-dumps"></a>Aktivera heap dumps

Följande alternativ aktiverar heap-dumpar när en OutOfMemoryError inträffar:

    -XX:+HeapDumpOnOutOfMemoryError

Anger **+** att det här alternativet är aktiverat. Standardvärdet är inaktiverad.

> [!WARNING]  
> Heap dumpar är inte aktiverade för Hadoop-tjänster på HDInsight som standard, eftersom dumpfilerna kan vara stora. Om du aktiverar dem för felsökning, kom ihåg att inaktivera dem när du har återgett problemet och samlat dumpfilerna.

### <a name="dump-location"></a>Dumpa plats

Standardplatsen för dumpfilen är den aktuella arbetskatalogen. Du kan styra var filen lagras med följande alternativ:

    -XX:HeapDumpPath=/path

Om du `-XX:HeapDumpPath=/tmp` till exempel använder kan du lagra dumparna i katalogen /tmp.

### <a name="scripts"></a>Skript

Du kan också utlösa ett skript när en **OutOfMemoryError** inträffar. Utlöser till exempel ett meddelande så att du vet att felet har uppstått. Använd följande alternativ för att utlösa ett skript på ett __OutOfMemoryError:__

    -XX:OnOutOfMemoryError=/path/to/script

> [!NOTE]  
> Eftersom Apache Hadoop är ett distribuerat system måste alla skript som används placeras på alla noder i klustret som tjänsten körs på.
> 
> Skriptet måste också finnas på en plats som är tillgänglig för kontot som tjänsten körs som och måste ange körningsbehörighet. Du kanske till exempel vill lagra `/usr/local/bin` skript `chmod go+rx /usr/local/bin/filename.sh` i och använda för att bevilja läs- och körningsbehörigheter.

## <a name="using-apache-ambari"></a>Använda Apache Ambari

Så här ändrar du konfigurationen för en tjänst:

1. Från en webbläsare navigerar du till `https://CLUSTERNAME.azurehdinsight.net`, var `CLUSTERNAME` är namnet på klustret.

2. Välj det serviceområde som du vill ändra med hjälp av listan till vänster. Till exempel **HDFS**. Välj fliken **Configs** i mittområdet.

    ![Bild av Ambari webben med fliken HDFS Configs vald](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-service-config-tab.png)

3. Med hjälp av **filterinmatningen** anger du **väljer**. Endast objekt som innehåller den här texten visas.

    ![Apache Ambari config filtrerad lista](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdinsight-filter-list.png)

4. Leta reda på ** \* \_OPTS-posten** för den tjänst som du vill aktivera heap-dumpar för och lägg till de alternativ du vill aktivera. I följande bild har jag `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` lagt till i **posten HADOOP\_NAMENODE\_OPTS:**

    ![Apache Ambari hadoop-namenode-väljer](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hadoop-namenode-opts.png)

   > [!NOTE]  
   > När du aktiverar heap dumpar för kartan eller minska underordnade processen, leta efter de fält som heter **mapreduce.admin.map.child.java.opts** och **mapreduce.admin.reduce.child.java.opts**.

    Använd knappen **Spara** för att spara ändringarna. Du kan ange en kort anteckning som beskriver ändringarna.

5. När ändringarna har tillämpats visas ikonen **Starta om obligatoriskt** bredvid en eller flera tjänster.

    ![starta om den nödvändiga ikonen och knappen starta om](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/restart-required-icon.png)

6. Välj varje tjänst som behöver startas om och använd knappen **Serviceåtgärder** för att **aktivera underhållsläge**. Underhållsläge förhindrar att aviseringar genereras från tjänsten när du startar om den.

    ![Aktivera menyn för underhållsläge för hdi](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-maintenance-mode.png)

7. När du har aktiverat underhållsläge använder du knappen **Starta om** för att tjänsten ska **starta om all effekt**

    ![Apache Ambari Starta om alla berörda posten](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-restart-all-button.png)

   > [!NOTE]  
   > Posterna för knappen **Starta om** kan vara olika för andra tjänster.

8. När tjänsterna har startats om använder du knappen **Serviceåtgärder** för att **stänga av underhållsläget**. Denna Ambari att återuppta övervakningen för varningar för tjänsten.