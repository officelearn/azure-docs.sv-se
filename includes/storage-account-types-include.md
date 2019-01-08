---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/02/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b278cce8f885f31f9d59fd389261812e04e58405
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2019
ms.locfileid: "54066998"
---
Azure Storage erbjuder tre typer av lagringskonton. Varje typ av stöder olika funktioner och har en egen prismodell. Överväg att skillnaderna innan du skapar ett lagringskonto för att avgöra vilken typ av konto som är bäst för dina program. Det finns tre typer av lagringskonton:

* **Gpv2-konton**: Grundläggande lagringskontotypen för blobar, filer, köer och tabeller. Rekommenderas för de flesta scenarier med hjälp av Azure Storage.
* **General-Purpose v1-konton**: Äldre kontotyp för blobar, filer, köer och tabeller. Använd gpv2-konton i stället när det är möjligt.
* **BLOB storage-konton**: Endast BLOB storage-konton. Använd gpv2-konton i stället när det är möjligt. 

I följande tabell beskrivs vilka typer av lagringskonton och deras funktioner:

| Storage Account-typ | Tjänster som stöds                       | Stöds prestandanivåer | Stöds åtkomstnivåerna               | Replikeringsalternativ                                                | Distributionsmodell<sup>1</sup>  | Kryptering<sup>2</sup> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------------|--------------------------------------------------------------------|-------------------|------------|
| Generell användning V2   | BLOB, fil, kö, tabell och Disk       | Standard, Premium           | Frekvent, lågfrekvent, Arkiv<sup>3</sup> | LRS, ZRS<sup>4</sup>, GRS, RA-GRS | Resource Manager | Krypterade  |
| General-Purpose V1   | BLOB, fil, kö, tabell och Disk       | Standard, Premium           | Gäller inte                                  | LRS, GRS, RA-GRS                                                   | Resurshanteraren, klassisk  | Krypterade  |
| Blob Storage         | BLOB (blockblobbar och tilläggsblobbar endast) | Standard                    | Frekvent, lågfrekvent, Arkiv<sup>3</sup>                            | LRS, GRS, RA-GRS                                                   | Resource Manager  | Krypterade  |

<sup>1</sup>med hjälp av Azure Resource Manager-distributionsmodellen rekommenderas. Lagringskonton med hjälp av den klassiska distributionsmodellen kan fortfarande skapas på vissa platser och befintliga klassiska konton fortfarande användas. Mer information finns i [Azure Resource Manager och klassisk distribution: Distributionsmodeller och dina resursers tillstånd](../articles/azure-resource-manager/resource-manager-deployment-model.md).

<sup>2</sup>alla lagringskonton har krypterats med Storage Service Encryption (SSE) för vilande data. Mer information finns i [Azure Storage Service Encryption för vilande Data](../articles/storage/common/storage-service-encryption.md).

<sup>3</sup>the arkivnivån är tillgängliga på nivån för en enskild blob, inte på lagringskontonivån. Endast blockblob-objekt och lägger till BLOB-objekt kan arkiveras. Mer information finns i [Azure Blob storage: Frekvent, lågfrekvent, och Arkivlagringsnivån](../articles/storage/blobs/storage-blob-storage-tiers.md).

<sup>4</sup>zone-redundant lagring (ZRS) är endast tillgänglig för standard gpv2-konton. Mer information om ZRS finns i [zonen-redundant lagring (ZRS): Azure Storage-program med hög tillgänglighet](../articles/storage/common/storage-redundancy-zrs.md). Läs mer om andra replikeringsalternativ [Azure Storage-replikering](../articles/storage/common/storage-redundancy.md).
