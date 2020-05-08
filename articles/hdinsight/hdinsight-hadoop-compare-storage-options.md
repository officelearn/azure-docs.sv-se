---
title: Jämför lagrings alternativ för användning med Azure HDInsight-kluster
description: Ger en översikt över lagrings typer och hur de fungerar med Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: ed93ba937a843618f36bac6e88b15ff77355ca75
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2020
ms.locfileid: "82610708"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Jämför lagrings alternativ för användning med Azure HDInsight-kluster

Du kan välja mellan några olika Azure Storage-tjänster när du skapar HDInsight-kluster:

* [Azure Storage](./overview-azure-storage.md)
* [Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen2.md)
* [Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)

Den här artikeln innehåller en översikt över dessa lagrings typer och deras unika funktioner.

## <a name="storage-types-and-features"></a>Lagrings typer och funktioner

I följande tabell sammanfattas de Azure Storage tjänster som stöds med olika versioner av HDInsight:

| Lagrings tjänst | Kontotyp | Namn områdes typ | Tjänster som stöds | Prestanda nivåer som stöds | Åtkomst nivåer som stöds | HDInsight-version | Kluster typ |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| Allmänt-syfte v2 | Hierarkisk (FileSystem) | Blob | Standard | Frekvent, låg frekvent, Arkiv | 3.6 + | Alla utom Spark 2,1 och 2,2|
|Azure Storage| Allmänt-syfte v2 | Objekt | Blob | Standard | Frekvent, låg frekvent, Arkiv | 3.6 + | Alla |
|Azure Storage| Generell användning v1 | Objekt | Blob | Standard | E.t. | Alla | Alla |
|Azure Storage| Blob Storage * * | Objekt | Blockera BLOB | Standard | Frekvent, låg frekvent, Arkiv | Alla | Alla |
|Azure Data Lake Storage Gen1| E.t. | Hierarkisk (FileSystem) | E.t. | E.t. | E.t. | 3,6 endast | Alla utom HBase |

* * För HDInsight-kluster kan endast sekundära lagrings konton vara av typen BlobStorage och Page BLOB är inte ett lagrings alternativ som stöds.

Mer information om Azure Storage konto typer finns i [Översikt över Azure Storage-konto](../storage/common/storage-account-overview.md)

Mer information om Azure Storage åtkomst nivåer finns i [Azure Blob Storage: Premium (för hands version), frekvent, låg frekvent och Arkiv lag](../storage/blobs/storage-blob-storage-tiers.md) rings nivå

Du kan skapa kluster med hjälp av kombinationer av tjänster för primär och valfri sekundär lagring. I följande tabell sammanfattas de kluster lagrings konfigurationer som för närvarande stöds i HDInsight:

| HDInsight-version | Primär lagring | Sekundär lagring | Stöds |
|---|---|---|---|
| 3,6 & 4,0 | Generell användning v1, Generell användning v2 | Generell användning v1, Generell användning v2, BlobStorage (block blobbar) | Ja |
| 3,6 & 4,0 | Generell användning v1, Generell användning v2 | Data Lake Storage Gen2 | Inga |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Data Lake Storage Gen2 | Ja |
| 3,6 & 4,0 | Data Lake Storage Gen2 * | Generell användning v1, Generell användning v2, BlobStorage (block blobbar) | Ja |
| 3,6 & 4,0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | Inga |
| 3,6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | Ja |
| 3,6 | Data Lake Storage Gen1 | Generell användning v1, Generell användning v2, BlobStorage (block blobbar) | Ja |
| 3,6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | Inga |
| 4.0 | Data Lake Storage Gen1 | Alla | Inga |
| 4.0 | Generell användning v1, Generell användning v2 | Data Lake Storage Gen1 | Inga |

* = Detta kan vara ett eller flera Data Lake Storage Gen2-konton, så länge de är konfigurerat för att använda samma hanterade identitet för kluster åtkomst.

> [!NOTE]
> Data Lake Storage Gen2 primär lagring stöds inte för Spark 2,1-eller 2,2-kluster.

## <a name="next-steps"></a>Nästa steg

* [Azure Storage-översikt](./overview-azure-storage.md)
* [Azure Data Lake Storage Gen1-översikt](./overview-data-lake-storage-gen1.md)
* [Azure Data Lake Storage Gen2-översikt](./overview-data-lake-storage-gen2.md)
* [Introduktion till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introduktion till Azure Storage](../storage/common/storage-introduction.md)
