---
title: Konfigurera Apache HBase- och Apache Phoenix säkerhetskopiering och replikering – Azure HDInsight
description: Konfigurera säkerhetskopiering och replikering för HBase och Phoenix.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: d7be248e49baf4e7fd10d6b37df1473e92ccfce7
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/20/2018
ms.locfileid: "53651732"
---
# <a name="set-up-backup-and-replication-for-apache-hbase-and-apache-phoenix-on-hdinsight"></a>Konfigurera säkerhetskopiering och replikering för Apache HBase- och Apache Phoenix i HDInsight

Apache HBase stöder flera metoder för skydd mot dataförlust:

* Kopiera den `hbase` mapp
* Exportera sedan importera
* Kopiera tabeller
* Ögonblicksbilder
* Replikering

> [!NOTE]  
> Apache Phoenix lagras dess metadata i HBase-tabeller, så att metadata har säkerhetskopierats när du säkerhetskopierar HBase catalog systemtabeller.

I följande avsnitt beskrivs användningsscenariot för var och en av dessa metoder.

## <a name="copy-the-hbase-folder"></a>Kopiera mappen hbase

Med den här metoden kan kopiera du alla data i HBase, utan att kunna välja en delmängd av tabeller eller kolumnserier. Efterföljande metoder ger större kontroll.

HBase i HDInsight använder standardlagring valt när du skapar klustret, Azure Storage-blobbar eller Azure Data Lake Storage. I båda fallen lagrar HBase dess data och metadata filer på följande sökväg:

    /hbase

* I ett Azure Storage-konto i `hbase` mappen finns i roten på blob-behållaren:

    ```
    wasbs://<containername>@<accountname>.blob.core.windows.net/hbase
    ```

* I Azure Data Lake Storage den `hbase` mappen finns under rotsökvägen som du angav när du etablerar ett kluster. Den här rotsökvägen har vanligtvis en `clusters` mapp med en undermapp med namnet efter ditt HDInsight-kluster:

    ```
    /clusters/<clusterName>/hbase
    ```

I båda fallen den `hbase` mappen innehåller alla data som HBase har skrivits till disken, men det får inte innehålla data i minnet. Innan du kan lita på den här mappen som en korrekt återgivning av HBase-data, måste du stänga av klustret.

När du tar bort klustret, kan du lämna data på plats eller kopiera data till en ny plats:

* Skapa en ny HDInsight-instans som pekar till den aktuella lagringsplatsen. Den nya instansen skapas med alla befintliga data.

* Kopiera den `hbase` mappen till en annan Azure Storage blob-behållare eller Data Lake-lagringsplats och starta sedan ett nytt kluster med dessa data. Azure Storage, använda [AzCopy](../../storage/common/storage-use-azcopy.md), och för Data Lake Storage användning [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md).

## <a name="export-then-import"></a>Exportera sedan importera

På källklustret HDInsight Använd Export-verktyget (ingår i HBase) att exportera data från en källtabellen till anslutna standardlagring. Du kan sedan kopiera den exporterade mappen till lagringsplatsen för mål och kör verktyget Import på HDInsight målklustret.

Att exportera en tabell, första SSH till klustrets huvudnod ditt källa HDInsight-kluster och kör sedan följande `hbase` kommando:

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>"

Importera en tabell, SSH till klustrets huvudnod ditt mål HDInsight-kluster och kör sedan följande `hbase` kommando:

    hbase org.apache.hadoop.hbase.mapreduce.Import "<tableName>" "/<path>/<to>/<export>"

Ange fullständig exportsökvägen till standardlagring eller till någon av de anslutna lagringsalternativ. Till exempel i Azure Storage:

    wasbs://<containername>@<accountname>.blob.core.windows.net/<path>

I Azure Data Lake Storage är syntaxen:

    adl://<accountName>.azuredatalakestore.net:443/<path>

Den här metoden erbjuder kornighet för tabellen på servernivå. Du kan också ange ett datumintervall för rader ska ingå, där du kan utföra processen inkrementellt. Varje datum som anges i millisekunder sedan Unix-epokintervallet.

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>" <numberOfVersions> <startTimeInMS> <endTimeInMS>

