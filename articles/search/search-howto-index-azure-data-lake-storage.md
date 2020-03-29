---
title: Sök över Azure Data Lake Storage Gen2 (förhandsversion)
titleSuffix: Azure Cognitive Search
description: Lär dig hur du indexerar innehåll och metadata i Azure Data Lake Storage Gen2. Den här funktionen är för närvarande i offentlig förhandsversion
manager: nitinme
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4b725c8a1bf0649a640c02a9a1828ec9014d36d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905657"
---
# <a name="indexing-documents-in-azure-data-lake-storage-gen2"></a>Indexera dokument i Azure Data Lake Storage Gen2

> [!IMPORTANT] 
> Azure Data Lake Storage Gen2-stöd är för närvarande i offentlig förhandsversion. Förhandsversionsfunktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Du kan begära åtkomst till förhandsgranskningarna genom att fylla [i det här formuläret](https://aka.ms/azure-cognitive-search/indexer-preview). [REST API-versionen 2019-05-06-Preview](search-api-preview.md) innehåller den här funktionen. Det finns för närvarande inget stöd för portal eller .NET SDK.


När du konfigurerar ett Azure-lagringskonto har du möjlighet att aktivera [hierarkiskt namnområde](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace). På så sätt kan insamlingen av innehåll i ett konto ordnas i en hierarki av kataloger och kapslade underkataloger. Genom att aktivera hierarkiskt namnområde aktiverar du [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction).

I den här artikeln beskrivs hur du kommer igång med indexering av dokument som finns i Azure Data Lake Storage Gen2.

## <a name="set-up-azure-data-lake-storage-gen2-indexer"></a>Konfigurera Azure Data Lake Storage Gen2-indexerare

Det finns några steg som du måste slutföra för att indexera innehåll från Data Lake Storage Gen2.

### <a name="step-1-sign-up-for-the-preview"></a>Steg 1: Registrera dig för förhandsgranskningen

Registrera dig för förhandsversionen av Indexeraren för Data Lake Storage Gen2 genom att fylla [i det här formuläret](https://aka.ms/azure-cognitive-search/indexer-preview). Du kommer att få en bekräftelse via e-post när du har godkänts i förhandsgranskningen.

### <a name="step-2-follow-the-azure-blob-storage-indexing-setup-steps"></a>Steg 2: Följ installationsstegen för Azure Blob-lagringsindexering

När du har fått en bekräftelse på att din förhandsanmälning har lyckades är du redo att skapa indexeringspipelinen.

Du kan indexera innehåll och metadata från Data Lake Storage Gen2 med hjälp av [REST API-versionen 2019-05-06-Preview](search-api-preview.md). Det finns för närvarande inget stöd för portalen eller .NET SDK.

Indexering av innehåll i Data Lake Storage Gen2 är identiskt med indexering av innehåll i Azure Blob-lagring. Så för att förstå hur du ställer in datakällan, indexet och indexeraren Datasjölagring Gen2 finns i Så här indexerar du [dokument i Azure Blob Storage med Azure Cognitive Search](search-howto-indexing-azure-blob-storage.md). Blob-lagringsartikeln innehåller också information om vilka dokumentformat som stöds, vilka blobmetadataegenskaper som extraheras, inkrementell indexering med mera. Den här informationen kommer att vara densamma för Data Lake Storage Gen2.

## <a name="access-control"></a>Åtkomstkontroll

Azure Data Lake Storage Gen2 implements an [access control model](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control) that supports both Azure role-based access control (RBAC) and POSIX-like access control lists (ACLs). När du indexerar innehåll från Data Lake Storage Gen2 extraherar Azure Cognitive Search inte RBAC- och ACL-informationen från innehållet. Därför inkluderas inte den här informationen i ditt Azure Cognitive Search-index.

Om det är viktigt att underhålla åtkomstkontrollen för varje dokument i indexet är det upp till programutvecklaren att implementera [säkerhetstrimning](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search).

## <a name="change-detection"></a>Ändra identifiering

Indexeraren Data Lake Storage Gen2 stöder ändringsidentifiering. Det innebär att när indexeraren kör det bara indexerar om de `LastModified` ändrade blobbar som bestäms av blobens tidsstämpel.

> [!NOTE] 
> Data Lake Storage Gen2 gör att kataloger kan döpas om. När en katalog har bytt namn uppdateras inte tidsstämplarna för blobbar i den katalogen. Det innebär att indexeraren inte indexerar om dessa blobbar. Om du behöver blobbar i en katalog som ska indexeras om efter ett katalogbyte eftersom `LastModified` de nu har nya webbadresser, måste du uppdatera tidsstämpeln för alla blobbar i katalogen så att indexeraren vet att indexera om dem under en framtida körning.
