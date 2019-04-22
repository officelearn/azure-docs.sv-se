---
title: Migrera lokala Apache Hadoop-kluster till Azure HDInsight - Metodtips för migrering av data
description: Lär dig data migration Metodtips för att migrera lokala Hadoop-kluster till Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: hrasheed
ms.openlocfilehash: 02c7f53c090559ca0ada46ec90de3a44b0518a29
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683608"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---data-migration-best-practices"></a>Migrera lokala Apache Hadoop-kluster till Azure HDInsight - Metodtips för migrering av data

Den här artikeln ger rekommendationer för migrering av data till Azure HDInsight. Det är en del i en serie som ger bästa praxis för att hjälpa migrera lokala Apache Hadoop-system till Azure HDInsight.

## <a name="migrate-on-premises-data-to-azure"></a>Migrera lokala data till Azure

Det finns två huvudsakliga alternativ för att migrera data från en lokal plats till Azure-miljön:

1.  Överföra data över nätverket med TLS
    1. Via internet - kan du överföra data till Azure storage via en vanliga Internetanslutning med någon av flera verktyg som: Azure Storage Explorer, AzCopy, Azure Powershell och Azure CLI.  Se [flytta data till och från Azure Storage](../../storage/common/storage-moving-data.md) för mer information.
    2. Expressroute – ExpressRoute är en Azure-tjänst som låter dig skapa privata anslutningar mellan Microsofts datacenter och infrastruktur som finns lokalt eller i en delade miljö. ExpressRoute-anslutningar inte går via det offentliga Internet och är högre säkerhet, tillförlitlighet och hastighet med kortare svarstider än vanliga anslutningar via Internet. Mer information finns i [skapa och ändra en ExpressRoute-krets](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
    1. Data Box online dataöverföring – Data Box Edge och Data Box-Gateway är online-data transfer produkter som fungerar som nätverks-storage-gatewayer för att hantera data mellan webbplatsen och Azure. Data Box Edge, en lokal nätverksenhet, överför data till och från Azure och använder AI-aktiverad (artificiell intelligens) gränsdatabearbetning för att bearbeta data. Data Box Gateway är en virtuell installation med lagringsgatewayfunktioner. Mer information finns i [Azure Data Box-dokumentation – Online överföra](https://docs.microsoft.com/azure/databox-online/).
1.  Levererade data Offline
    1. Data rutan offline dataöverföring – Data Box Data Box-Disk, och Data Box tung enheter kan överföra stora mängder data till Azure när nätverket inte är ett alternativ. Dessa offlineenheter för dataöverföring skickas mellan din organisation och Azure-datacentret. De använder AES-kryptering för att skydda dina data i transit, och de genomgår en noggrann sanering efter uppladdning för att ta bort dina data från enheten. Mer information om Data Box offline överföring enheter finns i [Azure Data Box-dokumentation – Offline överföring](https://docs.microsoft.com/azure/databox/). Mer information om migrering av Hadoop-kluster finns i [Använd Azure Data Box för att migrera från en lokal HDFS-databas till Azure Storage](../../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md).

I följande tabell har ungefärliga data transfer varaktighet baserat på bandbredden som data volym och nätverk. Använd en Data box om migrering av data förväntas ta mer än tre veckor.

|**Mängd data**|**Nätverkets bandbredd**||||
|---|---|---|---|---|
|| **45 Mbps (T3)**|**100 Mbit/s**|**1 Gbit/s**|**10 Gbit/s**|
|1 TB|2 dagar|1 dag| 2 timmar|14 minuter|
|10 TB|22 dagar|10 dagar|1 dag|2 timmar|
|35 TB|76 dagar|34 dagar|3 dagar|8 timmar|
|80 TB|173 dagar|78 dagar|8 dagar|19 timmar|
|100 TB|216 dagar|97 dagar|10 dagar|1 dag|
|200 TB|1 år|194 dagar|19 dagar|2 dagar|
|500 TB|3 år|1 år|49 dagar|5 dagar|
|1 PB|6 år|3 år|97 dagar|10 dagar|
|2 PB|12 år|5 år|194 dagar|19 dagar|

Verktyg som är inbyggt i Azure, till exempel Apache Hadoop DistCp, Azure Data Factory och AzureCp, kan användas för att överföra data över nätverket. Verktyg från tredje part WANDisco kan också användas för samma ändamål. Apache Kafka Mirrormaker och Apache Sqoop kan användas för pågående dataöverföring från en lokal plats till Azure storage-system.


## <a name="performance-considerations-when-using-apache-hadoop-distcp"></a>Prestandaöverväganden när du använder Apache Hadoop DistCp


DistCp är ett Apache-projekt som använder en karta för MapReduce-jobb för att överföra data, hantera fel och återställa dessa fel. Det tilldelar en lista över källfiler varje aktivitet i kartan. Kartan aktiviteten kopierar sedan alla dess tilldelade filer till målplatsen. Det finns flera tekniker kan förbättra prestandan för DistCp.

### <a name="increase-the-number-of-mappers"></a>Öka antalet Mappningskomponenter

DistCp försöker skapa kartan uppgifter så att var och en kopierar ungefär samma antal byte. Som standard använder DistCp jobb 20 Mappningskomponenter. Med fler Mappningskomponenter för Distcp (med den är ”parametern på kommandoraden) ökar parallellitet under överföringen data och minskar längden på dataöverföringen. Men finns det två saker att tänka på när du ökar antalet Mappningskomponenter:

1. Distcps lägsta Granulariteten är en enskild fil. Ange ett antal Mappningskomponenter mer än antalet källfiler hjälper inte och är slöseri med tillgängliga resurser.
1. Överväg att det tillgängliga minnet Yarn på klustret för att avgöra hur många Mappningskomponenter. Varje aktivitet i kartan öppnas som en Yarn-behållare. Om vi antar att inga andra tunga arbetsbelastningar körs på klustret, antalet Mappningskomponenter kan fastställas genom följande formel: m = (antal arbetsnoder \* YARN minne för varje arbetsnod) / YARN behållarens storlek. Om andra program använder minne kan sedan välja att bara använda en del av minnet för YARN för DistCp jobb.

### <a name="use-more-than-one-distcp-job"></a>Använda mer än ett DistCp jobb

När storleken på datauppsättningen som ska flyttas är större än 1 TB, mer än en DistCp jobbet. Med hjälp av fler än en begränsar effekten av fel. Om alla jobb misslyckas kan behöva du bara starta om specifika jobbet i stället för alla jobb.

### <a name="consider-splitting-files"></a>Överväg att dela filer

Om det finns ett litet antal stora filer kan du överväga att dela upp dem i 256 MB filsegment att få mer potentiella samtidighet med fler Mappningskomponenter.

### <a name="use-the-strategy-command-line-parameter"></a>Använder du kommandoradsparametern 'strategi ”

Överväg att använda `strategy = dynamic` parametern på kommandoraden. Standardvärdet för den `strategy` parametern är `uniform size`, då varje kartan kopierar ungefär samma antal byte. När den här parametern ändras till `dynamic`, lista filen delas upp i flera ”segment-filer”. Antalet segment-filer som är en multipel av antalet maps. Varje aktivitet i kartan tilldelas en av segment-filerna. När alla sökvägar i ett segment bearbetas det aktuella segmentet tas bort och ett nytt segment förvärvas. Processen fortsätter tills inga fler segment är tillgängliga. ”Dynamiska” på så sätt kan snabbare kartan-uppgifter för att använda flera sökvägar från långsammare som, därför snabbare övergripande DistCp jobbet.

### <a name="increase-the-number-of-threads"></a>Öka antalet trådar

Om du ökar den `-numListstatusThreads` parametern förbättrar prestanda. Den här parametern styr antalet trådar som ska användas för att skapa filen lista och 40 är det maximala värdet.

### <a name="use-the-output-committer-algorithm"></a>Använd utdata committer algoritmen

Se om skicka parametern `-Dmapreduce.fileoutputcommitter.algorithm.version=2` förbättrar DistCp prestanda. Den här utdata committer algoritmen har optimeringar runt skriver utdatafilerna till målet. Följande kommando är ett exempel som visar användningen av olika parametrar:

```bash
hadoop distcp -Dmapreduce.fileoutputcommitter.algorithm.version=2 -numListstatusThreads 30 -m 100 -strategy dynamic hdfs://nn1:8020/foo/bar wasb://<container_name>@<storage_account_name>.blob.core.windows.net/foo/
```

## <a name="metadata-migration"></a>Metadata-migrering

### <a name="apache-hive"></a>Apache Hive

Hive-metaarkiv kan migreras med hjälp av skript eller med hjälp av DB-replikering.

#### <a name="hive-metastore-migration-using-scripts"></a>Hive-metaarkiv migrering med hjälp av skript

1. Skapa Hive-DDLs från på plats Hive-metaarkiv. Det här steget kan göras med hjälp av en [omslutning bash-skript](https://github.com/hdinsight/hdinsight.github.io/blob/master/hive/hive-export-import-metastore.md).
1. Redigera den genererade DDL för att ersätta HDFS url med WASB/ADLS/ABFS URL: er.
1. Kör den uppdaterade DDL på metaarkiv från HDInsight-kluster.
1. Se till att Hive-metaarkiv version är kompatibla mellan lokala platser och molnet.

#### <a name="hive-metastore-migration-using-db-replication"></a>Hive-metaarkiv migrering med hjälp av DB replikering

- Konfigurera databasreplikering mellan lokala Hive-metaarkiv DB och HDInsight metaarkiv DB.
- Använd den ”Hive MetaTool” för att ersätta HDFS url med WASB/ADLS/ABFS URL: er, till exempel:

```bash
./hive --service metatool -updateLocation hdfs://nn1:8020/ wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
```

### <a name="apache-ranger"></a>Apache Ranger

- Exportera lokala Ranger-principer till XML-filer.
- Omvandla lokalt specifika HDFS-baserade sökvägar till WASB/ADLS med ett verktyg som XSLT.
- Importera principer in Ranger som körs på HDInsight.

## <a name="next-steps"></a>Nästa steg

Läs nästa artikel i den här serien:

- [Metodtips för säkerhet och DevOps för lokalt till Azure HDInsight Hadoop-migrering](apache-hadoop-on-premises-migration-best-practices-security-devops.md)
