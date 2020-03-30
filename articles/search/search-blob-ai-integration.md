---
title: Använda AI för att förstå Blob-lagringsdata
titleSuffix: Azure Cognitive Search
description: Lägg till semantisk, bearbetning av naturligt språk och bildanalys i Azure-blobbar med hjälp av en AI-anrikningspipeline i Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 591437eb3951164d53388b6164103948e9ad65e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73496440"
---
# <a name="use-ai-to-understand-blob-storage-data"></a>Använda AI för att förstå Blob-lagringsdata

Data i Azure Blob-lagring är ofta en mängd ostrukturerat innehåll som bilder, lång text, PDF-filer och Office-dokument. Genom att använda AI-funktionerna i Azure Cognitive Search kan du förstå och extrahera värdefull information från blobbar på flera olika sätt. Exempel på att använda AI på blob-innehåll är:

+ Extrahera text från bilder med optisk teckenigenkänning (OCR)
+ Skapa en scenbeskrivning eller taggar från ett foto
+ Identifiera språk och översätta text till olika språk
+ Bearbeta text med namngiven entitetsigenkänning (NER) för att hitta referenser till personer, datum, platser eller organisationer 

Även om du kanske behöver bara en av dessa AI-funktioner, är det vanligt att kombinera flera av dem till samma pipeline (till exempel extrahera text från en skannad bild och sedan hitta alla datum och platser som refereras i den). 

AI-anrikning skapar ny information, fångad som text, lagrad i fält. Efter anrikning kan du komma åt den här informationen från ett sökindex genom fulltextsökning eller skicka tillbaka berikade dokument till Azure-lagring för att driva nya programupplevelser som inkluderar att utforska data för identifierings- eller analysscenarier. 

I den här artikeln visar vi AI-anrikning via ett brett objektiv så att du snabbt kan förstå hela processen, från att omvandla rådata i blobbar till frågebar information i antingen ett sökindex eller ett kunskapslager.

## <a name="what-it-means-to-enrich-blob-data-with-ai"></a>Vad det innebär att "berika" blob data med AI

*AI-anrikning* är en del av indexeringsarkitekturen för Azure Cognitive Search som integrerar inbyggd AI från Microsoft eller anpassad AI som du tillhandahåller. Det hjälper dig att implementera helhetsscenarier där du behöver bearbeta blobbar (både befintliga och nya när de kommer in eller uppdateras), öppna alla filformat för att extrahera bilder och text, extrahera önskad information med hjälp av olika AI-funktioner och indexera dem i ett sökindex för snabb sökning, hämtning och utforskning. 

Indata är dina blobbar, i en enda behållare, i Azure Blob-lagring. Blobbar kan vara nästan alla typer av text- eller bilddata. 

Utdata är alltid ett sökindex som används för snabb textsökning, hämtning och utforskning i klientprogram. Dessutom kan utdata också vara ett *kunskapslager* som projicerar utökade dokument till Azure-blobbar eller Azure-tabeller för nedströmsanalys i verktyg som Power BI eller i datavetenskapsarbetsbelastningar.

Däremellan är pipeline arkitekturen själv. Pipelinen är baserad på *indexeringsfunktionen,* som du kan tilldela en *kompetens*, som består av en eller flera *färdigheter* som tillhandahåller AI. Syftet med pipelinen är att producera *berikade dokument* som anges som råinnehåll men plocka upp ytterligare struktur, kontext och information när du flyttar genom pipelinen. Berikade dokument förbrukas under indexeringen för att skapa inverterade index och andra strukturer som används i fulltextsökning eller utforskning och analys.

## <a name="start-with-services"></a>Börja med tjänster

Du behöver Azure Cognitive Search och Azure Blob-lagring. I Blob-lagring behöver du en behållare som tillhandahåller källinnehåll.

Du kan börja direkt på portalsidan för lagringskontot. Klicka på **Lägg till Azure Cognitive Search** under **Blob-tjänsten** på den vänstra navigeringssidan för att skapa en ny tjänst eller välja en befintlig. 

När du har lagt till Azure Cognitive Search i ditt lagringskonto kan du följa standardprocessen för att berika data i alla Azure-datakällor. Vi rekommenderar **guiden Importera data** i Azure Cognitive Search för en enkel första introduktion till AI-anrikning. Den här snabbstarten går igenom stegen: [Skapa en AI-anrikningspipeline i portalen](cognitive-search-quickstart-blob.md). 

