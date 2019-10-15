---
title: Lägg till fullständig texts ökning i Azure Blob Storage
titleSuffix: Azure Search
description: Extrahera innehåll och Lägg till strukturen i Azure-blobbar när du skapar ett fullständigt texts öknings index i Azure Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 1aec65ab08cd1c0711e51a222a8e674ef56ef508
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2019
ms.locfileid: "72312197"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-search"></a>Lägg till fullständig texts ökning i Azure blob-data med Azure Search

Det kan vara svårt att söka igenom olika innehålls typer som lagras i Azure Blob Storage. Du kan dock indexera och söka i innehållet i dina blobbar på bara några få klick med [Azure Search](search-what-is-azure-search.md). Azure Search har inbyggd integrering för indexering av blob-lagring via en [*BLOB-indexerare*](search-howto-indexing-azure-blob-storage.md) som lägger till data käll känsliga funktioner för indexering.

## <a name="supported-content-types"></a>Innehålls typer som stöds

Genom att köra en BLOB-indexerare över en behållare kan du extrahera text och metadata från följande innehålls typer med en enda fråga:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

Du kan också bifoga AI- [berikning](search-blob-ai-integration.md) i form av en *färdigheter* för att skapa ny information och struktur från blobbar, inklusive text representationer av bildfiler. Genom att lägga till AI-anrikning expanderar du innehålls typ listan så att den innehåller JPEG och PNG. Den lägger till bild bearbetnings färdigheter som [optisk tecken läsning (OCR)](cognitive-search-skill-ocr.md) och identifiering av [visuella funktioner](cognitive-search-skill-image-analysis.md) som gör det möjligt att indexera det visuella innehållet som finns i varje bild.

## <a name="search-through-your-blob-metadata"></a>Sök igenom dina BLOB-metadata
Ett vanligt scenario som gör det enkelt att sortera genom blobbar av vilken innehålls typ som helst är att indexera både anpassade metadata och system egenskaper för varje blob. På så sätt indexeras information för alla blobbar oavsett dokument typ. Sedan kan du gå vidare till sortera, filtrera och fasett över allt Blob Storage-innehåll.

[Läs mer om att indexera BLOB-metadata.](https://aka.ms/azsblobmetadataindexing)

## <a name="index-and-search-through-json-blobs"></a>Indexera och Sök via JSON-blobbar
Azure Search kan konfigureras för att extrahera strukturerat innehåll som finns i blobbar som innehåller JSON. Azure Search kan läsa JSON-blobbar och parsa det strukturerade innehållet i lämpliga fält i ett Azure Search dokument. Azure Search kan också ta blobbar som innehåller en matris med JSON-objekt och mappa varje element till ett separat Azure Search-dokument.

JSON-parsning kan för närvarande inte konfigureras via portalen. [Läs mer om JSON-parsning i Azure Search.](https://aka.ms/azsjsonblobindexing)

## <a name="quickstart"></a>Snabbstart
Azure Search kan läggas till i blobbar direkt från sidan Blob Storage Portal.

![](./media/search-blob-storage-integration/blob-blade.png)

Klicka på **Lägg till Azure Search** för att starta ett flöde där du kan välja en befintlig Azure Search tjänst eller skapa en ny tjänst. Om du skapar en ny tjänst navigerar du till ditt lagrings kontos Portal upplevelse. Du kan gå tillbaka till sidan Storage Portal och sedan välja alternativet **Lägg till Azure Search** där du kan välja den befintliga tjänsten.

## <a name="next-steps"></a>Nästa steg
Läs mer om Azure Search BLOB-indexeraren i den fullständiga [dokumentationen](https://aka.ms/azsblobindexer).
