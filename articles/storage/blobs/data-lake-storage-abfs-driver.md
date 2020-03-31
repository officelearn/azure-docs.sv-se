---
title: Azure Blob-filsystemdrivrutinen för Azure Data Lake Storage Gen2
description: Drivrutinen för ABFS Hadoop-filsystem
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 3db039d39ef532ea51143dc9cbdb6bd5f29d6225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75970282"
---
# <a name="the-azure-blob-filesystem-driver-abfs-a-dedicated-azure-storage-driver-for-hadoop"></a>Azure Blob Filesystem driver (ABFS): En dedikerad Azure Storage-drivrutin för Hadoop

En av de primära åtkomstmetoderna för data i Azure Data Lake Storage Gen2 är via [Hadoop FileSystem](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/index.html). DataSjölagring Gen2 ger användare av Azure Blob Storage åtkomst till en `ABFS`ny drivrutin, Azure Blob File System-drivrutinen eller . ABFS är en del av Apache Hadoop och ingår i många av de kommersiella distributioner av Hadoop. Med den här drivrutinen kan många program och ramverk komma åt data i Azure Blob Storage utan att någon kod uttryckligen refererar till Data Lake Storage Gen2.

## <a name="prior-capability-the-windows-azure-storage-blob-driver"></a>Tidigare funktioner: Windows Azure Storage Blob-drivrutinen

Windows Azure Storage Blob-drivrutinen eller [WASB-drivrutinen](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) gav det ursprungliga stödet för Azure Blob Storage. Den här drivrutinen utförde den komplexa uppgiften att mappa filsystemsenmantik (som krävs av Hadoop FileSystem-gränssnittet) till den för gränssnittet för objektlagringsformat som exponeras av Azure Blob Storage. Den här drivrutinen fortsätter att stödja den här modellen, vilket ger hög prestandaåtkomst till data som lagras i blobbar, men innehåller en betydande mängd kod som utför den här mappningen, vilket gör det svårt att underhålla. Dessutom kräver vissa åtgärder som [FileSystem.rename()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_renamePath_src_Path_d) och [FileSystem.delete()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_deletePath_p_boolean_recursive) när de tillämpas på kataloger att drivrutinen utför ett stort antal åtgärder (på grund av objektlager brist på stöd för kataloger) vilket ofta leder till försämrad prestanda. ABFS-föraren var utformad för att övervinna de inneboende bristerna i WASB.

## <a name="the-azure-blob-file-system-driver"></a>Drivrutinen för Azure Blob File System

[Azure Data Lake Storage REST-gränssnittet](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) är utformat för att stödja filsystemssenmantik över Azure Blob Storage. Med tanke på att Hadoop FileSystem också är utformat för att stödja samma semantik finns det inget krav på en komplex mappning i drivrutinen. Azure Blob File System-drivrutinen (eller ABFS) är alltså bara ett klientshims för REST API.Thus, the Azure Blob File System driver (or ABFS) is a mere client shim for the REST API.

Det finns dock vissa funktioner som drivrutinen fortfarande måste utföra:

### <a name="uri-scheme-to-reference-data"></a>URI-schema för referensdata

I enlighet med andra FileSystem-implementeringar inom Hadoop definierar ABFS-drivrutinen sitt eget URI-schema så att resurser (kataloger och filer) kan hanteras tydligt. URI-schemat dokumenteras i [Använd Azure Data Lake Storage Gen2 URI](./data-lake-storage-introduction-abfs-uri.md). URI:ns struktur är:`abfs[s]://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name>`

Med hjälp av ovanstående URI-format kan standardverktyg och samarbetsramverk användas för att referera till dessa resurser:

```bash
hdfs dfs -mkdir -p abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data
hdfs dfs -put flight_delays.csv abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data/
```

Internt översätter ABFS-drivrutinen de resurser som anges i URI till filer och kataloger och anlöpar till AZURE Data Lake Storage REST API med dessa referenser.

### <a name="authentication"></a>Autentisering

ABFS-drivrutinen stöder två former av autentisering så att Hadoop-programmet kan komma åt resurser som finns i ett datasjölagringsgenm2-konto. Fullständig information om tillgängliga autentiseringsscheman finns i [säkerhetsguiden för Azure Storage](security-recommendations.md). De är:

- **Delad nyckel:** Detta ger användare åtkomst till alla resurser i kontot. Nyckeln krypteras och lagras i Hadoop-konfigurationen.

- **Token för Oauth-bärare i Azure Active Directory:** Azure AD-innehavartoken hämtas och uppdateras av drivrutinen med antingen slutanvändarens identitet eller ett konfigurerat tjänsthuvudnamn. Med den här autentiseringsmodellen auktoriseras all åtkomst per samtal med hjälp av identiteten som är associerad med den angivna token och utvärderas mot den tilldelade POSIX Access Control List (ACL).

   > [!NOTE]
   > Azure Data Lake Storage Gen2 stöder endast Azure AD v1.0-slutpunkter.

### <a name="configuration"></a>Konfiguration

All konfiguration för ABFS-drivrutinen lagras i konfigurationsfilen. <code>core-site.xml</code> På Hadoop distributioner featuring [Ambari](https://ambari.apache.org/), konfigurationen kan också hanteras med hjälp av webbportalen eller Ambari REST API.

Information om alla konfigurationsposter som stöds anges i den [officiella Hadoop-dokumentationen](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html).

### <a name="hadoop-documentation"></a>Hadoop-dokumentation

ABFS-föraren är fullständigt dokumenterad i [hadoopdokumentationen](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html)

## <a name="next-steps"></a>Nästa steg

- [Skapa ett Azure Databricks-kluster](./data-lake-storage-quickstart-create-databricks-account.md)
- [Använda Azure Data Lake Storage Gen2 URI](./data-lake-storage-introduction-abfs-uri.md)
