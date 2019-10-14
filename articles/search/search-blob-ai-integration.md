---
title: Använd AI för att förstå BLOB-data
titleSuffix: Azure Search
description: Lägg till semantisk, naturlig språk bearbetning och bild analys till Azure-blobbar med hjälp av en AI-pipeline i Azure Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 2513825fcb275aeb3c4f0ca49ff5f2a6bd9441f0
ms.sourcegitcommit: bd4198a3f2a028f0ce0a63e5f479242f6a98cc04
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/14/2019
ms.locfileid: "72303020"
---
# <a name="use-ai-to-understand-blob-data"></a>Använd AI för att förstå BLOB-data

Data i Azure Blob Storage är ofta en mängd ostrukturerat innehåll, till exempel bilder, långa text, PDF-dokument och Office-dokument. Genom att använda AI-funktionerna i Azure Search kan du förstå och extrahera värdefull information från blobbar på flera olika sätt. Exempel på att använda AI på BLOB-innehåll är:

+ Extrahera text från bilder med hjälp av optisk tecken läsning (OCR)
+ Skapa en scen beskrivning eller taggar från ett foto
+ Identifiera språk och översätta text till olika språk
+ Bearbeta text med namngiven entitets igenkänning (NER) för att hitta referenser till personer, datum, platser eller organisationer 

Även om du kanske behöver bara en av dessa AI-funktioner är det vanligt att kombinera flera av dem till samma pipeline (till exempel Extrahera text från en skannad bild och sedan hitta alla datum och platser som refereras till). 

AI-berikning skapar ny information, insamlad som text, lagrad i fält. Efter användningen kan du komma åt den här informationen från ett sökindex via fullständig texts ökning eller skicka berikade dokument tillbaka till Azure Storage för att få nya program upplevelser som innehåller utforska data för identifierings-eller analys scenarier. 

I den här artikeln visar vi AI-berikning genom en bred lins så att du snabbt kan grepp hela processen, från att omvandla rå data i blobbar, till information som kan sökas i antingen ett sökindex eller i ett kunskaps lager.

## <a name="what-it-means-to-enrich-blob-data"></a>Vad det innebär att "utöka" BLOB-data

*AI-anrikning* är en del av indexerings arkitekturen för Azure Search som integrerar inbyggda AI från Microsoft eller anpassad AI som du anger. Det hjälper dig att implementera scenarier med slut punkt till slut punkt där du behöver bearbeta blobbar (både befintliga och nya som de kommer in eller uppdateras), öppna alla fil format för att extrahera bilder och text, extrahera önskad information med olika AI-funktioner och indexera dem i ett Azure Search-index för snabb sökning, hämtning och utforskning. 

Indata är dina blobbar i en enda behållare i Azure Blob Storage. Blobbar kan vara nästan alla typer av text-eller bilddata. 

Utdata är alltid ett Azure Search-index som används för snabb texts ökning, hämtning och utforskning i klient program. Dessutom kan utdata också vara ett *kunskaps lager* som Project innehåller dokument till Azure-blobbar eller Azure-tabeller för underordnad analys i verktyg som Power BI eller i arbets belastningar för data vetenskap.

I between är själva pipeline-arkitekturen. Pipelinen baseras på funktionen *Indexer* , som du kan använda för att tilldela en *färdigheter*, som består av en eller flera *kunskaper* som tillhandahåller AI. Syftet med pipelinen är att skapa *omfattande dokument* som anger som rå innehåll, men som hämtar ytterligare struktur, kontext och information samtidigt som du går igenom pipelinen. Omfattande dokument förbrukas under indexeringen för att skapa inverterade index och andra strukturer som används i full texts ökning eller utforskning och analys.

## <a name="how-to-get-started"></a>Så här kommer du igång

Du kan starta direkt på din Portal sida för lagrings konto. Klicka på **Lägg till Azure Search** och skapa en ny Azure Search tjänst eller Välj en befintlig. Om du redan har en befintlig Sök tjänst i samma prenumeration, klickar du på **Lägg till Azure Search** öppnar guiden Importera data så att du snabbt kan stega igenom indexerings-, beriknings-och index definition.

