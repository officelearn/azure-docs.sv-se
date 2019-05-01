---
title: Skala klusterstorlekar - Azure HDInsight
description: Skala ett HDInsight-kluster för din arbetsbelastning.
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: ashish
ms.openlocfilehash: a172024e4662e647b39fe999f1be3cfcef04b5ce
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64698253"
---
# <a name="scale-hdinsight-clusters"></a>Skala HDInsight-kluster

HDInsight ger flexibilitet när det gäller genom att ge dig möjlighet att skala upp och skala ned antalet arbetarnoder i dina kluster. På så sätt kan du minska ett kluster efter timmar eller helger och expanderas under toppefterfrågan för företag.

Till exempel om du har några batchbearbetning som inträffar en gång om dagen eller en gång i månaden, HDInsight-kluster kan skalas upp ett par minuter före den schemalagda händelsen så att det ska finnas tillräckligt med minne och CPU-beräkningskraft.  Senare, när bearbetningen är klar och användning som kraschar igen, kan du skala ned HDInsight-klustret till färre arbetarnoder.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="utilities-to-scale-clusters"></a>Verktyg för att skala kluster

Microsoft tillhandahåller följande verktyg för att skala kluster:

|Verktyget | Beskrivning|
|---|---|
|[PowerShell Az](https://docs.microsoft.com/powershell/azure)|[Set-AzHDInsightClusterSize](https://docs.microsoft.com/powershell/module/az.hdinsight/set-azhdinsightclustersize) -ClusterName \<Cluster Name> -TargetInstanceCount \<NewSize>|
|[PowerShell AzureRM](https://docs.microsoft.com/powershell/azure/azurerm) |[Set-AzureRmHDInsightClusterSize](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/set-azurermhdinsightclustersize) -ClusterName \<Cluster Name> -TargetInstanceCount \<NewSize>|
|[Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)|[AZ hdinsight ändra storlek på](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) --resursgrupp \<resursgrupp >--name \<klustrets namn >--target instansantalet \<NewSize >|
|[Azure Classic CLI](hdinsight-administer-use-command-line.md)|Azure hdinsight-kluster storleksändring \<klusternamn > \<Instansantalet för mål >|
|[Azure Portal](https://portal.azure.com)|Öppna fönstret ditt HDInsight-kluster, Välj **klusterstorlek** i den vänstra menyn och sedan i fönstret kluster storlek, Skriv i antalet arbetarnoder och klicka på Spara.|  

![Skala ett kluster](./media/hdinsight-scaling-best-practices/scale-cluster-blade.png)

Med hjälp av någon av dessa metoder kan du skala ditt HDInsight-kluster upp eller ned inom några minuter.

> [!IMPORTANT]  
> * Aure klassiskt CLI är inaktuellt och bör endast användas med den klassiska distributionsmodellen. Använd för alla andra distributioner, den [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).  
> * PowerShell-AzureRM-modulen är inaktuell.  Använd den [Az modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) när det är möjligt.

## <a name="scaling-impacts-on-running-jobs"></a>Skala påverkan på jobb som körs

När du **lägga till** noder till ditt HDInsight-kluster som körs alla väntande eller pågående jobb påverkas inte. Dessutom kan kan nya jobb på ett säkert sätt skickas medan skalning processen körs. Om skalning åtgärder utföras av någon anledning, är felet ett smidigt sätt hanterade, lämnar klustret fungerar på.

Men om du skalar ned klustret genom **tar bort** noder, alla väntande eller pågående jobb misslyckas när skalning åtgärden har slutförts. Det här felet beror på de tjänster som startas om under processen.

För det här problemet, kan du vänta jobb slutförts innan du skalar ned ditt kluster, Avsluta jobb manuellt eller skicka jobb när åtgärden skalning har avslutat.

Om du vill se en lista över väntande och jobb som körs, kan du använda YARN ResourceManager UI, följa dessa steg:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Från vänster, gå till **alla tjänster** > **Analytics** > **HDInsight-kluster**, och välj sedan ditt kluster.
3. Huvudvy, navigera till **Klusterinstrumentpaneler** > **Ambari home**. Ange dina autentiseringsuppgifter för klusterinloggning.
4. Ambari UI, Välj **YARN** på listan över tjänster i den vänstra menyn.  
5. YARN-sidan väljer du **snabblänkar** och hovra över aktiva huvudnoden och välj sedan **ResourceManager UI**.

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

## <a name="rebalancing-an-apache-hbase-cluster"></a>Balansera ett Apache HBase-kluster

Regionservrar balanseras automatiskt inom ett par minuter efter skalning åtgärden slutfördes. För att jämna ut regionservrar manuellt, använder du följande steg:

1. Ansluta till HDInsight-klustret med SSH. Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. Starta HBase-gränssnittet:

        hbase shell

3. Använder du följande kommando för att balansera regionservrar manuellt:

        balancer

## <a name="scale-down-implications"></a>Skala ned effekter

Som tidigare nämnts är avslutas alla väntande eller pågående jobb vid slutförandet av en skalas ned igen. Det finns dock andra eventuella effekter att skala ned som kan uppstå.

## <a name="hdinsight-name-node-stays-in-safe-mode-after-scaling-down"></a>HDInsight namn noden är i felsäkert läge när du skalar ned

Om du minska ditt kluster till minst en underordnad nod fastna Apache HDFS i felsäkert läge när arbetsnoder startas om på grund av korrigeringar eller omedelbart efter skalning igen.

Den primära orsaken till detta är att Hive använder några `scratchdir` filer och förväntar sig tre repliker av varje block som standard men det finns bara en replik möjligt om du skalar till minst en arbetsnoden. Som en följd filerna i den `scratchdir` blir *under-replikerade*. Det kan leda till HDFS vara i felsäkert läge när tjänsterna har startats om efter åtgärden.

När en skala ned försök sker, använder HDInsight Apache Ambari-hanteringsgränssnitt du först inaktivera de extra oönskad arbetsnoder som replikerar sina HDFS-block till andra online arbetsnoder, och på ett säkert sätt skala klustret ned. HDFS hamnar i felsäkert läge under underhållsfönstret, och ska komma när den skalning är klar. Nu är det att HDFS fastna i felsäkert läge.

HDFS är konfigurerad med en `dfs.replication` inställning av 3. Därför är block av tillfälliga filer under-replikerade när det finns färre än tre arbetsnoder online, eftersom det finns inte de förväntade tre kopiorna av varje fil-block.

Du kan köra ett kommando för att aktivera HDFS från felsäkert läge. Till exempel om du vet att det enda skälet felsäkert läge finns på eftersom de temporära filerna är under-replikerade, sedan på ett säkert sätt kan du lämna felsäkert läge. Det beror på att under-replikerade filer är Hive temporära tillfälliga filer.

```bash
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
```

Efter att ha lämnat felsäkert läge, kan du manuellt bort temporära filer eller vänta tills Hive att så småningom Rensa dem automatiskt.

### <a name="example-errors-when-safe-mode-is-turned-on"></a>Exempel på fel när felsäkert läge är aktiverat

* H070 det går inte att öppna Hive-session. org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.ipc.RetriableException): org.apache.hadoop.hdfs.server.namenode.SafeModeException: **Det går inte att skapa katalog** /tmp/hive/hive/819c215c-6d 87-4311-97 c 8-4f0b9d2adcf0. **Namn på noden är i felsäkert läge**. De rapporterade blocken 75 måste ytterligare 12 förutsättningarna för att nå tröskelvärdet 0.9900 av totalt antal block 87. Antalet live datanodes 10 har nått det lägsta tillåtna värdet 0. Felsäkert läge stängs automatiskt när tröskelvärdena har uppnåtts.

* H100 inte går att skicka instruktionen Visa databaser: org.apache.thrift.transport.TTransportException: org.apache.http.conn.HttpHostConnectException: Ansluta till hn0-clustername.servername.internal.cloudapp.net:10001 [hn0 clustername.servername. Det gick inte att internal.cloudapp.NET/1.1.1.1]: **Anslutningen nekades**

* H020 gick inte att upprätta någon anslutning till hn0 hdisrv.servername.bx.internal.cloudapp .net: 10001: org.apache.thrift.transport.TTransportException: Det gick inte att skapa http-anslutning till http:\//hn0-hdisrv.servername.bx.internal.cloudapp.net:10001/. org.apache.http.conn.HttpHostConnectException: Anslut till hn0-hdisrv.servername.bx.internal.cloudapp.net:10001 [hn0-hdisrv.servername.bx.internal.cloudapp.net/10.0.0.28] misslyckades: Anslutningen nekades: org.apache.thrift.transport.TTransportException: Det gick inte att skapa http-anslutning till http:\//hn0-hdisrv.servername.bx.internal.cloudapp.net:10001/. org.apache.http.conn.HttpHostConnectException: Anslut till hn0-hdisrv.servername.bx.internal.cloudapp.net:10001 [hn0-hdisrv.servername.bx.internal.cloudapp.net/10.0.0.28] misslyckades: **Anslutningen nekades**

* Från Hive-loggar: Varna [main]: server. HiveServer2 (HiveServer2.java:startHiveServer2(442)) – gick inte att starta HiveServer2 vid försöket 21, kommer att försöka igen i 60 sekunder java.lang.RuntimeException: Fel vid tillämpningen av auktoriseringsprincip på hive-konfiguration: org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.ipc.RetriableException): org.apache.hadoop.hdfs.server.namenode.SafeModeException: **Det går inte att skapa katalog** /tmp/hive/hive/70a42b8a-9437-466e-acbe-da90b1614374. **Namn på noden är i felsäkert läge**.
    De rapporterade blocken 0 måste ytterligare 9 förutsättningarna för att nå tröskelvärdet 0.9900 av totalt antal block 9.
    Antalet live datanodes 10 har nått det lägsta tillåtna värdet 0. **Felsäkert läge kommer att inaktiveras automatiskt när tröskelvärdena har uppnåtts**.
    at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1324)

