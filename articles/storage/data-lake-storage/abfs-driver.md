---
title: Azure Blob-filsystem-drivrutin för Azure Data Lake Storage Gen2 förhandsversion
description: Drivrutinen ABFS Hadoop-filsystem
services: storage
keywords: ''
author: jamesbak
ms.topic: article
ms.author: jamesbak
ms.date: 06/27/2018
ms.service: storage
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 8be6df5f4098b8a97e41c73edc5664799fd3edbe
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39520827"
---
# <a name="the-azure-blob-filesystem-driver-abfs-a-dedicated-azure-storage-driver-for-hadoop"></a>Azure Blob-filsystem-drivrutinen (ABFS): en dedikerad Azure Storage-drivrutin för Hadoop

En av de primära åtkomst metoderna för data i Azure Data Lake Storage Gen2 förhandsversion är den [Hadoop FileSystem](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/index.html). Azure Data Lake Storage Gen2 har en associerad drivrutin, Azure Blob-filsystem drivrutinen eller `ABFS`. ABFS är en del av Apache Hadoop och som ingår i många av de kommersiella Hadoop-distributionerna. Med den här drivrutinen kan många program och ramverk kan komma åt data i Data Lake Storage Gen2 utan någon kod som uttryckligen refererar till en tjänst för Data Lake Storage Gen2.

## <a name="prior-capability-the-windows-azure-storage-blob-driver"></a>Tidigare funktionen: Windows Azure Storage Blob-drivrutinen

Windows Azure Storage Blob-drivrutinen eller [WASB-drivrutinen](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) anges det ursprungliga stödet för Azure Storage-Blobbar. Den här drivrutinen utföra uppgiften att mappningen filsystemet semantik (som krävs av Hadoop FileSystem-gränssnitt) som objektets lagra style-gränssnitt som visas av Azure Blob Storage. Den här drivrutinen fortsätter att stödja den här modellen ger högpresterande åtkomst till data som lagras i Blobbar, men innehåller en betydande mängd kod som utför den här mappningen, vilket gör det svårt att underhålla. Dessutom kan vissa åtgärder som [FileSystem.rename()](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_renamePath_src_Path_d) och [FileSystem.delete()](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_deletePath_p_boolean_recursive) vid tillämpning kataloger kräver drivrutinen att utföra ett stort antal åtgärder (på grund av brist för objekt-butiker support för kataloger) vilket ofta kan leda till försämrade prestanda. Den nya tjänsten Azure Data Lake Storage har utformats för att övervinna inneboende brister i WASB.

## <a name="the-azure-blob-file-system-driver"></a>Azure Blob-filsystem-drivrutinen

Den [Azure Data Lake Storage REST-gränssnittet](https://docs.microsoft.com/en-us/rest/api/storageservices/data-lake-storage-gen2) är utformad för att stödja filsystemen via Azure Blob Storage. Med hänsyn till att Hadoop FileSystem är också utformad för att stödja samma semantik finns det inga krav på en komplex mappning i drivrutinen. Därför är drivrutinen Azure Blob-filsystem (eller ABFS) en är antalet klient-shim för REST API.

Det finns dock vissa funktioner som drivrutinen måste fortfarande utföra:

### <a name="uri-scheme-to-reference-data"></a>URI-schemat till referensdata

Konsekvent med andra filsystem-implementeringar i Hadoop, ABFS drivrutinen definierar sin egen URI-schema så att resurser (kataloger och filer) kan åtgärdas tydligt. URI-schemat dokumenteras i [Använd Azure Data Lake Storage Gen2 URI](./introduction-abfs-uri.md). Strukturen för URI: N är: `abfs[s]://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name>`

Med hjälp av ovanstående URI-format, kan standard Hadoop-verktyg och ramverk användas för att referera till dessa resurser:

```bash
hdfs dfs -mkdir -p abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data 
hdfs dfs -put flight_delays.csv abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data/ 
```

Internt ABFS drivrutinen översätter resurserna som anges i URI: N till filer och kataloger och gör anrop till Azure Data Lake Storage REST API med dessa referenser.

### <a name="authentication"></a>Autentisering

ABFS-drivrutinen stöder för närvarande autentisering med delad nyckel så att Hadoop-program på ett säkert sätt kan komma åt resurser som ingår i Data Lake Storage Gen2. Nyckeln krypteras och lagras i Hadoop-konfiguration.

### <a name="configuration"></a>Konfiguration

All konfiguration för drivrutinen ABFS lagras i den <code>core-site.xml</code> konfigurationsfilen. På Hadoop-distributioner med [Ambari](http://ambari.apache.org/), konfigurationen kan också hanteras med webbportal eller Ambari REST API.

Detaljerad information om transaktioner för alla konfigurationer som stöds anges i den [officiella Hadoop-dokumentation](http://hadoop.apache.org/docs/current/hadoop-azure/index.html).

### <a name="hadoop-documentation"></a>Hadoop-dokumentation

Drivrutinen ABFS fullständigt dokumenterade i den [officiella Hadoop-dokumentation](http://hadoop.apache.org/docs/current/hadoop-azure/index.html)

## <a name="next-steps"></a>Nästa steg

- [Installera HDInsight-kluster](./quickstart-create-connect-hdi-cluster.md)
- [Skapa ett Azure Databricks-kluster](./quickstart-create-databricks-account.md)
- [Använda Azure Data Lake Storage Gen2 URI](./introduction-abfs-uri.md)
