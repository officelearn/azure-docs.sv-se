---
title: Lägga till Azure Search till Blob Storage | Microsoft Docs
description: Skapa ett index med kod med hjälp av HTTP REST-API:et för Azure Search.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/04/2017
author: chaosrealm
manager: jlembicz
ms.author: eugenesh
ms.openlocfilehash: 71e43920f0e6a64beb7cdb28d0707dd30502bf05
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="searching-blob-storage-with-azure-search"></a>Söka i Blob Storage med Azure Search

Att söka i olika typer av innehåll lagras i Azure Blob storage kan vara svårt att lösa problem. Du kan dock index och söker igenom innehållet i dina Blobbar i bara några klickningar med hjälp av Azure Search. Sökning i Blob storage kräver etablering av en Azure Search-tjänst. De olika tjänstbegränsningarna och prisnivåer för Azure Search kan hittas på den [sida med priser](https://aka.ms/azspricing).

## <a name="what-is-azure-search"></a>Vad är Azure Search?
[Azure Search](https://aka.ms/whatisazsearch) är en search-tjänst som gör det lätt för utvecklare att lägga till robust fulltextsökning inträffar webb- och mobilprogram. Som en tjänst Azure Search eliminerar behovet av att hantera all Sök infrastruktur när erbjudande en [99,9% drifttid SLA](https://aka.ms/azuresearchsla).

## <a name="index-and-search-enterprise-document-formats"></a>Index och Sök enterprise dokumentets format
Med stöd för [dokumentera extrahering](https://aka.ms/azsblobindexer) i Azure Blob storage kan du indexera följande innehåll:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

Genom att extrahera text och metadata från de här filtyperna, kan du söka i flera filformat med en enskild fråga. 

## <a name="search-through-your-blob-metadata"></a>Söka igenom blobbmetadata
Ett vanligt scenario som gör det enkelt att gå igenom BLOB för content-type är att indexera både anpassade metadata och Systemegenskaper för varje blob. På så sätt kan indexeras information för alla BLOB oavsett dokumenttyp. Du kan fortsätta att sortera, filtrera och aspekten över allt innehåll för Blob-lagring.

[Lär dig mer om indexering blob-metadata.](https://aka.ms/azsblobmetadataindexing)

## <a name="image-search"></a>Image-sökning
Azure Search fulltextsökning fasetterad navigering funktioner, och sortera går nu att använda metadata för bilder som är lagrade i blobar.

Om dessa avbildningar bearbetas före med den [datorn Vision API](https://www.microsoft.com/cognitive-services/computer-vision-api) från Microsofts kognitiva Services, är det möjligt att indexera visuellt innehåll finns i varje avbildning inklusive OCR och handskriftsigenkänning. Vi arbetar på att lägga till OCR och andra funktioner för bearbetning av avbildningen direkt till Azure Search om du är intresserad av dessa funktioner kan du skicka en begäran för på vår [UserVoice](https://aka.ms/azsuv) eller [mejla oss](mailto:azscustquestions@microsoft.com).

## <a name="index-and-search-through-json-blobs"></a>Index och sökning i JSON-blobbar
Azure Search kan konfigureras för att extrahera strukturerat innehåll finns i BLOB som innehåller JSON. Azure Search kan läsa JSON-blobbar och parsa strukturerat innehåll till relevanta fält i ett Azure Search-dokument. Azure Search kan också dra blobbar som innehåller en matris av JSON-objekt och mappa varje element till ett separat Azure Search-dokument.

Parsning av JSON är inte för närvarande kan konfigureras via portalen. [Läs mer om JSON parsning i Azure Search.](https://aka.ms/azsjsonblobindexing)

## <a name="quick-start"></a>Snabbstart
Azure Search kan läggas till blobbar direkt från Portalsida för Blob storage.

![](./media/search-blob-storage-integration/blob-blade.png)

Klicka på **lägga till Azure Search** att starta ett flöde där du kan välja en befintlig Azure Search-tjänst eller skapa en ny tjänst. Om du skapar en ny tjänst, navigerar du utanför ditt lagringskonto-portaler. Du kan gå tillbaka till sidan lagring företagsportal och välj nytt den **lägga till Azure Search** alternativet, där du kan välja den befintliga tjänsten.

### <a name="next-steps"></a>Nästa steg
Lär dig mer om Azure Blob sökindexeraren i hela [dokumentationen](https://aka.ms/azsblobindexer).