Du kan granska namnet node-loggar från den `/var/log/hadoop/hdfs/` mappen nära tiden när klustret har skalats för att se när också övergick den i felsäkert läge. Loggfilerna är namngivna `Hadoop-hdfs-namenode-hn0-clustername.*`.

De grundläggande orsakerna till tidigare fel är att Hive beror på tillfälliga filer i HDFS vid körning av frågor. När HDFS anger felsäkert läge, det går inte att Hive köra frågor eftersom den inte kan skrivas till HDFS. Tillfälliga filer i HDFS finns i den lokala enheten monteras till arbetsnoden enskilda virtuella datorer och replikerade bland andra arbetsnoder i tre repliker, minsta.

Den `hive.exec.scratchdir` parameter i Hive konfigureras i `/etc/hive/conf/hive-site.xml`:

```xml
<property>
    <name>hive.exec.scratchdir</name>
    <value>hdfs://mycluster/tmp/hive</value>
</property>
```

### <a name="view-the-health-and-state-of-your-hdfs-file-system"></a>Visa hälso- och tillståndet för HDFS-filsystemets

Du kan visa en statusrapport från varje namn-nod för att se om det finns noder i felsäkert läge. Visa rapporten, SSH till varje huvudnod och kör följande kommando:

```
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode get
```

