---
title: "Konfigurera HBase och Phoenix säkerhetskopiering och replikering - Azure HDInsight | Microsoft Docs"
description: "Konfigurera säkerhetskopiering och replikering för HBase och Phoenix."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: 0385e85f7924da73132ae82fa776be274928e535
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2018
---
# <a name="set-up-backup-and-replication-for-hbase-and-phoenix-on-hdinsight"></a>Konfigurera säkerhetskopiering och replikering för HBase och Phoenix på HDInsight

HBase stöder flera metoder för skydd mot dataförlust:

* Kopiera den `hbase` mapp
* Exportera sedan importera
* Kopiera tabeller
* Ögonblicksbilder
* Replikering

> [!NOTE]
> Apache Phoenix lagras dess metadata i HBase-tabeller, så att metadata har säkerhetskopierats när du säkerhetskopierar HBase katalog systemtabeller.

I följande avsnitt beskrivs användningsscenariot för var och en av dessa metoder.

## <a name="copy-the-hbase-folder"></a>Kopiera mappen hbase

Med den här metoden kopiera alla data i HBase, utan att kunna välja en delmängd av tabeller eller kolumnserier. Följande metoder ger större kontroll.

HBase i HDInsight använder standardlagring valt när du skapar klustret, Azure Storage-blobbar eller Azure Data Lake Store. I båda fallen lagrar HBase dess data och metadata filer på följande sökväg:

    /hbase

* I ett Azure Storage-konto i `hbase` mappen finns i roten på blob-behållaren:

    ```
    wasbs://<containername>@<accountname>.blob.core.windows.net/hbase
    ```

* I Azure Data Lake Store den `hbase` mappen finns under rotsökvägen som du angav när du etablerar ett kluster. Den här rotsökvägen har vanligtvis en `clusters` mapp med en undermapp som heter efter ditt HDInsight-kluster:

    ```
    /clusters/<clusterName>/hbase
    ```

I båda fallen den `hbase` mappen innehåller de data som HBase har skrivits till disken, men den får inte innehålla data i minnet. Innan du kan förlita dig på den här mappen som exakt så HBase-data, måste du stänga av klustret.

När du tar bort klustret kan du antingen lämna data på plats eller kopiera data till en ny plats:

* Skapa en ny HDInsight-instans som pekar på den aktuella lagringsplatsen. Den nya instansen skapas med alla befintliga data.

* Kopiera den `hbase` mappen till en annan Azure Storage blob-behållare eller plats för Data Lake Store och sedan starta ett nytt kluster med dessa data. Azure Storage, Använd [AzCopy](../../storage/common/storage-use-azcopy.md), och för Data Lake Store [AdlCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md).

## <a name="export-then-import"></a>Exportera sedan importera

På HDInsight-kluster källa verktyget Export (ingår i HBase) att exportera data från en källtabell till ansluten standardlagring. Du kan sedan kopiera den exportera mappen till lagringsplatsen för mål och kör verktyget Import på HDInsight målklustret.

Exportera en tabell, första SSH till huvudnod ditt HDInsight-kluster för källan och kör sedan följande `hbase` kommando:

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>"

Du importerar en tabell SSH till huvudnod ditt mål HDInsight-kluster och kör sedan följande `hbase` kommando:

    hbase org.apache.hadoop.hbase.mapreduce.Import "<tableName>" "/<path>/<to>/<export>"

Ange fullständig exportsökvägen till lagringsplatsen standard eller något av alternativen direktansluten lagring. Till exempel i Azure Storage:

    wasbs://<containername>@<accountname>.blob.core.windows.net/<path>

I Azure Data Lake Store är syntaxen:

    adl://<accountName>.azuredatalakestore.net:443/<path>

Den här metoden innebär tabell nivå granularitet. Du kan också ange ett datumintervall för rader ska tas med, så att du kan utföra processen inkrementellt. Varje datum är i millisekunder sedan Unix epok.

    hbase org.apache.hadoop.hbase.mapreduce.Export "<tableName>" "/<path>/<to>/<export>" <numberOfVersions> <startTimeInMS> <endTimeInMS>

Observera att du måste ange antal versioner av varje rad att exportera. För att inkludera alla versioner i datumintervall, ange `<numberOfVersions>` till ett större värde än högsta möjliga rad-versioner, till exempel 100000.

