---
title: Introduktion till AI-anrikning
titleSuffix: Azure Cognitive Search
description: Innehållsextrahering, NLP (Natural Language Processing) och bildbehandling används för att skapa sökbart innehåll i Azure Cognitive Search-index med både fördefinierade kognitiva färdigheter och anpassade AI-algoritmer.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: a41dcc9c7ec86f41c64a69ea1aba762b960b2633
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283029"
---
# <a name="getting-started-with-ai-enrichment"></a>Komma igång med AI-anrikning

AI-anrikning är en funktion i Azure Cognitive Search-indexering som används för att extrahera text från bilder, blobbar och andra ostrukturerade datakällor. Berikande och extrahering gör ditt innehåll mer sökbart i ett [index](search-what-is-an-index.md) eller [ett kunskapslager.](knowledge-store-concept-intro.md) Extrahering och anrikning implementeras med hjälp av *kognitiva färdigheter* som är kopplade till indexeringspipelinen. Kognitiva färdigheter inbyggda i tjänsten kan delas in i dessa kategorier: 

+ **Kunskaper i bearbetning av naturligt språk** omfattar [entitetsigenkänning,](cognitive-search-skill-entity-recognition.md) [språkidentifiering,](cognitive-search-skill-language-detection.md) [extrahering av nyckelfraser,](cognitive-search-skill-keyphrases.md)textmanipulering, [sentimentidentifiering](cognitive-search-skill-sentiment.md)och [PII-identifiering](cognitive-search-skill-pii-detection.md). Med dessa kunskaper mappas ostrukturerad text som sökbara och filterbara fält i ett index.

+ **Bildbehandling** färdigheter inkluderar [optisk teckenigenkänning (OCR)](cognitive-search-skill-ocr.md) och identifiering av [visuella funktioner,](cognitive-search-skill-image-analysis.md)såsom ansiktsigenkänning, bildtolkning, bildigenkänning (kända personer och landmärken) eller attribut som bildorientering. Dessa kunskaper skapar textrepresentationer av bildinnehåll, vilket gör det sökbart med hjälp av frågefunktionerna i Azure Cognitive Search.

![Pipelinediagram för anrikning](./media/cognitive-search-intro/cogsearch-architecture.png "översikt över anrikningspipeline")

Kognitiva kunskaper i Azure Cognitive Search baseras på förutbildade maskininlärningsmodeller i Cognitive Services API:er: [Datorseende](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) och [Textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview). 

Naturligt språk och bildbehandling tillämpas under datainmatningsfasen, med resultat som blir en del av ett dokuments sammansättning i ett sökbart index i Azure Cognitive Search. Data kommer från som en [Azure-datauppsättning](cognitive-search-predefined-skills.md) och sedan genom en indexeringspipeline med hjälp av de inbyggda kunskaper du behöver. Arkitekturen är utökningsbar så om de inbyggda färdigheterna inte är tillräckliga kan du skapa och bifoga [anpassade kunskaper](cognitive-search-create-custom-skill-example.md) för att integrera anpassad bearbetning. Exempel kan vara en anpassad entitetsmodul eller dokumentklassificerare som riktar sig till en viss domän, till exempel ekonomi, vetenskapliga publikationer eller läkemedel.

## <a name="when-to-use-ai-enrichment"></a>När du ska använda AI-anrikning

Du bör överväga att använda inbyggda kognitiva färdigheter om råinnehållet är ostrukturerat text, bildinnehåll eller innehåll som behöver språkidentifiering och översättning. Om du använder AI via de inbyggda kognitiva färdigheterna kan du låsa upp det här innehållet, vilket ökar dess värde och användbarhet i dina sök- och datavetenskapsappar. 

Dessutom kan du överväga att lägga till en anpassad färdighet om du har kod med öppen källkod, tredje part eller första part som du vill integrera i pipelinen. Klassificeringsmodeller som identifierar framträdande egenskaper hos olika dokumenttyper hör till den här kategorin, men alla paket som tillför värde till ditt innehåll kan användas.

### <a name="more-about-built-in-skills"></a>Mer om inbyggda färdigheter

En [kompetens](cognitive-search-defining-skillset.md) som är sammansatt med hjälp av inbyggda kunskaper är väl lämpad för följande programscenarier:

+ Skannade dokument (JPEG) som du vill göra fulltext sökbar. Du kan bifoga en OCR-färdighet (Optical Character Recognition) för att identifiera, extrahera och få text från JPEG-filer.

+ PDF-filer med kombinerad bild och text. Text i PDF-filer kan extraheras under indexeringen utan användning av anrikningssteg, men tillägg av bild- och bearbetning av naturligt språk kan ofta ge ett bättre resultat än vad en standardindexering ger.

