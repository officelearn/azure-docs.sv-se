---
title: Skala klusterstorlekar - Azure HDInsight
description: Skala ett Azure HDInsight-kluster Elastiskt för att matcha din arbetsbelastning.
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: b85277a4238351b6448c2cf29676ae3d8c118385
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077212"
---
# <a name="scale-hdinsight-clusters"></a>Skala HDInsight-kluster

HDInsight ger flexibilitet när det gäller genom att ge dig möjlighet att skala upp och skala ned antalet arbetarnoder i dina kluster. Den här elasticitet, kan du minska ett kluster efter timmar eller helger och expanderas under toppefterfrågan för företag.

Om du har periodiska batchbearbetning kan HDInsight-kluster skalas upp ett par minuter innan åtgärden, så att klustret har tillräckligt med minne och processorer.  Senare, när bearbetningen är klar och användning som kraschar igen, kan du skala ned HDInsight-klustret till färre arbetarnoder.

Du kan skala ett kluster manuellt med hjälp av någon av metoderna som beskrivs nedan eller använda [Autoskala](hdinsight-autoscale-clusters.md) alternativ för att låta systemet automatiskt skalar upp och ned som svar på processor, minne och andra mått.

> [!NOTE]  
> Endast kluster med HDInsight version 3.1.3 eller högre stöds. Om du är osäker på vilken version av ditt kluster kan kontrollera du egenskapssidan.

## <a name="utilities-to-scale-clusters"></a>Verktyg för att skala kluster

Microsoft tillhandahåller följande verktyg för att skala kluster:

