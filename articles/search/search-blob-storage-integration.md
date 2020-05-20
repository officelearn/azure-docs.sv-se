---
title: Lägg till fullständig texts ökning i Azure Blob Storage
titleSuffix: Azure Cognitive Search
description: Extrahera innehåll och Lägg till struktur i Azure-blobbar när du skapar ett fullständigt texts öknings index i Azure Kognitiv sökning.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: fd405513f8bdef09e6d3ab996fc2c04bd397db13
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659434"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-cognitive-search"></a>Lägg till fullständig texts ökning i Azure blob-data med Azure Kognitiv sökning

Det kan vara svårt att söka igenom olika innehålls typer som lagras i Azure Blob Storage. Du kan dock indexera och söka i innehållet i dina blobbar på bara några få klick genom att använda [Azure kognitiv sökning](search-what-is-azure-search.md). Azure Kognitiv sökning har inbyggd integrering för indexering av blob-lagring via en [*BLOB-indexerare*](search-howto-indexing-azure-blob-storage.md) som lägger till data käll känsliga funktioner för indexering.

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>Det innebär att du kan lägga till full texts ökning till BLOB-data

Azure Kognitiv sökning är en moln Sök tjänst som tillhandahåller indexerings-och sökmotorer som använder användardefinierade index som finns på din Sök tjänst. Att samplacera ditt sökbara innehåll med frågemotor i molnet är nödvändigt för prestanda och returnerar resultat till en hastighet som användare har förväntat sig från Sök frågor.

Azure Kognitiv sökning integreras med Azure Blob Storage i indexerings skiktet, så att du kan importera ditt BLOB-innehåll som sökdokument som är indexerade i *inverterade index* och andra fråge strukturer som stöder text frågor för fritext och filter uttryck. Eftersom ditt BLOB-innehåll är indexerat i ett sökindex kan åtkomst till BLOB-innehåll utnyttja hela antalet fråge funktioner i Azure Kognitiv sökning.

När indexet har skapats och fyllts i finns det oberoende av BLOB-behållaren, men du kan köra om indexerings åtgärder för att uppdatera indexet med ändringar i den underliggande behållaren. Tidsstämpel-information om enskilda blobbar används för ändrings identifiering. Du kan välja antingen schemalagd körning eller indexering på begäran som uppdaterings metod.

Indata är dina blobbar i en enda behållare i Azure Blob Storage. Blobbar kan vara nästan alla typer av text data. Om Blobbarna innehåller bilder kan du lägga till [AI-anrikning till BLOB-indexering](search-blob-ai-integration.md) för att skapa och extrahera text från bilder.

Utdata är alltid ett Azure Kognitiv sökning-index som används för snabb texts ökning, hämtning och utforskning i klient program. I mellan är själva arkitekturen för indexerings pipelinen. Pipelinen baseras på *indexerings* funktionen, som beskrivs i den här artikeln.

## <a name="start-with-services"></a>Starta med tjänster

Du behöver Azure Kognitiv sökning och Azure Blob Storage. I Blob Storage behöver du en behållare som tillhandahåller käll innehåll.

Du kan starta direkt på din Portal sida för lagrings konto. På den vänstra navigerings sidan, under **BLOB service** klickar du på **Lägg till Azure-kognitiv sökning** för att skapa en ny tjänst eller välja en befintlig. 

När du har lagt till Azure-Kognitiv sökning till ditt lagrings konto kan du följa standard processen för att indexera BLOB-data. Vi rekommenderar guiden **Importera data** i Azure kognitiv sökning för en enkel inledande introduktion eller anropa REST-API: er med hjälp av ett verktyg som Postman. I den här självstudien får du stegvisa anvisningar om hur du anropar REST API i Postman: [index och söker i semi-strukturerade data (JSON-blobbar) i Azure kognitiv sökning](search-semi-structured-data.md). 

## <a name="use-a-blob-indexer"></a>Använda en BLOB-indexerare

En *indexerare* är en data källa medveten under tjänst som är utrustad med intern logik för att sampla data, läsa metadata, hämta data och serialisera data från interna format till JSON-dokument för efterföljande import. 

