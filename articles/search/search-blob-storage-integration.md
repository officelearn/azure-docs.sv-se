---
title: Lägg till fullständig texts ökning i Azure Blob Storage – Azure Search
description: Crawla text innehåll i Azure Blob Storage för Azure Search indexering i kod med hjälp av HTTP-REST API.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/01/2019
author: mgottein
manager: nitinme
ms.author: magottei
ms.custom: seodec2018
ms.openlocfilehash: f0801931b57302ae1d627dab783a40d2407c19ac
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650090"
---
# <a name="searching-blob-storage-with-azure-search"></a>Söka i Blob Storage med Azure Search

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

## <a name="quick-start"></a>Snabbstart
Azure Search kan läggas till i blobbar direkt från sidan Blob Storage Portal.

![](./media/search-blob-storage-integration/blob-blade.png)

Klicka på **Lägg till Azure Search** för att starta ett flöde där du kan välja en befintlig Azure Search tjänst eller skapa en ny tjänst. Om du skapar en ny tjänst navigerar du till ditt lagrings kontos Portal upplevelse. Du kan gå tillbaka till sidan Storage Portal och sedan välja alternativet **Lägg till Azure Search** där du kan välja den befintliga tjänsten.

## <a name="next-steps"></a>Nästa steg
Läs mer om Azure Search BLOB-indexeraren i den fullständiga [dokumentationen](https://aka.ms/azsblobindexer).