När du lägger till Azure Search i ditt lagrings konto kan du följa standard processen för att utöka data i alla Azure-datakällor. Förutsatt att du redan har BLOB-innehåll kan du använda guiden Importera data i Azure Search för att få en enkel första introduktion till AI-berikning. Den här snabb starten förklarar stegen: [skapa en pipeline för AI-anrikning i portalen](cognitive-search-quickstart-blob.md). 

I följande avsnitt kommer vi att utforska fler komponenter och begrepp.

## <a name="use-blob-indexers"></a>Använda BLOB-indexerare

AI-anrikning är ett tillägg till en indexerings pipeline, och i Azure Search skapas pipelinerna ovanpå en *indexerare*. En indexerare är en data källa medveten under tjänst som är utrustad med intern logik för att sampla data, läsa metadata, hämta data och serialisera data från interna format till JSON-dokument för efterföljande import. Indexerare används ofta av sig själva för import, separat från AI, men om du vill bygga en pipeline för AI-anrikning behöver du en indexerare och en färdigheter för att gå med den. I det här avsnittet ska vi fokusera på själva indexeraren.

Blobbar i Azure Storage indexeras med hjälp av [Azure Search Blob Storage-indexeraren](search-howto-indexing-azure-blob-storage.md). Du anropar den här indexeraren genom att ange typ och genom att tillhandahålla anslutnings information som innehåller ett Azure Storage konto tillsammans med en BLOB-behållare. Om du inte tidigare har ordnat blobbar i en virtuell katalog, som du sedan kan skicka som en parameter, hämtas BLOB-indexeraren från hela behållaren.