![Felsäkert läge av](./media/hdinsight-scaling-best-practices/safe-mode-off.png)

> [!NOTE]  
> Den `-D` växel är nödvändigt eftersom standardfilsystemet i HDInsight är antingen Azure Storage eller Azure Data Lake Storage. `-D` Anger att kommandona körs mot lokala HDFS-filsystemet.

Därefter kan du visa en rapport som visar information om HDFS-tillstånd:

```
hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -report
```

Det här kommandot resulterar i följande i ett felfritt kluster där alla block replikeras till den förväntade graden:

![Felsäkert läge av](./media/hdinsight-scaling-best-practices/report.png)

HDFS stöder den `fsck` kommando för att kontrollera efter inkonsekvenser med olika filer, till exempel saknas blockerar för en fil eller under-replikerade block. Att köra den `fsck` kommandot mot den `scratchdir` (tillfällig virtuellt minne) filer:

```
hdfs fsck -D 'fs.default.name=hdfs://mycluster/' /tmp/hive/hive
```

När den körs på en felfri HDFS-filsystemets med inga under-replikerade blocken, kan du se utdata som liknar följande:

```
Connecting to namenode via http://hn0-scalin.name.bx.internal.cloudapp.net:30070/fsck?ugi=sshuser&path=%2Ftmp%2Fhive%2Fhive
FSCK started by sshuser (auth:SIMPLE) from /10.0.0.21 for path /tmp/hive/hive at Thu Jul 06 20:07:01 UTC 2017
..Status: HEALTHY
 Total size:    53 B
 Total dirs:    5
 Total files:   2
 Total symlinks:                0 (Files currently being written: 2)
 Total blocks (validated):      2 (avg. block size 26 B)
 Minimally replicated blocks:   2 (100.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       0 (0.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     3.0
 Corrupt blocks:                0
 Missing replicas:              0 (0.0 %)
 Number of data-nodes:          4
 Number of racks:               1
FSCK ended at Thu Jul 06 20:07:01 UTC 2017 in 3 milliseconds


The filesystem under path '/tmp/hive/hive' is HEALTHY
```

