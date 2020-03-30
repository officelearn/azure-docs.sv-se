---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/05/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 726fc9cc0e4eb5a8f9bed5cca8dd4d3df588ac7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371568"
---
Azure Storage erbjuder flera typer av lagringskonton. Varje typ stöder olika funktioner och har sin egen prismodell. Tänk på dessa skillnader innan du skapar ett lagringskonto för att avgöra vilken typ av konto som är bäst för dina program. De typer av lagringskonton är:

- **Virtuella konton för allmänt ändamål**: Grundläggande lagringskontotyp för blobbar, filer, köer och tabeller. Rekommenderas för de flesta scenarier med Azure Storage.
- **V1-konton för allmänt bruk**: Äldre kontotyp för blobbar, filer, köer och tabeller. Använd allmänna v2-konton i stället när det är möjligt.
- **BlockBlobStorage-konton**: Lagringskonton med prestandaegenskaper för premiumprestanda för blockblobar och tilläggsblobbar. Rekommenderas för scenarier med höga transaktionshastigheter eller scenarier som använder mindre objekt eller kräver konsekvent låg lagringsfördröjning.
- **FileStorage-konton**: Lagringskonton med endast filer med prestandaegenskaper för premier. Rekommenderas för program med företags- eller högprestandaskala.
- **BlobStorage-konton**: Lagringskonton med endast äldre blob. Använd allmänna v2-konton i stället när det är möjligt.

I följande tabell beskrivs vilka typer av lagringskonton och deras funktioner:

| Storage Account-typ | Tjänster som stöds                       | Prestandanivåer som stöds      | Åtkomstnivåer som stöds         | Replikeringsalternativ               | Distributionsmodell<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Kryptering<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Allmänt V2   | Blob, Fil, Kö, Tabell, Disk och DataSjögen2<div role="complementary" aria-labelledby="data-lake-gen2"><sup>6</sup></div>      | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Hot, Cool, Arkiv<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS, GZRS (förhandsgranskning), RA-GZRS (förhandsgranskning)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | Krypterade              |
| Allmänt V1   | Blob, Arkiv, Kö, Tabell och Disk       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Ej tillämpligt                            | LRS, GRS, RA-GRS                  | Resurshanteraren, Klassisk    | Krypterade              |
| BlockBlobStorage   | Blob (blockera blobbar och tillägg blobbar endast) | Premium                       | Ej tillämpligt                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Krypterade              |
| FileStorage (filstora)   | Endast fil | Premium                       | Ej tillämpligt                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Krypterade              |
| BlobStorage (BlobStorage)         | Blob (blockera blobbar och tillägg blobbar endast) | Standard                      | Hot, Cool, Arkiv<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | Krypterade              |

<div id="deployment-model"><sup>1.</sup> Användning av distributionsmodellen för Azure Resource Manager rekommenderas. Lagringskonton som använder den klassiska distributionsmodellen kan fortfarande skapas på vissa platser och befintliga klassiska konton stöds fortfarande. Mer information finns i <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager jämfört med klassisk distribution: Förstå distributionsmodeller och tillståndet för dina resurser</a>.</div><br/>

<div id="encryption"><sup>2.</sup> Alla lagringskonton krypteras med SSE (Storage Service Encryption) för data i vila. Mer information finns i <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">Azure Storage Service Encryption for Data at Rest</a>.</div><br/>

<div id="archive"><sup>3</sup> Arkivlagring och blob-nivånivånivå stöder endast blockblobar. Arkivnivån är endast tillgänglig på nivån för en enskild blob, inte på lagringskontonivå. Mer information finns i <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">Azure Blob storage: Hot, Cool och Archive storage tiers</a>.</div><br/>

<div id="zone-redundant-storage"><sup>4.</sup> Zonupptraktad lagring (ZRS) och geozons redundant lagring (GZRS/RA-GZRS) (förhandsversion) är endast tillgängliga för vanliga konton v2, BlockBlobStorage och FileStorage i vissa regioner. Mer information om Azure Storage redundansalternativ finns i <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Azure Storage redundans</a>.</div><br/>

<div id="premium-performance"><sup>5 5</sup> Premiumprestanda för virtuella v2-konton och generell v1-konton är endast tillgängliga för disk- och sidblobb. Premiumprestanda för block- eller tilläggsblobar är endast tillgängliga på BlockBlobStorage-konton. Premiumprestanda för filer är endast tillgängliga på FileStorage-konton.</div><br/>

<div id="data-lake-gen2"><sup>6.</sup> Azure Data Lake Storage Gen2 är en uppsättning funktioner som är dedikerade till stordataanalys, byggd på Azure Blob-lagring. DataSjölagringsgen2 stöds endast på V2-lagringskonton för allmänt ändamål med hierarkiskt namnområde aktiverat. Mer information om Data Lake Storage Gen2 finns i <a href="https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction">Introduktion till Azure Data Lake Storage Gen2</a>.</div>
