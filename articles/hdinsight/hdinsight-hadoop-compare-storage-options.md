---
title: Jämföra lagringsalternativ för användning med Azure HDInsight-kluster
description: Ger en översikt över lagringstyper och hur de fungerar med Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: b351b9a4edd81da2983cca93127513f9041716bd
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869843"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Jämföra lagringsalternativ för användning med Azure HDInsight-kluster

Du kan välja mellan några olika Azure-lagringstjänster när du skapar HDInsight-kluster:

* [Azure Storage](./overview-azure-storage.md)
* [Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen1.md)
* [Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen2.md)

Den här artikeln innehåller en översikt över dessa lagringstyper och deras unika funktioner.

## <a name="storage-types-and-features"></a>Lagringstyper och -funktioner

I följande tabell sammanfattas De Azure Storage-tjänster som stöds med olika versioner av HDInsight:

| Lagringstjänst | Kontotyp | Namnområdestyp | Tjänster som stöds | Prestandanivåer som stöds | Åtkomstnivåer som stöds | HDInsight-version | Klustertyp |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| Allmänt V2 | Hierarkisk (filsystem) | Blob | Standard | Hot, Cool, Arkiv | 3.6+ | Alla utom Spark 2.1 och 2.2|
|Azure Storage| Allmänt V2 | Objekt | Blob | Standard | Hot, Cool, Arkiv | 3.6+ | Alla |
|Azure Storage| Allmänt V1 | Objekt | Blob | Standard | Ej tillämpligt | Alla | Alla |
|Azure Storage| Blob Lagring** | Objekt | Block Blob | Standard | Hot, Cool, Arkiv | Alla | Alla |
|Azure Data Lake Storage Gen1| Ej tillämpligt | Hierarkisk (filsystem) | Ej tillämpligt | Ej tillämpligt | Ej tillämpligt | 3.6 Endast | Alla utom HBase |

**För HDInsight-kluster kan endast sekundära lagringskonton vara av typen BlobStorage och Page Blob är inte ett lagringsalternativ som stöds.

Mer information om Azure Storage-kontotyper finns i [Översikt över Azure storage-konto](../storage/common/storage-account-overview.md)

Mer information om Azure Storage-åtkomstnivåer finns i [Azure Blob storage: Premium (förhandsversion), lagringsnivåer för Hot, Cool och Arkiv](../storage/blobs/storage-blob-storage-tiers.md)

Du kan skapa kluster med hjälp av kombinationer av tjänster för primär och valfri sekundär lagring. I följande tabell sammanfattas de klusterlagringskonfigurationer som för närvarande stöds i HDInsight:

| HDInsight-version | Primär lagring | Sekundär lagring | Stöds |
|---|---|---|---|
| 3,6 & 4,0 | Allmänt ändamål V1, Allmänt ändamål V2 | Allmänt ändamål V1, Allmänt ändamål V2, BlobStorage (Block Blobbar) | Ja |
| 3,6 & 4,0 | Allmänt ändamål V1, Allmänt ändamål V2 | Data Lake Storage Gen2 | Inga |
| 3,6 & 4,0 | Gen2 för lagring av datasjöar* | Data Lake Storage Gen2 | Ja |
| 3,6 & 4,0 | Gen2 för lagring av datasjöar* | Allmänt ändamål V1, Allmänt ändamål V2, BlobStorage (Block Blobbar) | Ja |
| 3,6 & 4,0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | Inga |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | Ja |
| 3.6 | Data Lake Storage Gen1 | Allmänt ändamål V1, Allmänt ändamål V2, BlobStorage (Block Blobbar) | Ja |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | Inga |
| 4.0 | Data Lake Storage Gen1 | Alla | Inga |
| 4.0 | Allmänt ändamål V1, Allmänt ändamål V2 | Data Lake Storage Gen1 | Inga |

*=Det kan vara ett eller flera DataSjölagringsgenm2-konton, så länge de alla är inställda på att använda samma hanterade identitet för klusteråtkomst.

> [!NOTE]
> Primär lagringsgendlagring i DataSjögend2 stöds inte för Spark 2.1- eller 2.2-kluster.

## <a name="next-steps"></a>Nästa steg

* [Översikt över Azure Storage](./overview-azure-storage.md)
* [Översikt över Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)
* [Översikt över Azure Data Lake Storage Gen2](./overview-data-lake-storage-gen2.md)
* [Introduktion till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Introduktion till Azure Storage](../storage/common/storage-introduction.md)