Däremot, när den `fsck` kommando körs på ett HDFS-filsystemets med vissa under-replikerade blocken, utdata som liknar följande:

```
Connecting to namenode via http://hn0-scalin.name.bx.internal.cloudapp.net:30070/fsck?ugi=sshuser&path=%2Ftmp%2Fhive%2Fhive
FSCK started by sshuser (auth:SIMPLE) from /10.0.0.21 for path /tmp/hive/hive at Thu Jul 06 20:13:58 UTC 2017
.
/tmp/hive/hive/4f3f4253-e6d0-42ac-88bc-90f0ea03602c/inuse.info:  Under replicated BP-1867508080-10.0.0.21-1499348422953:blk_1073741826_1002. Target Replicas is 3 but found 1 live replica(s), 0 decommissioned replica(s) and 0 decommissioning replica(s).
.
/tmp/hive/hive/e7c03964-ff3a-4ee1-aa3c-90637a1f4591/inuse.info: CORRUPT blockpool BP-1867508080-10.0.0.21-1499348422953 block blk_1073741825

/tmp/hive/hive/e7c03964-ff3a-4ee1-aa3c-90637a1f4591/inuse.info: MISSING 1 blocks of total size 26 B.Status: CORRUPT
 Total size:    53 B
 Total dirs:    5
 Total files:   2
 Total symlinks:                0 (Files currently being written: 2)
 Total blocks (validated):      2 (avg. block size 26 B)
  ********************************
  UNDER MIN REPL'D BLOCKS:      1 (50.0 %)
  dfs.namenode.replication.min: 1
  CORRUPT FILES:        1
  MISSING BLOCKS:       1
  MISSING SIZE:         26 B
  CORRUPT BLOCKS:       1
  ********************************
 Minimally replicated blocks:   1 (50.0 %)
 Over-replicated blocks:        0 (0.0 %)
 Under-replicated blocks:       1 (50.0 %)
 Mis-replicated blocks:         0 (0.0 %)
 Default replication factor:    3
 Average block replication:     0.5
 Corrupt blocks:                1
 Missing replicas:              2 (33.333332 %)
 Number of data-nodes:          1
 Number of racks:               1
FSCK ended at Thu Jul 06 20:13:58 UTC 2017 in 28 milliseconds


The filesystem under path '/tmp/hive/hive' is CORRUPT
```

Du kan också visa status för HDFS i Ambari UI genom att välja den **HDFS** tjänsten till vänster eller med `https://<HDInsightClusterName>.azurehdinsight.net/#/main/services/HDFS/summary`.

![Ambari HDFS-status](./media/hdinsight-scaling-best-practices/ambari-hdfs.png)

