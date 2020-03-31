---
title: Bestämma när Azure Blobbar, Azure-filer eller Azure-diskar ska användas
description: Lär dig mer om olika sätt att lagra och komma åt data i Azure för att hjälpa dig att bestämma vilken teknik som ska användas.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 4b1a42e25a6d8c7b4a3c24dffcb858ffe63dd10b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71671035"
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Bestämma när Azure Blobbar, Azure-filer eller Azure-diskar ska användas

Microsoft Azure innehåller flera funktioner i Azure Storage för lagring och åtkomst till dina data i molnet. Den här artikeln innehåller Azure-filer, blobbar och diskar och är utformad för att hjälpa dig att välja mellan dessa funktioner.

## <a name="scenarios"></a>Scenarier

I följande tabell jämförs Filer, Blobbar och Diskar och visar exempelscenarier som är lämpliga för varje.

| Funktion | Beskrivning | När du ska använda detta |
|--------------|-------------|-------------|
| **Azure-filer** | Tillhandahåller ett SMB-gränssnitt, klientbibliotek och ett [REST-gränssnitt](/rest/api/storageservices/file-service-rest-api) som ger åtkomst från var som helst till lagrade filer. | Du vill "lyfta och flytta" ett program till molnet som redan använder de inbyggda api:erna för filsystem för att dela data mellan det och andra program som körs i Azure.<br/><br/>Du vill lagra utvecklings- och felsökningsverktyg som måste nås från många virtuella datorer. |
| **Azure-blobar** | Tillhandahåller klientbibliotek och ett [REST-gränssnitt](/rest/api/storageservices/blob-service-rest-api) som gör att ostrukturerade data kan lagras och nås i stor skala i blockblobar.<br/><br/>Stöder även [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) för lösningar för stordataanalys för företag. | Du vill att ditt program ska stödja strömmande och slumpmässiga åtkomstscenarier.<br/><br/>Du vill kunna komma åt programdata var som helst.<br/><br/>Du vill skapa en företagsdatasjö på Azure och utföra stordataanalys. |
| **Azure-diskar** | Tillhandahåller klientbibliotek och ett [REST-gränssnitt](/rest/api/compute/manageddisks/disks/disks-rest-api) som gör att data kan lagras och kommas åt från en ansluten virtuell hårddisk. | Du vill lyfta och flytta program som använder inbyggda api:er för filsystem för att läsa och skriva data till beständiga diskar.<br/><br/>Du vill lagra data som inte behöver nås från utanför den virtuella datorn som disken är ansluten till. |


## <a name="next-steps"></a>Nästa steg

När du fattar beslut om hur dina data lagras och nås bör du också ta hänsyn till kostnaderna. Mer information finns i [Azure Storage Pricing](https://azure.microsoft.com/pricing/details/storage/).
  
Vissa SMB-funktioner är inte tillämpliga på molnet. Mer information finns i [Funktioner som inte stöds av Azure File-tjänsten](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).
 
Mer information om Azure Blobbar finns i vår artikel, [Vad är Azure Blob-lagring?](../blobs/storage-blobs-overview.md).

Mer information om Disklagring finns i vår [Introduktion till hanterade diskar](../../virtual-machines/windows/managed-disks-overview.md).

Mer information om Azure-filer finns i vår [artikel, Planera för en Azure Files-distribution](../files/storage-files-planning.md).