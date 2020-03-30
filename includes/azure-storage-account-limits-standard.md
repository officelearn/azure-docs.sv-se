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
ms.openlocfilehash: 6cd883289513091ff1a57a130b12e25e012c1160
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334954"
---
I följande tabell beskrivs standardgränser för Azure general purpose v1, v2, Blob storage, block blob storage och Data Lake Storage Gen2-aktiverade lagringskonton. *Ingressgränsen* refererar till alla data som skickas till ett lagringskonto. *Utgående* gräns refererar till alla data som tas emot från ett lagringskonto.

| Resurs | Gräns |
| --- | --- |
| Antal lagringskonton per region per prenumeration, inklusive standard-, premium- och Datasjölagringsgen2-aktiverade lagringskonton. <sup>3.</sup> | 250 |
| Maximal lagringskontokapacitet | 5 PiB <sup>1</sup>|
| Maximalt antal blob-behållare, blobbar, filresurser, tabeller, köer, entiteter eller meddelanden per lagringskonto | Obegränsad |
| Maximal begärandegrad<sup>1</sup> per lagringskonto | 20 000 förfrågningar per sekund |
| Maximalt ingående<sup>1</sup> per lagringskonto (regioner i USA, Europa) | 25 Gbit/s |
| Maximal inträngning<sup>1</sup> per lagringskonto (andra regioner än USA och Europa) | 5 Gbit/s om RA-GRS/GRS är aktiverat, 10 Gbit/s för LRS/ZRS<sup>2</sup> |
| Maximal utgående för konton för v2- och bloblagring (alla regioner) | 50 Gbit/s |
| Maximal utgående för virtuella lagringskonton för allmänt ändamål (regioner i USA) | 20 Gbit/s om RA-GRS/GRS är aktiverat, 30 Gbit/s för LRS/ZRS<sup>2</sup> |
| Maximal utgående för allmänna v1-lagringskonton (regioner utanför USA) | 10 Gbit/s om RA-GRS/GRS är aktiverat, 15 Gbit/s för LRS/ZRS<sup>2</sup> |
| Maximalt antal virtuella nätverksregler per lagringskonto | 200 |
| Maximalt antal IP-adressregler per lagringskonto | 200 |

<sup>1</sup> Azure Storage-standardkonton stöder högre kapacitetsgränser och högre gränser för inträngning på begäran. Om du vill begära en ökning av kontogränserna kontaktar du [Azure Support](https://azure.microsoft.com/support/faq/).

<sup>2</sup> Om ditt lagringskonto har läsåtkomst aktiverat med geo-redundant lagring (RA-GRS) eller geo-zon-redundant lagring (RA-GZRS), är utgående mål för den sekundära platsen identiska med de primära platsens. [Azure Storage-replikeringsalternativ](https://docs.microsoft.com/azure/storage/common/storage-redundancy) inkluderar:

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

<sup>3</sup> [Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md) är en uppsättning funktioner som är avsedda för stordataanalys, byggd på Azure Blob-lagring. Azure Storage- och blob-lagringsbegränsningar gäller för DataSjölagring gen2.

> [!NOTE]
> Microsoft rekommenderar att du använder ett allmänt v2-lagringskonto för de flesta scenarier. Du kan enkelt uppgradera ett allmänt v1- eller Azure Blob-lagringskonto till ett allmänt v2-konto utan driftstopp och utan att behöva kopiera data. Mer information finns i [Uppgradera till ett allmänt v2-lagringskonto](../articles/storage/common/storage-account-upgrade.md).

Om behoven i ditt program överskrider skalbarhetsmålen för ett enda lagringskonto kan du skapa programmet så att det använder flera lagringskonton. Du kan sedan partitionera dina dataobjekt över dessa lagringskonton. Information om volympriser finns i [Azure Storage-priser](https://azure.microsoft.com/pricing/details/storage/).

Alla lagringskonton körs på en platt nätverkstopologi oavsett när de skapades. Mer information om Azure Storage-arkitekturen för platta nätverk och om skalbarhet finns i [Microsoft Azure Storage: En molnbaserad lagringstjänst med stark konsekvens](https://docs.microsoft.com/archive/blogs/hanuk/windows-azures-flat-network-storage-to-enable-higher-scalability-targets). 
