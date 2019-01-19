---
title: Kognitiv sökning, extrahering av data, naturligt språk AI - processen i Azure Search
description: Innehåll extrahering, naturlig språkbearbetning (NLP) och bildbearbetning för att skapa sökbart innehåll i Azure Search indexering med kognitiva kunskaper och AI-algoritmer.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: d30a6446f40105985388d3c2ee077b36af32f208
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2019
ms.locfileid: "54412214"
---
# <a name="what-is-cognitive-search-in-azure-search"></a>Vad är ”cognitive search” i Azure Search?

Cognitive search är en AI-funktion i Azure Search används för att extrahera text från bilder, blobbar och andra Ostrukturerade datakällor - berikas innehållet så att de blir mer sökbara i ett Azure Search-index. Extrahering och berikande implementeras via *kognitiva kunskaper* kopplade till en pipeline för fulltextindexering. AI enrichments stöds på följande sätt: 

+ **Bearbetning av naturligt språk** kunskaper inkluderar [entitetsidentifiering](cognitive-search-skill-entity-recognition.md), [språkidentifiering](cognitive-search-skill-language-detection.md), [nyckeln diskussionsämne](cognitive-search-skill-keyphrases.md), manipulering av text och [sentimentidetntifiering](cognitive-search-skill-sentiment.md). Med dessa kunskaper kan ostrukturerad text anta nya formulär, mappas som sökbart och filtrerbara fält i ett index.

+ **Bildbehandling** kunskaper inkluderar [optisk teckenläsning (OCR)](cognitive-search-skill-ocr.md) och identifiering av [visuella funktioner](cognitive-search-skill-image-analysis.md), till exempel ansiktsigenkänning, bild tolkning, bild erkännande ( sökfras och Orienteringspunkter) eller attribut som färger eller avbildning orientering. Du kan skapa textrepresentationer av bildinnehåll sökbara med alla fråga funktioner i Azure Search.

![Kognitiv sökning diagram med datapipeline](./media/cognitive-search-intro/cogsearch-architecture.png "kognitiv sökning pipeline-översikt")

Kognitiva funktioner i Azure Search baseras på machine learning-modeller i Cognitive Services API: er: [Visuellt](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) och [textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview). 

Naturligt språk och bearbetning av avbildning tillämpas under fasen för inmatning av data med resultatet bli en del av ett dokument sammansättning i ett sökbart index i Azure Search. Data hämtas som en Azure datauppsättning och sedan skickas via en pipeline för fulltextindexering med beroende på vilket som [inbyggda kunskaper](cognitive-search-predefined-skills.md) du behöver. Arkitektur kan utökas så att du kan också skapa och koppla om de inbyggda kunskaperna inte är tillräckliga [anpassade funktioner](cognitive-search-create-custom-skill-example.md) att integrera anpassad bearbetning. Exempel kan vara en anpassad entitet modulen eller dokumentet klassificerare som riktar in sig på en specifik domän, till exempel Ekonomi, vetenskapliga publikationer eller medicin.

