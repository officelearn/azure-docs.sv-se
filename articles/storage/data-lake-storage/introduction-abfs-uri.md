---
title: Använd Azure Data Lake lagring Gen2 Förhandsgranska URI
description: Använd Azure Data Lake lagring Gen2 Förhandsgranska URI
services: storage
keywords: ''
author: jamesbak
ms.topic: article
ms.author: jamesbak
manager: jahogg
ms.date: 06/27/2018
ms.service: storage
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 75edf6dc7382a8a2ece7c25edd09aeacfe1c5189
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060067"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Använda Azure Data Lake lagring Gen2 URI

Den [Hadoop Filesystem](http://www.aosabook.org/en/hdfs.html) drivrutin som är kompatibel med Azure Data Lake lagring Gen2 förhandsgranskning är känd via dess schema identifierare `abfs` (Azure Blob-filsystem). Konsekvent med andra Hadoop Filesystem drivrutiner, ABFS drivrutinen använder en URI-format för att adressera filer och kataloger i ett Data Lake lagring Gen2 kompatibla konto.

## <a name="uri-syntax"></a>URI-syntax

URI-syntax för Data Lake lagring Gen2 beror på om ditt lagringskonto har konfigurerats till att har Data Lake lagring Gen2 som standard-filsystem.

Om Data Lake lagring Gen2 kan kontot som du vill adress anges som standardfilsystem under kontoskapande av är shorthand URI-syntax:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Sökvägen**: ett snedstreck avgränsade (`/`) representation av katalogstrukturen.

2. **Filnamn**: namnet på enskilda filer.

Om kontot Data Lake lagring Gen2 kan du inte vill att adressen *inte* standardfilsystemet URI-syntax är:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.widows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Schemat identifierare**: den `abfs` protokollet används som schemat identifierare. Du har möjlighet att ansluta med eller utan en secure socket layer (SSL)-anslutning. Använd `abfss` att ansluta till en secure socket layer-anslutning.

2. **Filsystem**: den överordnade platsen som innehåller de filer och mappar. Detta är samma som behållare i Azure Storage BLOB-tjänsten.

3. **Kontonamn**: namnet på ditt lagringskonto under skapandet.

4. **Sökvägar**: ett snedstreck avgränsade (`/`) representation av katalogstrukturen.

5. **Filnamnet**: namnet på enskilda filer. Den här parametern är valfri om du adressering en katalog.

## <a name="next-steps"></a>Nästa steg

- [Använda Azure Data Lake lagring Gen2 med Azure HDInsight-kluster](use-hdi-cluster.md)