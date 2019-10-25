---
title: Använd AI för att förstå Blob Storage-data
titleSuffix: Azure Search
description: Lägg till semantisk, naturlig språk bearbetning och bild analys till Azure-blobbar med hjälp av en AI-pipeline i Azure Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 5f65667ac8ffacdd12e57ae0d46e25c586624a31
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792474"
---
# <a name="use-ai-to-understand-blob-storage-data"></a>Använd AI för att förstå Blob Storage-data

Data i Azure Blob Storage är ofta en mängd ostrukturerat innehåll, till exempel bilder, långa text, PDF-dokument och Office-dokument. Genom att använda AI-funktionerna i Azure Search kan du förstå och extrahera värdefull information från blobbar på flera olika sätt. Exempel på att använda AI på BLOB-innehåll är:

+ Extrahera text från bilder med hjälp av optisk tecken läsning (OCR)
+ Skapa en scen beskrivning eller taggar från ett foto
+ Identifiera språk och översätta text till olika språk
+ Bearbeta text med namngiven entitets igenkänning (NER) för att hitta referenser till personer, datum, platser eller organisationer 

Även om du kanske behöver bara en av dessa AI-funktioner är det vanligt att kombinera flera av dem till samma pipeline (till exempel Extrahera text från en skannad bild och sedan hitta alla datum och platser som refereras till). 

AI-berikning skapar ny information, insamlad som text, lagrad i fält. Efter användningen kan du komma åt den här informationen från ett sökindex via fullständig texts ökning eller skicka berikade dokument tillbaka till Azure Storage för att få nya program upplevelser som innehåller utforska data för identifierings-eller analys scenarier. 

I den här artikeln visar vi AI-berikning genom en bred lins så att du snabbt kan grepp hela processen, från att omvandla rå data i blobbar, till information som kan sökas i antingen ett sökindex eller i ett kunskaps lager.

## <a name="what-it-means-to-enrich-blob-data-with-ai"></a>Vad det innebär att "utöka" BLOB-data med AI

*AI-anrikning* är en del av indexerings arkitekturen för Azure Search som integrerar inbyggda AI från Microsoft eller anpassad AI som du anger. Det hjälper dig att implementera scenarier med slut punkt till slut punkt där du behöver bearbeta blobbar (både befintliga och nya som de kommer in eller uppdateras), öppna alla fil format för att extrahera bilder och text, extrahera önskad information med olika AI-funktioner och indexera dem i ett Azure Search-index för snabb sökning, hämtning och utforskning. 

Indata är dina blobbar i en enda behållare i Azure Blob Storage. Blobbar kan vara nästan alla typer av text-eller bilddata. 

Utdata är alltid ett Azure Search-index som används för snabb texts ökning, hämtning och utforskning i klient program. Dessutom kan utdata också vara ett *kunskaps lager* som Project innehåller dokument till Azure-blobbar eller Azure-tabeller för underordnad analys i verktyg som Power BI eller i arbets belastningar för data vetenskap.

I between är själva pipeline-arkitekturen. Pipelinen baseras på funktionen *Indexer* , som du kan använda för att tilldela en *färdigheter*, som består av en eller flera *kunskaper* som tillhandahåller AI. Syftet med pipelinen är att skapa *omfattande dokument* som anger som rå innehåll, men som hämtar ytterligare struktur, kontext och information samtidigt som du går igenom pipelinen. Omfattande dokument förbrukas under indexeringen för att skapa inverterade index och andra strukturer som används i full texts ökning eller utforskning och analys.

## <a name="start-with-services"></a>Starta med tjänster

Du behöver Azure Search och Azure Blob Storage. I Blob Storage behöver du en behållare som tillhandahåller käll innehåll.

Du kan starta direkt på din Portal sida för lagrings konto. På den vänstra navigerings sidan under **BLOB service** klickar du på **Lägg till Azure Search** för att skapa en ny tjänst eller välja en befintlig. 

När du lägger till Azure Search i ditt lagrings konto kan du följa standard processen för att utöka data i alla Azure-datakällor. Vi rekommenderar guiden **Importera data** i Azure Search för en enkel första introduktion till AI-berikning. Den här snabb starten vägleder dig genom stegen: [skapa en pipeline för AI-anrikning i portalen](cognitive-search-quickstart-blob.md). 