|Verktyget | Beskrivning|
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure)|[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) -ClusterName \<Cluster Name> -TargetInstanceCount \<NewSize>|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[Set-AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) -ClusterName \<Cluster Name> -TargetInstanceCount \<NewSize>|
|[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)| [AZ hdinsight ändra storlek på](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) --resursgrupp \<resursgrupp >--name \<klustrets namn >--target instansantalet \<NewSize >|
|[Azure CLI](hdinsight-administer-use-command-line.md)|Azure hdinsight-kluster storleksändring \<klusternamn > \<Instansantalet för mål > |
|[Azure Portal](https://portal.azure.com)|Öppna fönstret ditt HDInsight-kluster, Välj **klusterstorlek** i den vänstra menyn och sedan i fönstret kluster storlek, Skriv i antalet arbetarnoder och klicka på Spara.|  

![Skala ett kluster](./media/hdinsight-scaling-best-practices/scale-cluster-blade.png)

Med hjälp av någon av dessa metoder kan du skala ditt HDInsight-kluster upp eller ned inom några minuter.

> [!IMPORTANT]  
> * Aure klassiskt CLI är inaktuellt och bör endast användas med den klassiska distributionsmodellen. Använd för alla andra distributioner, den [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).  
> * PowerShell-AzureRM-modulen är inaktuell.  Använd den [Az modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) när det är möjligt.

## <a name="impact-of-scaling-operations"></a>Effekten av skalning

När du **lägga till** noder till ditt körs HDInsight-kluster (skala upp), alla väntande eller pågående jobb påverkas inte. Nya jobb kan skickas på ett säkert sätt när skalning processen körs. Om skalning åtgärden misslyckas av någon anledning, hanteras felet om du vill lämna ditt kluster i ett fungerande tillstånd.

Om du **ta bort** noder (skala ned), kommer alla väntande eller pågående jobb misslyckas när skalning åtgärden har slutförts. Det här felet beror på de tjänster som startas om under skalning. Det finns också en risk att klustret kan få har fastnat i felsäkert läge under en manuell skalning igen.

Effekten av att ändra antalet datanoder varierar för varje typ av kluster som stöds av HDInsight:

* Apache Hadoop

    Du kan smidigt öka antalet arbetarnoder i ett Hadoop-kluster som körs utan att påverka alla väntande eller pågående jobb. Också du kan skicka nya jobb medan åtgärden pågår. Fel i en åtgärd för skalning hanteras ett smidigt sätt så att klustret finns alltid kvar i funktionsdugligt tillstånd.

    När ett Hadoop-kluster skalas genom att minska antalet datanoder några tjänster i klustret startas om. Detta medför allt körs och väntande jobb misslyckas vid skalning åtgärden slutfördes. Du kan dock skicka jobb när åtgärden har slutförts.

* Apache HBase

    Du kan smidigt lägga till eller ta bort noder till HBase-kluster medan den körs. Regionservrar balanseras automatiskt inom ett par minuter efter att du skalar igen. Du kan också manuellt balansera regionservrar genom att logga in till huvudnod i klustret och köra följande kommandon från en kommandotolk:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Mer information om hur du använder HBase-gränssnittet finns i [Kom igång med Apache HBase-exempel i HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    Du kan smidigt lägga till eller ta bort datanoder till ditt Storm-kluster medan den körs. Efter slutförande av skalning åtgärden kommer du dock behöva balansera om topologin.

    Ombalansering kan utföras på två sätt:

  * Storm-Webbgränssnittet
  * Verktyget kommandoradsgränssnittet (CLI)

    Referera till den [Apache Storm-dokumentationen](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) för mer information.

    Storm-webbgränssnittet finns på HDInsight-klustret:

    ![HDInsight Storm skala ombalansering](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Här är ett exempel CLI-kommando för att balansera om Storm-topologi:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

## <a name="how-to-safely-scale-down-a-cluster"></a>Hur du på ett säkert sätt skala ned ett kluster

### <a name="scale-down-a-cluster-with-running-jobs"></a>Skala ned ett kluster med pågående jobb

För att undvika att din körs jobben misslyckas under en skala ned igen kan försöka du tre saker:

1. Vänta tills de slutförts innan du skalar ned klustret.
1. Avsluta jobb manuellt.
1. Skicka jobb när skalning åtgärden har avslutats.

Om du vill se en lista över väntande och jobb som körs, kan du använda YARN **Resource Manager UI**, följa dessa steg:

1. Från den [Azure-portalen](https://portal.azure.com/), Välj ditt kluster.  Se [lista och visa kluster](./hdinsight-administer-use-portal-linux.md#showClusters) anvisningar. Klustret har öppnats i en ny Portalsida.
2. Huvudvy, navigera till **Klusterinstrumentpaneler** > **Ambari home**. Ange dina autentiseringsuppgifter för klustret.
3. Ambari UI, Välj **YARN** på listan över tjänster i den vänstra menyn.  
4. YARN-sidan väljer du **snabblänkar** och hovra över aktiva huvudnoden och välj sedan **ResourceManager UI**.

    ![ResourceManager UI](./media/hdinsight-scaling-best-practices/resourcemanager-ui.png)

Du kan direkt komma åt ResourceManager UI med `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`.

Du kan se en lista över jobb, tillsammans med enheternas aktuella status. Det finns ett jobb som körs på skärmbilden är:

![ResourceManager UI-program](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Om du vill avsluta manuellt köra programmet, kör du följande kommando från SSH-gränssnittet:

```bash
yarn application -kill <application_id>
```

Exempel:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Fastnar i felsäkert läge

När du skalar ned ett kluster använder HDInsight Apache Ambari hanteringsgränssnitt för att först inaktivera de extra arbetsnoder som replikerar sina HDFS-block till andra online arbetsnoder. Efter det kan skalas HDInsight på ett säkert sätt klustret. HDFS hamnar i felsäkert läge under skalning åtgärden och ska komma när den skalning är klar. I vissa fall kan dock fastnar HDFS i felsäkert läge under en skalning på grund av filen block under replikering.

Som standard HDFS har konfigurerats med en `dfs.replication` inställningen för den 3, som styr hur många kopior av varje fil block är tillgängliga. Varje kopia av ett block med filen lagras på en annan nod i klustret.

När HDFS upptäcker att det förväntade antalet block kopior inte är tillgängliga, HDFS försätts i felsäkert läge och Ambari genererar aviseringar. Om HDFS försätts i felsäkert läge för en åtgärd för skalning, men det går inte att avsluta felsäkert läge eftersom det begärda antalet noder inte identifieras för replikering, fastna klustret i felsäkert läge.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Exempel på fel när felsäkert läge är aktiverat

```
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```
org.apache.http.conn.HttpHostConnectException: Connect to hn0-clustername.servername.internal.cloudapp.net:10001 [hn0-clustername.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

Du kan granska namnet node-loggar från den `/var/log/hadoop/hdfs/` mappen nära tiden när klustret har skalats för att se när också övergick den i felsäkert läge. Loggfilerna är namngivna `Hadoop-hdfs-namenode-hn0-clustername.*`.

De grundläggande orsakerna till tidigare fel är att Hive beror på tillfälliga filer i HDFS vid körning av frågor. När HDFS anger felsäkert läge, det går inte att Hive köra frågor eftersom den inte kan skrivas till HDFS. Tillfälliga filer i HDFS finns i den lokala enheten monteras till arbetsnoden enskilda virtuella datorer och replikerade bland andra arbetsnoder i tre repliker, minsta.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Hur du förhindrar att HDInsight blir kvar i felsäkert läge

Det finns flera sätt att förhindra att HDInsight kan lämnas i felsäkert läge:

* Stoppa alla Hive-jobb innan du skalar ned HDInsight. Alternativt kan du schemalägga skala ned processen för att undvika motstridiga med att köra Hive-jobb.
* Rensa manuellt Hives grunden `tmp` directory filer i HDFS innan du skalar.
* Endast skala ned HDInsight till tre arbetsnoder, minsta. Undvik att gå så lite som en underordnad nod.
* Kör kommandot för att lämna felsäkert läge, om det behövs.

I följande avsnitt beskrivs dessa alternativ.

#### <a name="stop-all-hive-jobs"></a>Stoppa alla Hive-jobb

Stoppa alla Hive-jobb innan du skalar till en underordnad nod. Om din arbetsbelastning har schemalagts, kör du din skala ned när Hive-jobbet är klart.

Stoppa Hive-jobb innan du skalar, minimera hjälper till att antalet tillfälliga filer i Temp-mappen (i förekommande fall).

#### <a name="manually-clean-up-hives-scratch-files"></a>Manuellt Rensa Hives tillfälliga filer

Om Hive har lämnat bakom temporära filer, och sedan du manuellt Rensa upp dessa filer innan du skalar ned till undvika felsäkert läge.

1. Kontrollera vilken plats som används för Hive temporära filer genom att titta på den `hive.exec.scratchdir` konfigurationsegenskapen. Den här parametern anges inom `/etc/hive/conf/hive-site.xml`:

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Stoppa Hive-tjänster och se till att alla frågor och jobb har slutförts.
2. Visa innehållet i den tillfälliga katalogen hittas ovan, `hdfs://mycluster/tmp/hive/` att se om det innehåller alla filer:

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    Här är ett exempel på utdata när filer finns:

    ```output
    sshuser@hn0-scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. Om du vet Hive görs med de här filerna kan du ta bort dem. Var noga med att Hive inte har några frågor som körs genom att titta på Yarn ResourceManager UI-sidan.

    Exempel kommandoraden för att ta bort filer från HDFS:

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>Skala HDInsight till minst tre arbetsnoder

Om dina kluster fastnar i felsäkert läge ofta när skala ned till färre än tre arbetsnoder och föregående steg inte fungerar, kan du undvika klustret ska felsäkert läge helt och hållet genom att hålla minst tre arbetsnoder.

Behålla tre arbetsnoder är dyrare än att skala ned till endast en underordnad nod, men det förhindrar att klustret fastnar i felsäkert läge.

#### <a name="run-the-command-to-leave-safe-mode"></a>Kör kommando för att lämna felsäkert läge

Det sista alternativet är att köra kommandot lämna felsäkert läge. Om du vet att orsaken till HDFS att ange felsäkert läge är på grund av Hive-filen under replikering kan köra du följande kommando för att lämna felsäkert läge:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Skala ned ett Apache HBase-kluster

Regionservrar balanseras automatiskt inom några minuter när du har slutfört en åtgärd för skalning. För att jämna ut regionservrar manuellt, gör du följande:

1. Ansluta till HDInsight-klustret med SSH. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. Starta HBase-gränssnittet:

    ```bash
    hbase shell
    ```

3. Använder du följande kommando för att balansera regionservrar manuellt:

    ```bash
    balancer
    ```

## <a name="next-steps"></a>Nästa steg

* [Skala automatiskt Azure HDInsight-kluster](hdinsight-autoscale-clusters.md)
* [Introduktion till Azure HDInsight](hadoop/apache-hadoop-introduction.md)
