---
title: Introduktion till Blob storage – Objektlagring i Azure | Microsoft Docs
description: Azure Blob storage är utformat för att lagra stora mängder ostrukturerade objektdata, exempelvis text eller binära data. Dina program kan komma åt objekt i Blob storage från PowerShell eller Azure CLI, från kod via Azure Storage-klientbibliotek eller via REST.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: overview
ms.date: 03/27/2018
ms.author: tamram
ms.openlocfilehash: 0fff0032ec2452413bcd1df3175634b14a64208f
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2018
---
# <a name="introduction-to-blob-storage"></a>Introduktion till bloblagring

Azure Blob storage är Microsofts molntjänstlagringslösning för dataobjekt. Blob storage kan lagra stora mängder ostrukturerade objektdata, exempelvis text eller binära data. Data i Blob storage kan nås från vilken plats som helst i världen via HTTP eller HTTPS. Du kan använda Blob Storage för att exponera data offentligt eller lagra programdata privat.

Vanliga användningsområden för Blob Storage är:

* Leverera bilder eller dokument direkt till en webbläsare
* Lagra filer för distribuerad åtkomst
* Direktuppspelning av video och ljud
* Lagra för säkerhetskopiering och återställning, haveriberedskap och arkivering
* Lagra data för analys av en tjänst som kan vara lokal eller Azure-baserad
* Lagra virtuella hårddiskar för användning med virtuella Azure-datorer

## <a name="blob-service-concepts"></a>Blob Service-koncept

Blob Service innehåller följande komponenter:

![Blobb-arkitektur](./media/storage-blobs-introduction/blob1.png)

* **Lagringskonto:** All åtkomst till Azure Storage görs genom ett lagringskonto. Det här lagringskontot kan vara ett **Allmänt lagringskonto (v1 eller v2)** eller ett **Blob-lagringskonto**. Mer information finns i [Om Azure Storage-konton](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

* **Behållare:** En behållare grupperar en uppsättning blobbar. Alla blobbar måste vara i en behållare. Ett konto kan innehålla ett obegränsat antal behållare. En behållare kan lagra ett obegränsat antal blobbar. Observera att behållarens namn får innehålla endast gemener.

* **Blob:** en fil av valfri typ och storlek. Azure Storage erbjuder tre typer av blobar: blockblobar, [sidblobar](storage-blob-pageblob-overview.md) och tilläggsblobar.
  
    *Blockblobbar* lämpar sig för lagring av text eller binära filer, exempelvis dokument och mediafiler. *Tilläggsblobbar* liknar blockblobbar i det avseendet att de består av block, men de är optimerade för tilläggsåtgärder, så de lämpar sig väl för loggningsscenarier. En enda blockblobb kan innehålla upp till 50 000 block med upp till 100 MB vardera, vilket ger en total storlek på lite över 4,75 TB (100 MB X 50 000). En enda tilläggsblobb kan innehålla upp till 50 000 block med upp till 4 MB vardera, vilket ger en total storlek på lite över 195 GB (4 MB X 50 000).
  
    *Sidblobar* kan vara upp till 8 TB stora och är bäst för frekventa läs-/skrivåtgärder. Azure Virtual Machines använder sig av sidblobbar som operativsystem- och datadiskar.
  
    Mer information om namngivning av behållare och blobbar finns i [Namngivning och referens av behållare, blobbar och metadata](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).

## <a name="next-steps"></a>Nästa steg

* [Skapa ett lagringskonto](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Kom igång med bloblagring med .NET](storage-dotnet-how-to-use-blobs.md)
* [Azure Storage-exempel med .NET](../common/storage-samples-dotnet.md)
* [Azure Storage-exempel med Java](../common/storage-samples-java.md)