Observera att du måste ange antalet versioner för varje rad att exportera. För att inkludera alla versioner i datumintervall, ange `<numberOfVersions>` till ett värde som är större än de högsta möjliga rad-versionerna, till exempel 100000.

## <a name="copy-tables"></a>Kopiera tabeller

Verktyget CopyTable kopierar data från en källtabellen, rad för rad i en befintlig måltabell med samma schema som källa. Måltabellen kan finnas på samma kluster eller ett annat HBase-kluster.

Att använda CopyTable i ett kluster, SSH till klustrets huvudnod i din källa HDInsight-kluster och kör det här `hbase` kommando:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> <srcTableName>

Om du vill använda CopyTable för att kopiera till en tabell i ett annat kluster, lägger du till den `peer` växel med mål klusteradress:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> --peer.adr=<destinationAddress> <srcTableName>

Måladressen består av följande tre delar:

    <destinationAddress> = <ZooKeeperQuorum>:<Port>:<ZnodeParent>

* `<ZooKeeperQuorum>` är en kommaavgränsad lista med Apache ZooKeeper-noder, till exempel:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net

* `<Port>` på HDInsight standard till 2181, och `<ZnodeParent>` är `/hbase-unsecure`, så den fullständiga `<destinationAddress>` skulle bli:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net:2181:/hbase-unsecure

