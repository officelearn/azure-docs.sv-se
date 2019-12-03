---
title: Skala kluster storlekar – Azure HDInsight
description: Skala ett Apache Hadoop kluster elastiskt för att matcha din arbets belastning i Azure HDInsight
author: ashishthaps
ms.author: ashish
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 15d44f95cccf15fd0f7615655f5bbac1b0c35127
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706063"
---
# <a name="scale-azure-hdinsight-clusters"></a>Skala Azure HDInsight-kluster

HDInsight tillhandahåller elastiskhet genom att ge dig möjlighet att skala upp och ned antalet arbetsnoder i klustren. Med den här elastiskheten kan du minska ett kluster efter timmar eller helger och expandera det under de högsta affärs behoven.

Om du har periodisk batchbearbetning kan HDInsight-klustret skalas upp några minuter innan den åtgärden, så att klustret har tillräckligt med minne och processor kraft.  Senare, när bearbetningen är färdig och användningen går ned igen, kan du skala HDInsight-klustret till färre arbetsnoder.

Du kan skala ett kluster manuellt med någon av metoderna som beskrivs nedan eller använda alternativ för automatisk [skalning](hdinsight-autoscale-clusters.md) för att systemet automatiskt ska skalas upp och ned som svar på processor, minne och andra mått.

> [!NOTE]  
> Endast kluster med HDInsight version 3.1.3 eller högre stöds. Om du är osäker på vilken version av klustret du har kan du kontrol lera sidan Egenskaper.

## <a name="utilities-to-scale-clusters"></a>Verktyg för att skala kluster

Microsoft tillhandahåller följande verktyg för att skala kluster:

