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
ms.openlocfilehash: b8a72640ebe8daffedb196456df7d40bc41b7b1b
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95562877"
---
Azure Storage erbjuder flera typer av lagrings konton. Varje typ stöder olika funktioner och har en egen pris modell. Tänk på dessa skillnader innan du skapar ett lagrings konto för att fastställa vilken typ av konto som passar bäst för dina program. Typerna av lagrings konton är:

- **Allmänna-Purpose v2-konton**: grundläggande lagrings konto typ för blobbar, filer, köer och tabeller. Rekommenderas för de flesta scenarier som använder Azure Storage.
- **Generella v1-konton**: typ av äldre konto för blobbar, filer, köer och tabeller. Använd allmänna-Purpose v2-konton i stället när det är möjligt.
- **BlockBlobStorage-konton**: lagrings konton med förstklassiga prestanda egenskaper för block-blobbar och bifogade blobbar. Rekommenderas för scenarier med höga transaktioner, eller scenarier som använder mindre objekt eller kräver konsekvent låg lagrings fördröjning.
- **FileStorage-konton**: endast filer lagrings konton med förstklassiga prestanda egenskaper. Rekommenderas för program med företags-eller hög prestanda skalning.
- **BlobStorage-konton**: äldre BLOB-endast lagrings konton. Använd allmänna-Purpose v2-konton i stället när det är möjligt.

I följande tabell beskrivs typerna av lagrings konton och deras funktioner:

| Storage Account-typ | Tjänster som stöds                       | Prestanda nivåer som stöds      | Åtkomst nivåer som stöds         | Replikeringsalternativ               | Distributionsmodell<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Kryptering<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Allmänt-syfte v2   | BLOB, fil, kö, tabell, disk och Data Lake Gen2<div role="complementary" aria-labelledby="data-lake-gen2"><sup>6</sup></div>      | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Frekvent, låg frekvent, Arkiv<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS, GZRS (för hands version), RA-GZRS (för hands version)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | Krypterad              |
| Generell användning v1   | BLOB, fil, kö, tabell och disk       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Ej tillämpligt                            | LRS, GRS, RA-GRS                  | Resource Manager, klassisk    | Krypterad              |
| BlockBlobStorage   | BLOB (endast block-blobar och bifogade blobbar) | Premium                       | Ej tillämpligt                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Krypterad              |
| FileStorage   | Endast fil | Premium                       | Ej tillämpligt                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Krypterad              |
| BlobStorage         | BLOB (endast block-blobar och bifogade blobbar) | Standard                      | Frekvent, låg frekvent, Arkiv<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | Krypterad              |

<div id="deployment-model"><sup>1</sup> Vi rekommenderar att du använder Azure Resource Manager distributions modell. Lagrings konton som använder den klassiska distributions modellen kan fortfarande skapas på vissa platser och befintliga klassiska konton fortsätter att stödjas. Mer information finns i <a href="/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager vs. klassisk distribution: förstå distributions modeller och status för dina resurser</a>.</div><br/>

<div id="encryption"><sup>2</sup> Alla lagrings konton är krypterade med Kryptering för lagringstjänst (SSE) för vilande data. Mer information finns i <a href="/azure/storage/common/storage-service-encryption">Azure Storage tjänst kryptering för vilande data</a>.</div><br/>

<div id="archive"><sup>3</sup> Arkiv lag ring och skiktning på BLOB-nivå stöder bara block-blobar. Arkiv nivån är bara tillgänglig på nivån för en enskild BLOB, inte på lagrings konto nivå. Mer information finns i <a href="/azure/storage/blobs/storage-blob-storage-tiers">Azure Blob Storage: frekvent, låg frekvent lagring och Arkiv lag</a>rings nivåer.</div><br/>

<div id="zone-redundant-storage"><sup>4</sup> Zone-redundant lagring (ZRS) och geo-Zone-redundant lagring (GZRS/RA-GZRS) (för hands version) är endast tillgängliga för generella standard-, BlockBlobStorage-och FileStorage-konton i vissa regioner. Mer information om alternativ för Azure Storage redundans finns i <a href="/azure/storage/common/storage-redundancy">Azure Storage redundans</a>.</div><br/>

<div id="premium-performance"><sup>5</sup> Premium-prestanda för General-Purpose v2-och General-Purpose v1-konton är endast tillgängligt för disk-och sid-blob. Premium-prestanda för block-eller append-blobar är bara tillgängliga på BlockBlobStorage-konton. Premium-prestanda för filer är bara tillgängliga på FileStorage-konton.</div><br/>

<div id="data-lake-gen2"><sup>6</sup> Azure Data Lake Storage Gen2 är en uppsättning funktioner som är avsedda för stor data analys och bygger på Azure Blob Storage. Data Lake Storage Gen2 stöds endast för allmänna-syfte v2-lagrings konton med hierarkiskt namn område aktiverat. Mer information om Data Lake Storage Gen2 finns i <a href="/azure/storage/blobs/data-lake-storage-introduction">Introduktion till Azure Data Lake Storage Gen2</a>.</div>