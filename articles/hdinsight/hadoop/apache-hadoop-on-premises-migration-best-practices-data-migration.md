---
title: 'Datamigrering: Lokal Apache Hadoop till Azure HDInsight'
description: Lär dig metodtips för datamigrering för att migrera lokala Hadoop-kluster till Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: 41112359408497d84243ed9bb06f396acf008dc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74666009"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---data-migration-best-practices"></a>Migrera lokala Apache Hadoop-kluster till Azure HDInsight – metodtips för datamigrering

Den här artikeln innehåller rekommendationer för datamigrering till Azure HDInsight. Det är en del av en serie som innehåller metodtips för att hjälpa till med att migrera lokala Apache Hadoop-system till Azure HDInsight.

## <a name="migrate-on-premises-data-to-azure"></a>Migrera lokala data till Azure

Det finns två huvudalternativ för att migrera data från lokalt till Azure-miljö:

* Överföra data via nätverk med TLS
    * Över internet – Du kan överföra data till Azure-lagring via en vanlig internetanslutning med något av flera verktyg som: Azure Storage Explorer, AzCopy, Azure Powershell och Azure CLI. Mer information finns i [Flytta data till och från Azure Storage](../../storage/common/storage-moving-data.md).

    * Express Route - ExpressRoute är en Azure-tjänst som låter dig skapa privata anslutningar mellan Microsoft-datacenter och infrastruktur som finns i dina lokaler eller i en samlokaliseringsfunktion. ExpressRoute-anslutningar går inte över det offentliga Internet och erbjuder högre säkerhet, tillförlitlighet och hastigheter med lägre fördröjningar än vanliga anslutningar via Internet. Mer information finns i [Skapa och ändra en ExpressRoute-krets](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).

    * Data Box online dataöverföring - Data Box Edge och Data Box Gateway är online dataöverföring produkter som fungerar som nätverkslagring gateways för att hantera data mellan din webbplats och Azure. Data Box Edge, en lokal nätverksenhet, överför data till och från Azure och använder artificiell intelligens (AI)-aktiverad kantberäkning för att bearbeta data. Data Box Gateway är en virtuell installation med lagringsgatewayfunktioner. Mer information finns i [Azure Data Box Documentation - Online Transfer](https://docs.microsoft.com/azure/databox-online/).

* Leverans av data offline

    Data Box offline dataöverföring - Data Box, Data Box Disk och Data Box Tunga enheter hjälper dig att överföra stora mängder data till Azure när nätverket inte är ett alternativ. Dessa offline dataöverföringsenheter levereras mellan din organisation och Azure-datacentret. De använder AES-kryptering för att skydda dina data under överföring, och de genomgår en grundlig saniseringsprocess efter uppladdning för att ta bort dina data från enheten. Mer information om offlineöverföringsenheterna i Data Box finns i [Azure Data Box Documentation - Offline Transfer](https://docs.microsoft.com/azure/databox/). Mer information om migrering av Hadoop-kluster finns i [Använda Azure Data Box för att migrera från ett lokalt HDFS-arkiv till Azure Storage](../../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md).

Följande tabell har ungefärlig varaktighet för dataöverföring baserat på datavolymen och nätverksbandbredden. Använd en dataruta om datamigreringen förväntas ta mer än tre veckor.

|Datatr|Bandbredd för nätverk||||
|---|---|---|---|---|
|| **45 Mbit/s (T3)**|**100 Mbps**|**1 Gbit/s**|**10 Gbit/s**|
|1 TB|2 dagar|1 dag| 2 timmar|14 minuter|
|10 TB|22 dagar|10 dagar|1 dag|2 timmar|
|35 TB|76 dagar|34 dagar|3 dagar|8 timmar|
|80 TB|173 dagar|78 dagar|8 dagar|19 timmar|
|100 TB|216 dagar|97 dagar|10 dagar|1 dag|
|200 TB|1 år|194 dagar|19 dagar|2 dagar|
|500 TB|3 år|1 år|49 dagar|5 dagar|
|1 PB (pb)|6 år|3 år|97 dagar|10 dagar|
|2 PB (PB)|12 år|5 år|194 dagar|19 dagar|

Verktyg som är inbyggda i Azure, till exempel Apache Hadoop DistCp, Azure Data Factory och AzureCp, kan användas för att överföra data över nätverket. Verktyget WANDisco från tredje part kan också användas för samma ändamål. Apache Kafka Mirrormaker och Apache Sqoop kan användas för pågående dataöverföring från lokala till Azure-lagringssystem.

## <a name="performance-considerations-when-using-apache-hadoop-distcp"></a>Prestandaöverväganden när du använder Apache Hadoop DistCp

DistCp är ett Apache-projekt som använder ett MapReduce Map-jobb för att överföra data, hantera fel och återställa från dessa fel. Den tilldelar en lista över källfiler till varje kartuppgift. Kartuppgiften kopierar sedan alla tilldelade filer till målet. Det finns flera tekniker kan förbättra prestanda distCp.

### <a name="increase-the-number-of-mappers"></a>Öka antalet mappers

DistCp försöker skapa kartuppgifter så att var och en kopierar ungefär samma antal byte. Som standard använder DistCp-jobb 20 mappers. Om du använder fler Mappers för Distcp (med parametern 'm' på kommandoraden) ökar parallellismen under dataöverföringsprocessen och minskar dataöverföringens längd. Det finns dock två saker att tänka på samtidigt som antalet Mappers ökar:

* DistCp lägsta granularitet är en enda fil. Ange ett antal Mappers mer än antalet källfiler hjälper inte och kommer att slösa de tillgängliga klusterresurser.

* Tänk dig det tillgängliga Yarn-minnet i klustret för att fastställa antalet Mappers. Varje kartuppgift startas som en Yarn-behållare. Om du antar att inga andra tunga arbetsbelastningar körs i klustret kan antalet Mappers bestämmas \* av följande formel: m = (antal arbetsnoder YARN-minne för varje arbetsnod) / YARN-behållarstorlek. Men om andra program använder minne väljer du att bara använda en del av YARN-minnet för DistCp-jobb.

### <a name="use-more-than-one-distcp-job"></a>Använda mer än ett DistCp-jobb

När storleken på den datauppsättning som ska flyttas är större än 1 TB använder du mer än ett DistCp-jobb. Om du använder mer än ett jobb begränsas effekten av fel. Om något jobb misslyckas behöver du bara starta om det specifika jobbet i stället för alla jobb.

### <a name="consider-splitting-files"></a>Överväg att dela filer

Om det finns ett litet antal stora filer, sedan överväga att dela upp dem i 256 MB filsegment för att få mer potentiell samtidighet med fler Mappers.

### <a name="use-the-strategy-command-line-parameter"></a>Använd kommandoradsparametern "strategi"

Överväg `strategy = dynamic` att använda parametern på kommandoraden. Parameterns standardvärde `strategy` är `uniform size`, i vilket fall varje karta kopierar ungefär samma antal byte. När den här `dynamic`parametern ändras till delas listfilen upp i flera "chunk-files". Antalet segmentfiler är en multipel av antalet kartor. Varje kartuppgift tilldelas en av segmentfilerna. När alla sökvägar i ett segment har bearbetats tas det aktuella segmentet bort och ett nytt segment hämtas. Processen fortsätter tills inga fler segment är tillgängliga. Den här "dynamiska" metoden gör att snabbare kartuppgifter kan förbruka fler sökvägar än långsammare, vilket påskyndar DistCp-jobbet totalt sett.

### <a name="increase-the-number-of-threads"></a>Öka antalet trådar

Se om `-numListstatusThreads` det förbättrar prestanda genom att öka parametern. Den här parametern styr antalet trådar som ska användas för att skapa fillistning och 40 är det maximala värdet.

### <a name="use-the-output-committer-algorithm"></a>Använd algoritmen för utdatas committer

Se om du `-Dmapreduce.fileoutputcommitter.algorithm.version=2` överför parametern förbättrar DistCp-prestanda. Denna utdata committer algoritm har optimeringar runt att skriva utdatafiler till målet. Följande kommando är ett exempel som visar användningen av olika parametrar:

```bash
hadoop distcp -Dmapreduce.fileoutputcommitter.algorithm.version=2 -numListstatusThreads 30 -m 100 -strategy dynamic hdfs://nn1:8020/foo/bar wasb://<container_name>@<storage_account_name>.blob.core.windows.net/foo/
```

## <a name="metadata-migration"></a>Migrering av metadata

### <a name="apache-hive"></a>Apache Hive

Hive-metabutiken kan migreras antingen med hjälp av skripten eller med hjälp av DB Replication.

#### <a name="hive-metastore-migration-using-scripts"></a>Hive metastore migrering med skript

1. Generera Hive-DDLs från lokala Hive-metabutiken. Det här steget kan göras med hjälp av ett [omslagspappersskript](https://github.com/hdinsight/hdinsight.github.io/blob/master/hive/hive-export-import-metastore.md).
1. Redigera den genererade DDL för att ersätta HDFS url med WASB/ADLS/ABFS webbadresser.
1. Kör den uppdaterade DDL på metabutiken från HDInsight-klustret.
1. Kontrollera att Hive-metastore-versionen är kompatibel mellan lokalt och molnet.

#### <a name="hive-metastore-migration-using-db-replication"></a>Hive metastore migrering med hjälp av DB replikering

- Konfigurera Databasreplikering mellan lokala Hive-metabutik DB och HDInsight metastore DB.
- Använd "Hive MetaTool" för att ersätta HDFS url med WASB/ADLS/ABFS-url:er, till exempel:

    ```bash
    ./hive --service metatool -updateLocation hdfs://nn1:8020/ wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
    ```

### <a name="apache-ranger"></a>Apache Ranger

- Exportera lokala Ranger-principer till XML-filer.
- Omvandla på lokala HDFS-baserade sökvägar till WASB/ADLS med ett verktyg som XSLT.
- Importera principerna till Ranger som körs på HDInsight.

## <a name="next-steps"></a>Nästa steg

Läs nästa artikel i den här serien:

- [Säkerhets- och DevOps-metodtips för lokal till Azure HDInsight Hadoop-migrering](apache-hadoop-on-premises-migration-best-practices-security-devops.md)