I följande avsnitt kommer vi att utforska fler komponenter och begrepp.

## <a name="use-a-blob-indexer"></a>Använda en BLOB-indexerare

AI-anrikning är ett tillägg till en indexerings pipeline, och i Azure Search skapas pipelinerna ovanpå en *indexerare*. En indexerare är en data källa medveten under tjänst som är utrustad med intern logik för att sampla data, läsa metadata, hämta data och serialisera data från interna format till JSON-dokument för efterföljande import. Indexerare används ofta av sig själva för import, separat från AI, men om du vill bygga en pipeline för AI-anrikning behöver du en indexerare och en färdigheter för att gå med den. I det här avsnittet beskrivs indexeraren. nästa avsnitt fokuserar på färdighetsuppsättningar.

Blobbar i Azure Storage indexeras med hjälp av [Azure Search Blob Storage-indexeraren](search-howto-indexing-azure-blob-storage.md). Du kan anropa denna indexerare med hjälp av guiden **Importera data** , en REST API eller .NET SDK. I kod använder du denna indexerare genom att ange typ och genom att tillhandahålla anslutnings information som innehåller ett Azure Storage konto tillsammans med en BLOB-behållare. Du kan ange en delmängd av Blobbarna genom att skapa en virtuell katalog som du sedan kan skicka som en parameter eller genom att filtrera efter fil typs tillägg.