I följande avsnitt utforskar vi fler komponenter och koncept.

## <a name="use-a-blob-indexer"></a>Använda en Blob-indexerare

AI-anrikning är ett tillägg till en indexeringspipeline, och i Azure Cognitive Search byggs dessa pipelines ovanpå en *indexerare*. En indexerare är en data-källmedveten undertjänst utrustad med intern logik för samplingsdata, läsning av metadatadata, hämtning av data och serialisering av data från inbyggda format i JSON-dokument för efterföljande import. Indexerare används ofta av sig själva för import, separat från AI, men om du vill bygga en AI-anrikningspipeline behöver du en indexerare och en kompetens för att gå med den. I det här avsnittet framhålls indexeraren. nästa avsnitt fokuserar på kompetenser.

Blobbar i Azure Storage indexeras med hjälp av [Azure Cognitive Search Blob storage indexer](search-howto-indexing-azure-blob-storage.md). Du kan anropa den här indexeraren med hjälp av **guiden Importera data,** ett REST API eller .NET SDK. I kod använder du den här indexeraren genom att ange typen och genom att tillhandahålla anslutningsinformation som innehåller ett Azure Storage-konto tillsammans med en blob-behållare. Du kan dela upp blobbar genom att skapa en virtuell katalog, som du sedan kan skicka som en parameter, eller genom att filtrera på ett filtypstillägg.

