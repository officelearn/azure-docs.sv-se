---
title: Skala klusterstorlekar – Azure HDInsight
description: Skala ett Apache Hadoop-kluster elastiskt så att det matchar din arbetsbelastning i Azure HDInsight
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/26/2020
ms.openlocfilehash: 96a72541255ad0059abe5ad280f1728518dbf68c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234737"
---
# <a name="scale-azure-hdinsight-clusters"></a>Skala Azure HDInsight-kluster

HDInsight ger elasticitet genom att ge dig möjlighet att skala upp och skala ner antalet arbetsnoder i dina kluster. Denna elasticitet, kan du krympa ett kluster efter timmar eller på helger, och expandera den under topp affärskrav.

Om du har periodisk batchbearbetning kan HDInsight-klustret skalas upp några minuter före den åtgärden, så att klustret har tillräckligt med minne och processorkraft. Senare, när bearbetningen är klar, och användningen går ner igen, kan du skala ner HDInsight-klustret till färre arbetsnoder.

Du kan skala ett kluster manuellt med någon av metoderna nedan, eller använda alternativ [för automatisk skalning](hdinsight-autoscale-clusters.md) för att systemet automatiskt ska kunna skalas upp och ned som svar på CPU, minne och andra mått.

> [!NOTE]  
> Endast kluster med HDInsight version 3.1.3 eller senare stöds. Om du är osäker på vilken version av klustret du har kan du kontrollera sidan Egenskaper.

## <a name="utilities-to-scale-clusters"></a>Verktyg för att skala kluster

Microsoft tillhandahåller följande verktyg för att skala kluster:

