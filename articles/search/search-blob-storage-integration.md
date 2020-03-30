---
title: Lägga till fulltextsökning i Azure Blob Storage
titleSuffix: Azure Cognitive Search
description: Extrahera innehåll och lägga till struktur i Azure-blobbar när du skapar ett fulltextsökindex i Azure Cognitive earch.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: af7d04bd74ada296b9f0e0f7c149c2a781cec579
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73496486"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-cognitive-search"></a>Lägga till fulltextsökning i Azure-blobdata med Azure Cognitive Search

Att söka över de olika innehållstyper som lagras i Azure Blob-lagring kan vara ett svårt problem att lösa. Du kan dock indexera och söka efter innehållet i dina Blobbar med bara några klick med hjälp av [Azure Cognitive Search](search-what-is-azure-search.md). Azure Cognitive Search har inbyggd integrering för indexering av Blob-lagring via en [*Blob-indexerare*](search-howto-indexing-azure-blob-storage.md) som lägger till data-källmedvetna funktioner i indexeringen.

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>Vad det innebär att lägga till fulltextsökning i blob-data

Azure Cognitive Search är en molnsöktjänst som tillhandahåller indexerings- och frågemotorer som fungerar över användardefinierade index som finns på söktjänsten. Att samlokalisera ditt sökbara innehåll med frågemotorn i molnet är nödvändigt för prestanda, och returnera resultat med en hastighet som användarna har kommit att förvänta sig av sökfrågor.

Azure Cognitive Search integreras med Azure Blob-lagring på indexeringslagret och importerar blob-innehåll som sökdokument som indexeras *till inverterade index* och andra frågestrukturer som stöder textfrågor och filteruttryck i fri form. Eftersom blob-innehållet indexeras till ett sökindex kan åtkomst till blob-innehåll utnyttja hela skalan av frågefunktioner i Azure Cognitive Search.

När indexet har skapats och fyllts i finns det oberoende av blob-behållaren, men du kan köra indexeringsåtgärder igen för att uppdatera indexet med ändringar i den underliggande behållaren. Tidsstämpelinformation om enskilda blobbar används för ändringsidentifiering. Du kan välja antingen schemalagd körning eller indexering på begäran som uppdateringsmekanism.

Indata är dina blobbar, i en enda behållare, i Azure Blob-lagring. Blobbar kan vara nästan alla typer av textdata. Om blobbar innehåller bilder kan du lägga till [AI-anrikning i blobindexering](search-blob-ai-integration.md) för att skapa och extrahera text från bilder.

Utdata är alltid ett Azure Cognitive Search-index som används för snabb textsökning, hämtning och utforskning i klientprogram. Däremellan är indexering pipeline arkitekturen själv. Pipelinen är baserad på *indexerfunktionen,* som diskuteras vidare i den här artikeln.

## <a name="start-with-services"></a>Börja med tjänster

Du behöver Azure Cognitive Search och Azure Blob-lagring. I Blob-lagring behöver du en behållare som tillhandahåller källinnehåll.

Du kan börja direkt på portalsidan för lagringskontot. Klicka på **Lägg till Azure Cognitive Search** under **Blob-tjänsten** på den vänstra navigeringssidan för att skapa en ny tjänst eller välja en befintlig. 

När du har lagt till Azure Cognitive Search i ditt lagringskonto kan du följa standardprocessen för att indexera blob-data. Vi rekommenderar **guiden Importera data** i Azure Cognitive Search för en enkel första introduktion, eller anropa REST-API:erna med ett verktyg som Postman. Den här självstudien går igenom stegen för att anropa REST API i Postman: [Index och sök efter semistrukturerade data (JSON-blobbar) i Azure Cognitive Search](search-semi-structured-data.md). 

## <a name="use-a-blob-indexer"></a>Använda en Blob-indexerare

