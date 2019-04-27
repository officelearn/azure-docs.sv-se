---
title: Använda Azure Data Lake Storage Gen2 URI
description: Använda Azure Data Lake Storage Gen2 URI
services: storage
author: jamesbak
ms.topic: conceptual
ms.author: jamesbak
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: e4f148ac6f66cfbd7d89eff3623418197c2e0a33
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60708489"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Använda Azure Data Lake Storage Gen2 URI

Den [Hadoop Filesystem](https://www.aosabook.org/en/hdfs.html) drivrutin som är kompatibel med Azure Data Lake Storage Gen2 är känd av dess schema-identifierare `abfs` (Azure Blob-filsystem). Konsekvent med andra Hadoop Filesystem-drivrutiner, ABFS drivrutinen använder en URI-format för att adressera filer och kataloger i ett kompatibelt Gen2 för Data Lake Storage-konto.

## <a name="uri-syntax"></a>URI-syntax

URI-syntax för Data Lake Storage Gen2 beror på om ditt lagringskonto har konfigurerats att har Data Lake Storage Gen2 som standardfilsystem.

Om kontot Data Lake Storage Gen2 kan du inte vill att adressen **är inte** ange som standardfilsystemet när kontot skapas snabbformat URI-syntax är:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Schema-ID**: Den `abfs` -protokollet används som identifierare för schemat. Du har möjlighet att ansluta med eller utan en secure socket layer (SSL)-anslutning. Använd `abfss` kan ansluta till en secure socket layer-anslutning.

2. **Filsystem**: Den överordnade platsen som innehåller de filer och mappar. Det här är samma som behållare i Azure Storage BLOB-tjänsten.

3. **Kontonamn**: Namnet på ditt lagringskonto när du skapar.

4. **Sökvägar**: Ett snedstreck avgränsade (`/`) representation av katalogstrukturen.

5. **Filnamnet**: Namnet på den enskilda filen. Den här parametern är valfri om du åtgärdar en katalog.

Men om det konto som du vill adress har angetts som standardfilsystemet när kontot skapas, är snabbformat URI-syntax:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Sökväg**: Ett snedstreck avgränsade (`/`) representation av katalogstrukturen.

2. **Filnamn**: Namnet på den enskilda filen.


## <a name="next-steps"></a>Nästa steg

- [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
