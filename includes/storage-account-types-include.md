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
ms.openlocfilehash: d96e69fb526cff633c78e9ac8a1679762014cd4b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67132956"
---
Azure Storage erbjuder flera typer av lagringskonton. Varje typ av stöder olika funktioner och har en egen prismodell. Överväg att skillnaderna innan du skapar ett lagringskonto för att avgöra vilken typ av konto som är bäst för dina program. Typer av lagringskonton är:

- **Gpv2-konton**: Grundläggande lagringskontotypen för blobar, filer, köer och tabeller. Rekommenderas för de flesta scenarier med hjälp av Azure Storage.
- **General-Purpose v1-konton**: Äldre kontotyp för blobar, filer, köer och tabeller. Använd gpv2-konton i stället när det är möjligt.
- **Blockera blob storage-konton**: Endast BLOB storage-konton med premium prestandaegenskaper. Rekommenderas för scenarier med hög transaktioner pris, med mindre objekt eller som kräver lagring konsekvent låg fördröjning.
- **FileStorage (förhandsgranskning)-lagringskonton**: Endast filer storage-konton med premium prestandaegenskaper. Rekommenderas för enterprise eller skala program med hög prestanda.
- **BLOB storage-konton**: Endast BLOB storage-konton. Använd gpv2-konton i stället när det är möjligt.

I följande tabell beskrivs vilka typer av lagringskonton och deras funktioner:

| Storage Account-typ | Tjänster som stöds                       | Stöds prestandanivåer      | Stöds åtkomstnivåerna         | Replikeringsalternativ               | Distributionsmodell<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Kryptering<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Generell användning V2   | BLOB, fil, kö, tabell och Disk       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Frekvent, lågfrekvent, Arkiv<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | Krypterade              |
| General-Purpose V1   | BLOB, fil, kö, tabell och Disk       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Gäller inte                            | LRS, GRS, RA-GRS                  | Resurshanteraren, klassisk    | Krypterade              |
| Block blob-lagring   | BLOB (blockblobbar och tilläggsblobbar endast) | Premium                       | Gäller inte                            | LRS                               | Resource Manager             | Krypterade              |
| FileStorage (förhandsversion)   | Endast filer | Premium                       | Gäller inte                            | LRS                               | Resource Manager             | Krypterade              |
| Blob Storage         | BLOB (blockblobbar och tilläggsblobbar endast) | Standard                      | Frekvent, lågfrekvent, Arkiv<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | Krypterade              |

<div id="deployment-model"><sup>1</sup>med hjälp av Azure Resource Manager-distributionsmodellen rekommenderas. Lagringskonton med hjälp av den klassiska distributionsmodellen kan fortfarande skapas på vissa platser och befintliga klassiska konton fortfarande användas. Mer information finns i <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager och klassisk distribution: Distributionsmodeller och dina resursers tillstånd</a>.</div>

<div id="encryption"><sup>2</sup>alla lagringskonton har krypterats med Storage Service Encryption (SSE) för vilande data. Mer information finns i <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">Azure Storage Service Encryption för vilande Data</a>.</div>

<div id="archive"><sup>3</sup>the arkivnivån är tillgängliga på nivån för en enskild blob, inte på lagringskontonivån. Endast blockblob-objekt och lägger till BLOB-objekt kan arkiveras. Mer information finns i <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">Azure Blob storage: Frekvent, lågfrekvent, och Arkivlagringsnivån</a>.</div>

<div id="zone-redundant-storage"><sup>4</sup>zone-redundant lagring (ZRS) är endast tillgänglig för standard gpv2-konton. Mer information om ZRS finns i <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs">zonen-redundant lagring (ZRS): Azure Storage-program med hög tillgänglighet</a>. Läs mer om andra replikeringsalternativ <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Azure Storage-replikering</a>.</div>

<div id="premium-performance"><sup>5</sup>premium-prestanda för generell användning v2 och general-purpose v1-konton är tillgängliga för disk och page blob.</div>
