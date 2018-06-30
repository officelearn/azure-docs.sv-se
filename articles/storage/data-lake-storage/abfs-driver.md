---
title: Azure Blob-filsystem drivrutinen för Azure Data Lake lagring Gen2 Preview
description: Drivrutinens ABFS Hadoop-filsystem
services: storage
keywords: ''
author: jamesbak
manager: jahogg
ms.topic: article
ms.author: jamesbak
ms.date: 06/27/2018
ms.service: storage
ms.component: data-lake-storage-gen2
ms.openlocfilehash: e92c4efba29f1c40f6d4cb155974ca3a896796e5
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37114341"
---
# <a name="the-azure-blob-filesystem-driver-abfs-a-dedicated-azure-storage-driver-for-hadoop"></a>Drivrutinens Azure Blob-filsystem (ABFS): en dedikerad Azure Storage-drivrutin för Hadoop

En av de primära metoderna för data i Azure Data Lake lagring Gen2 Preview är den [Hadoop FileSystem](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/index.html). Azure Data Lake lagring Gen2 har en associerad drivrutin drivrutinens Azure Blob-filsystem eller `ABFS`. ABFS är en del av Apache Hadoop och ingår i många av de kommersiella Hadoop-distributioner. Med den här drivrutinen kan många program och ramverk kan komma åt data i Data Lake lagring Gen2 utan någon kod som uttryckligen refererar till Data Lake lagring Gen2-tjänst.

## <a name="prior-capability-the-windows-azure-storage-blob-driver"></a>Tidigare kapaciteten: Windows Azure Storage Blob-drivrutin

Windows Azure Storage Blob-drivrutin eller [WASB drivrutinen](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) ursprungliga stöd för Azure Storage BLOB. Den här drivrutinen utföra uppgiften att mappningen filsystemet semantik (som krävs av Hadoop FileSystem-gränssnitt) som objektet lagrar style-gränssnitt som exponeras av Azure Blob Storage. Den här drivrutinen fortsätter att stödja den här modellen ger hög prestanda tillgång till data som lagras i BLOB, men innehåller kod som utför den här mappningen, vilket gör det svårt att underhålla mycket. Dessutom kan vissa åtgärder som [FileSystem.rename()](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_renamePath_src_Path_d) och [FileSystem.delete()](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_deletePath_p_boolean_recursive) när det används kataloger kräver drivrutinen att utföra en mängd åtgärder (på grund av objektet lagrar saknas stöd för kataloger) vilket ofta kan leda till försämrade prestanda.

Om du vill lösa inbyggd design brister i WASB implementerades därför den nya Azure Data Lake Storage-tjänsten med stöd från den nya ABFS-drivrutinen.

## <a name="the-azure-blob-file-system-driver"></a>Drivrutinens Azure Blob-filsystem

Den [Azure Data Lake Storage REST-gränssnittet](https://docs.microsoft.com/en-us/rest/api/storageservices/data-lake-storage-gen2) är utformad för att stödja filsystemen över Azure Blob Storage. Med tanke på att Hadoop FileSystem är också designad att stöd för samma semantik krävs ingen för en komplex mappning i drivrutinen. Därför är drivrutinen Azure Blob-filsystem (eller ABFS) en enbart klienten shim för REST-API.

Det finns dock vissa funktioner som drivrutinen måste fortfarande utföra:

### <a name="uri-scheme-to-reference-data"></a>URI-schemat kan referera till data

Konsekvent med andra filsystem implementeringar inom Hadoop, drivrutinens ABFS definierar en egen URI-schema så att resurser (kataloger och filer) kan åtgärdas tydligt. URI-schemat dokumenteras i [Använd Azure Data Lake lagring Gen2 URI](./introduction-abfs-uri.md). Strukturen för URI: N är: `abfs[s]://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name>`

I formatet ovan URI, kan standard Hadoop-verktyg och ramverk användas för att hänvisa till dessa resurser:

```bash
hdfs dfs -mkdir -p abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data 
hdfs dfs -put flight_delays.csv abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data/ 
```

Internt ABFS drivrutinen översätter de resurser som har angetts i URI: N till filer och kataloger och gör anrop till Azure Data Lake Storage REST API med dessa referenser.

### <a name="authentication"></a>Autentisering

ABFS-drivrutinen stöder för närvarande autentisering med delad nyckel så att Hadoop-program på ett säkert sätt kan komma åt resurser som ingår i Data Lake lagring Gen2. Nyckeln krypteras och lagras i Hadoop-konfigurationen.

### <a name="configuration"></a>Konfiguration

All konfiguration för drivrutinens ABFS lagras i den <code>core-site.xml</code> konfigurationsfilen. På Hadoop-distributioner med [Ambari](http://ambari.apache.org/), konfigurationen kan också hanteras med hjälp av web-portalen eller Ambari REST API.

Information om poster för alla konfigurationer som stöds anges i den [officiella Hadoop-dokumentation](http://hadoop.apache.org/docs/current/hadoop-azure/index.html).

### <a name="hadoop-documentation"></a>Hadoop-dokumentation

Drivrutinens ABFS fullständigt dokumenterade i den [officiella Hadoop-dokumentation](http://hadoop.apache.org/docs/current/hadoop-azure/index.html)

## <a name="next-steps"></a>Nästa steg

- [Installera HDInsight-kluster](./quickstart-create-connect-hdi-cluster.md)
- [Skapa ett Azure Databricks-kluster](./quickstart-create-databricks-account.md)
- [Använda Azure Data Lake lagring Gen2 URI](./introduction-abfs-uri.md)