|Verktyg | Beskrivning|
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure)|[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) -ClusterName \<klusternamn> -TargetInstanceCount \<NewSize>|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[Set-AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) -ClusterName-klusternamn \<> -TargetInstanceCount \<NewSize>|
|[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)| [az hdinsight resize](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) --resource-group \<Resource group> \<--name Cluster Name> --workernode-count \<NewSize>|
|[Klassiska Azure CLI](hdinsight-administer-use-command-line.md)|azure hdinsight-kluster \<ändra storlek på \<clusterName> Antal målinstanser> |
|[Azure-portal](https://portal.azure.com)|Öppna klusterfönstret HDInsight, välj **Klusterstorlek** på menyn till vänster och skriv sedan in antalet arbetsnoder i fönstret Klusterstorlek och välj Spara.|  

![Klusteralternativ för Azure Portal-skala](./media/hdinsight-scaling-best-practices/azure-portal-settings-nodes.png)

Med någon av dessa metoder kan du skala ditt HDInsight-kluster uppåt eller nedåt inom några minuter.

> [!IMPORTANT]  
> * Azure klassiska CLI är föråldrad och bör endast användas med den klassiska distributionsmodellen. Använd [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)för alla andra distributioner .  
> * PowerShell AzureRM-modulen är föråldrad.  Använd [Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) när det är möjligt.

## <a name="impact-of-scaling-operations"></a>Inverkan av skalningsåtgärder

När du **lägger till** noder i ditt hdinsight-kluster som körs (skala upp) påverkas inte alla väntande eller löpjobb. Nya jobb kan skickas in på ett säkert sätt medan skalningsprocessen körs. Om skalningsåtgärden misslyckas av någon anledning hanteras felet för att lämna klustret i ett funktionellt tillstånd.

Om du **tar bort** noder (skala ned) misslyckas alla väntande eller pågående jobb när skalningsåtgärden är klar. Det här felet beror på att vissa av tjänsterna startas om under skalningsprocessen. Det finns också en risk att klustret kan fastna i felsäkert läge under en manuell skalning.

Effekten av att ändra antalet datanoder varierar för varje typ av kluster som stöds av HDInsight:

* Apache Hadoop

    Du kan sömlöst öka antalet arbetsnoder i ett Hadoop-kluster som körs utan att påverka väntande eller köra jobb. Nya jobb kan också skickas in medan åtgärden pågår. Fel i en skalningsåtgärd hanteras smidigt så att klustret alltid lämnas i ett funktionellt tillstånd.

    När ett Hadoop-kluster skalas ned genom att minska antalet datanoder startas vissa tjänster i klustret om. Detta medför att alla jobb som körs och väntar misslyckas när skalningsåtgärden har slutförts. Du kan dock skicka jobben igen när åtgärden är klar.

* Apache HBase

    Du kan sömlöst lägga till eller ta bort noder i HBase-klustret medan det körs. Regionala servrar balanseras automatiskt inom några minuter efter att skalningsåtgärden har slutförts. Du kan dock också balansera de regionala servrarna manuellt genom att logga in på klustrets huvudnod och köra följande kommandon från ett kommandotolksfönster:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Mer information om hur du använder HBase-skalet finns i [Komma igång med ett Apache HBase-exempel i HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    Du kan sömlöst lägga till eller ta bort datanoder i Storm-klustret medan det körs. Men efter ett framgångsrikt slutförande av skalningsåtgärden måste du balansera om topologin.

    Ombalansering kan åstadkommas på två sätt:

  * Användargränssnittet för stormwebb
  * Cli-verktyg (Command-line interface)

    Mer information finns i [Apache Storm-dokumentationen.](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)

    Webbgränssnittet Storm är tillgängligt i HDInsight-klustret:

    ![HDInsight Storm skala balans](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Här är ett exempel CLI kommando för att balansera Storm topologi:

    ```console
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

## <a name="how-to-safely-scale-down-a-cluster"></a>Så här skalar du säkert ned ett kluster

### <a name="scale-down-a-cluster-with-running-jobs"></a>Skala ned ett kluster med jobb som körs

Om du vill undvika att dina jobb misslyckas under en nedskalningsåtgärd kan du prova tre saker:

1. Vänta tills jobben har slutförts innan du skalar ned klustret.
1. Avsluta jobben manuellt.
1. Skicka jobben igen när skalningsåtgärden har slutförts.

Om du vill visa en lista över jobb som väntar och körs kan du använda **användargränssnittet**YARN Resource Manager och följa dessa steg:

1. Välj ditt kluster i [Azure-portalen.](https://portal.azure.com/)  Se [Lista och visa kluster](./hdinsight-administer-use-portal-linux.md#showClusters) för instruktionerna. Klustret öppnas på en ny portalsida.
2. Från huvudvyn navigerar du till **klusterinstrumentpaneler** > **Ambari home**. Ange klusterautentiseringsuppgifterna.
3. Välj **YARN** i listan över tjänster på menyn till vänster.  
4. På sidan YARN väljer du **Snabblänkar** och hovrar över den aktiva huvudnoden och väljer sedan **ResourceManager UI**.

    ![Apache Ambari snabblänkar ResourceManager UI](./media/hdinsight-scaling-best-practices/resource-manager-ui1.png)

Du kan komma åt ResourceManager-användargränssnittet direkt med `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`.

Du ser en lista över jobb, tillsammans med deras nuvarande tillstånd. I skärmbilden finns det ett jobb som körs för närvarande:

![ResourceManager UI-program](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Kör följande kommando manuellt från SSH-skalet:

```bash
yarn application -kill <application_id>
```

Ett exempel:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Fastnar i felsäkert läge

När du skalar ned ett kluster använder HDInsight Apache Ambari-hanteringsgränssnitt för att först inaktivera de extra arbetsnoderna, som replikerar sina HDFS-block till andra onlinearbetarenoder. Därefter skalar HDInsight klustret säkert. HDFS går in i felsäkert läge under skalningsåtgärden och ska komma ut när skalningen är klar. I vissa fall fastnar HDFS dock i felsäkert läge under en skalningsåtgärd på grund av filblock underreplikering.

Som standard konfigureras HDFS `dfs.replication` med en inställning på 1, som styr hur många kopior av varje filblock som är tillgängliga. Varje kopia av ett filblock lagras på en annan nod i klustret.

När HDFS upptäcker att det förväntade antalet blockkopior inte är tillgängliga går HDFS in i felsäkert läge och Ambari genererar aviseringar. Om HDFS går in i felsäkert läge för en skalning, men sedan inte kan avsluta felsäkert läge eftersom det erforderliga antalet noder inte identifieras för replikering, kan klustret fastna i felsäkert läge.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Exempel på fel när felsäkert läge är aktiverat

```output
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```output
org.apache.http.conn.HttpHostConnectException: Connect to active-headnode-name.servername.internal.cloudapp.net:10001 [active-headnode-name.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

Du kan granska namnnodloggarna `/var/log/hadoop/hdfs/` från mappen, nära den tidpunkt då klustret skalades, för att se när det gick in i felsäkert läge. Loggfilerna heter `Hadoop-hdfs-namenode-<active-headnode-name>.*`.

Grundorsaken till de tidigare felen är att Hive är beroende av temporära filer i HDFS när du kör frågor. När HDFS går in i felsäkert läge kan Hive inte köra frågor eftersom det inte kan skriva till HDFS. Temp-filerna i HDFS finns i den lokala enheten som är monterad på de enskilda arbetsnod-datorerna och replikeras bland andra arbetsnoder vid minst tre repliker.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Så här förhindrar du att HDInsight fastnar i felsäkert läge

Det finns flera sätt att förhindra att HDInsight lämnas i felsäkert läge:

* Stoppa alla Hive-jobb innan du skalar ned HDInsight. Schemalägg alternativt nedskalningsprocessen för att undvika att du kan köra Hive-jobb.
* Rensa Hive's scratch-katalogfiler `tmp` manuellt i HDFS innan du skalar ned.
* Skala endast ner HDInsight till tre arbetsnoder, minimum. Undvik att gå så lågt som en arbetsnod.
* Kör kommandot för att lämna felsäkert läge, om det behövs.

I följande avsnitt beskrivs dessa alternativ.

#### <a name="stop-all-hive-jobs"></a>Stoppa alla Hive jobb

Stoppa alla Hive-jobb innan du skalar ned till en arbetsnod. Om din arbetsbelastning är schemalagd, kör sedan din skala ned efter Hive arbete är klar.

Om du stoppar Hive-jobben före skalningen minimeras antalet scratch-filer i tmp-mappen (om sådana finns).

#### <a name="manually-clean-up-hives-scratch-files"></a>Rensa Hive's scratch-filer manuellt

Om Hive har lämnat efter sig temporära filer kan du rensa filerna manuellt innan du skalar ned för att undvika felsäkert läge.

1. Kontrollera vilken plats som används för Hive-temporära filer genom att titta på konfigurationsegenskapen. `hive.exec.scratchdir` Den här parametern är inställd inom: `/etc/hive/conf/hive-site.xml`

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Stoppa Hive-tjänster och se till att alla frågor och jobb har slutförts.
2. Lista innehållet i scratch katalogen `hdfs://mycluster/tmp/hive/` finns ovan, för att se om den innehåller några filer:

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    Här är ett exempel på utdata när det finns filer:

    ```output
    sshuser@scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. Om du vet att Hive är gjort med dessa filer kan du ta bort dem. Se till att Hive inte har några frågor som körs genom att titta på sidan Yarn ResourceManager UI.

    Exempel på kommandoraden för att ta bort filer från HDFS:

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>Skala HDInsight till tre eller flera arbetarnoder

Om dina kluster fastnar i felsäkert läge ofta när du skalar ned till färre än tre arbetsnoder och de föregående stegen inte fungerar, kan du undvika att klustret går in i felsäkert läge helt och hållet genom att behålla minst tre arbetsnoder.

Det är dyrare att behålla tre arbetsnoder än att skala ned till endast en arbetsnod, men det förhindrar att klustret fastnar i felsäkert läge.

### <a name="scale-hdinsight-down-to-one-worker-node"></a>Skala HDInsight nedåt till en arbetsnod

Även när klustret skalas ned till 1 nod överlever arbetarnod 0 fortfarande. Arbetsnod 0 kan aldrig inaktiveras.

#### <a name="run-the-command-to-leave-safe-mode"></a>Kör kommandot för att lämna felsäkert läge

Det sista alternativet är att köra kommandot lämna felsäkert läge. Om du vet att orsaken till att HDFS går in i felsäkert läge är på grund av Hive-filens underreplikering kan du köra följande kommando för att lämna felsäkert läge:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Skala ner ett Apache HBase-kluster

Regionservrar balanseras automatiskt inom några minuter efter att en skalningsåtgärd har slutförts. Så här balanserar du regionservrar manuellt:

1. Anslut till HDInsight-klustret med SSH. Mer information finns i [Använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Starta HBase-skalet:

    ```bash
    hbase shell
    ```

3. Använd följande kommando för att manuellt balansera regionservrarna:

    ```bash
    balancer
    ```

## <a name="next-steps"></a>Nästa steg

* [Skala Azure HDInsight-kluster automatiskt](hdinsight-autoscale-clusters.md)
* [Introduktion till Azure HDInsight](hadoop/apache-hadoop-introduction.md)
