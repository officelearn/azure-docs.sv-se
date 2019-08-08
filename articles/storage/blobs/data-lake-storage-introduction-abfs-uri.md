---
title: Använd Azure Data Lake Storage Gen2-URI
description: Använd Azure Data Lake Storage Gen2-URI
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: 04df30c2a97e865d23999df26768b38cb38be607
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855563"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Använd Azure Data Lake Storage Gen2-URI

[Hadoop-filsystem](https://www.aosabook.org/en/hdfs.html) -drivrutinen som är kompatibel med Azure Data Lake Storage Gen2 är känd av sitt schema `abfs` -ID (Azure Blob File System). ABFS-drivrutinen är konsekvent med andra Hadoop-filsystem-drivrutiner och använder ett URI-format för att adressera filer och kataloger inom ett Data Lake Storage Gen2-kompatibelt konto.

## <a name="uri-syntax"></a>URI-syntax

URI-syntaxen för Data Lake Storage Gen2 beror på om ditt lagrings konto har kon figurer ATS att ha Data Lake Storage Gen2 som standard fil system.

Om det Data Lake Storage Gen2-kapabla konto som du vill adressera **inte har** angetts som standard fil system när kontot skapas, är den stenografiska URI-syntaxen:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Schema-ID**: `abfs` Protokollet används som schema-ID. Du har möjlighet att ansluta till eller utan en SSL-anslutning (Secure Socket Layer). Använd `abfss` för att ansluta till en säker anslutning till Socket Layer.

2. **Fil system**: Den överordnade plats som innehåller filerna och mapparna. Detta är samma som behållare i tjänsten Azure Storage blobs.

3. **Kontonamn**: Det namn som har angetts för ditt lagrings konto när det skapas.

4. **Sökvägar**: En snedstrecks avgränsad`/`() representation av katalog strukturen.

5. **Fil namn**: Namnet på den enskilda filen. Den här parametern är valfri om du adresserar en katalog.

Men om det konto som du vill adressera anges som standard fil system när kontot skapas, är den stenografiska URI-syntaxen:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Sökväg**: En snedstrecks avgränsad`/`() representation av katalog strukturen.

2. **Fil namn**: Namnet på den enskilda filen.


## <a name="next-steps"></a>Nästa steg

- [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
