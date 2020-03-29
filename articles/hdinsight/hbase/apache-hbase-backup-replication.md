---
title: Säkerhetskopiering & replikering för Apache HBase, Phoenix – Azure HDInsight
description: Konfigurera säkerhetskopiering och replikering för Apache HBase och Apache Phoenix i Azure HDInsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/19/2019
ms.openlocfilehash: c6d33158b581bf4394a0d1bac2b277830328e110
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495947"
---
# <a name="set-up-backup-and-replication-for-apache-hbase-and-apache-phoenix-on-hdinsight"></a>Konfigurera säkerhetskopiering och replikering för Apache HBase och Apache Phoenix på HDInsight

Apache HBase stöder flera metoder för att skydda mot dataförlust:

* Kopiera `hbase` mappen
* Exportera sedan Importera
* Kopiera tabeller
* Ögonblicksbilder
* Replikering

> [!NOTE]  
> Apache Phoenix lagrar sina metadata i HBase-tabeller, så att metadata säkerhetskopieras när du säkerhetskopierar HBase-systemkatalogtabellerna.

I följande avsnitt beskrivs användningsscenariot för var och en av dessa metoder.

## <a name="copy-the-hbase-folder"></a>Kopiera hbase-mappen

Med den här metoden kopierar du alla HBase-data utan att kunna välja en delmängd av tabeller eller kolumnfamiljer. Efterföljande metoder ger större kontroll.

HBase i HDInsight använder standardlagringen som valts när klustret skapas, antingen Azure Storage-blobbar eller Azure Data Lake Storage. I båda fallen lagrar HBase sina data- och metadatafiler under följande sökväg:

    /hbase

* I ett Azure `hbase` Storage-konto finns mappen vid roten till blob-behållaren:

    ```
    wasbs://<containername>@<accountname>.blob.core.windows.net/hbase
    ```

* I Azure Data Lake `hbase` Storage finns mappen under den rotsökväg som du angav när du etablerade ett kluster. Den här rotsökvägen har vanligtvis en `clusters` mapp med en undermapp uppkallad efter ditt HDInsight-kluster:

    ```
    /clusters/<clusterName>/hbase
    ```

I båda fallen `hbase` innehåller mappen alla data som HBase har tömts på disk, men den kanske inte innehåller minnesdata. Innan du kan lita på den här mappen som en korrekt representation av HBase-data måste du stänga av klustret.

När du har tagit bort klustret kan du antingen lämna data på plats eller kopiera data till en ny plats:

* Skapa en ny HDInsight-instans som pekar på den aktuella lagringsplatsen. Den nya instansen skapas med alla befintliga data.

* Kopiera `hbase` mappen till en annan Azure Storage-blob-behållare eller datasjölagringsplats och starta sedan ett nytt kluster med dessa data. För Azure Storage använder du [AzCopy](../../storage/common/storage-use-azcopy.md)och för lagring av datasjö använder [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md).

## <a name="export-then-import"></a>Exportera sedan Importera

