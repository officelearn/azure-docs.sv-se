---
title: Apache Hadoop-arkitektur - Azure HDInsight
description: Beskriver Apache Hadoop-lagring och bearbetning på Azure HDInsight-kluster.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/07/2020
ms.openlocfilehash: 3feacd94558ba275c81469827993aef106ae633c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162216"
---
# <a name="apache-hadoop-architecture-in-hdinsight"></a>Apache Hadoop-arkitektur i HDInsight

[Apache Hadoop](https://hadoop.apache.org/) innehåller två kärnkomponenter: [Apache Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) som tillhandahåller lagring och [Apache Hadoop Ännu en resursförhandlare (YARN)](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) som tillhandahåller bearbetning. Med lagrings- och bearbetningsfunktioner kan ett kluster köra [MapReduce-program](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) för att utföra önskad databehandling.

> [!NOTE]  
> En HDFS distribueras vanligtvis inte i HDInsight-klustret för att tillhandahålla lagring. I stället används ett HDFS-kompatibelt gränssnittslager av Hadoop-komponenter. Den faktiska lagringskapaciteten tillhandahålls av antingen Azure Storage eller Azure Data Lake Storage. För Hadoop körs MapReduce-jobb som körs på HDInsight-klustret som om en HDFS var närvarande och kräver därför inga ändringar för att stödja deras lagringsbehov. I Hadoop på HDInsight läggs lagring ut, men YARN-bearbetning förblir en kärnkomponent. Mer information finns i [Introduktion till Azure HDInsight](hadoop/apache-hadoop-introduction.md).

Den här artikeln introducerar YARN och hur det samordnar genomförandet av applikationer på HDInsight.

## <a name="apache-hadoop-yarn-basics"></a>Grunderna i Apache Hadoop YARN

YARN reglerar och orkestrerar databehandling i Hadoop. YARN har två kärntjänster som körs som processer på noder i klustret:

* ResourceManager
* NodeManager

ResourceManager beviljar klusterberäkningsresurser till program som MapReduce-jobb. ResourceManager beviljar dessa resurser som behållare, där varje behållare består av en allokering av CPU-kärnor och RAM-minne. Om du kombinerade alla tillgängliga resurser i ett kluster och sedan distribuerade kärnorna och minnet i block, är varje resursblock en behållare. Varje nod i klustret har en kapacitet för ett visst antal behållare, därför har klustret en fast gräns för antalet tillgängliga behållare. Tilldelningen av resurser i en behållare kan konfigureras.

När ett MapReduce-program körs i ett kluster tillhandahåller ResourceManager programmet de behållare som ska köras. ResourceManager spårar status för program som körs, tillgänglig klusterkapacitet och spårar program när de slutför och släpper sina resurser.

ResourceManager kör också en webbserverprocess som tillhandahåller ett webbanvändargränssnitt för att övervaka programmens status.

När en användare skickar ett MapReduce-program för att köras i klustret skickas programmet till ResourceManager. ResourceManager allokerar i sin tur en behållare på tillgängliga NodeManager-noder. NodeManager-noderna är där programmet faktiskt körs. Den första tilldelade behållaren kör ett särskilt program som kallas ApplicationMaster. Denna ApplicationMaster ansvarar för att förvärva resurser, i form av efterföljande behållare, som behövs för att köra den inskickade ansökan. ApplicationMaster undersöker stegen i programmet, till exempel kartstadiet och minskar stadiet, och faktorer i hur mycket data som behöver bearbetas. ApplicationMaster begär sedan (*förhandlar)* resurserna från ResourceManager för programmets räkning. ResourceManager beviljar i sin tur resurser från NodeManagers i klustret till ApplicationMaster som den ska använda för att köra programmet.

NodeManagers kör de aktiviteter som utgör programmet och rapporterar sedan deras status och status tillbaka till ApplicationMaster. ApplicationMaster i sin tur rapporterar status för programmet tillbaka till ResourceManager. ResourceManager returnerar alla resultat till klienten.

## <a name="yarn-on-hdinsight"></a>GARN på HDInsight

Alla HDInsight-klustertyper distribuerar YARN. ResourceManager distribueras för hög tillgänglighet med en primär och sekundär instans, som körs på de första och andra huvudnoderna i klustret respektive. Endast en instans av ResourceManager är aktiv åt gången. NodeManager-instanserna körs över de tillgängliga arbetsnoderna i klustret.

![Apache YARN på Azure HDInsight](./media/hdinsight-hadoop-architecture/apache-yarn-on-hdinsight.png)

## <a name="soft-delete"></a>Mjuk borttagning

Om du vill ta bort en fil från ditt lagringskonto läser du:

### <a name="azure-storage"></a>Azure Storage

* [Mjuk borttagning för Azure Storage-blobar](../storage/blobs/storage-blob-soft-delete.md)
* [Undelete Blob](https://docs.microsoft.com/rest/api/storageservices/undelete-blob)

### <a name="azure-data-lake-storage-gen-1"></a>Azure Data Lake Storage Gen 1

[Återställ-AzDataLakeStoreDeletedItem](https://docs.microsoft.com/powershell/module/az.datalakestore/restore-azdatalakestoredeleteditem)

### <a name="azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2

[Kända problem med Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-known-issues.md)

## <a name="trash-purging"></a>Soprenning

Egenskapen `fs.trash.interval` från **HDFS** > **Advanced core-site** bör `0` ligga kvar på standardvärdet eftersom du inte bör lagra några data på det lokala filsystemet. Det här värdet påverkar inte fjärrlagringskonton(WASB, ADLS GEN1, ABFS)

## <a name="next-steps"></a>Nästa steg

* [Använda MapReduce i Apache Hadoop på HDInsight](hadoop/hdinsight-use-mapreduce.md)
* [Introduktion till Azure HDInsight](hadoop/apache-hadoop-introduction.md)