## <a name="copy-tables"></a>Kopiera tabeller

Verktyget CopyTable kopierar data från en källtabell, rad för rad i en befintlig måltabell med samma schema som källa. Måltabellen kan vara på samma kluster eller ett annat HBase-kluster.

Att använda CopyTable inom ett kluster SSH till huvudnod ditt HDInsight-kluster för källan och kör den här `hbase` kommando:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> <srcTableName>

Om du vill använda CopyTable för att kopiera till en tabell i ett annat kluster, lägger du till den `peer` växel med mål klusteradress:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable --new.name=<destTableName> --peer.adr=<destinationAddress> <srcTableName>

Måladress består av följande tre delar:

    <destinationAddress> = <ZooKeeperQuorum>:<Port>:<ZnodeParent>

* `<ZooKeeperQuorum>`är en kommaavgränsad lista över ZooKeeper-noder, till exempel:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net

* `<Port>`på HDInsight standard 2181, och `<ZnodeParent>` är `/hbase-unsecure`, så det fullständiga `<destinationAddress>` skulle vara:

    zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net:2181:/hbase-unsecure

Se [manuellt samla in listan över kvorum ZooKeeper](#manually-collect-the-zookeeper-quorum-list) i den här artikeln för information om hur du hämtar värdena för HDInsight-kluster.

Verktyget CopyTable stöder också parametrar för att ange tidsintervall rader att kopiera och ange del av kolumnserier i en tabell för att kopiera. Om du vill se en fullständig lista över parametrar som stöds av CopyTable kör CopyTable utan några parametrar:

    hbase org.apache.hadoop.hbase.mapreduce.CopyTable

CopyTable söker igenom hela tabellen källinnehållet som ska kopieras över till måltabellen. Detta kan minska prestandan för din HBase-kluster medan CopyTable körs.

> [!NOTE]
> För att automatisera kopiering av data mellan tabeller, finns det `hdi_copy_table.sh` skript i den [verktyg för Azure HBase webbplatsuppgradering](https://github.com/Azure/hbase-utils/tree/master/replication) databasen på GitHub.

### <a name="manually-collect-the-zookeeper-quorum-list"></a>Samla in ZooKeeper kvorum listan manuellt

När båda HDInsight-kluster finns i samma virtuella nätverk, som beskrivs ovan, är interna värdnamnsmatchning automatisk. Om du vill använda CopyTable för HDInsight-kluster i två separata virtuella nätverk som är anslutna via en VPN-Gateway, behöver du ange värden IP-adresser Zookeeper-noder i kvorum.

Kör kommandot curl för att hämta kvorum värdnamn:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/configurations?type=hbase-site&tag=TOPOLOGY_RESOLVED" | grep "hbase.zookeeper.quorum"

Curl-kommando hämtar ett JSON-dokument med HBase konfigurationsinformation och grep-kommando returnerar bara posten ”hbase.zookeeper.quorum”, till exempel:

    "hbase.zookeeper.quorum" : "zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk4-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net,zk3-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net"

Värdet för kvorum värden namn är hela strängen till höger om kolon.

Använd kommandot curl för varje värd i listan ovan om du vill hämta IP-adresserna för dessa värdar:

    curl -u admin:<password> -X GET -H "X-Requested-By: ambari" "https://<clusterName>.azurehdinsight.net/api/v1/clusters/<clusterName>/hosts/<zookeeperHostFullName>" | grep "ip"

I det här kommandot curl, `<zookeeperHostFullName>` är det fullständiga DNS-namnet för en ZooKeeper-värden, till exempel exemplet `zk0-hdizc2.54o2oqawzlwevlfxgay2500xtg.dx.internal.cloudapp.net`. Utdata från kommandot innehåller IP-adressen för den angivna värden, till exempel:

    100    "ip" : "10.0.0.9",

När du samlar in IP-adresser för alla ZooKeeper-noder i ditt kvorum återskapa måladressen:

    <destinationAddress>  = <Host_1_IP>,<Host_2_IP>,<Host_3_IP>:<Port>:<ZnodeParent>

I vårt exempel:

    <destinationAddress> = 10.0.0.9,10.0.0.8,10.0.0.12:2181:/hbase-unsecure

## <a name="snapshots"></a>Ögonblicksbilder

Ögonblicksbilder kan du ta point-in-time säkerhetskopiering av data i HBase-datalagret. Ögonblicksbilder har minimal kostnader och slutföra i sekunder, eftersom en ögonblicksbild åtgärd effektivt en metadata-åtgärd som avbildar namnen på alla filer i lagring på det ögonblicket. När en ögonblicksbild kopieras inga faktiska data. Ögonblicksbilder är beroende av ändras arten av de data som lagras i HDFS, där uppdateringar, borttagningar och infogningar alla visas i form av nya data. Du kan återställa (*klona*) en ögonblicksbild på samma kluster eller exportera en ögonblicksbild till ett annat kluster.

Att skapa en ögonblicksbild, SSH i till huvudnod i HDInsight HBase-kluster och starta den `hbase` shell:

    hbase shell

Använd kommandot ögonblicksbild med namnen i tabellen och den här ögonblicksbilden i hbase-gränssnittet:

    snapshot '<tableName>', '<snapshotName>'

Så här återställer du en ögonblicksbild av namnet i den `hbase` shell, först inaktivera tabellen, och sedan återställa ögonblicksbilden och återaktivera tabellen:

    disable '<tableName>'
    restore_snapshot '<snapshotName>'
    enable '<tableName>'

Om du vill återställa en ögonblicksbild till en ny tabell, använder du clone_snapshot:

    clone_snapshot '<snapshotName>', '<newTableName>'

Om du vill exportera en ögonblicksbild till HDFS för användning av ett annat kluster, först skapa en ögonblicksbild som beskrivits ovan och sedan använda verktyget ExportSnapshot. Köra verktyget från SSH-sessionen till huvudnod, inte inom den `hbase` shell:

     hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot <snapshotName> -copy-to <hdfsHBaseLocation>

Den `<hdfsHBaseLocation>` kan vara någon av lagringsplatser tillgängliga för källklustret och måste peka på hbase-mappen som används av din målklustret. Om du har en sekundär Azure Storage-konto som är kopplade till källklustret och det kontot ger tillgång till den behållare som används av standardlagring av målklustret, kan du använda det här kommandot:

    hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshot 'Snapshot1' -copy-to 'wasbs://secondcluster@myaccount.blob.core.windows.net/hbase'

När ögonblicksbilden exporteras kommandot SSH till huvudnod målklustret och återställning ögonblicksbilden med hjälp av restore_snapshot som beskrivits tidigare.

Ögonblicksbilder ger en fullständig säkerhetskopiering av en tabell vid tidpunkten för den `snapshot` kommando. Ögonblicksbilder ger inte möjlighet att utföra inkrementell ögonblicksbilder av windows tid och inte heller att ange delmängder av kolumner familjer ska ingå i ögonblicksbilden.

## <a name="replication"></a>Replikering

HBase-replikering skickar automatiskt transaktioner från ett kluster som datakällan till ett mål-kluster med hjälp av en asynkron metod med minimal belastning på källklustret. I HDInsight, du kan konfigurera replikering mellan kluster där:

* Käll- och -kluster finns i samma virtuella nätverk.
* Källa och mål-kluster finns i olika virtuella nätverk som är anslutna via en VPN-gateway, men båda klustren finns i samma geografiska plats.
* Klustret för källa och mål-kluster finns i olika virtuella nätverk som är anslutna via en VPN-gateway och varje kluster finns i en annan geografisk plats.

De allmänna stegen för att konfigurera replikeringen är:

1. Skapa tabellerna och fylla i data på källklustret.
2. Skapa tomma måltabell med källan tabellschemat på målklustret.
3. Registrera målklustret som en peer i käll-klustret.
4. Aktivera replikering på önskad källtabellerna.
5. Kopiera befintliga data från källtabellerna till mål-tabeller.
6. Replikering kopierar automatiskt nya dataändringar till källtabellerna i mål-tabeller.

Om du vill aktivera replikering på HDInsight gälla en skriptåtgärd körs källa HDInsight-kluster. En genomgång för att aktivera replikering i klustret eller att experimentera med replikering på exemplet kluster som skapas i virtuella nätverk med hjälp av Azure Resource Manager-mallar finns [konfigurera HBase-replikering](apache-hbase-replication.md). Artikeln innehåller också anvisningar för att aktivera replikering av Phoenix metadata.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera HBase-replikering](apache-hbase-replication.md)