En indexerare gör "dokument sprickor" och öppnar en BLOB för att granska innehåll. När du har anslutit till data källan är det första steget i pipelinen. För BLOB-data är detta var PDF, Office-dokument, bild och andra innehålls typer identifieras. Dokument sprickor med text extrahering är ingen kostnad. Dokument sprickor med avbildnings extrahering debiteras enligt de priser som du hittar på [sidan Azure Search priser](https://azure.microsoft.com/pricing/details/search/).

Även om alla dokument kommer att bli skadade, sker berikningen bara om du anger de kunskaper du behöver. Om din pipeline till exempel består av enbart bild analys, ignoreras texten i din behållare eller dina dokument.

BLOB-indexeraren innehåller konfigurations parametrar och stöder ändrings spårning om underliggande data innehåller tillräckligt med information. Du kan lära dig mer om kärn funktionerna i [Azure Search Blob Storage-indexeraren](search-howto-indexing-azure-blob-storage.md).

## <a name="add-ai-components"></a>Lägg till AI-komponenter

AI-anrikning syftar på moduler som söker efter mönster eller egenskaper och utför sedan en åtgärd enligt detta. Ansikts igenkänning i foton, text beskrivningar av foton, identifiering av nyckel fraser i ett dokument och OCR (eller igenkänning av skriven text i binära filer) är exempel på exempel.

I Azure Search är *färdigheter* de enskilda komponenterna i AI-bearbetning som du kan använda fristående eller i kombination med andra kunskaper. 

+ Inbyggda kunskaper backas upp av Cognitive Services, med bild analys baserat på Visuellt innehåll och naturlig språk bearbetning baserat på Textanalys. En fullständig lista finns i [inbyggda kunskaper för innehålls berikning](cognitive-search-predefined-skills.md).

+ Anpassade kunskaper är anpassad kod, omsluten i en [gränssnitts definition](cognitive-search-custom-skill-interface.md) som möjliggör integrering i pipelinen. I kund lösningar är det vanligt att använda båda, med anpassade kunskaper som tillhandahåller AI-moduler med öppen källkod, tredje part eller första part.

En *färdigheter* är en samling kunskaper som används i en pipeline, och den anropas när dokumentets cracking-fas gör innehåll tillgängligt. En indexerare kan använda exakt en färdigheter, men det färdigheter finns oberoende av en indexerare så att du kan återanvända den i andra scenarier.

Anpassade kunskaper kan vara komplicerade, men kan vara enkla och enkla när det gäller implementering. Om du har befintliga paket som tillhandahåller mönster matchnings-eller klassificerings modeller, kan det innehåll som du extraherar från blobbar skickas till dessa modeller för bearbetning. Eftersom AI-berikning är Azure-baserad bör din modell också vara på Azure. Några vanliga värd metoder är att använda [Azure Functions](cognitive-search-create-custom-skill-example.md) eller [behållare](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).

Inbyggda kunskaper som backas upp av Cognitive Services kräver en [bifogad Cognitive Services](cognitive-search-attach-cognitive-services.md) allt-i-ett-prenumerations nyckel som ger dig åtkomst till resursen. En allt-i-ett-nyckel ger dig bild analys, språk identifiering, text översättning och text analys. Andra inbyggda kunskaper är funktioner i Azure Search och kräver ingen ytterligare tjänst eller nyckel. Text formaren, delar och fusion är exempel på hjälp kunskaper som ibland är nödvändiga när du skapar pipelinen.

Om du bara använder anpassade kunskaper och inbyggda verktygs kunskaper, finns det inget beroende eller kostnader som är relaterade till Cognitive Services.

<!-- ## Order of operations

Now we've covered indexers, content extraction, and skills, we can take a closer look at pipeline mechanisms and order of operations.

A skillset is a composition of one or more skills. When multiple skills are involved, the skillset operates as sequential pipeline, producing dependency graphs, where output from one skill becomes input to another. 

For example, given a large blob of unstructured text, a sample order of operations for text analytics might be as follows:

1. Use Text Splitter to break the blob into smaller parts.
1. Use Language Detection to determine if content is English or another language.
1. Use Text Translator to get all text into a common language.
1. Run Entity Recognition, Key Phrase Extraction, or Sentiment Analysis on chunks of text. In this step, new fields are created and populated. Entities might be location, people, organization, dates. Key phrases are short combinations of words that appear to belong together. Sentiment score is a rating on continuum of negative (0) to positive (1) sentiment.
1. Use Text Merger to reconstitute the document from the smaller chunks. -->

## <a name="consume-ai-enriched-output-in-downstream-solutions"></a>Använd AI – berikade utdata i underordnade lösningar

Utdata från AI-anrikning är antingen ett sökindex på Azure Search eller ett [kunskaps lager](knowledge-store-concept-intro.md) i Azure Storage.

I Azure Search används ett sökindex för interaktiv utforskning med fri text och filtrerade frågor i en klient app. Omfattande dokument som skapats via AI formateras i JSON och indexeras på samma sätt som alla dokument indexeras i Azure Search, vilket utnyttjar alla fördelar som en indexerare tillhandahåller. Vid indexering, till exempel, refererar BLOB-indexeraren till konfigurations parametrar och inställningar för att använda alla fält mappningar eller logik för ändrings identifiering. Sådana inställningar är helt tillgängliga för regelbundna indexering och AI-berikade arbets belastningar. Efter indexeringen, när innehåll lagras på Azure Search, kan du bygga omfattande frågor och filter uttryck för att förstå ditt innehåll.

I Azure Storage har ett kunskaps lager två godsspecifikationer: en BLOB-behållare eller tabeller i Table Storage. 

+ En BLOB-behållare fångar omfattande dokument i sin helhet, vilket är användbart om du vill mata in i andra processer. 

+ Dessutom kan Table Storage hantera fysiska projektioner av förrikade dokument. Du kan skapa segment eller lager av omfattande dokument som inkluderar eller undantar vissa delar. För analys i Power BI blir tabellerna i Azure Table Storage data källan för ytterligare visualisering och utforskning.

Ett berikat dokument i slutet av pipelinen skiljer sig från den ursprungliga indata-versionen genom förekomst av ytterligare fält som innehåller ny information som har extraherats eller genererats under berikning. Därför kan du arbeta med en kombination av ursprungligt och skapat innehåll, oavsett vilken utmatnings struktur du använder.

## <a name="next-steps"></a>Nästa steg

Det finns mycket mer du kan göra med AI-anrikning för att få ut mesta möjliga av dina data i Azure Storage, inklusive att kombinera Cognitive Services på olika sätt och redigera anpassade kunskaper för fall där det inte finns någon befintlig kognitiv tjänst för scenariot. Du kan lära dig mer genom att följa länkarna nedan.

+ [Ladda upp, ladda ned och lista blobar med Azure Portal (Azure Blob Storage)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
+ [Konfigurera en BLOB-indexerare (Azure Search)](search-howto-indexing-azure-blob-storage.md) 
+ [Översikt över AI-anrikning (Azure Search)](cognitive-search-concept-intro.md) 
+ [Skapa en färdigheter (Azure Search)](cognitive-search-defining-skillset.md)
+ [Mappa noder i ett antecknings träd (Azure Search)](cognitive-search-output-field-mapping.md)