> [!NOTE]
> Från och med den 21 December 2018 kan du [bifoga en resurs för Cognitive Services](cognitive-search-attach-cognitive-services.md) med en Azure Search-kompetens. På så sätt kan vi börjar debitera för körning av kompetens. På det här datumet måste också började vi debitera för extrahering av avbildningen som en del av dokumentknäckning fasen. Textextrahering från dokument fortsätter att erbjudas utan extra kostnad.
>
> Körningen av inbyggda färdigheter är en avgift för Cognitive Services, enligt den befintliga [betala-som-du gå pris](https://azure.microsoft.com/pricing/details/cognitive-services/) . Bild extrahering priser är en Azure Search-avgift kan för närvarande faktureras enligt priset för förhandsversionen enligt beskrivningen på den [Azure Search sidan med priser](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="components-of-cognitive-search"></a>Komponenter i kognitiv sökning

Cognitive search är en funktion i förhandsversionen av [Azure Search](search-what-is-azure-search.md), stöds i [dessa regioner](#where-do-i-start). 

Kognitiv sökning pipelinen baseras på [Azure Search *indexerare* ](search-indexer-overview.md) som crawla datakällor och tillhandahåller index för slutpunkt till slutpunkt bearbetning. Färdigheter är nu kopplad till indexerare, spärra och berikas dokument enligt kompetens du definierar. När indexerade, du kan komma åt innehåll via sökförfrågningar igenom alla [fråga typer som stöds av Azure Search](search-query-overview.md).  Om du är nybörjare på indexerare vägleder dig genom stegen i det här avsnittet.

### <a name="step-1-connection-and-document-cracking-phase"></a>Steg 1: Anslutning och document cracking-fas

I början av pipelinen som du har ostrukturerad text eller textinnehåll (t.ex bild och skannade dokument JPEG-filer). Data måste finnas i en lagringstjänst för Azure data som kan användas av en indexerare. Indexerare kan ”knäcka” källa dokument för att extrahera text från datakällan.

![Document cracking-fasen](./media/cognitive-search-intro/document-cracking-phase-blowup.png "dokumentknäckning")

 Källor som stöds omfattar Azure blob storage, Azure table storage, Azure SQL Database och Azure Cosmos DB. Du kan extrahera information från följande filtyper: PDF-filer, Word, PowerPoint och CSV-filer. En fullständig lista finns i [stöds format](search-howto-indexing-azure-blob-storage.md#supported-document-formats).

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>Steg 2: Kognitiva kunskaper och berikande fas

Berikande är via *kognitiva kunskaper* utför atomiska åtgärder. Till exempel när du har textinnehåll från en PDF-fil kan kan du använda entiteten erkännande språkidentifiering eller extrahering av diskussionsämne att producera nya fält i indexet som inte är tillgängliga internt i källan. Helt och hållet, insamling av kunskaper som används i din pipeline kallas en *kompetens*.  

![Berikande fas](./media/cognitive-search-intro/enrichment-phase-blowup.png "berikande fas")

En kompetens baseras på [fördefinierade kognitiva kunskaper](cognitive-search-predefined-skills.md) eller [anpassade funktioner](cognitive-search-create-custom-skill-example.md) du anger och ansluta till kompetens. En kompetens kan vara minimal eller mycket komplexa och anger inte bara typ av bearbetning, men också utförs. En kompetens plus fältmappningar som definierats som en del av en indexerare anger fullständigt berikande pipelinen. Läs mer om hur du drar pusslas ihop [definierar en kompetens](cognitive-search-defining-skillset.md).

Internt genererar pipelinen en samling avancerad och dokument. Du kan bestämma vilka delar av avancerad och dokumenten som ska mappas till vara fält i sökindexet. Till exempel om du använt extrahering av nyckelfraser och entiteten igenkänning av kunskaper, sedan dessa nya fält skulle bli en del av avancerad och dokumentet och de kan mappas till fält i ditt index. Se [anteckningar](cognitive-search-concept-annotations-syntax.md) mer information om indata/utdata buskmarker.

### <a name="step-3-search-index-and-query-based-access"></a>Steg 3: Search-index och frågebaserade åtkomst

När bearbetningen är klar har du en sökkorpus som består av en avancerad och dokument, text-sökbara i Azure Search. [Fråga indexet](search-query-overview.md) är hur utvecklare och användare kan komma åt det avancerad och innehåll som genereras av pipelinen. 

![Index med sökikonen](./media/cognitive-search-intro/search-phase-blowup.png "Index med sökikonen")

Indexet är sätt som andra du kan skapa för Azure Search: du kan komplettera med anpassade analysverktyg, anropa fuzzy-sökning-frågor, Lägg till filtrerade sökning eller experimentera med poängprofiler om du vill omforma sökresultaten.

Index som ska genereras från ett indexschema som definierar fälten, attribut, och andra konstruktioner som är kopplat till ett specifikt index, till exempel poängprofiler och synonymen mappar. När ett index är definierat och fylls i, kan du indexera stegvis för att hämta upp källa för nya och uppdaterade dokument. Vissa ändringar kräver återskapning av en fullständig. Du bör använda en liten datamängd tills design schema är stabil. Läs mer i informationen om hur du [återskapar ett index](search-howto-reindex.md).

<a name="feature-concepts"></a>

## <a name="key-features-and-concepts"></a>Huvudfunktioner och koncept

| Begrepp | Beskrivning| Länkar |
|---------|------------|-------|
| Skillset | En på översta nivån med namnet resurs som innehåller en samling av kunskaper. En kompetens är berikande pipelinen. Den anropas under indexering av en indexerare. | [Definiera en kompetens](cognitive-search-defining-skillset.md) |
| Kognitiva kunskaper | En atomisk omvandling i en berikande pipeline. Ofta är det en komponent som extraherar eller härleder struktur och därför förstärker din förståelse av indata. Nästan alltid utdata är textbaserade och bearbetningen är naturlig språkbearbetning eller bildbehandling som extraherar eller genererar text från avbildningen indata. Utdata från en färdighet kan mappas till ett fält i ett index eller används som indata för en underordnad berikande. En färdighet antingen fördefinierade och tillhandahålls av Microsoft eller anpassade: skapas och distribueras av dig. | [Fördefinierade kunskaper](cognitive-search-predefined-skills.md) |
| Extrahering av data | Omfattar ett brett utbud av bearbetning, men hör till kognitiv sökning, namngiven entitet erkännande färdighet mest normalt används för att extrahera data (en entitet) från en källa som inte tillhandahåller denna information internt. | [Namngiven entitet erkännande färdighet](cognitive-search-skill-named-entity-recognition.md)| 
| Bearbetning av avbildning | Härleder text från en avbildning, till exempel möjligheten att identifiera en landmärken eller extraherar text från en bild. Vanliga exempel är OCR för att lyfta tecken från en skannade dokument (JPEG)-fil eller känna igen en gatuadress i ett foto som innehåller en gatuadress. | [Bild Analysis färdighet](cognitive-search-skill-image-analysis.md) eller [OCR färdighet](cognitive-search-skill-ocr.md)
| Bearbetning av naturligt språk | Text för insikter och information om Textinmatningar bearbetades. Extrahering av diskussionsämne, språkidentifiering och attitydanalys är färdigheter som hör till bearbetning av naturligt språk.  | [Nyckeln frasen extrahering färdighet](cognitive-search-skill-keyphrases.md), [språk identifiering färdighet](cognitive-search-skill-language-detection.md), [Sentiment Analysis färdighet](cognitive-search-skill-sentiment.md) |
| Document cracking- | Processen för att extrahera eller skapa textinnehåll från icke-text källor under indexering. Optisk teckenläsning (OCR) är ett exempel, men den avser vanligen indexeraren kärnfunktioner som indexeraren extraherar innehåll från programfiler. Datakällan som tillhandahåller plats för källfilen och att indexerarens definition att tillhandahålla fältmappningar, är båda viktiga faktorer för document cracking. | Se [indexerare](search-indexer-overview.md) |
| Forma | Konsolidera textfragment i en större struktur eller omvänt bryta ned den större textsegment i en lämplig storlek för ytterligare nedströms bearbetning. | [Formaren färdighet](cognitive-search-skill-shaper.md), [Text fusion färdighet](cognitive-search-skill-textmerger.md), [Text dela färdighet](cognitive-search-skill-textsplit.md) |
| Avancerad och dokument | Ett övergående interna strukturen, inte direkt åtkomliga i kod. Avancerad och dokument genereras under bearbetningen, men endast slutgiltiga utdata finns kvar i ett sökindex. Fältmappningar avgör vilka data läggs till i indexet. | Se [åtkomst till avancerad och dokument](cognitive-search-tutorial-blob.md#access-enriched-document). |
| Indexerare |  En crawler som extraherar sökbara data och metadata från en extern datakälla och fylla ett index baserat på fältet till fält-mappningar mellan indexet och din datakälla för dokumentknäckning. Indexeraren för kognitiv sökning enrichments anropar en kompetens och innehåller fältmappningar associera berikande utdata till målfält i indexet. Indexerardefinitionen innehåller alla instruktioner och referenser för pipeline-åtgärder och pipeline anropas när du kör indexeraren. | [Indexerare](search-indexer-overview.md) |
| Datakälla  | Ett objekt som används av en indexerare för att ansluta till en extern datakälla av typer som stöds på Azure. | Se [indexerare](search-indexer-overview.md) |
| Index | Beständiga sökkorpus i Azure Search bygger på ett indexschema som definierar strukturen för fältet och användning. | [Index i Azure Search](search-what-is-an-index.md) | 

<a name="where-do-i-start"></a>

## <a name="where-do-i-start"></a>Vad ska jag börja med?

**Steg 1: [Skapa en Azure Search-resurs](search-create-service-portal.md) i en region som tillhandahåller API: er** 

+ Västra centrala USA
+ Södra centrala USA
+ Östra USA
+ USA, östra 2
+ Västra USA 2
+ Centrala Kanada
+ Västra Europa
+ Storbritannien, södra
+ Norra Europa
+ Södra Brasilien
+ Sydostasien
+ Indien, centrala
+ Östra Australien

**Steg 2: Praktisk erfarenhet att arbetsflödet**

+ [Snabbstart (portal)](cognitive-search-quickstart-blob.md)
+ [Självstudie (HTTP-begäranden)](cognitive-search-tutorial-blob.md)
+ [Exempel anpassade funktioner (C#)](cognitive-search-create-custom-skill-example.md)

**Steg 3: Granska API (REST)**

För närvarande finns endast REST API: er. Använd `api-version=2017-11-11-Preview` för alla förfrågningar. Använd följande API: er för att skapa ett cognitive search-lösning. Endast två API: er läggs till eller utökats för kognitiv sökning. API: er har samma syntax som är allmänt tillgängliga versioner.

| REST-API | Beskrivning |
|-----|-------------|
| [Skapa datakälla](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | En resurs som identifierar en extern datakälla att tillhandahålla källdata som används för att skapa avancerad och dokument.  |
| [Skapa kompetens (api-version = 2017-11-11-förhandsversion)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | En resurs som samordna användningen av [fördefinierade kunskaper](cognitive-search-predefined-skills.md) och [anpassade kognitiva kunskaper](cognitive-search-custom-skill-interface.md) används i en pipeline för berikande under indexering. |
| [Skapa Index](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Ett schema som uttrycker ett Azure Search-index. Fält i indexet mappar till fält i datakällan eller fält som tillverkade under fasen för berikande (t.ex, ett fält för organisationsnamn som skapats av entitetsidentifiering). |
| [Skapa indexerare (api-version = 2017-11-11-förhandsversion)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | En resurs som definierar komponenter som används under indexering: som en datakälla, en kompetens, fältkopplingar från käll- och mellanliggande datastrukturer till målindex och indexet själva. Köra indexeraren är en utlösare för datainmatning och funktioner. Utdata är en sökkorpus baserat på indexschemat fylls med källdata, berikats via kompetens.  |

**Checklista: Ett vanligt arbetsflöde**

1. Delmängd data Azure källa till ett representativt urval. Indexering tar tid så börja med en liten, representativ datauppsättning och sedan skapar det inkrementellt som din lösning utvecklas.

1. Skapa en [datakällobjektet](https://docs.microsoft.com/rest/api/searchservice/create-data-source) i Azure Search för att ange en anslutningssträng för datahämtning.

1. Skapa en [kompetens](https://docs.microsoft.com/rest/api/searchservice/create-skillset) berikande steg.

1. Definiera den [indexschema](https://docs.microsoft.com/rest/api/searchservice/create-index). Den *fält* samling innehåller fält från datakällan. Du bör också stub-ut ytterligare fält som ska innehålla genererade värden för innehåll som har skapats under funktioner.

1. Definiera den [indexeraren](https://docs.microsoft.com/rest/api/searchservice/create-skillset) refererar till datakällan, kompetens och index.

1. Inom indexeraren, lägger du till *outputFieldMappings*. Det här avsnittet mappar utdata från kompetens (i steg 3) till indata-fälten i indexschemat (i steg 4).

1. Skicka *skapa et indexerare* begär du nyss skapade (en POST-begäran med en indexerardefinition i begärandetexten) för indexerare i Azure Search. Det här steget är hur köra indexeraren, anropar din pipeline.

1. Köra frågor för att utvärdera resultaten och ändra koden för att uppdatera kompetens, ett schema eller indexerarkonfiguration.

1. [Återställ indexeraren](search-howto-reindex.md) innan du återskapar pipelinen.

Mer information om specifika frågor eller problem finns i [felsökningstips](cognitive-search-concept-troubleshooting.md).

## <a name="next-steps"></a>Nästa steg

+ [Dokumentation om cognitive search](cognitive-search-resources-documentation.md)
+ [Snabbstart: Prova cognitive search i en genomgång av portalen](cognitive-search-quickstart-blob.md)
+ [Självstudier: Lär dig kognitiv sökning API: er](cognitive-search-tutorial-blob.md)