I käll-HDInsight-klustret använder du [verktyget Exportera](https://hbase.apache.org/book.html#export) (medföljer HBase) för att exportera data från en källtabell till standardansluten lagring. Du kan sedan kopiera den exporterade mappen till mållagringsplatsen och köra [importverktyget](https://hbase.apache.org/book.html#import) i mål-HDInsight-klustret.

Om du vill exportera tabelldata exporterar du först SSH till huvudnoden för ditt HDInsight-källkluster och kör sedan följande `hbase` kommando:

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>"

Exportkatalogen får inte redan finnas. Tabellnamnet är skiftlägeskänsligt.

Om du vill importera tabelldata går SSH till huvudnoden för `hbase` ditt HDInsight-kluster och kör sedan följande kommando:

    hbase org.apache.hadoop.hbase.mapreduce.Import "<tableName>" "/<path>/<to>/<export>"

Tabellen måste redan finnas.

Ange den fullständiga exportsökvägen till standardlagringen eller till något av de bifogade lagringsalternativen. Till exempel i Azure Storage:

    wasbs://<containername>@<accountname>.blob.core.windows.net/<path>

I Azure Data Lake Storage Gen2 är syntaxen:

    abfs://<containername>@<accountname>.dfs.core.windows.net/<path>

I Azure Data Lake Storage Gen1 är syntaxen:

    adl://<accountName>.azuredatalakestore.net:443/<path>

Den här metoden erbjuder detaljrikighet på bordsnivå. Du kan också ange ett datumintervall för raderna som ska inkluderas, vilket gör att du kan utföra processen stegvis. Varje datum är i millisekunder sedan Unix-epoken.

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>" <numberOfVersions> <startTimeInMS> <endTimeInMS>

Observera att du måste ange antalet versioner av varje rad som ska exporteras. Om du vill inkludera alla `<numberOfVersions>` versioner i datumintervallet anger du ett värde som är större än dina maximala möjliga radversioner, till exempel 100000.

## <a name="copy-tables"></a>Kopiera tabeller

[Verktyget CopyTable](https://hbase.apache.org/book.html#copy.table) kopierar data från en källtabell, rad för rad, till en befintlig måltabell med samma schema som källan. Måltabellen kan finnas i samma kluster eller ett annat HBase-kluster. Tabellnamnen är skiftlägeskänsliga.

Om du vill använda CopyTable i ett kluster sidentar SSH i `hbase` huvudnoden för ditt HDInsight-kluster och kör sedan det här kommandot:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> <srcTableName>


Om du vill använda CopyTable för att kopiera `peer` till en tabell i ett annat kluster lägger du till växeln med målklustrets adress:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> --peer.adr=<destinationAddress> <srcTableName>

Måladressen består av följande tre delar:

    <destinationAddress> = <ZooKeeperQuorum>:<Port>:<ZnodeParent>

* `<ZooKeeperQuorum>`är en kommaavgränsad lista över Apache ZooKeeper-noder, till exempel:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net

* `<Port>`på HDInsight standard till 2181, och `<ZnodeParent>` är `/hbase-unsecure`, så den kompletta `<destinationAddress>` skulle vara:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net:2181:/hbase-unsecure

Mer information om hur du hämtar dessa värden för DITT HDInsight-kluster finns i [Manuellt samla in Apache ZooKeeper Quorum-listan](#manually-collect-the-apache-zookeeper-quorum-list) i den här artikeln.

Verktyget CopyTable stöder också parametrar för att ange tidsintervallet för rader som ska kopieras och för att ange delmängden av kolumnfamiljer i en tabell som ska kopieras. Om du vill se hela listan över parametrar som stöds av CopyTable kör du CopyTable utan några parametrar:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable

CopyTable söker igenom hela källtabellinnehållet som kopieras över till måltabellen. Detta kan minska HBase-klustrets prestanda medan CopyTable körs.

> [!NOTE]  
> Information om hur du automatiserar kopieringen `hdi_copy_table.sh` av data mellan tabeller finns i skriptet i [Azure HBase Utils-databasen](https://github.com/Azure/hbase-utils/tree/master/replication) på GitHub.

### <a name="manually-collect-the-apache-zookeeper-quorum-list"></a>Samla in Apache ZooKeeper-kvorumlistan manuellt

När båda HDInsight-kluster finns i samma virtuella nätverk, som tidigare beskrivits, är intern värdnamnsmatchning automatisk. Om du vill använda CopyTable för HDInsight-kluster i två separata virtuella nätverk som är anslutna via en VPN-gateway måste du ange värd-IP-adresserna för Zookeeper-noderna i kvorumet.

Om du vill hämta kvorumvärden kör du följande curl-kommando:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/configurations?type=hbase-site&tag=TOPOLOGY_RESOLVED" | grep "hbase.zookeeper.quorum"

Kommandot Curl hämtar ett JSON-dokument med HBase-konfigurationsinformation och kommandot grep returnerar endast posten "hbase.zookeeper.quorum", till exempel:

    "hbase.zookeeper.quorum" : "zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net"

Värdet för kvorumvärden är hela strängen till höger om kolon.

Om du vill hämta IP-adresserna för dessa värdar använder du följande curl-kommando för varje värd i föregående lista:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/hosts/<zookeeperHostFullName>" | grep "ip"

I det här `<zookeeperHostFullName>` curl-kommandot är det fullständiga DNS-namnet `zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net`på en ZooKeeper-värd, till exempel exemplet . Utdata för kommandot innehåller IP-adressen för den angivna värden, till exempel:

    100    "ip" : "10.0.0.9",

När du har samlat in IP-adresserna för alla ZooKeeper-noder i kvorumet återskapar du måladressen:

    <destinationAddress>  = <Host_1_IP>,<Host_2_IP>,<Host_3_IP>:<Port>:<ZnodeParent>

I vårt exempel:

    <destinationAddress> = 10.0.0.9,10.0.0.8,10.0.0.12:2181:/hbase-unsecure

## <a name="snapshots"></a>Ögonblicksbilder

[Ögonblicksbilder](https://hbase.apache.org/book.html#ops.snapshots) kan du ta en point-in-time säkerhetskopiering av data i din HBase datalager. Ögonblicksbilder har minimal overhead och slutföra inom några sekunder, eftersom en ögonblicksbild operation är effektivt en metadataåtgärd fånga namnen på alla filer i lagring på den tidpunkten. Vid tidpunkten för en ögonblicksbild kopieras inga faktiska data. Ögonblicksbilder är beroende av den oföränderliga karaktären hos de data som lagras i HDFS, där uppdateringar, borttagningar och infogningar alla representeras som nya data. Du kan återställa *(klona)* en ögonblicksbild på samma kluster eller exportera en ögonblicksbild till ett annat kluster.

Om du vill skapa en ögonblicksbild går SSH in till huvudnoden `hbase` för ditt HDInsight HBase-kluster och startar skalet:

    hbase shell

I hbase-skalet använder du kommandot ögonblicksbild med namnen på tabellen och den här ögonblicksbilden:

    snapshot '<tableName>', '<snapshotName>'

Om du vill återställa `hbase` en ögonblicksbild efter namn i skalet inaktiverar du först tabellen och återställer sedan ögonblicksbilden och aktiverar tabellen igen:

    disable '<tableName>'
    restore_snapshot '<snapshotName>'
    enable '<tableName>'

Om du vill återställa en ögonblicksbild till en ny tabell använder du clone_snapshot:

    clone_snapshot '<snapshotName>', '<newTableName>'

Om du vill exportera en ögonblicksbild till HDFS för användning av ett annat kluster skapar du först ögonblicksbilden enligt beskrivningen tidigare och använder sedan verktyget ExportSnapshot. Kör det här verktyget inifrån SSH-sessionen till `hbase` huvudnoden, inte i skalet:

     hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot <snapshotName> -copy-to <hdfsHBaseLocation>

Den `<hdfsHBaseLocation>` kan vara någon av de lagringsplatser som är tillgängliga för källklustret och bör peka på hbase-mappen som används av målklustret. Om du till exempel har ett sekundärt Azure Storage-konto kopplat till källklustret och det kontot ger åtkomst till behållaren som används av standardlagringen i målklustret, kan du använda det här kommandot:

    hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'

När ögonblicksbilden har exporterats, SSH till huvudet noden för målklustret och återställa ögonblicksbilden med hjälp av restore_snapshot kommandot som tidigare beskrivits.

Ögonblicksbilder ger en fullständig säkerhetskopia av en `snapshot` tabell vid tidpunkten för kommandot. Ögonblicksbilder ger inte möjlighet att utföra inkrementella ögonblicksbilder av tidsfönster, inte heller att ange delmängder av kolumner familjer att inkludera i ögonblicksbilden.

## <a name="replication"></a>Replikering

[HBase-replikering](https://hbase.apache.org/book.html#_cluster_replication) skickar automatiskt transaktioner från ett källkluster till ett målkluster med hjälp av en asynkron mekanism med minimal omkostnader i källklustret. I HDInsight kan du ställa in replikering mellan kluster där:

* Käll- och målkluster finns i samma virtuella nätverk.
* Käll- och målkluster finns i olika virtuella nätverk som är anslutna via en VPN-gateway, men båda klustren finns på samma geografiska plats.
* Källkluster och målkluster finns i olika virtuella nätverk som är anslutna via en VPN-gateway och varje kluster finns på en annan geografisk plats.

De allmänna stegen för att ställa in replikering är:

1. Skapa tabellerna och fylla i data i källklustret.
2. Skapa tomma måltabeller med källtabellens schema i målklustret.
3. Registrera målklustret som peer i källklustret.
4. Aktivera replikering i önskade källtabeller.
5. Kopiera befintliga data från källtabellerna till måltabellerna.
6. Replikeringen kopierar automatiskt nya dataändringar till källtabellerna till måltabellerna.

Om du vill aktivera replikering på HDInsight använder du en skriptåtgärd på ditt HDInsight-kluster som körs. Information om hur du aktiverar replikering i klustret eller att experimentera med replikering på exempelkluster som skapats i virtuella nätverk med azure Resource Management-mallar finns i [Konfigurera Apache HBase-replikering](apache-hbase-replication.md). Artikeln innehåller även instruktioner för hur du aktiverar replikering av Phoenix-metadata.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera Apache HBase-replikering](apache-hbase-replication.md)
* [Arbeta med HBase Import and Export Utility](https://blogs.msdn.microsoft.com/data_otaku/2016/12/21/working-with-the-hbase-import-and-export-utility/)
