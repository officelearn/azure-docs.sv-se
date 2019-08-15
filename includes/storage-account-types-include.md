---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/23/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 280ef8550177a514a6704a8bfab226745222f91e
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029868"
---
Azure Storage erbjuder flera typer av lagrings konton. Varje typ stöder olika funktioner och har en egen pris modell. Tänk på dessa skillnader innan du skapar ett lagrings konto för att fastställa vilken typ av konto som passar bäst för dina program. Typerna av lagrings konton är:

- **Allmänna-Purpose v2-konton**: Grundläggande lagrings konto typ för blobbar, filer, köer och tabeller. Rekommenderas för de flesta scenarier som använder Azure Storage.
- **Generella v1-konton**: Typ av äldre konto för blobbar, filer, köer och tabeller. Använd allmänna-Purpose v2-konton i stället när det är möjligt.
- **Block Blob Storage-konton**: Endast BLOB-lagrings konton med förstklassiga prestanda egenskaper. Rekommenderas för scenarier med höga transaktioner, som använder mindre objekt eller kräver konsekvent låg lagrings fördröjning.
- **FileStorage lagrings konton**: Lagrings konton med förstklassiga prestanda egenskaper. Rekommenderas för program med företags-eller hög prestanda skalning.
- **Blob Storage-konton**: Enbart BLOB-lagrings konton. Använd allmänna-Purpose v2-konton i stället när det är möjligt.

I följande tabell beskrivs typerna av lagrings konton och deras funktioner:

| Typ av lagringskonto | Tjänster som stöds                       | Prestanda nivåer som stöds      | Åtkomst nivåer som stöds         | Replikeringsalternativ               | Distributionsmodell<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Kryptering<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Allmänt-syfte v2   | BLOB, fil, kö, tabell och disk       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Frekvent, låg frekvent, Arkiv<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS, ZGRS (för hands version), RA-ZGRS (för hands version)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | Krypterad              |
| Generell användning v1   | BLOB, fil, kö, tabell och disk       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Gäller inte                            | LRS, GRS, RA-GRS                  | Resource Manager, klassisk    | Krypterad              |
| Block Blob Storage   | BLOB (endast block-blobar och bifogade blobbar) | Premium                       | Gäller inte                            | LRS                               | Resource Manager             | Krypterad              |
| FileStorage   | Endast filer | Premium                       | Gäller inte                            | LRS                               | Resource Manager             | Krypterad              |
| Blob Storage         | BLOB (endast block-blobar och bifogade blobbar) | Standard                      | Frekvent, låg frekvent, Arkiv<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | Krypterad              |

<div id="deployment-model"><sup>1</sup> Vi rekommenderar att du använder Azure Resource Manager distributions modell. Lagrings konton som använder den klassiska distributions modellen kan fortfarande skapas på vissa platser och befintliga klassiska konton fortsätter att stödjas. Mer information finns i <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager vs. Classic-distribution: Förstå distributions modeller och status för dina resurser</a>.</div>

<div id="encryption"><sup>2</sup> Alla lagrings konton är krypterade med Kryptering för lagringstjänst (SSE) för vilande data. Mer information finns i <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">Azure Storage tjänst kryptering för vilande data</a>.</div>

<div id="archive"><sup>3</sup> Arkiv nivån är bara tillgänglig på nivån för en enskild BLOB, inte på lagrings konto nivå. Det går bara att arkivera blobbar och bifogade blobbar. Mer information finns i <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">Azure Blob Storage: Lagrings nivåerna</a>frekvent, låg frekvent och Arkiv lag ring.</div>

<div id="zone-redundant-storage"><sup>4</sup> Zone-redundant lagring (ZRS) och geo-Zone-redundant lagring (GZRS) (för hands version) är endast tillgängliga för allmänna lagrings konton för generell användning v2. Mer information om ZRS finns i <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs">zon-redundant lagring (ZRS): Azure Storage program</a>med hög tillgänglighet. Mer information om GZRS finns i <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-gzrs">geo-Zone-redundant lagring för hög tillgänglighet och maximal hållbarhet (för hands version)</a>. Mer information om andra replikeringsalternativ finns <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Azure Storage replikering</a>.</div>

<div id="premium-performance"><sup>5</sup> Premium-prestanda för General-Purpose v2-och General-Purpose v1-konton är endast tillgängligt för disk-och sid-blob.</div>