Du kan också se en eller flera kritiska fel på den aktiva eller vänteläge NameNodes. Välj länken NameNode bredvid aviseringen om du vill visa NameNode block hälsotillstånd.

![NameNode Blocks Health](./media/hdinsight-scaling-best-practices/ambari-hdfs-crit.png)

Om du vill rensa tillfälliga filer, vilket tar bort replikeringsfel block, SSH till varje gå nod och kör följande kommando:

```
hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
```

> [!NOTE]  
> Det här kommandot kan bryta Hive om vissa jobb körs fortfarande.

### <a name="how-to-prevent-hdinsight-from-getting-stuck-in-safe-mode-due-to-under-replicated-blocks"></a>Hur du förhindrar att HDInsight blir kvar i felsäkert läge på grund av under-replikerade block

Det finns flera sätt att förhindra att HDInsight kan lämnas i felsäkert läge:

* Stoppa alla Hive-jobb innan du skalar ned HDInsight. Alternativt kan du schemalägga skala ned processen för att undvika motstridiga med att köra Hive-jobb.
* Rensa manuellt Hives grunden `tmp` directory filer i HDFS innan du skalar.
* Endast skala ned HDInsight till tre arbetsnoder, minsta. Undvik att gå så lite som en underordnad nod.
* Kör kommandot för att lämna felsäkert läge, om det behövs.

I följande avsnitt beskrivs dessa alternativ.

#### <a name="stop-all-hive-jobs"></a>Stoppa alla Hive-jobb

Stoppa alla Hive-jobb innan du skalar till en underordnad nod. Om din arbetsbelastning har schemalagts, kör du din skala ned när Hive-jobbet är klart.

På så sätt kan du minimera antalet tillfälliga filer i Temp-mappen (i förekommande fall).

#### <a name="manually-clean-up-hives-scratch-files"></a>Manuellt Rensa Hives tillfälliga filer

Om Hive har lämnat bakom temporära filer, och sedan du manuellt Rensa upp dessa filer innan du skalar ned till undvika felsäkert läge.

1. Stoppa Hive-tjänster och se till att alla frågor och jobb har slutförts.

2. Visa innehållet i den `hdfs://mycluster/tmp/hive/` katalog för att se om det innehåller alla filer:

    ```
    hadoop fs -ls -R hdfs://mycluster/tmp/hive/hive
    ```
    
    Här är ett exempel på utdata när filer finns:

    ```
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

    ```
    hadoop fs -rm -r -skipTrash hdfs://mycluster/tmp/hive/
    ```
    
#### <a name="scale--hdinsight-to-three-worker-nodes"></a>Skala HDInsight till tre arbetsnoder

Om fastnar i felsäkert läge är beständiga problem och föregående steg är inte alternativ, och sedan kan du undvika problemet genom att endast skala ned till tre arbetsnoder. Det kanske inte är optimalt på grund av kostnader, jämfört med skala ned till en nod. Men med endast en underordnad nod HDFS kan inte garantera tre repliker av data är tillgängliga för klustret.

#### <a name="run-the-command-to-leave-safe-mode"></a>Kör kommando för att lämna felsäkert läge

Det sista alternativet är att bevaka sällsynta fall där HDFS försätts i felsäkert läge och sedan köra kommandot lämna felsäkert läge. När du har bestämt att orsaken HDFS har försatts i felsäkert läge är på grund av Hive-filer som under-replikerade, kör följande kommando för att lämna felsäkert läge:

* HDInsight på Linux:

    ```bash
    hdfs dfsadmin -D 'fs.default.name=hdfs://mycluster/' -safemode leave
    ```
    
* HDInsight på Windows:

    ```bash
    hdfs dfsadmin -fs hdfs://headnodehost:9000 -safemode leave
    ```
    
## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure HDInsight](hadoop/apache-hadoop-introduction.md)
* [Skala kluster](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Hantera HDInsight-kluster med hjälp av Apache Ambari-webbgränssnittet](hdinsight-hadoop-manage-ambari.md)
