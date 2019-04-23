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
ms.openlocfilehash: d4f57eca89cbb68d61546c6d5ce5bcd04f9256e7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799515"
---
Azure Storage erbjuder flera typer av lagringskonton. Varje typ av stöder olika funktioner och har en egen prismodell. Överväg att skillnaderna innan du skapar ett lagringskonto för att avgöra vilken typ av konto som är bäst för dina program. Typer av lagringskonton är:

- **Gpv2-konton**: Grundläggande lagringskontotypen för blobar, filer, köer och tabeller. Rekommenderas för de flesta scenarier med hjälp av Azure Storage.
- **General-Purpose v1-konton**: Äldre kontotyp för blobar, filer, köer och tabeller. Använd gpv2-konton i stället när det är möjligt.
- **Blockera blob storage-konton**: Endast BLOB storage-konton med premium prestandaegenskaper. Rekommenderas för scenarier med hög transaktioner pris, med mindre objekt eller som kräver lagring konsekvent låg fördröjning.
- **FileStorage (förhandsgranskning)-lagringskonton**: Endast filer storage-konton med premium prestandaegenskaper. Rekommenderas för enterprise eller skala program med hög prestanda.
- **BLOB storage-konton**: Endast BLOB storage-konton. Använd gpv2-konton i stället när det är möjligt.

I följande tabell beskrivs vilka typer av lagringskonton och deras funktioner:

| Storage Account-typ | Tjänster som stöds                       | Stöds prestandanivåer      | Stöds åtkomstnivåerna         | Replikeringsalternativ               | Distributionsmodell<sup>1</sup> | Kryptering<sup>2</sup> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Generell användning V2   | BLOB, fil, kö, tabell och Disk       | Standard, Premium<sup>5</sup> | Frekvent, lågfrekvent, Arkiv<sup>3</sup> | LRS, ZRS<sup>4</sup>, GRS, RA-GRS | Resource Manager             | Krypterade              |
| General-Purpose V1   | BLOB, fil, kö, tabell och Disk       | Standard, Premium<sup>5</sup> | Gäller inte                            | LRS, GRS, RA-GRS                  | Resurshanteraren, klassisk    | Krypterade              |
| Block blob-lagring   | BLOB (blockblobbar och tilläggsblobbar endast) | Premium                       | Gäller inte                            | LRS                               | Resource Manager             | Krypterade              |
| FileStorage (förhandsversion)   | Endast filer | Premium                       | Gäller inte                            | LRS                               | Resource Manager             | Krypterade              |
| Blob Storage         | BLOB (blockblobbar och tilläggsblobbar endast) | Standard                      | Frekvent, lågfrekvent, Arkiv<sup>3</sup> | LRS, GRS, RA-GRS                  | Resource Manager             | Krypterade              |

<sup>1</sup>med hjälp av Azure Resource Manager-distributionsmodellen rekommenderas. Lagringskonton med hjälp av den klassiska distributionsmodellen kan fortfarande skapas på vissa platser och befintliga klassiska konton fortfarande användas. Mer information finns i [Azure Resource Manager och klassisk distribution: Distributionsmodeller och dina resursers tillstånd](../articles/azure-resource-manager/resource-manager-deployment-model.md).

<sup>2</sup>alla lagringskonton har krypterats med Storage Service Encryption (SSE) för vilande data. Mer information finns i [Azure Storage Service Encryption för vilande Data](../articles/storage/common/storage-service-encryption.md).

<sup>3</sup>the arkivnivån är tillgängliga på nivån för en enskild blob, inte på lagringskontonivån. Endast blockblob-objekt och lägger till BLOB-objekt kan arkiveras. Mer information finns i [Azure Blob storage: Frekvent, lågfrekvent, och Arkivlagringsnivån](../articles/storage/blobs/storage-blob-storage-tiers.md).

<sup>4</sup>zone-redundant lagring (ZRS) är endast tillgänglig för standard gpv2-konton. Mer information om ZRS finns i [zonen-redundant lagring (ZRS): Azure Storage-program med hög tillgänglighet](../articles/storage/common/storage-redundancy-zrs.md). Läs mer om andra replikeringsalternativ [Azure Storage-replikering](../articles/storage/common/storage-redundancy.md).

<sup>5</sup> premium-prestanda för generell användning v2 och general-purpose v1-konton är tillgängliga för disk och page blob.