En *indexerare* är en data-källmedveten undertjänst utrustad med intern logik för samplingsdata, läsning av metadatadata, hämtning av data och serialisering av data från inbyggda format i JSON-dokument för efterföljande import. 

Blobbar i Azure Storage indexeras med hjälp av [Azure Cognitive Search Blob storage indexer](search-howto-indexing-azure-blob-storage.md). Du kan anropa den här indexeraren med hjälp av **guiden Importera data,** ett REST API eller .NET SDK. I kod använder du den här indexeraren genom att ange typen och genom att tillhandahålla anslutningsinformation som innehåller ett Azure Storage-konto tillsammans med en blob-behållare. Du kan dela upp blobbar genom att skapa en virtuell katalog, som du sedan kan skicka som en parameter, eller genom att filtrera på ett filtypstillägg.

En indexerare gör "dokumentsprickor", vilket öppnar en blob för att granska innehållet. När du har anslutit till datakällan är det det första steget i pipelinen. För blob-data är det här PDF, office-dokument och andra innehållstyper identifieras. Dokumentsprickor med textextrahering är ingen kostnad. Om blobbar innehåller bildinnehåll ignoreras bilder om du inte [lägger till AI-anrikning](search-blob-ai-integration.md). Standardindexering gäller endast för textinnehåll.

Blob-indexeraren levereras med konfigurationsparametrar och stöder ändringsspårning om de underliggande data ger tillräcklig information. Du kan läsa mer om kärnfunktionerna i [Azure Cognitive Search Blob storage indexer](search-howto-indexing-azure-blob-storage.md).

### <a name="supported-content-types"></a>Innehållstyper som stöds

Genom att köra en Blob-indexerare över en behållare kan du extrahera text och metadata från följande innehållstyper med en enda fråga:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>Indexera blobmetadata

Ett vanligt scenario som gör det enkelt att sortera mellan blobbar av någon innehållstyp är att indexera både anpassade metadata och systemegenskaper för varje blob. På så sätt indexeras information för alla blobbar oavsett dokumenttyp, som lagras i ett index i söktjänsten. Med hjälp av det nya indexet kan du sedan fortsätta att sortera, filtrera och aspekt över allt Blob-lagringsinnehåll.

### <a name="indexing-json-blobs"></a>Indexering JSON blobbar
Indexerare kan konfigureras för att extrahera strukturerat innehåll som finns i blobbar som innehåller JSON. En indexerare kan läsa JSON-blobbar och tolka det strukturerade innehållet i lämpliga fält i ett sökdokument. Indexerare kan också ta blobbar som innehåller en matris med JSON-objekt och mappa varje element till ett separat sökdokument. Du kan ställa in ett tolkningsläge så att det påverkar den typ av JSON-objekt som skapas av indexeraren.

## <a name="search-blob-content-in-a-search-index"></a>Söka blob-innehåll i ett sökindex 

Utdata för en indexering är ett sökindex som används för interaktiv utforskning med hjälp av fritext och filtrerade frågor i en klientapp. För inledande utforskning och verifiering av innehåll rekommenderar vi att du börjar med [Search Explorer](search-explorer.md) i portalen för att undersöka dokumentstrukturen. Du kan använda [enkel frågesyntax,](query-simple-syntax.md) [fullständig frågesyntax](query-lucene-syntax.md)och [filteruttryckssyntax](query-odata-filter-orderby-syntax.md) i Sökutforskaren.

En mer permanent lösning är att samla in frågeindata och presentera svaret som sökresultat i ett klientprogram. Följande C#-självstudie förklarar hur du skapar ett sökprogram: [Skapa ditt första program i Azure Cognitive Search](tutorial-csharp-create-first-app.md).

## <a name="next-steps"></a>Nästa steg

+ [Ladda upp, hämta och lista blobbar med Azure-portalen (Azure Blob storage)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [Konfigurera en blob-indexerare (Azure Cognitive Search)](search-howto-indexing-azure-blob-storage.md) 
