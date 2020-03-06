---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 02/27/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: e29cdd56d1c43b3d0e8fc6ca233ac19d8b0004ff
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78355785"
---
I följande tabell beskrivs standard gränserna för Azure General-Purpose v1 (GPv1), v2 (GPv2), BLOB, Premium BlockBlob och Data Lake Gen2-aktiverade lagrings konton. Den *inkommande* gränsen refererar till alla data som skickas till ett lagrings konto. *Utgående* gräns avser alla data som tas emot från ett lagrings konto.

| Resurs | Standardgräns |
| --- | --- |
| Antal lagrings konton per region per prenumeration, inklusive både standard-, Premium-och data Lake Gen2-konton<sup>3</sup> | 250 |
| Maximal kapacitet för lagrings konto | 5 PiB <sup>1</sup>|
| Maximalt antal BLOB-behållare, blobbar, fil resurser, tabeller, köer, entiteter eller meddelanden per lagrings konto | Obegränsad |
| Högsta begär ande frekvens<sup>1</sup> per lagrings konto | 20 000 begär Anden per sekund |
| Maximalt antal inkommande<sup>1</sup> per lagrings konto (USA, Europa regioner) | 25 Gbit/s |
| Maximalt antal inkommande<sup>1</sup> per lagrings konto (andra regioner än USA och Europa) | 5 Gbit/s om RA-GRS/GRS är aktiverat, 10 Gbit/s för LRS/ZRS<sup>2</sup> |
| Maximalt utgånget för General-Purpose v2-och Blob Storage-konton (alla regioner) | 50 Gbit/s |
| Högsta utgående trafik för generella v1-lagrings konton (amerikanska regioner) | 20 Gbit/s om RA-GRS/GRS är aktiverat, 30 Gbit/s för LRS/ZRS<sup>2</sup> |
| Maximalt utgående för allmänna v1-lagrings konton (icke-amerikanska regioner) | 10 Gbit/s om RA-GRS/GRS är aktiverat, 15 Gbit/s för LRS/ZRS<sup>2</sup> |
| Maximalt antal virtuella nätverks regler per lagrings konto | 200 |
| Maximalt antal IP-adress regler per lagrings konto | 200 |

<sup>1</sup> Azure Storage Standard konton stöder högre kapacitets gränser och högre gränser för ingångar efter begäran. Kontakta [Azure-supporten](https://azure.microsoft.com/support/faq/)om du vill begära en ökning av konto gränserna.

<sup>2</sup> om ditt lagrings konto har Läs behörighet aktiverat med Geo-redundant lagring (RA-GRS) eller geo-Zone-redundant lagring (ra-GZRS), är utgångs målen för den sekundära platsen identiska med de på den primära platsen. [Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy) alternativen för replikering inkluderar:

[!INCLUDE [azure-storage-redundancy](azure-storage-redundancy.md)]

<sup>3</sup> [Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-introduction.md) är en uppsättning funktioner som är avsedda för stor data analys och bygger på Azure Blob Storage. Begränsningar för Azure Storage och blob-lagring gäller för data Lake-Gen2.

> [!NOTE]
> Microsoft rekommenderar att du använder ett allmänt-syfte v2-lagrings konto för de flesta scenarier. Du kan enkelt uppgradera ett allmänt v1-eller Azure Blob Storage-konto till ett allmänt-syfte v2-konto utan avbrott och utan att behöva kopiera data. Mer information finns i [Uppgradera till ett allmänt-syfte v2-lagrings konto](../articles/storage/common/storage-account-upgrade.md).

Om ditt programs behov överskrider skalbarhets målen för ett enda lagrings konto kan du bygga ditt program för att använda flera lagrings konton. Du kan sedan partitionera dina data objekt över dessa lagrings konton. Mer information om volym priser finns i [Azure Storage priser](https://azure.microsoft.com/pricing/details/storage/).

Alla lagrings konton som körs på en låg nätverks sto pol Ogin oavsett när de skapades. Mer information om Azure Storage platt nätverks arkitektur och om skalbarhet finns i [Microsoft Azure Storage: en moln lagrings tjänst med hög tillgänglighet med stark konsekvens](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx). Ett [hierarkiskt namn område kan aktive ras för ett data Lake Gen2-konto](../articles/storage/blobs/data-lake-storage-namespace.md) förutom det fasta namn området för åtkomst till protokollet. Både fasta och hierarkiska namn rymds lagrings konton stöder samma skalbarhet och prestanda mål som beskrivs i den här artikeln.