|Checker | Beskrivning|
|---|---|
|[PowerShell-AZ](https://docs.microsoft.com/powershell/azure)|[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) -kluster namn \<kluster namn >-TargetInstanceCount \<NewSize >|
|[PowerShell-AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[Set-AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) -kluster namn \<kluster namn >-TargetInstanceCount \<NewSize >|
|[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)| [AZ HDInsight ändra storlek](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) --resurs grupp \<resurs grupp >--Name \<kluster namn >--Target-instance-Count \<NewSize >|
|[Azure CLI](hdinsight-administer-use-command-line.md)|storlek på Azure HDInsight-kluster ändra storlek \<kluster namn > \<mål instans antal > |
|[Azure-portalen](https://portal.azure.com)|Öppna fönstret HDInsight-kluster, Välj **kluster storlek** på den vänstra menyn och skriv sedan antalet arbetsnoder i rutan kluster storlek och välj Spara.|  

![Alternativet Azure Portal skalnings kluster](./media/hdinsight-scaling-best-practices/scale-cluster-blade1.png)

Med någon av dessa metoder kan du skala ditt HDInsight-kluster upp eller ned på några minuter.

> [!IMPORTANT]  
> * Den klassiska Azure CLI är inaktuell och bör endast användas med den klassiska distributions modellen. Använd [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)för alla andra distributioner.  
> * PowerShell-modulen AzureRM är föråldrad.  Använd AZ- [modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) när det är möjligt.

## <a name="impact-of-scaling-operations"></a>Effekt av skalnings åtgärder

När du **lägger till** noder i ditt HDInsight-kluster (skala upp) kommer väntande eller pågående jobb inte att påverkas. Nya jobb kan skickas på ett säkert sätt medan skalnings processen körs. Om skalnings åtgärden Miss lyckas av någon anledning, hanteras misslyckandet för att lämna klustret i ett fungerande tillstånd.

Om du **tar bort** noder (skala ned) kommer väntande eller pågående jobb att Miss lyckas när skalnings åtgärden slutförs. Det här felet beror på att några av tjänsterna har startats om under skalnings processen. Det finns också en risk att klustret kan fastna i fel säkert läge under en manuell skalnings åtgärd.

Effekten av att ändra antalet datanoder varierar för varje typ av kluster som stöds av HDInsight:

* Apache Hadoop

    Du kan sömlöst öka antalet arbetsnoder i ett Hadoop-kluster som körs utan att påverka väntande eller pågående jobb. Nya jobb kan också skickas medan åtgärden pågår. Fel i en skalnings åtgärd hanteras på ett smidigt sätt så att klustret alltid lämnas i ett fungerande tillstånd.

    När ett Hadoop-kluster skalas ned genom att minska antalet datanoder, startas vissa av tjänsterna i klustret om. Detta innebär att alla pågående och väntande jobb inte kan köras vid slutförandet av skalnings åtgärden. Du kan dock skicka jobben igen när åtgärden har slutförts.

* Apache HBase

    Du kan enkelt lägga till eller ta bort noder i HBase-klustret medan det körs. Regionala servrar uppdelas automatiskt inom några minuter efter att du har slutfört skalnings åtgärden. Du kan dock även manuellt balansera de regionala servrarna genom att logga in på huvudnoden för klustret och köra följande kommandon från ett kommando tolks fönster:

    ```bash
    pushd %HBASE_HOME%\bin
    hbase shell
    balancer
    ```

    Mer information om hur du använder HBase-gränssnittet finns i [Kom igång med ett Apache HBase-exempel i HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md).

* Apache Storm

    Du kan enkelt lägga till eller ta bort datanoder i Storm-klustret när den körs. Men när skalnings åtgärden har slutförts måste du balansera om topologin.

    Ombalansering kan utföras på två sätt:

  * Webb gränssnitt för Storm
  * Kommando rads gränssnitt (CLI)

    Mer information finns i [Apache Storm-dokumentationen](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) .

    Webb gränssnittet för Storm är tillgängligt i HDInsight-klustret:

    ![Återbalansering av HDInsight Storm-skala](./media/hdinsight-scaling-best-practices/hdinsight-portal-scale-cluster-storm-rebalance.png)

    Här är ett exempel på CLI-kommando för att balansera om Storm-topologin:

    ```cli
    ## Reconfigure the topology "mytopology" to use 5 worker processes,
    ## the spout "blue-spout" to use 3 executors, and
    ## the bolt "yellow-bolt" to use 10 executors
    $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10
    ```

## <a name="how-to-safely-scale-down-a-cluster"></a>Så här skalar du ned ett kluster på ett säkert sätt

### <a name="scale-down-a-cluster-with-running-jobs"></a>Skala ned ett kluster med jobb som körs

Du kan prova tre saker för att undvika att köra jobb som inte körs under en nedskalning-åtgärd:

1. Vänta tills jobben har slutförts innan du skalar ned klustret.
1. Avsluta jobben manuellt.
1. Skicka jobben igen när skalnings åtgärden har slutförts.

Om du vill se en lista över väntande och pågående jobb kan du använda garn **Resource Manager-gränssnittet**med hjälp av följande steg:

1. Välj ditt kluster från [Azure Portal](https://portal.azure.com/).  Se [lista och Visa kluster](./hdinsight-administer-use-portal-linux.md#showClusters) för instruktioner. Klustret öppnas på en ny Portal sida.
2. I huvudvyn navigerar du till **kluster instrument paneler** > **Ambari start**. Ange dina autentiseringsuppgifter för klustret.
3. Välj **garn** i listan över tjänster på den vänstra menyn i AMBARI-användargränssnittet.  
4. På sidan garn väljer du **snabb länkar** och hovring över den aktiva Head-noden och väljer sedan **ResourceManager-gränssnitt**.

    ![Apache Ambari Quick Links ResourceManager-gränssnitt](./media/hdinsight-scaling-best-practices/resource-manager-ui1.png)

Du kan komma åt användar gränssnittet för ResourceManager direkt med `https://<HDInsightClusterName>.azurehdinsight.net/yarnui/hn/cluster`.

Du ser en lista över jobb, tillsammans med deras aktuella status. I skärm bilden finns ett jobb som körs för tillfället:

![Användar gränssnitts program för ResourceManager](./media/hdinsight-scaling-best-practices/resourcemanager-ui-applications.png)

Om du vill avsluta programmet manuellt kör du följande kommando från SSH-gränssnittet:

```bash
yarn application -kill <application_id>
```

Exempel:

```bash
yarn application -kill "application_1499348398273_0003"
```

### <a name="getting-stuck-in-safe-mode"></a>Få fastna i fel säkert läge

När du skalar ned ett kluster använder HDInsight Apache Ambari Management Interfaces för att först inaktivera de extra arbetsnoderna, som replikerar deras HDFS-block till andra online Worker-noder. Därefter skalar HDInsight säkert klustret. HDFS försätts i fel säkert läge under skalnings åtgärden och kommer att tas ut när skalningen är slutförd. I vissa fall fastnar HDFS i fel säkert läge under en skalnings åtgärd på grund av fil block under replikering.

Som standard konfigureras HDFS med en `dfs.replication` inställning på 1, som styr hur många kopior av varje fil block som är tillgängliga. Varje kopia av ett fil block lagras på en annan nod i klustret.

När HDFS upptäcker att det förväntade antalet block kopior inte är tillgängligt, så kommer HDFS att gå in i fel säkert läge och Ambari genererar aviseringar. Om HDFS går in i fel säkert läge för en skalnings åtgärd, men det inte går att avsluta fel säkert läge eftersom antalet noder som krävs inte har identifierats för replikering, kan klustret fastna i fel säkert läge.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Exempel fel när fel säkert läge är aktiverat

```
org.apache.hadoop.hdfs.server.namenode.SafeModeException: Cannot create directory /tmp/hive/hive/819c215c-6d87-4311-97c8-4f0b9d2adcf0. Name node is in safe mode.
```

```
org.apache.http.conn.HttpHostConnectException: Connect to hn0-clustername.servername.internal.cloudapp.net:10001 [hn0-clustername.servername. internal.cloudapp.net/1.1.1.1] failed: Connection refused
```

Du kan granska namn-nodens loggar från `/var/log/hadoop/hdfs/`-mappen, vid den tidpunkt då klustret skalades, för att se när det skrevs i fel säkert läge. Loggfilerna heter `Hadoop-hdfs-namenode-hn0-clustername.*`.

Rotor saken till föregående fel är att Hive är beroende av temporära filer i HDFS när frågor körs. När HDFS går in i fel säkert läge kan Hive inte köra frågor eftersom det inte går att skriva till HDFS. De temporära filerna i HDFS finns på den lokala enheten som är monterad på de enskilda arbetsnoderna VM: ar och replikeras bland andra arbetsnoder på tre repliker, minst.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode"></a>Förhindra att HDInsight får fastna i fel säkert läge

Det finns flera sätt att förhindra att HDInsight lämnas i fel säkert läge:

* Stoppa alla Hive-jobb innan du skalar ned HDInsight. Du kan också schemalägga skalnings processen för att undvika konflikter med körning av Hive-jobb.
* Rensa Hive-mappens Scratch `tmp`-katalogfiler manuellt i HDFS innan du skalar ned.
* Skala bara ned HDInsight till tre arbetsnoder, minimum. Undvik att gå så lågt som en arbetsnod.
* Kör kommandot för att lämna fel säkert läge vid behov.

I följande avsnitt beskrivs de här alternativen.

#### <a name="stop-all-hive-jobs"></a>Stoppa alla Hive-jobb

Stoppa alla Hive-jobb innan du skalar ned till en arbets nod. Om din arbets belastning har schemalagts ska du köra en nedskalning när Hive-jobbet är färdigt.

Om du stoppar Hive-jobb före skalning, bidrar till att minimera antalet virtuella installationsfiler i mappen tmp (om det finns några).

#### <a name="manually-clean-up-hives-scratch-files"></a>Rensa Hive-startfiler manuellt

Om Hive har lämnat kvar temporära filer kan du rensa filerna manuellt innan du skalar ned för att undvika fel säkert läge.

1. Kontrol lera vilken plats som används för temporära Hive-filer genom att titta på konfigurations egenskapen `hive.exec.scratchdir`. Den här parametern anges i `/etc/hive/conf/hive-site.xml`:

    ```xml
    <property>
        <name>hive.exec.scratchdir</name>
        <value>hdfs://mycluster/tmp/hive</value>
    </property>
    ```

1. Stoppa Hive-tjänster och se till att alla frågor och jobb är slutförda.
2. Ange innehållet i den tillfälliga katalogen som finns ovan, `hdfs://mycluster/tmp/hive/` för att se om den innehåller några filer:

    ```bash
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```

    Här är ett exempel på utdata när det finns filer:

    ```output
    sshuser@hn0-scalin:~$ hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c
    drwx------   - hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/_tmp_space.db
    -rw-r--r--   3 hive hdfs         27 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info
    -rw-r--r--   3 hive hdfs          0 2017-07-06 13:40 hdfs://mycluster/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.lck
    drwx------   - hive hdfs          0 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699
    -rw-r--r--   3 hive hdfs         26 2017-07-06 20:30 hdfs://mycluster/tmp/hive/hive/c108f1c2-453e-400f-ac3e-e3a9b0d22699/inuse.info
    ```

3. Om du vet att Hive har gjorts med de här filerna kan du ta bort dem. Se till att Hive inte har några frågor som körs genom att titta på användar gränssnitts sidan för garn-ResourceManager.

    Exempel på kommando rad för att ta bort filer från HDFS:

    ```bash
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```

#### <a name="scale-hdinsight-to-three-or-more-worker-nodes"></a>Skala HDInsight till tre eller flera arbetsnoder

Om klustret blir fastnat i fel säkert läge när du skalar ned till färre än tre arbetsnoder, och föregående steg inte fungerar, så kan du undvika att klustret går in i fel säkert läge helt genom att behålla minst tre arbetsnoder.

Att behålla tre arbetsnoder är dyrare än att skala ned till bara en arbetsnoden, men det förhindrar att ditt kluster fastnar i fel säkert läge.

#### <a name="run-the-command-to-leave-safe-mode"></a>Kör kommandot för att lämna fel säkert läge

Det sista alternativet är att köra kommandot lämna fel säkert läge. Om du vet att orsaken till HDFS som är i fel säkert läge är på grund av Hive-filen under replikering, kan du köra följande kommando för att lämna fel säkert läge:

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

### <a name="scale-down-an-apache-hbase-cluster"></a>Skala ned ett Apache HBase-kluster

Region servrar bal anse ras automatiskt inom några minuter efter att en skalnings åtgärd har slutförts. Slutför följande steg för att balansera region servrar manuellt:

1. Anslut till HDInsight-klustret med SSH. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. Starta HBase-gränssnittet:

    ```bash
    hbase shell
    ```

3. Använd följande kommando för att balansera region servrarna manuellt:

    ```bash
    balancer
    ```

## <a name="next-steps"></a>Nästa steg

* [Skala Azure HDInsight-kluster automatiskt](hdinsight-autoscale-clusters.md)
* [Introduktion till Azure HDInsight](hadoop/apache-hadoop-introduction.md)
