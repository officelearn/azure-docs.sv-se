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
ms.openlocfilehash: 6d8ad27766e85568cc759af84cc586476ad3ef9a
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678440"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-search"></a>Lägg till fullständig texts ökning i Azure blob-data med Azure Search

Det kan vara svårt att söka igenom olika innehålls typer som lagras i Azure Blob Storage. Du kan dock indexera och söka i innehållet i dina blobbar på bara några få klick med [Azure Search](search-what-is-azure-search.md). Azure Search har inbyggd integrering för indexering av blob-lagring via en [*BLOB-indexerare*](search-howto-indexing-azure-blob-storage.md) som lägger till data käll känsliga funktioner för indexering.

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>Det innebär att du kan lägga till full texts ökning till BLOB-data

Azure Search är en moln Sök tjänst som tillhandahåller indexerings-och sökmotorer som använder användardefinierade index som finns på din Sök tjänst. Att samplacera ditt sökbara innehåll med frågemotor i molnet är nödvändigt för prestanda och returnerar resultat till en hastighet som användare har förväntat sig från Sök frågor.

Azure Search integreras med Azure Blob Storage i indexerings skiktet, så att du kan importera ditt BLOB-innehåll som sökdokument som är indexerade i *inverterade index* och andra fråge strukturer som stöder fritext frågor och filter uttryck. Eftersom ditt BLOB-innehåll är indexerat i ett sökindex, kan åtkomst till BLOB-innehåll utnyttja hela den mängd olika fråge funktioner i Azure Search.

När indexet har skapats och fyllts i finns det oberoende av BLOB-behållaren, men du kan köra om indexerings åtgärder för att uppdatera indexet med ändringar i den underliggande behållaren. Tidsstämpel-information om enskilda blobbar används för ändrings identifiering. Du kan välja antingen schemalagd körning eller indexering på begäran som uppdaterings metod.

Indata är dina blobbar i en enda behållare i Azure Blob Storage. Blobbar kan vara nästan alla typer av text data. Om Blobbarna innehåller bilder kan du lägga till [AI-anrikning till BLOB-indexering](search-blob-ai-integration.md) för att skapa och extrahera text från bilder.

Utdata är alltid ett Azure Search-index som används för snabb texts ökning, hämtning och utforskning i klient program. I mellan är själva arkitekturen för indexerings pipelinen. Pipelinen baseras på *indexerings* funktionen, som beskrivs i den här artikeln.

## <a name="start-with-services"></a>Starta med tjänster

Du behöver Azure Search och Azure Blob Storage. I Blob Storage behöver du en behållare som tillhandahåller käll innehåll.

Du kan starta direkt på din Portal sida för lagrings konto. På den vänstra navigerings sidan under **BLOB service** klickar du på **Lägg till Azure Search** för att skapa en ny tjänst eller välja en befintlig. 

När du lägger till Azure Search i ditt lagrings konto kan du följa standard processen för att indexera BLOB-data. Vi rekommenderar guiden **Importera data** i Azure Search för en enkel första introduktion eller anropa REST-API: er med hjälp av ett verktyg som Postman. I den här självstudien får du stegvisa anvisningar om hur du anropar REST API i Postman: [index och söker i semi-strukturerade data (JSON-blobbar) i Azure Search](search-semi-structured-data.md). 

## <a name="use-a-blob-indexer"></a>Använda en BLOB-indexerare

En *indexerare* är en data källa medveten under tjänst som är utrustad med intern logik för att sampla data, läsa metadata, hämta data och serialisera data från interna format till JSON-dokument för efterföljande import. 

Blobbar i Azure Storage indexeras med hjälp av [Azure Search Blob Storage-indexeraren](search-howto-indexing-azure-blob-storage.md). Du kan anropa denna indexerare med hjälp av guiden **Importera data** , en REST API eller .NET SDK. I kod använder du denna indexerare genom att ange typ och genom att tillhandahålla anslutnings information som innehåller ett Azure Storage konto tillsammans med en BLOB-behållare. Du kan ange en delmängd av Blobbarna genom att skapa en virtuell katalog som du sedan kan skicka som en parameter eller genom att filtrera efter fil typs tillägg.

En indexerare gör "dokument sprickor" och öppnar en BLOB för att granska innehåll. När du har anslutit till data källan är det första steget i pipelinen. För BLOB-data är detta var PDF, Office-dokument och andra innehålls typer identifieras. Dokument sprickor med text extrahering är ingen kostnad. Om dina blobbar innehåller bild innehåll, ignoreras bilderna om du inte [lägger till AI-berikning](search-blob-ai-integration.md). Standard indexering gäller endast för text innehåll.

BLOB-indexeraren innehåller konfigurations parametrar och stöder ändrings spårning om underliggande data innehåller tillräckligt med information. Du kan lära dig mer om kärn funktionerna i [Azure Search Blob Storage-indexeraren](search-howto-indexing-azure-blob-storage.md).

### <a name="supported-content-types"></a>Innehålls typer som stöds

Genom att köra en BLOB-indexerare över en behållare kan du extrahera text och metadata från följande innehålls typer med en enda fråga:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>Indexera BLOB-metadata

Ett vanligt scenario som gör det enkelt att sortera genom blobbar av vilken innehålls typ som helst är att indexera både anpassade metadata och system egenskaper för varje blob. På så sätt indexeras informationen för alla blobbar oavsett dokument typ, som lagras i ett index på Azure Search. Med ditt nya index kan du fortsätta att sortera, filtrera och fasett över allt Blob Storage-innehåll.

### <a name="indexing-json-blobs"></a>Indexera JSON-blobbar
Azure Search kan konfigureras för att extrahera strukturerat innehåll som finns i blobbar som innehåller JSON. Azure Search kan läsa JSON-blobbar och parsa det strukturerade innehållet i lämpliga fält i ett Azure Search dokument. Azure Search kan också ta blobbar som innehåller en matris med JSON-objekt och mappa varje element till ett separat Azure Search-dokument. Du kan ställa in ett tolknings läge för att påverka typen av JSON-objekt som skapas av indexeraren.

## <a name="search-blob-content-in-a-search-index"></a>Sök i BLOB-innehåll i ett Sök index 

Utdata från en indexering är ett sökindex som används för interaktiv utforskning med fri text och filtrerade frågor i en klient app. För inledande utforskning och kontroll av innehåll rekommenderar vi att du börjar med [Sök Utforskaren](search-explorer.md) i portalen för att undersöka dokument strukturen. Du kan använda [enkel](query-simple-syntax.md)frågesyntax, [fullständig frågesyntax](query-lucene-syntax.md)och [syntax för filter uttryck](query-odata-filter-orderby-syntax.md) i Sök Utforskaren.

En permanent lösning är att samla in indata från frågor och presentera svaret som Sök resultat i ett klient program. I följande C# självstudie förklaras hur du skapar ett sökprogram: [skapa ditt första program i Azure Search](tutorial-csharp-create-first-app.md).

## <a name="next-steps"></a>Nästa steg

+ [Ladda upp, ladda ned och lista blobar med Azure Portal (Azure Blob Storage)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [Konfigurera en BLOB-indexerare (Azure Search)](search-howto-indexing-azure-blob-storage.md) 
