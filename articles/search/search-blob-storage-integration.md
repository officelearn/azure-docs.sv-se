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
ms.openlocfilehash: 24886d8b8a7b679f6474789748a002c8d045a746
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331277"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-search"></a>Lägg till fullständig texts ökning i Azure blob-data med Azure Search

Det kan vara svårt att söka igenom olika innehålls typer som lagras i Azure Blob Storage. Du kan dock indexera och söka i innehållet i dina blobbar på bara några få klick med [Azure Search](search-what-is-azure-search.md). Azure Search har inbyggd integrering för indexering av blob-lagring via en [*BLOB-indexerare*](search-howto-indexing-azure-blob-storage.md) som lägger till data käll känsliga funktioner för indexering.

## <a name="supported-content-types"></a>Innehålls typer som stöds

Genom att köra en BLOB-indexerare över en behållare kan du extrahera text och metadata från följande innehålls typer med en enda fråga:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

Du kan också bifoga AI- [berikning](search-blob-ai-integration.md) i form av en *färdigheter* för att skapa ny information och struktur från blobbar, inklusive text representationer av bildfiler. Genom att lägga till AI-anrikning expanderar du innehålls typ listan så att den innehåller JPEG och PNG. Den lägger till bild bearbetnings färdigheter som [optisk tecken läsning (OCR)](cognitive-search-skill-ocr.md) och identifiering av [visuella funktioner](cognitive-search-skill-image-analysis.md) som gör det möjligt att indexera det visuella innehållet som finns i varje bild.

## <a name="search-through-your-blob-metadata"></a>Sök igenom dina BLOB-metadata
Ett vanligt scenario som gör det enkelt att sortera genom blobbar av vilken innehålls typ som helst är att indexera både anpassade metadata och system egenskaper för varje blob. På så sätt indexeras informationen för alla blobbar oavsett dokument typ, som lagras i ett index på Azure Search. Med ditt nya index kan du fortsätta att sortera, filtrera och fasett över allt Blob Storage-innehåll.

### <a name="indexing-json-blobs"></a>Indexera JSON-blobbar
Azure Search kan konfigureras för att extrahera strukturerat innehåll som finns i blobbar som innehåller JSON. Azure Search kan läsa JSON-blobbar och parsa det strukturerade innehållet i lämpliga fält i ett Azure Search dokument. Azure Search kan också ta blobbar som innehåller en matris med JSON-objekt och mappa varje element till ett separat Azure Search-dokument. Du kan ställa in ett tolknings läge för att påverka typen av JSON-objekt som skapas av indexeraren.

## <a name="how-to-get-started"></a>Så här kommer du igång

Du kan starta direkt på din Portal sida för lagrings konto. Klicka på **Lägg till Azure Search** för att starta ett flöde där du kan välja en befintlig Azure Search tjänst eller skapa en ny tjänst. Om du skapar en ny tjänst navigerar du till ditt lagrings kontos Portal upplevelse. Du kan gå tillbaka till sidan Storage Portal och sedan välja alternativet **Lägg till Azure Search** där du kan välja den befintliga tjänsten. 

![](./media/search-blob-storage-integration/blob-blade.png)

Om du redan har en befintlig Sök tjänst i samma prenumeration, klickar du på **Lägg till Azure Search** öppnar guiden Importera data så att du snabbt kan stega igenom indexerings-, beriknings-och index definition.

Du kan också [skapa en Azure Search-tjänst](search-create-index-portal.md) och definiera BLOB-indexerare som hämtar innehåll från BLOB-behållare.

Följande snabb starter och självstudier använder BLOB-data:

+ [Indexera halv strukturerade blobbar med REST API: er](search-semi-structured-data.md)
+ [Skapa en pipeline för AI-anrikning i portalen](cognitive-search-quickstart-blob.md)
+ [Skapa en pipeline för AI-anrikning iC#](cognitive-search-tutorial-blob-dotnet.md)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera en BLOB-indexerare](search-howto-indexing-azure-blob-storage.md) 
