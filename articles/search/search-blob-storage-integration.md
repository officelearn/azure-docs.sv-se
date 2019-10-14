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
ms.openlocfilehash: 7e5eb73cc6abc72689bbc674b29f4d288dd66b6f
ms.sourcegitcommit: bd4198a3f2a028f0ce0a63e5f479242f6a98cc04
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2019
ms.locfileid: "72302903"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-search"></a>Lägg till fullständig texts ökning i Azure blob-data med Azure Search

Det kan vara svårt att söka igenom olika innehålls typer som lagras i Azure Blob Storage. Du kan dock indexera och söka i innehållet i dina blobbar på bara några få klick med Azure Search. Att söka via Blob Storage kräver etablering av en Azure Search-tjänst. De olika tjänst gränserna och pris nivåerna för Azure Search hittar du på [sidan med priser](https://aka.ms/azspricing).

## <a name="what-is-azure-search"></a>Vad är Azure Search?
[Azure Search](https://aka.ms/whatisazsearch) är en Sök tjänst som gör det enkelt för utvecklare att lägga till robusta full texts öknings upplevelser till webb-och mobil program. Som en tjänst tar Azure Search bort behovet av att hantera en Sök infrastruktur och erbjuder ett [service avtal på 99,9% drift tid](https://aka.ms/azuresearchsla).

## <a name="index-and-search-enterprise-document-formats"></a>Indexera och Sök i företags dokument format
Med stöd för [dokument extrahering](https://aka.ms/azsblobindexer) i Azure Blob Storage kan du indexera följande innehåll:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

Genom att extrahera text och metadata från dessa filtyper kan du söka i flera fil format med en enda fråga. 

## <a name="search-through-your-blob-metadata"></a>Sök igenom dina BLOB-metadata
Ett vanligt scenario som gör det enkelt att sortera genom blobbar av vilken innehålls typ som helst är att indexera både anpassade metadata och system egenskaper för varje blob. På så sätt indexeras information för alla blobbar oavsett dokument typ. Sedan kan du gå vidare till sortera, filtrera och fasett över allt Blob Storage-innehåll.

[Läs mer om att indexera BLOB-metadata.](https://aka.ms/azsblobmetadataindexing)

## <a name="image-search"></a>Bilds ökning
Azure Search full texts ökning, fasett-navigering och sorterings funktioner kan nu användas för metadata för avbildningar som lagras i blobbar.

Kognitiv sökning omfattar bild bearbetnings färdigheter som [optisk tecken läsning (OCR)](cognitive-search-skill-ocr.md) och identifiering av [visuella funktioner](cognitive-search-skill-image-analysis.md) som gör det möjligt att indexera det visuella innehållet som finns i varje bild.

## <a name="index-and-search-through-json-blobs"></a>Indexera och Sök via JSON-blobbar
Azure Search kan konfigureras för att extrahera strukturerat innehåll som finns i blobbar som innehåller JSON. Azure Search kan läsa JSON-blobbar och parsa det strukturerade innehållet i lämpliga fält i ett Azure Search dokument. Azure Search kan också ta blobbar som innehåller en matris med JSON-objekt och mappa varje element till ett separat Azure Search-dokument.

JSON-parsning kan för närvarande inte konfigureras via portalen. [Läs mer om JSON-parsning i Azure Search.](https://aka.ms/azsjsonblobindexing)

## <a name="quickstart"></a>Snabbstart
Azure Search kan läggas till i blobbar direkt från sidan Blob Storage Portal.

![](./media/search-blob-storage-integration/blob-blade.png)

Klicka på **Lägg till Azure Search** för att starta ett flöde där du kan välja en befintlig Azure Search tjänst eller skapa en ny tjänst. Om du skapar en ny tjänst navigerar du till ditt lagrings kontos Portal upplevelse. Du kan gå tillbaka till sidan Storage Portal och sedan välja alternativet **Lägg till Azure Search** där du kan välja den befintliga tjänsten.

## <a name="next-steps"></a>Nästa steg
Läs mer om Azure Search BLOB-indexeraren i den fullständiga [dokumentationen](https://aka.ms/azsblobindexer).
