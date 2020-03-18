---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/13/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b5de45086b324006a3dafdef5c80689923650759
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2020
ms.locfileid: "79381751"
---
I följande tabell beskrivs standardgränserna för Azure-lagringskonton för generell användning v1, v2, bloblagring, blockbloblagring och Data Lake Storage Gen2. Den *inkommande* gränsen syftar på alla data som skickas till ett lagringskonto. Den *utgående* gränsen syftar på alla data som tas emot från ett lagringskonto.

| Resurs | Standardgräns |
| --- | --- |
| Antal lagringskonton per region per prenumeration, inklusive standard-, premium- och Data Lake Storage Gen2-aktiverade lagringskonton.<sup>3</sup> | 250 |
| Maximal kapacitet för lagringskonto | 5 PiB <sup>1</sup>|
| Maximalt antal blobcontainrar, blobbar, filresurser, tabeller, köer, entiteter eller meddelanden per lagringskonto | Obegränsad |
| Maximal förfrågningsfrekvens<sup>1</sup> per lagringskonto | 20 000 begäranden per sekund |
| Maximalt inkommande<sup>1</sup> per lagringskonto (regionerna USA, Europa) | 25 Gbit/s |
| Maximalt inkommande<sup>1</sup> per lagringskonto (andra regioner än USA och Europa) | 5 Gbit/s om RA-GRS/GRS är aktiverat, 10 Gbit/s för LRS/ZRS<sup>2</sup> |
| Maximalt utgående för generell användning v2- och Blob-lagringskonton (alla regioner) | 50 Gbit/s |
| Maximalt utgående för generell användning v1-lagringskonton (USA-regioner) | 20 Gbit/s om RA-GRS/GRS är aktiverat, 30 Gbit/s för LRS/ZRS<sup>2</sup> |
| Maximalt utgående för generell användning v1-lagringskonton (andra regioner än USA) | 10 Gbit/s om RA-GRS/GRS är aktiverat, 15 Gbit/s för LRS/ZRS<sup>2</sup> |
| Maximalt antal regler för virtuellt nätverk per lagringskonto | 200 |
| Maximalt antal regler för IP-adress per lagringskonto | 200 |

<sup>1</sup> Azure Storage Standard-konton stöder högre kapacitetsgränser och högre gränser för inkommande efter begäran. Om du vill begära en ökning av kontogränser kontaktar du [Azure-supporten](https://azure.microsoft.com/support/faq/).

<sup>2</sup> Om ditt lagringskonto har läsåtkomst aktiverat med geo-redundant lagring (RA-GRS) eller geo-zonredundant lagring (RA-GZRS), kommer de utgående målen för den sekundära platsen att vara identiska med de på den primära platsen. Det finns följande alternativ för [Azure Storage-replikering](https://docs.microsoft.com/azure/storage/common/storage-redundancy):

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

<sup>3</sup> [Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md) är en uppsättning funktioner som är avsedda för analys av stordata, byggt på Azure Blob Storage. Begränsningar för Azure Storage och Blob Storage gäller Data Lake Storage Gen2.

> [!NOTE]
> Microsoft rekommenderar att du använder lagringskonton av typen generell användning v2 för de flesta scenarier. Du kan enkelt uppgradera ett lagringskonto för generell användning v1 eller Azure Blob-lagringskonto till ett generell användning v2-konto utan driftavbrott och utan att behöva kopiera data. Mer information finns i [Uppgradera till ett V2-lagringskonto för generell användning](../articles/storage/common/storage-account-upgrade.md).

Om ditt programs behov överskrider skalbarhetsmålen för ett enda lagringskonto kan du bygga ditt program så att det använder flera lagringskonton. Du kan sedan partitionera dina dataobjekt på dessa lagringskonton. Mer information om volympriser finns i [Priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

Alla lagringskonton körs på en platt nätverkstopologi oavsett när de skapades. Du hittar mer informationen om den platta nätverksarkitekturen i Azure Storage och om skalbarhet i [Microsoft Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets). 
