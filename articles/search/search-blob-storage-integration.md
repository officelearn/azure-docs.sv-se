---
title: Lägg till fulltextsökning i Azure Blob Storage - Azure Search
description: Crawlningen textinnehåll i Azure Blob storage för Azure Search indexering i kod med hjälp av HTTP REST API.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 10/17/2018
author: mgottein
manager: cgronlun
ms.author: magottei
ms.custom: seodec2018
ms.openlocfilehash: 7bd7bcf66f0d91a87519a5d5bff7df7c73e2603f
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310886"
---
# <a name="searching-blob-storage-with-azure-search"></a>Söka i Blob Storage med Azure Search

Sökningen på olika typer av innehåll lagras i Azure Blob storage kan vara svårt att lösa problem. Du kan dock index och söker igenom innehållet i din Blobar med bara några få klick med hjälp av Azure Search. Sökning över Blob-lagring kräver att etablera en Azure Search-tjänst. De olika tjänstbegränsningar och prisnivåer för Azure Search finns på den [prissättningssidan](https://aka.ms/azspricing).

## <a name="what-is-azure-search"></a>Vad är Azure Search?
[Azure Search](https://aka.ms/whatisazsearch) är en sökning-tjänst som gör det enkelt för utvecklare att lägga till robusta fulltextsökning inträffar i webbprogram och mobilappar. Som en tjänst, Azure Search eliminerar behovet av att hantera search infrastruktur samtidigt erbjudande en [99,9% drifttid](https://aka.ms/azuresearchsla).

## <a name="index-and-search-enterprise-document-formats"></a>Index- och enterprise-dokumentformat
Med stöd för [dokumentera extrahering](https://aka.ms/azsblobindexer) i Azure Blob storage kan du indexera följande innehåll:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

Genom att extrahera text och metadata från de här filtyperna, kan du söka i flera format med en enda fråga. 

## <a name="search-through-your-blob-metadata"></a>Söka igenom din blob-metadata
Ett vanligt scenario som gör det enkelt att gå igenom blobar från alla innehållstypen är att indexera både anpassade metadata och Systemegenskaper för varje blob. På så sätt kan indexeras information för alla blobbar oavsett dokumenttyp. Du kan fortsätta att sortera, filtrera och aspekten över allt innehåll för Blob storage.

[Lär dig mer om indexering blob-metadata.](https://aka.ms/azsblobmetadataindexing)

## <a name="image-search"></a>Bildsökning
Azure Search fulltextsökning, fasetterad navigering och sortering funktioner kan nu tillämpas på metadata för bilder som lagras i blobbar.

Om dessa avbildningar bearbetas före med den [API för visuellt innehåll](https://www.microsoft.com/cognitive-services/computer-vision-api) från Microsofts Cognitive Services, är det möjligt att indexera den visuellt innehåll som hittas i varje avbildning, inklusive OCR-och handskriftsigenkänning. Vi arbetar på att lägga till OCR och andra funktioner för bearbetning av avbildning direkt till Azure Search, om du är intresserad av dessa funktioner du skicka en begäran för på vår [UserVoice](https://aka.ms/azsuv) eller [mejla oss](mailto:azscustquestions@microsoft.com).

## <a name="index-and-search-through-json-blobs"></a>Index och Sök igenom JSON-blobar
Azure Search kan konfigureras för att extrahera strukturerad innehåll som hittas i BLOB-objekt som innehåller JSON. Azure Search kan läsa JSON-blobar och parsa strukturerade innehållet i lämpliga fält i ett Azure Search-dokument. Azure Search kan även dra BLOB-objekt som innehåller en matris av JSON-objekt och mappa varje element till ett separat Azure Search-dokument.

JSON-parsning kan inte för närvarande konfigureras via portalen. [Läs mer om JSON-parsning i Azure Search.](https://aka.ms/azsjsonblobindexing)

## <a name="quick-start"></a>Snabbstart
Azure Search kan läggas till BLOB-direkt från Blob storage-portalsidan.

![](./media/search-blob-storage-integration/blob-blade.png)

Klicka på **Lägg till Azure Search** att starta ett flöde där du kan välja en befintlig Azure Search-tjänst eller skapa en ny tjänst. Om du skapar en ny tjänst är du navigerat utanför portalen för ditt lagringskonto. Du kan gå tillbaka till sidan Storage portal och välj nytt den **Lägg till Azure Search** alternativet, där du kan välja den befintliga tjänsten.

### <a name="next-steps"></a>Nästa steg
Läs mer om Azure Search Blob-indexeraren med fullständiga [dokumentation](https://aka.ms/azsblobindexer).