+ Flerspråkigt innehåll som du vill använda språkidentifiering mot och eventuellt textöversättning.

+ Ostrukturerade eller halvstrukturerade dokument som innehåller innehåll som har inneboende betydelse eller sammanhang som är dolt i det större dokumentet. 

  I synnerhet blobbar innehåller ofta en stor mängd innehåll som packas i ett enda "fält". Genom att koppla bild- och bearbetningsfärdigheter för naturligt språk till en indexerare kan du skapa ny information som finns i råinnehållet, men som inte annars visas som olika fält. Vissa färdiga inbyggda kognitiva färdigheter som kan hjälpa: utvinning av nyckelfraser, sentimentanalys och erkännande av entiteter (personer, organisationer och platser).

  Dessutom kan inbyggda kunskaper också användas för att omstrukturera innehåll genom textdelning, sammanslagning och formåtgärder.

### <a name="more-about-custom-skills"></a>Mer om anpassade kunskaper

Anpassade kunskaper kan stödja mer komplexa scenarier, till exempel känna igen formulär eller anpassad entitetsidentifiering med hjälp av en modell som du tillhandahåller och radbryt i [det anpassade färdighetswebbgränssnittet](cognitive-search-custom-skill-interface.md). Flera exempel på anpassade kunskaper är [Formulär recognizer](/azure/cognitive-services/form-recognizer/overview), integrering av API för sökning av [Bing-entitet](https://docs.microsoft.com/azure/search/cognitive-search-create-custom-skill-example)och [anpassad entitetsigenkänning](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).


## <a name="steps-in-an-enrichment-pipeline"></a>Steg i en anrikningspipeline

En anrikningspipeline baseras på [*indexerare*](search-indexer-overview.md). Indexerare fyller i ett index baserat på fält-till-fältmappningar mellan indexet och datakällan för dokumentsprickning. Färdigheter, nu knutna till indexerare, fånga upp och berika dokument enligt de färdigheter som du definierar. När du har indexerat kan du komma åt innehåll via sökbegäranden via alla [frågetyper som stöds av Azure Cognitive Search](search-query-overview.md).  Om du inte har tidigare till indexerare går du igenom stegen i det här avsnittet.

### <a name="step-1-connection-and-document-cracking-phase"></a>Steg 1: Anslutnings- och dokumentsprickningsfas

I början av pipelinen har du ostrukturerat text- eller icke-textinnehåll (till exempel bilder, skannade dokument eller JPEG-filer). Data måste finnas i en Azure-datalagringstjänst som kan nås av en indexerare. Indexerare kan "knäcka" källdokument för att extrahera text från källdata. Dokumentsprickning är processen att extrahera eller skapa textinnehåll från icke-textkällor under indexeringen.

![Fas för dokumentsprickbildning](./media/cognitive-search-intro/document-cracking-phase-blowup.png "dokument sprickbildning")

 Källor som stöds inkluderar Azure blob storage, Azure table storage, Azure SQL Database och Azure Cosmos DB. Textbaserat innehåll kan extraheras från följande filtyper: PDF-filer, Word, PowerPoint, CSV-filer. Den fullständiga listan finns i [Format som stöds](search-howto-indexing-azure-blob-storage.md#supported-document-formats). Indexering tar tid så börja med en liten, representativ datauppsättning och sedan bygga upp den stegvis när din lösning mognar.

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>Steg 2: Kognitiva färdigheter och berikningsfas

Berikning utförs med *kognitiva färdigheter* som utför atomära operationer. När du till exempel har knäckt en PDF-fil kan du använda entitetsigenkänning, språkidentifiering eller utvinning av nyckelfraser för att skapa nya fält i indexet som inte är tillgängliga internt i källan. Sammantaget kallas insamling av färdigheter som används i din pipeline en *kompetens.*  

![Berikningsfas](./media/cognitive-search-intro/enrichment-phase-blowup.png "berikningsfasen")

En kompetens bygger på [inbyggda kognitiva färdigheter](cognitive-search-predefined-skills.md) eller [anpassade färdigheter](cognitive-search-create-custom-skill-example.md) du tillhandahåller och ansluta till kompetensen. En kompetens kan vara minimal eller mycket komplex, och bestämmer inte bara typen av bearbetning, men också ordningen på åtgärder. En kompetens plus fältmappningar som definierats som en del av en indexerare anger helt anrikningspipelinen. Mer information om hur du samlar alla dessa delar finns i [Definiera en kompetens](cognitive-search-defining-skillset.md).

Internt genererar pipelinen en samling berikade dokument. Du kan bestämma vilka delar av de berikade dokumenten som ska mappas till indexerbara fält i sökindexet. Om du till exempel har använt extrahering av nyckelfraser och entitetsigenkänningsfärdigheterna blir dessa nya fält en del av det berikade dokumentet och kan mappas till fält i indexet. Se [Anteckningar om](cognitive-search-concept-annotations-syntax.md) du vill veta mer om indata-/utdataformationer.

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>Lägga till ett knowledgeStore-element för att spara enrichments

[Sök REST api-version=2019-05-06-Preview](search-api-preview.md) utökar `knowledgeStore` skillsets med en definition som tillhandahåller en Azure-lagringsanslutning och projektioner som beskriver hur berikarna lagras. Detta är utöver ditt index. I en standard-AI-pipeline är berikade dokument övergående, används endast under indexeringen och sedan kasseras. Med kunskapsbutik bevaras berikade dokument. Mer information finns i [Knowledge Store (preview)](knowledge-store-concept-intro.md).

### <a name="step-3-search-index-and-query-based-access"></a>Steg 3: Sökindex och frågebaserad åtkomst

När bearbetningen är klar har du ett sökindex som består av utökade dokument som är helt textsökbara i Azure Cognitive Search. [Att fråga om indexet](search-query-overview.md) är hur utvecklare och användare kommer åt det berikade innehåll som genereras av pipelinen. 

![Index med sökikon](./media/cognitive-search-intro/search-phase-blowup.png "Index med sökikon")

Indexet är som alla andra som du kan skapa för Azure Cognitive Search: du kan komplettera med anpassade analysatorer, anropa suddiga sökfrågor, lägga till filtrerad sökning eller experimentera med bedömningsprofiler för att omforma sökresultaten.

Index genereras från ett indexschema som definierar fält, attribut och andra konstruktioner som är kopplade till ett visst index, till exempel bedömningsprofiler och synonymmappningar. När ett index har definierats och fyllts i kan du indexera stegvis för att hämta nya och uppdaterade källdokument. Vissa ändringar kräver en fullständig ombyggnad. Du bör använda en liten datauppsättning tills schemadesignen är stabil. Läs mer i informationen om hur du [återskapar ett index](search-howto-reindex.md).

**Checklista: Ett typiskt arbetsflöde**

1. Delmängd dina Azure-källdata i ett representativt exempel. Indexering tar tid så börja med en liten, representativ datauppsättning och sedan bygga upp den stegvis när din lösning mognar.

1. Skapa ett [datakällobjekt](https://docs.microsoft.com/rest/api/searchservice/create-data-source) i Azure Cognitive Search för att tillhandahålla en anslutningssträng för datahämtning.

1. Skapa en [kompetens](https://docs.microsoft.com/rest/api/searchservice/create-skillset) med berikande steg.

1. Definiera [indexschemat](https://docs.microsoft.com/rest/api/searchservice/create-index). Samlingen *Fält* innehåller fält från källdata. Du bör också stub ut ytterligare fält för att hålla genererade värden för innehåll som skapats under anrikning.

1. Definiera [indexeraren](https://docs.microsoft.com/rest/api/searchservice/create-skillset) som refererar till datakällan, kompetensen och indexet.

1. Lägg till *outputFieldMappings*i indexeraren . I det här avsnittet mappas utdata från kompetensen (i steg 3) till indatafälten i indexschemat (i steg 4).

1. Skicka *Skapa Indexer-begäran* som du just har skapat (en POST-begäran med en indexeringsdefinition i begärantexten) för att uttrycka indexeraren i Azure Cognitive Search. Det här steget är hur du kör indexeraren och anropar pipelinen.

1. Kör frågor för att utvärdera resultat och ändra kod för att uppdatera kompetensuppsättningar, schema eller indexeringskonfiguration.

1. [Återställ indexeraren](search-howto-reindex.md) innan du återskapar pipelinen.

## <a name="next-steps"></a>Nästa steg

+ [Dokumentationslänkar för AI-anrikning](cognitive-search-resources-documentation.md)
+ [Exempel: Skapa en anpassad färdighet för AI-anrikning (C#)](cognitive-search-create-custom-skill-example.md)
+ [Snabbstart: Prova AI-anrikning i en portalgenomgång](cognitive-search-quickstart-blob.md)
+ [Självstudiekurs: Lär dig mer om API:er för AI-anrikning](cognitive-search-tutorial-blob.md)
+ [Knowledge Store (förhandsgranskning)](knowledge-store-concept-intro.md)
+ [Skapa ett kunskapslager i REST](knowledge-store-create-rest.md)
+ [Felsökningstips](cognitive-search-concept-troubleshooting.md)