En indexerare gör "dokument sprickor" och när du har anslutit till data källan är det det första steget i pipelinen. För BLOB-data är detta var PDF, Office-dokument, bild och andra innehålls typer identifieras. Dokument sprickor med text extrahering är ingen kostnad. Dokument sprickor med avbildnings extrahering debiteras enligt de priser som du hittar på [sidan Azure Search priser](https://azure.microsoft.com/pricing/details/search/).

Även om alla dokument kommer att bli skadade, sker berikningen bara om du anger de kunskaper du behöver. Om din pipeline exempelvis består av enbart text analys, kommer alla bilder i din behållare eller dina dokument att ignoreras.

BLOB-indexeraren innehåller konfigurations parametrar och stöder ändrings spårning om underliggande data innehåller tillräckligt med information. Du kan lära dig mer om kärn funktionerna i [Azure Search Blob Storage-indexeraren](search-howto-indexing-azure-blob-storage.md).

## <a name="add-ai"></a>Lägg till AI

*Färdigheter* är de enskilda komponenterna i AI-bearbetning som du kan använda fristående eller i kombination med andra kunskaper för sekventiell bearbetning. 

+ Inbyggda kunskaper backas upp av Cognitive Services, med bild analys baserat på Visuellt innehåll och naturlig språk bearbetning baserat på Textanalys. Några exempel är [OCR](cognitive-search-skill-ocr.md), [entitets igenkänning](cognitive-search-skill-entity-recognition.md)och [bild analys](cognitive-search-skill-image-analysis.md). Du kan granska den fullständiga listan med inbyggda kunskaper i [fördefinierade kunskaper för innehålls berikning](cognitive-search-predefined-skills.md).

+ Anpassade kunskaper är anpassad kod, omsluten i en gränssnitts definition som möjliggör integrering i pipelinen. I kund lösningar är det vanligt att använda båda, med anpassade kunskaper som tillhandahåller AI-moduler med öppen källkod, tredje part eller första part.

En *färdigheter* är en samling kunskaper som används i en pipeline, och den anropas när dokumentets cracking-fas gör innehåll tillgängligt. En indexerare kan använda exakt en färdigheter, men det färdigheter finns oberoende av en indexerare så att du kan återanvända den i andra scenarier.

Anpassade kunskaper kan vara komplicerade, men kan vara enkla och enkla när det gäller implementering. Om du har befintliga paket som tillhandahåller mönster matchnings-eller klassificerings modeller, kan det innehåll som du extraherar från blobbar skickas till dessa modeller för bearbetning. Eftersom AI-berikning är Azure-baserad bör din modell också vara på Azure. Några vanliga värd metoder är att använda [Azure Functions](cognitive-search-create-custom-skill-example.md) eller [behållare](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).

Inbyggda kunskaper som backas upp av Cognitive Services kräver en [bifogad Cognitive Services](cognitive-search-attach-cognitive-services.md) allt-i-ett-prenumerations nyckel som ger dig åtkomst till resursen. En allt-i-ett-nyckel ger dig bild analys, språk identifiering, text översättning och text analys. Andra inbyggda kunskaper är funktioner i Azure Search och kräver ingen ytterligare tjänst eller nyckel. Text formaren, delar och fusion är exempel på hjälp kunskaper som ibland är nödvändiga när du skapar pipelinen.

Om du bara använder anpassade kunskaper och inbyggda verktygs kunskaper, finns det inget beroende eller kostnader som är relaterade till Cognitive Services.

## <a name="order-of-operations"></a>Åtgärds ordning

Nu har vi täckt indexerare, innehålls extrahering och kunskaper. vi kan ta en närmare titt på pipeline-mekanismer och drifts ordning.

En färdigheter är en sammansättning av en eller flera kunskaper. När flera kunskaper är inblandade fungerar färdigheter som sekventiell pipeline och producerar beroende diagram där utdata från en färdighet blir indata till en annan. 

Till exempel kan en stor blob av ostrukturerad text vara en exempel ordning för åtgärder för text analys som är följande:

1. Använd text delning för att dela upp blobben i mindre delar.
1. Använd Språkidentifiering för att avgöra om innehållet är engelskt eller ett annat språk.
1. Använd text översättare för att hämta all text till ett gemensamt språk.
1. Kör entitets igenkänning, Extrahering av diskussionsämne eller Attitydanalys på text segment. I det här steget skapas och fylls nya fält. Entiteter kan vara plats, personer, organisation, datum. Viktiga fraser är korta kombinationer av ord som verkar tillhöra varandra. Sentiment Poäng är en klassificering på Continuum för negativa (0) till positiva (1) sentiment.
1. Använd text fusion för att färdigställa dokumentet från de mindre segmenten..


## <a name="outputs-and-use-cases"></a>Utdata och användnings fall

Ett berikat dokument i slutet av pipelinen skiljer sig från den ursprungliga indata-versionen genom förekomst av ytterligare fält som innehåller ny information som har extraherats eller genererats under berikning. Därför kan du arbeta med en kombination av ursprungliga och skapade värden på flera sätt.

Utmatnings formulären är ett sökindex på Azure Search eller ett kunskaps lager i Azure Storage.

I Azure Search formateras omfattande dokument i JSON och kan indexeras på samma sätt som alla dokument är indexerade, med de fördelar som en indexerare tillhandahåller. Fält från berikade dokument mappas till ett index schema. Under indexeringen refererar BLOB-indexeraren till konfigurations parametrar och inställningar för att använda alla fält mappningar eller den ändrings identifierings logik som du har angett. Efter indexeringen, när innehåll lagras på Azure Search, kan du bygga omfattande frågor och filter uttryck för att förstå ditt innehåll.

I Azure Storage har ett kunskaps lager två godsspecifikationer: en BLOB-behållare eller tabeller i Table Storage. En BLOB-behållare fångar omfattande dokument i sin helhet, vilket är användbart om du vill mata in i andra processer. Dessutom kan Table Storage hantera fysiska projektioner av förrikade dokument. Du kan skapa segment eller lager av omfattande dokument som inkluderar eller undantar vissa delar. För analys i Power BI blir tabellerna i Azure Table Storage data källan för ytterligare visualisering och utforskning.

## <a name="next-steps"></a>Nästa steg

Det finns mycket mer du kan göra med AI-anrikning för att få ut mesta möjliga av dina data i Azure Storage, inklusive att kombinera Cognitive Services på olika sätt och redigera anpassade kunskaper för fall där det inte finns någon befintlig kognitiv tjänst för scenariot. Du kan lära dig mer genom att följa länkarna nedan.

> [!div class="nextstepaction"]
> [Översikt över AI-anrikning](cognitive-search-concept-intro.md) 
> [skapa en färdigheter](cognitive-search-defining-skillset.md)
> [Mappa noder i ett antecknings träd](cognitive-search-output-field-mapping.md)
