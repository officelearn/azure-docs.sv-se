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
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371568"
---
Det finns flera olika typer av lagringskonton i Azure Storage. Varje typ stöder olika funktioner och har en egen prismodell. Fundera på de här skillnaderna innan du skapar ett lagringskonto för att komma fram till vilken kontotyp som passar bäst för dina behov. Det finns följande typer av lagringskonton:

- **GPv2-konton (generell användning v2)** : Basic-lagringskontotyp för blobbar, filer, köer och tabeller. Rekommenderas för de flesta scenarier med Azure Storage.
- **GPv1-konton (generell användning v1)** : Äldre kontotyp för blobbar, filer, köer och tabeller. Använd Gpv2-konton i stället när det är möjligt.
- **BlockBlobStorage-konton**: Lagringskonton med Premium-prestandaegenskaper för blockblobbar och tilläggsblobbar. Rekommenderas för scenarier med hög transaktionsfrekvens, eller scenarier som använder mindre objekt eller konsekvent kräver låg lagringssvarstid.
- **FileStorage-konton**: Lagringskonton endast för filer med Premium-prestandaegenskaper. Rekommenderas för företagsprogram eller program med hög prestandaskala.
- **BlobStorage-konton**: Äldre lagringskonton endast för blobbar. Använd Gpv2-konton i stället när det är möjligt.

I följande tabell beskrivs de olika typerna av lagringskonton och deras funktioner:

| Storage Account-typ | Tjänster som stöds                       | Prestandanivåer som stöds      | Åtkomstnivåer som stöds         | Replikeringsalternativ               | Distributionsmodell<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Kryptering<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Generell användning V2   | Blob, fil, kö, tabell, disk och Data Lake Gen2<div role="complementary" aria-labelledby="data-lake-gen2"><sup>6</sup></div>      | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Frekvent, lågfrekvent, arkiv<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS, GZRS (förhandsversion), RA-GZRS (förhandsversion)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Resource Manager             | Krypterade              |
| Generell användning V1   | Blob, fil, kö, tabell och disk       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Ej tillämpligt                            | LRS, GRS, RA-GRS                  | Resource Manager, klassisk    | Krypterade              |
| BlockBlobStorage   | Blob (endast blockblobbar och tilläggsblobbar) | Premium                       | Ej tillämpligt                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Krypterade              |
| FileStorage   | Endast fil | Premium                       | Ej tillämpligt                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Resource Manager             | Krypterade              |
| BlobStorage         | Blob (endast blockblobbar och tilläggsblobbar) | Standard                      | Frekvent, lågfrekvent, arkiv<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Resource Manager             | Krypterade              |

<div id="deployment-model"><sup>1</sup>Användning av Azure Resource Manager-distributionsmodellen rekommenderas. Lagringskonton som använder den klassiska distributionsmodellen kan fortfarande skapas på vissa platser, och befintliga klassiska konton får fortsättningsvis stöd. Mer information finns i <a href="https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager jämfört med klassisk distribution: Förstå distributionsmodeller och dina resursers tillstånd</a>.</div><br/>

<div id="encryption"><sup>2</sup>Alla lagringskonton krypteras med Kryptering för lagringstjänst (SSE) för vilande data. Mer information finns i <a href="https://docs.microsoft.com/azure/storage/common/storage-service-encryption">Azure-kryptering för lagringstjänst för vilande data</a>.</div><br/>

<div id="archive"><sup>3</sup> Arkivlagring och blobnivåindelning stöder endast blockblobbar. Arkivlagringsnivå är endast tillgänglig på nivån för en enskild blob och inte på lagringskontonivån. Mer information finns i <a href="https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers">Azure Blob Storage: Frekvent lagringsnivå, lågfrekvent lagringsnivå och arkivlagringsnivå</a>.</div><br/>

<div id="zone-redundant-storage"><sup>4</sup>Zonredundant lagring (ZRS) och geo-zonredundant lagrings (GZRS/RA-GZRS) (förhandsversion) är endast tillgängliga för standard GPv2-, BlockBlobStorage- och FileStorage-konton i vissa regioner. Mer information om alternativ för Azure Storage-redundans finns i <a href="https://docs.microsoft.com/azure/storage/common/storage-redundancy">Azure Storage-redundans</a>.</div><br/>

<div id="premium-performance"><sup>5</sup>Premium-prestanda för GPv2- och GPv1-konton är endast tillgängligt för disk- och sidblob. Premium-prestanda för block- eller tilläggsblobbar är endast tillgängliga för BlockBlobStorage-konton. Premium-prestanda för filer är endast tillgängligt för FileStorage-konton.</div><br/>

<div id="data-lake-gen2"><sup>6</sup>Azure Data Lake Storage Gen2 är en uppsättning funktioner som är avsedda för analys av stordata, byggt på Azure Blob Storage. Data Lake Storage Gen2 stöds endast för GPv2-lagringskonton med hierarkisk namnrymd aktiverad. Mer information om Data Lake Storage Gen2 finns i <a href="https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction">Introduktion till Azure Data Lake Storage Gen2</a>.</div>
