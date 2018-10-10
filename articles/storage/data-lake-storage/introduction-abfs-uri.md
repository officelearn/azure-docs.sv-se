---
title: Använda Azure Data Lake Storage Gen2 förhandsversion URI
description: Använda Azure Data Lake Storage Gen2 förhandsversion URI
services: storage
keywords: ''
author: jamesbak
ms.topic: article
ms.author: jamesbak
ms.date: 06/27/2018
ms.service: storage
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 71c3343db592162bcfa7ec46a29e0d680ca83079
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900714"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Använda Azure Data Lake Storage Gen2 URI

Den [Hadoop Filesystem](http://www.aosabook.org/en/hdfs.html) drivrutin som är kompatibel med Azure Data Lake Storage Gen2 förhandsversion är känd av dess schema-identifierare `abfs` (Azure Blob-filsystem). Konsekvent med andra Hadoop Filesystem-drivrutiner, ABFS drivrutinen använder en URI-format för att adressera filer och kataloger i ett kompatibelt Gen2 för Data Lake Storage-konto.

## <a name="uri-syntax"></a>URI-syntax

URI-syntax för Data Lake Storage Gen2 beror på om ditt lagringskonto har konfigurerats att har Data Lake Storage Gen2 som standardfilsystem.

Om kontot Data Lake Storage Gen2 kan du inte vill att adressen **är inte** ange som standardfilsystemet när kontot skapas snabbformat URI-syntax är:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Schema-ID**: den `abfs` -protokollet används som identifierare för schemat. Du har möjlighet att ansluta med eller utan en secure socket layer (SSL)-anslutning. Använd `abfss` kan ansluta till en secure socket layer-anslutning.

2. **Filsystem**: den överordnade platsen som innehåller de filer och mappar. Det här är samma som behållare i Azure Storage BLOB-tjänsten.

3. **Kontonamn**: namnet på ditt lagringskonto när du skapar.

4. **Sökvägar**: ett snedstreck avgränsade (`/`) representation av katalogstrukturen.

5. **Filnamnet**: namnet på den enskilda filen. Den här parametern är valfri om du åtgärdar en katalog.

Men om det konto som du vill adress har angetts som standardfilsystemet när kontot skapas, är snabbformat URI-syntax:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Sökvägen**: ett snedstreck avgränsade (`/`) representation av katalogstrukturen.

2. **Filnamn**: namnet på den enskilda filen.


## <a name="next-steps"></a>Nästa steg

- [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](use-hdi-cluster.md)
