---
title: Aktivera heap-dum par för Apache Hadoop tjänster i HDInsight – Azure
description: Aktivera heap-dum par för Apache Hadoop tjänster från Linux-baserade HDInsight-kluster för fel sökning och analys.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 9134eb6922b0ed37bbe6051b138da2c7c082b175
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2020
ms.locfileid: "75658805"
---
# <a name="enable-heap-dumps-for-apache-hadoop-services-on-linux-based-hdinsight"></a>Aktivera heap-dum par för Apache Hadoop tjänster på Linux-baserade HDInsight

[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Heap-dumpar innehåller en ögonblicks bild av programmets minne, inklusive värdena för variabler vid den tidpunkt då dumpningen skapades. Därför är de användbara för att diagnostisera problem som uppstår vid körning.

## <a name="services"></a>Services

Du kan aktivera heap-dum par för följande tjänster:

* **Apache HCatalog** – tempelton
* **Apache Hive** -hiveserver2, metaarkiv, derbyserver
* **MapReduce** – jobhistoryserver
* **Apache-garn** – ResourceManager, nodemanager, timelineserver
* **Apache HDFS** -datanode, secondarynamenode, namenode

Du kan också aktivera heap-dum par för kartan och minska de processer som körs av HDInsight.

## <a name="understanding-heap-dump-configuration"></a>Förstå heap dump-konfigurationen

Heap-dumpar aktive ras genom att skicka alternativ (ibland kallade eller parametrar) till JVM när en tjänst startas. För de flesta [Apache Hadoop](https://hadoop.apache.org/) -tjänster kan du ändra det Shell-skript som används för att starta tjänsten för att skicka dessa alternativ.

I varje skript finns det en export för **\*\_väljer**som innehåller de alternativ som skickas till JVM. I **Hadoop-ENV.sh** -skriptet innehåller till exempel den rad som börjar med `export HADOOP_NAMENODE_OPTS=` alternativen för NameNode-tjänsten.

Mappa och minska processer skiljer sig något åt, eftersom dessa åtgärder är underordnade processer i MapReduce-tjänsten. Varje karta eller minska processen körs i en underordnad behållare och det finns två poster som innehåller JVM-alternativen. Båda finns i **mapred-site. XML**:

* **MapReduce. admin. map. Child. java. väljer**
* **mapreduce.admin.reduce.child.java.opts**

> [!NOTE]  
> Vi rekommenderar att du använder [Apache Ambari](https://ambari.apache.org/) för att ändra både skript-och mapred-site. XML-inställningarna, som Ambari hanterar replikering av ändringar över noder i klustret. Mer information finns i avsnittet [using Apache Ambari](#using-apache-ambari) .

### <a name="enable-heap-dumps"></a>Aktivera heap dumps

Följande alternativ aktiverar heap-dum par när en OutOfMemoryError inträffar:

    -XX:+HeapDumpOnOutOfMemoryError

**+** anger att det här alternativet är aktiverat. Standardvärdet är inaktiverad.

> [!WARNING]  
> Heap-dumpar är inte aktiverade för Hadoop-tjänster på HDInsight som standard eftersom dumpfiler kan vara stora. Om du aktiverar dem för fel sökning, kom ihåg att inaktivera dem när du har återskapat problemet och samlat in dumpfiler.

### <a name="dump-location"></a>Dumpnings plats

Standard platsen för dumpfilen är den aktuella arbets katalogen. Du kan styra var filen lagras med följande alternativ:

    -XX:HeapDumpPath=/path

Om du till exempel använder `-XX:HeapDumpPath=/tmp` kommer dumparna att lagras i katalogen/tmp-katalogen.

### <a name="scripts"></a>Skript

Du kan också utlösa ett skript när ett **OutOfMemoryError** inträffar. Du kan till exempel utlösa ett meddelande så att du vet att felet har inträffat. Använd följande alternativ för att utlösa ett skript på en __OutOfMemoryError__:

    -XX:OnOutOfMemoryError=/path/to/script

> [!NOTE]  
> Eftersom Apache Hadoop är ett distribuerat system måste alla skript som används placeras på alla noder i klustret som tjänsten körs på.
> 
> Skriptet måste också finnas på en plats som är tillgänglig för det konto som tjänsten körs som, och måste ge kör-behörighet. Du kanske till exempel vill lagra skript i `/usr/local/bin` och använda `chmod go+rx /usr/local/bin/filename.sh` för att bevilja Läs-och kör behörigheter.

## <a name="using-apache-ambari"></a>Använda Apache Ambari

Använd följande steg för att ändra konfigurationen för en tjänst:

1. I en webbläsare navigerar du till `https://CLUSTERNAME.azurehdinsight.net`, där `CLUSTERNAME` är namnet på klustret.

2. Välj det tjänst områden som du vill ändra i listan till vänster. Till exempel **HDFS**. I mitten väljer du fliken **konfigurationer** .

    ![Bild av Ambari Web med HDFS configs-fliken vald](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-service-config-tab.png)

3. Använd posten **filter...** **och skriv in**. Endast objekt som innehåller den här texten visas.

    ![Filtrerad lista med Apache Ambari-konfiguration](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdinsight-filter-list.png)

4. Leta reda på posten **\*\_** som du vill aktivera heap-dum par för och Lägg till de alternativ som du vill aktivera. I följande bild har jag lagt till `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` till filen **HADOOP\_NAMENODE\_** indexord:

    ![Apache Ambari Hadoop-namenode-väljer](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hadoop-namenode-opts.png)

   > [!NOTE]  
   > När du aktiverar heap-dum par för kartan eller minskar den underordnade processen, letar du efter fälten med namnet **MapReduce. admin. map. Children. java.** sedan och **MapReduce. admin. reduce. Child. java. väljer**.

    Använd knappen **Spara** för att spara ändringarna. Du kan ange en kort anteckning som beskriver ändringarna.

5. När ändringarna har tillämpats visas ikonen **starta om som krävs** bredvid en eller flera tjänster.

    ![Starta om den ikon som krävs och knappen starta om](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/restart-required-icon.png)

6. Välj varje tjänst som behöver startas om och Använd knappen **tjänst åtgärder** för att **Aktivera underhålls läge**. Underhålls läget hindrar aviseringar från att genereras från tjänsten när du startar om den.

    ![Aktivera menyn för HDI underhålls läge](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-maintenance-mode.png)

7. När du har aktiverat underhålls läge använder du knappen **Starta** om för tjänsten för att **starta om alla effekter**

    ![Apache Ambari starta om alla påverkade poster](./media/hdinsight-hadoop-collect-debug-heap-dump-linux/hdi-restart-all-button.png)

   > [!NOTE]  
   > Posterna för knappen **starta om** kan vara olika för andra tjänster.

8. När tjänsterna har startats om använder du knappen **tjänst åtgärder** för att **stänga av underhålls läget**. Den här Ambari för att återuppta övervakningen av aviseringar för tjänsten.