En indexerare gör "dokumentsprickor", vilket öppnar en blob för att granska innehållet. När du har anslutit till datakällan är det det första steget i pipelinen. För blob-data är det här PDF, office docs, image och andra innehållstyper identifieras. Dokumentsprickor med textextrahering är ingen kostnad. Dokument sprickbildning med bildextrahering debiteras med priser som du hittar på [prissidan](https://azure.microsoft.com/pricing/details/search/).

Även om alla dokument kommer att knäckas, sker berikning endast om du uttryckligen ger färdigheter för att göra det. Om pipelinen till exempel uteslutande består av bildanalys ignoreras text i behållaren eller dokumenten.

Blob-indexeraren levereras med konfigurationsparametrar och stöder ändringsspårning om de underliggande data ger tillräcklig information. Du kan läsa mer om kärnfunktionerna i [Azure Cognitive Search Blob storage indexer](search-howto-indexing-azure-blob-storage.md).

## <a name="add-ai-components"></a>Lägg till AI-komponenter

AI-anrikning avser moduler som letar efter mönster eller egenskaper och sedan utför en operation därefter. Ansiktsigenkänning i foton, textbeskrivningar av foton, identifiering av nyckelfraser i ett dokument och OCR (eller känna igen tryckt eller handskriven text i binära filer) är belysande exempel.

I Azure Cognitive Search är *kunskaper* de enskilda komponenterna i AI-bearbetning som du kan använda fristående eller i kombination med andra kunskaper. 

+ Inbyggda färdigheter backas upp av Cognitive Services, med bildanalys baserad på datorseende och bearbetning av naturligt språk baserat på Text Analytics. Den fullständiga listan finns [i Inbyggda kunskaper för innehållsberikning](cognitive-search-predefined-skills.md).

+ Anpassade kunskaper är anpassad kod, insvept i en [gränssnittsdefinition](cognitive-search-custom-skill-interface.md) som möjliggör integrering i pipelinen. I kundlösningar är det vanligt att använda båda, med anpassade kunskaper som ger AI-moduler med öppen källkod, tredje part eller första part.

En *kompetens* är en samling av färdigheter som används i en pipeline, och det anropas efter att dokumentsprickningsfasen gör innehållet tillgängligt. En indexerare kan använda exakt en kompetens, men den kompetensen finns oberoende av en indexerare så att du kan återanvända den i andra scenarier.

Anpassade färdigheter kan låta komplexa men kan vara enkla och okomplicerade när det gäller implementering. Om du har befintliga paket som tillhandahåller mönstermatchnings- eller klassificeringsmodeller kan innehållet som du extraherar från blobbar skickas till dessa modeller för bearbetning. Eftersom AI-anrikning är Azure-baserad bör din modell också finnas på Azure. Några vanliga värdmetoder är att använda [Azure-funktioner](cognitive-search-create-custom-skill-example.md) eller [behållare](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).

Inbyggda kunskaper som backas upp av Cognitive Services kräver en [bifogad Cognitive Services](cognitive-search-attach-cognitive-services.md) allt-i-ett-prenumerationsnyckel som ger dig åtkomst till resursen. En allt-i-ett-tangent ger dig bildanalys, språkidentifiering, textöversättning och textanalys. Andra inbyggda kunskaper är funktioner i Azure Cognitive Search och kräver ingen ytterligare tjänst eller nyckel. Textformare, splitter och sammanslagning är exempel på hjälpkunskaper som ibland är nödvändiga när du utformar pipelinen.

Om du bara använder anpassade kunskaper och inbyggda nyttighetskunskaper finns det inget beroende eller kostnader relaterade till Cognitive Services.

<!-- ## Order of operations

Now we've covered indexers, content extraction, and skills, we can take a closer look at pipeline mechanisms and order of operations.

A skillset is a composition of one or more skills. When multiple skills are involved, the skillset operates as sequential pipeline, producing dependency graphs, where output from one skill becomes input to another. 

For example, given a large blob of unstructured text, a sample order of operations for text analytics might be as follows:

1. Use Text Splitter to break the blob into smaller parts.
1. Use Language Detection to determine if content is English or another language.
1. Use Text Translator to get all text into a common language.
1. Run Entity Recognition, Key Phrase Extraction, or Sentiment Analysis on chunks of text. In this step, new fields are created and populated. Entities might be location, people, organization, dates. Key phrases are short combinations of words that appear to belong together. Sentiment score is a rating on continuum of negative (0) to positive (1) sentiment.
1. Use Text Merger to reconstitute the document from the smaller chunks. -->

## <a name="consume-ai-enriched-output-in-downstream-solutions"></a>Förbruka AI-berikad utgång i nedströmslösningar

Utdata från AI-anrikning är antingen ett sökindex på Azure Cognitive Search eller ett [kunskapslager](knowledge-store-concept-intro.md) i Azure Storage.

I Azure Cognitive Search används ett sökindex för interaktiv utforskning med hjälp av fritext och filtrerade frågor i en klientapp. Berikade dokument som skapas via AI formateras i JSON och indexeras på samma sätt som alla dokument indexeras i Azure Cognitive Search, vilket utnyttjar alla fördelar som en indexerare ger. Under indexeringen refererar blob-indexeraren till exempel konfigurationsparametrar och inställningar för att använda alla fältmappningar eller ändra identifieringslogik. Sådana inställningar är fullt tillgängliga för regelbunden indexering och AI-berikade arbetsbelastningar. Efter indexering, när innehåll lagras på Azure Cognitive Search, kan du skapa omfattande frågor och filtrera uttryck för att förstå ditt innehåll.

I Azure Storage har ett kunskapsarkiv två uttrycksplatser: en blob-behållare eller tabeller i Tabelllagring. 

+ En blob-behållare fångar berikade dokument i sin helhet, vilket är användbart om du vill mata in andra processer. 

+ Tabelllagring kan däremot hantera fysiska projektioner av berikade dokument. Du kan skapa segment eller lager med utökade dokument som innehåller eller utesluter specifika delar. För analys i Power BI blir tabellerna i Azure Table storage datakällan för ytterligare visualisering och utforskning.

Ett berikat dokument i slutet av rörledningen skiljer sig från den ursprungliga indataversionen genom att det finns ytterligare fält som innehåller ny information som extraherades eller genererades under anrikningen. Du kan därför arbeta med en kombination av originalinnehåll och skapat innehåll, oavsett vilken utdatastruktur du använder.

## <a name="next-steps"></a>Nästa steg

Det finns mycket mer du kan göra med AI-anrikning för att få ut mestajförseln av dina data i Azure Storage, inklusive att kombinera Cognitive Services på olika sätt och skapa anpassade kunskaper för fall där det inte finns någon befintlig Cognitive Service för scenariot. Du kan läsa mer genom att följa länkarna nedan.

+ [Ladda upp, hämta och lista blobbar med Azure-portalen (Azure Blob storage)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [Konfigurera en blob-indexerare (Azure Cognitive Search)](search-howto-indexing-azure-blob-storage.md) 
+ [Översikt över AI-anrikning (Azure Cognitive Search)](cognitive-search-concept-intro.md) 
+ [Skapa en kompetens (Azure Cognitive Search)](cognitive-search-defining-skillset.md)
+ [Mappa noder i ett anteckningsträd (Azure Cognitive Search)](cognitive-search-output-field-mapping.md)