Blobbar i Azure Storage indexeras med hjälp av [Azure kognitiv sökning Blob Storage-indexeraren](search-howto-indexing-azure-blob-storage.md). Du kan anropa denna indexerare med hjälp av guiden **Importera data** , en REST API eller .NET SDK. I kod använder du denna indexerare genom att ange typ och genom att tillhandahålla anslutnings information som innehåller ett Azure Storage konto tillsammans med en BLOB-behållare. Du kan ange en delmängd av Blobbarna genom att skapa en virtuell katalog som du sedan kan skicka som en parameter eller genom att filtrera efter fil typs tillägg.

En indexerare gör "dokument sprickor" och öppnar en BLOB för att granska innehåll. När du har anslutit till data källan är det första steget i pipelinen. För BLOB-data är detta var PDF, Office-dokument och andra innehålls typer identifieras. Dokument sprickor med text extrahering är ingen kostnad. Om dina blobbar innehåller bild innehåll, ignoreras bilderna om du inte [lägger till AI-berikning](search-blob-ai-integration.md). Standard indexering gäller endast för text innehåll.

BLOB-indexeraren innehåller konfigurations parametrar och stöder ändrings spårning om underliggande data innehåller tillräckligt med information. Du kan lära dig mer om kärn funktionerna i [Azure kognitiv sökning Blob Storage-indexeraren](search-howto-indexing-azure-blob-storage.md).

### <a name="supported-content-types"></a>Innehålls typer som stöds

Genom att köra en BLOB-indexerare över en behållare kan du extrahera text och metadata från följande innehålls typer med en enda fråga:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>Indexera BLOB-metadata

Ett vanligt scenario som gör det enkelt att sortera genom blobbar av vilken innehålls typ som helst är att indexera både anpassade metadata och system egenskaper för varje blob. På så sätt indexeras information för alla blobbar oavsett dokument typ, som lagras i ett index i din Sök tjänst. Med ditt nya index kan du fortsätta att sortera, filtrera och fasett över allt Blob Storage-innehåll.

> [!NOTE]
> BLOB index-Taggar indexeras internt av Blob Storage-tjänsten och exponeras för frågor. Om dina blobbars nyckel/värde-attribut kräver indexerings-och filtrerings funktioner bör BLOB index-taggarna användas i stället för metadata.
>
> Mer information om BLOB-index finns i [Hantera och hitta data på Azure Blob Storage med BLOB-index](../storage/blobs/storage-manage-find-blobs.md).

### <a name="indexing-json-blobs"></a>Indexera JSON-blobbar
Indexerare kan konfigureras för att extrahera strukturerat innehåll som finns i blobbar som innehåller JSON. En indexerare kan läsa JSON-blobbar och parsa det strukturerade innehållet i lämpliga fält i ett Sök dokument. Indexerare kan också ta blobbar som innehåller en matris med JSON-objekt och mappa varje element till ett separat Sök dokument. Du kan ställa in ett tolknings läge för att påverka typen av JSON-objekt som skapas av indexeraren.

## <a name="search-blob-content-in-a-search-index"></a>Sök i BLOB-innehåll i ett Sök index 

Utdata från en indexering är ett sökindex som används för interaktiv utforskning med fri text och filtrerade frågor i en klient app. För inledande utforskning och kontroll av innehåll rekommenderar vi att du börjar med [Sök Utforskaren](search-explorer.md) i portalen för att undersöka dokument strukturen. Du kan använda [enkel](query-simple-syntax.md)frågesyntax, [fullständig frågesyntax](query-lucene-syntax.md)och [syntax för filter uttryck](query-odata-filter-orderby-syntax.md) i Sök Utforskaren.

En permanent lösning är att samla in indata från frågor och presentera svaret som Sök resultat i ett klient program. I följande C#-själv studie kurs beskrivs hur du skapar ett sökprogram: [skapa ditt första program i Azure kognitiv sökning](tutorial-csharp-create-first-app.md).

## <a name="next-steps"></a>Nästa steg

+ [Ladda upp, ladda ned och lista blobar med Azure Portal (Azure Blob Storage)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [Konfigurera en BLOB-indexerare (Azure Kognitiv sökning)](search-howto-indexing-azure-blob-storage.md) 