Se [manuellt samla in listan Apache ZooKeeper kvorum](#manually-collect-the-apache-zookeeper-quorum-list) i den här artikeln för information om hur du hämtar dessa värden för ditt HDInsight-kluster.

Verktyget CopyTable har även stöd för parametrar för att ange tidsintervall rader att kopiera och för att ange delen av kolumnserier i en tabell för att kopiera. Om du vill se den fullständiga listan över parametrar som stöds av CopyTable, kör du CopyTable utan några parametrar:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable

CopyTable söker igenom hela tabellen källinnehållet som ska kopieras över till tabellen. Detta kan försämra prestanda för din HBase-kluster medan CopyTable körs.

> [!NOTE]  
> Om du vill automatisera att kopiera data mellan tabeller, se den `hdi_copy_table.sh` skript i den [Azure HBase Utils](https://github.com/Azure/hbase-utils/tree/master/replication) arkivet på GitHub.

### <a name="manually-collect-the-apache-zookeeper-quorum-list"></a>Manuellt samla in Apache ZooKeeper kvorum lista

När båda HDInsight-kluster finns i samma virtuella nätverk, som beskrivs ovan, är interna värdnamnsmatchning automatisk. Om du vill använda CopyTable för HDInsight-kluster i två separata virtuella nätverk som är anslutna via en VPN-Gateway, behöver du ange IP-adresserna för Zookeeper-noder i kvorum.

Om du vill hämta värdnamn kvorum, kör du följande curl-kommando:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/configurations?type=hbase-site&tag=TOPOLOGY_RESOLVED" | grep "hbase.zookeeper.quorum"

Curl-kommandot hämtar ett JSON-dokument med konfigurationsinformation för HBase och grep-kommando returnerar bara ”hbase.zookeeper.quorum”-post, till exempel:

    "hbase.zookeeper.quorum" : "zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net"

Kvorum värd namn-värdet är hela strängen till höger om kolumnen.

Använd följande curl-kommando för varje värd i listan ovan för att hämta IP-adresser för dessa värdar:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/hosts/<zookeeperHostFullName>" | grep "ip"

I det här curl-kommandot `<zookeeperHostFullName>` är det fullständiga DNS-namnet för en ZooKeeper-värden, till exempel `zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net`. Kommandots utdata innehåller IP-adressen för den angivna värden, till exempel:

    100    "ip" : "10.0.0.9",

När du har samlat in IP-adresser för alla ZooKeeper-noder i dina kvorum återskapa måladressen:

    <destinationAddress>  = <Host_1_IP>,<Host_2_IP>,<Host_3_IP>:<Port>:<ZnodeParent>

I vårt exempel:

    <destinationAddress> = 10.0.0.9,10.0.0.8,10.0.0.12:2181:/hbase-unsecure

## <a name="snapshots"></a>Ögonblicksbilder

Ögonblicksbilder kan du ta point-in-time säkerhetskopiering av data i HBase-datalager. Ögonblicksbilder har minimalt merarbete och inom några sekunder att slutföra eftersom en ögonblicksbildsåtgärden effektivt en metadata-åtgärd samla in namnen på alla filer i storage i det ögonblicket. Vid tidpunkten för en ögonblicksbild kopieras inga faktiska data. Ögonblicksbilder är beroende av den inte kan ändras natur data som lagras i HDFS, där uppdateringar, borttagningar och infogningar alla representeras som nya data. Du kan återställa (*klona*) en ögonblicksbild på samma kluster, eller exportera en ögonblicksbild till ett annat kluster.

Att skapa en ögonblicksbild, SSH i till huvudnoden för ditt HDInsight HBase-kluster och starta den `hbase` shell:

    hbase shell

Använd kommandot ögonblicksbild med namnen på tabellen och den här ögonblicksbilden i hbase-gränssnittet:

    snapshot '<tableName>', '<snapshotName>'

Återställa en ögonblicksbild med namnet i den `hbase` shell först inaktiverar tabellen, och sedan återställa ögonblicksbilden och återaktivera tabellen:

    disable '<tableName>'
    restore_snapshot '<snapshotName>'
    enable '<tableName>'

Om du vill återställa en ögonblicksbild till en ny tabell, använder du clone_snapshot:

    clone_snapshot '<snapshotName>', '<newTableName>'

Om du vill exportera en ögonblicksbild till HDFS för användning av ett annat kluster, först skapa en ögonblicksbild som tidigare beskrivits och sedan använda verktyget ExportSnapshot. Kör det här verktyget från inom SSH-session till huvudnoden, inte i den `hbase` shell:

     hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot <snapshotName> -copy-to <hdfsHBaseLocation>

Den `<hdfsHBaseLocation>` kan vara något av lagringsplatser tillgängliga för källklustret och måste peka på hbase-mappen som används av din målklustret. Om du har en sekundär Azure Storage-konto som är kopplat till din källklustret och det kontot ger åtkomst till behållaren som används av standardlagring av målklustret, kan du till exempel använda det här kommandot:

    hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'

När ögonblicksbilden exporteras kommando SSH till klustrets huvudnod mål och Återställ ögonblicksbild med hjälp av restore_snapshot som det beskrivits.

Ögonblicksbilder ger en fullständig säkerhetskopia av en tabell vid tidpunkten för den `snapshot` kommando. Ögonblicksbilder ger inte möjlighet att utföra inkrementella ögonblicksbilder av windows tid, och inte heller att ange delmängder av kolumner familjer ska ingå i ögonblicksbilden.

## <a name="replication"></a>Replikering

HBase-replikering skickar automatiskt transaktioner från ett kluster för källa till ett mål-kluster med hjälp av en mekanism för asynkron med minimal belastning på källklustret. I HDInsight, du kan konfigurera replikering mellan kluster där:

* Käll- och -kluster finns i samma virtuella nätverk.
* Källa och mål-kluster finns i olika virtuella nätverk som är anslutna via en VPN-gateway, men båda klustren finns i samma geografiska plats.
* Klustret för källa och mål-kluster finns i olika virtuella nätverk som är anslutna via en VPN-gateway och varje kluster finns i en annan geografisk plats.

Allmänna steg för att konfigurera replikering är:

1. Skapa tabellerna på källklustret och fylla i data.
2. Skapa tom måltabellerna med källan tabellschemat på målklustret.
3. Registrera målklustret som en peer till källklustret.
4. Aktivera replikering på de önskade källtabellerna.
5. Kopiera befintliga data från källtabellerna till måltabeller.
6. Replikeringen kopierar automatiskt nya dataändringar av i källtabellerna till måltabeller.

Om du vill aktivera replikering på HDInsight, gäller en skriptåtgärd för ditt körs källa HDInsight-kluster. En genomgång av att ha aktiverat replikering i klustret eller för att experimentera med replikering på exemplet kluster som skapas i virtuella nätverk med hjälp av Azure Resource Manager-mallar finns i [konfigurerar Apache HBase-replikering](apache-hbase-replication.md). Den här artikeln innehåller också anvisningar för att aktivera replikering av Phoenix metadata.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera Apache HBase-replikering](apache-hbase-replication.md)
