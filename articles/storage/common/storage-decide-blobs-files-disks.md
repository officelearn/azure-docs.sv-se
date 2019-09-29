---
title: Bestämma när du ska använda Azure-blobbar, Azure Files eller Azure-diskar
description: Lär dig mer om olika sätt att lagra och få åtkomst till data i Azure för att hjälpa dig att avgöra vilken teknik som ska användas.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 4b1a42e25a6d8c7b4a3c24dffcb858ffe63dd10b
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671035"
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Bestämma när du ska använda Azure-blobbar, Azure Files eller Azure-diskar

Microsoft Azure innehåller flera funktioner i Azure Storage för att lagra och komma åt dina data i molnet. Den här artikeln beskriver Azure Files, blobbar och diskar och har utformats för att hjälpa dig att välja mellan de här funktionerna.

## <a name="scenarios"></a>Scenarier

I följande tabell jämförs filer, blobbar och diskar, och du kan se vilka exempel scenarier som är lämpliga för var och en.

| Funktion | Beskrivning | När du ska använda detta |
|--------------|-------------|-------------|
| **Azure Files** | Tillhandahåller ett SMB-gränssnitt, klient bibliotek och ett [rest-gränssnitt](/rest/api/storageservices/file-service-rest-api) som ger åtkomst från var som helst till lagrade filer. | Du vill "lyfta och byta" ett program till molnet som redan använder inbyggda API: er för fil system för att dela data mellan dem och andra program som körs i Azure.<br/><br/>Du vill lagra utvecklings-och fel söknings verktyg som behöver nås från många virtuella datorer. |
| **Azure-blobbar** | Tillhandahåller klient bibliotek och ett [rest-gränssnitt](/rest/api/storageservices/blob-service-rest-api) som tillåter att ostrukturerade data lagras och används i en enorm skala i block-blobbar.<br/><br/>Stöder också [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) för företags lösningar för Big data analys. | Du vill att ditt program ska stödja strömnings-och slumpmässiga åtkomst scenarier.<br/><br/>Du vill kunna komma åt program data var som helst.<br/><br/>Du vill skapa ett företags data Lake på Azure och utföra stor data analys. |
| **Azure Disks** | Tillhandahåller klient bibliotek och ett [rest-gränssnitt](/rest/api/compute/manageddisks/disks/disks-rest-api) som gör att data kan lagras permanent och nås från en ansluten virtuell hård disk. | Du vill lyfta och växla program som använder inbyggda API: er för fil system för att läsa och skriva data till beständiga diskar.<br/><br/>Du vill lagra data som inte behöver nås från utanför den virtuella dator som disken är ansluten till. |


## <a name="next-steps"></a>Nästa steg

När du fattar beslut om hur dina data lagras och används bör du också tänka på vilka kostnader som ingår. Mer information finns i [Azure Storage prissättning](https://azure.microsoft.com/pricing/details/storage/).
  
Vissa SMB-funktioner kan inte användas i molnet. Mer information finns i [funktioner som inte stöds av Azure File Service](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).
 
Mer information om Azure-blobar finns i vår artikel, [Vad är Azure Blob Storage?](../blobs/storage-blobs-overview.md).

Mer information om Disklagring finns i [Introduktion till Managed disks](../../virtual-machines/windows/managed-disks-overview.md).

Mer information om Azure Files finns i vår artikel, [Planera för en Azure Files distribution](../files/storage-files-planning.md).