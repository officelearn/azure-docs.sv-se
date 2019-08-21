---
title: Introduktion till kognitiv sökning och AI-anrikning – Azure Search
description: Innehålls extrahering, naturlig språk bearbetning (NLP) och bild bearbetning för att skapa sökbart innehåll i Azure Search indexering med kognitiva kunskaper och AI-algoritmer.
manager: nitinme
author: HeidiSteen
services: search
ms.service: search
ms.subservice: cognitive-search
ms.topic: overview
ms.date: 08/15/2019
ms.author: heidist
ms.openlocfilehash: 89c56496ed1f356ffedf4df93f1728bd4acda323
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639961"
---
# <a name="what-is-cognitive-search-in-azure-search"></a>Vad är "kognitiv sökning" i Azure Search?

Kognitiv sökning är en AI-funktion i Azure Search som används för att extrahera text från bilder, blobbar och andra ostrukturerade data källor – vilket gör att innehållet kan sökas i ett Azure Search-index. Extraktion och berikning implementeras genom kognitiva *kunskaper* som är kopplade till en indexerings pipeline. AI-berikningar stöds på följande sätt: 

+ Kunskaper om **bearbetning av naturligt språk** omfattar [enhets igenkänning](cognitive-search-skill-entity-recognition.md), [språk identifiering](cognitive-search-skill-language-detection.md), [extrahering av nyckel fraser](cognitive-search-skill-keyphrases.md), text manipulation och [sentiment identifiering](cognitive-search-skill-sentiment.md). Med dessa kunskaper kan ostrukturerad text utgå från nya formulär som är mappade som sökbara och filter bara fält i ett index.

+ **Bild bearbetnings** kunskaper omfattar [optisk tecken läsning (OCR)](cognitive-search-skill-ocr.md) och identifiering av [visuella funktioner](cognitive-search-skill-image-analysis.md), till exempel ansikts igenkänning, bild tolkning, bild igenkänning (berömda-användare och landmärken) eller attribut som färger eller bild orientering. Du kan skapa text representationer av bild innehåll, sökbar med alla fråge funktioner i Azure Search.

![Pipeline-diagram för kognitiv sökning](./media/cognitive-search-intro/cogsearch-architecture.png "Översikt över pipeline-sökning")

Kognitiva kunskaper i Azure Search baseras på maskin inlärnings modeller i API:er för Cognitive Services: [Visuellt innehåll](https://docs.microsoft.com/azure/cognitive-services/computer-vision/) -och [text analys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview). 

Naturligt språk och bild bearbetning används under fasen för data inmatning, med resultat som blir en del av ett dokuments sammansättning i ett sökbart index i Azure Search. Data visas som en Azure-datauppsättning och flyttas sedan till en indexerings pipeline med hjälp av de [inbyggda kunskaper](cognitive-search-predefined-skills.md) du behöver. Arkitekturen är utöknings bar så om de inbyggda färdigheterna inte räcker kan du skapa och koppla [anpassade kunskaper](cognitive-search-create-custom-skill-example.md) för att integrera anpassad bearbetning. Exempel kan vara en anpassad enhets-eller dokument klassificerare som riktar sig till en speciell domän, till exempel ekonomi, vetenskapliga publikationer eller medicin.

> [!NOTE]
> När du utökar omfattningen genom att öka frekvensen för bearbetning, lägga till fler dokument eller lägga till fler AI-algoritmer måste du [koppla en fakturerbar Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md). Avgifterna påförs när API: er anropas i Cognitive Services, och för avbildnings extrahering som en del av dokument-cracking-fasen i Azure Search. Det finns inga kostnader för text extrahering från dokument.
>
> Körningen av inbyggda kunskaper debiteras enligt den befintliga [Cognitive Services betala per](https://azure.microsoft.com/pricing/details/cognitive-services/)användning-pris. Priser för avbildnings extrahering beskrivs på [sidan Azure Search priser](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="when-to-use-cognitive-search"></a>När du ska använda kognitiv sökning

Kognitiv sökning, med färdiga kunskaper, passar bra för följande program scenarier:

+ Skannade dokument (JPEG) som du vill göra full text sökbar. Du kan bifoga en OCR-färdighet (optisk tecken läsning) för att identifiera, extrahera och mata in text från JPEG-filer.

+ PDF-filer med kombinerad bild och text. Text i PDF-filer kan extraheras under Azure Search indexering utan användning av kognitiv sökning, men det kan ofta uppstå ett bättre resultat än en standard indexering om du lägger till bilder och naturlig språk bearbetning.

+ Flerspråkigt innehåll som du vill använda för språk identifiering och eventuellt text översättning.

+ Ostrukturerade eller delvis strukturerade dokument med innehåll som har betydelse eller kontext som är dolt i det större dokumentet. 

  Blobbar innehåller ofta en stor innehålls förteckning som är förpackad i ett enda "fält". Genom att koppla färdigheter för avbildning och naturlig språk bearbetning till en indexerare kan du skapa ny information som är extant i rå data, men inte på annat sätt placeras som distinkta fält. Vissa inbyggda kognitiva kunskaper som kan hjälpa dig med att extrahera nyckel fraser, sentiment analys och enhets igenkänning (personer, organisationer och platser).

  Dessutom kan förskapade kunskaper också användas för att omstrukturera innehåll genom text delning, sammanslagning och form åtgärder.

Anpassade kunskaper kan stödja mer komplexa scenarier, till exempel igenkänning av formulär eller anpassad identifiering av entiteter med hjälp av en modell som du anger och omsluter i det [anpassade webb gränssnittet för kompetens](cognitive-search-custom-skill-interface.md). Flera exempel på anpassade kunskaper är [formulär igenkänning](), integrering av [API för entitetsökning i Bing](https://docs.microsoft.com/azure/search/cognitive-search-create-custom-skill-example)och [anpassad enhets igenkänning](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).


## <a name="component-pipeline-of-cognitive-search"></a>Komponent pipeline för kognitiv sökning

En kognitiv sökning-pipeline baseras på [Azure Search *indexerare* ](search-indexer-overview.md) som crawlar data källor och tillhandahåller index bearbetning från slut punkt till slut punkt. Färdigheterna är nu kopplade till indexerare, fångar upp och berikar dokument enligt de färdigheter som du definierar. När du har indexerat kan du komma åt innehåll via Sök begär Anden via alla [frågetyper som stöds av Azure Search](search-query-overview.md).  Om du inte har använt indexerare i det här avsnittet vägleder vi dig genom stegen.

### <a name="step-1-connection-and-document-cracking-phase"></a>Steg 1: Fasen för anslutning och dokument sprickor

I början av pipelinen har du ostrukturerad text eller innehåll som inte är text (till exempel bilder och skannade dokument JPEG-filer). Data måste finnas i en Azure Data Storage-tjänst som kan nås av en indexerare. Indexerare kan "knäcka" käll dokument för att extrahera text från källdata.

![Dokument sprickors fas](./media/cognitive-search-intro/document-cracking-phase-blowup.png "dokument sprickor")

 Källor som stöds är Azure Blob Storage, Azure Table Storage, Azure SQL Database och Azure Cosmos DB. Textbaserat innehåll kan extraheras från följande filtyper: PDF-filer, Word-, PowerPoint-och CSV-filer. En fullständig lista finns i [format som stöds](search-howto-indexing-azure-blob-storage.md#supported-document-formats).

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>Steg 2: Kognitiva färdigheter och anriknings fas

Berikning är genom kognitiva *kunskaper* som utför atomiska åtgärder. När du har text innehåll från en PDF kan du till exempel använda identifiering av enhets igenkännings språk, eller extrahering av nyckel fraser för att skapa nya fält i indexet som inte är tillgängliga i källan. Den samling med kunskaper som används i din pipeline kallas helt en *färdigheter*.  

![Anriknings fas](./media/cognitive-search-intro/enrichment-phase-blowup.png "anriknings fas")

En färdigheter baseras på [fördefinierade kognitiva kunskaper](cognitive-search-predefined-skills.md) eller [anpassade kunskaper](cognitive-search-create-custom-skill-example.md) som du anger och ansluter till färdigheter. En färdigheter kan vara minimal eller mycket komplicerad och bestämmer inte bara typ av bearbetning, utan även ordningen på åtgärder. En färdigheter plus de fält mappningar som definierats som en del av en indexerare anger helt en pipeline för anrikning. Mer information om hur du hämtar alla dessa delar finns i [definiera en färdigheter](cognitive-search-defining-skillset.md).

Internt genererar pipelinen en samling av förrikade dokument. Du kan bestämma vilka delar av de berikade dokumenten som ska mappas till index bara fält i ditt sökindex. Om du till exempel använde extraheringen av nyckel fraser och entitets igenkänning, blir de nya fälten en del av det förrikade dokumentet och de kan mappas till fält i indexet. Se [anteckningar](cognitive-search-concept-annotations-syntax.md) om du vill veta mer om indata/utdata-formulär.

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>Lägg till ett knowledgeStore-element för att spara dina berikare

[Search service REST API-version = 2019-05-06](search-api-preview.md) utökar färdighetsuppsättningar med en knowledgeStore-definition som tillhandahåller en Azure Storage-anslutning och projektioner som beskriver hur de är lagrade. 

Genom att lägga till ett kunskaps lager till en färdigheter kan du projicera en representation av dina kunskaper för andra scenarier än fullständig texts ökning. Mer information finns i [Vad är kunskaps lager](knowledge-store-concept-intro.md).

### <a name="step-3-search-index-and-query-based-access"></a>Steg 3: Sök index och fråge-baserad åtkomst

När bearbetningen är färdig har du ett sökindex som består av omfattande dokument, fullständigt text – sökbar i Azure Search. Att [fråga indexet](search-query-overview.md) är hur utvecklare och användare får åtkomst till det innehåll som genereras av pipelinen. 

![Index med sökikon](./media/cognitive-search-intro/search-phase-blowup.png "Index med sökikon")

Indexet är som alla andra som du kan skapa för Azure Search: du kan komplettera med anpassade analyser, anropa oskarpa Sök frågor, lägga till filtrerad sökning eller experimentera med bedömnings profiler för att ändra form på Sök resultaten.

Index genereras från ett index schema som definierar fält, attribut och andra konstruktioner som är kopplade till ett särskilt index, till exempel bedömnings profiler och synonym mappningar. När ett index har definierats och fyllts i kan du indexera stegvis för att hämta nya och uppdaterade käll dokument. Vissa ändringar kräver en fullständig återuppbyggnad. Du bör använda en liten data uppsättning tills schema designen är stabil. Läs mer i informationen om hur du [återskapar ett index](search-howto-reindex.md).

<a name="feature-concepts"></a>

## <a name="key-features-and-concepts"></a>Huvudfunktioner och koncept

| Begrepp | Beskrivning| Länkar |
|---------|------------|-------|
| Färdigheter | En namngiven resurs på översta nivån som innehåller en samling kunskaper. En färdigheter är en pipeline för anrikning. Den anropas vid indexering av en indexerare. | [Definiera en färdigheter](cognitive-search-defining-skillset.md) |
| Kognitiva kunskaper | En atomisk omvandling i en anriknings pipeline. Ofta är det en komponent som extraherar eller härleder struktur, och därmed förstärker din förståelse av indata. Nästan alltid är utdatan textbaserad och bearbetningen är en text-och bearbetnings bearbetning av naturligt språk eller bild bearbetning som extraherar eller genererar text från bildfiler. Utdata från en färdighet kan mappas till ett fält i ett index eller användas som indata för en underordnad berikning. En kunskap är antingen fördefinierad och tillhandahålls av Microsoft, eller anpassad: skapas och distribueras av dig. | [Fördefinierade kunskaper](cognitive-search-predefined-skills.md) |
| Extrahering av data | Omfattar ett brett utbud av bearbetning, men tillhör kognitiv sökning, används vanligt vis för att extrahera data (en entitet) från en källa som inte tillhandahåller den informationen internt. | [Kompetens för enhets igenkänning](cognitive-search-skill-entity-recognition.md)| 
| Bearbetning av avbildning | Härleder text från en bild, till exempel möjlighet att identifiera ett landmärke eller extrahera text från en bild. Vanliga exempel är OCR för att lyfta tecken från en JPEG-fil (scannad dokument) eller som identifierar ett gatu namn i ett fotografi som innehåller ett gatu tecken. | [Bild analys kunskaper](cognitive-search-skill-image-analysis.md) eller [OCR-kunskaper](cognitive-search-skill-ocr.md)
| Bearbetning av naturligt språk | Text bearbetning för insikter och information om text inmatningar. Språk identifiering, sentiment analys och extrahering av nyckel fraser är kunskaper som omfattas av bearbetning av naturligt språk.  | [Extrahering av diskussionsämne kunskaper](cognitive-search-skill-keyphrases.md), [Språkidentifiering kunskaper](cognitive-search-skill-language-detection.md), [Attitydanalys kunskaper](cognitive-search-skill-sentiment.md) |
| Dokument sprickor | Processen att extrahera eller skapa text innehåll från icke-text källor under indexering. Optisk tecken läsning (OCR) är ett exempel, men vanligt vis refererar det till funktioner för kärn funktioner som indexeraren extraherar innehåll från programfiler. Data källan som tillhandahåller käll fils platsen och index definitions definitionen som tillhandahåller fält mappningar är båda viktiga faktorer i dokument sprickor. | Se [indexerare](search-indexer-overview.md) |
| Givning | Konsolidera text fragment i en större struktur eller dela upp större text segment i en hanterbar storlek för ytterligare bearbetning i följd. | [Formaren skicklighet](cognitive-search-skill-shaper.md), [text fusions kompetens](cognitive-search-skill-textmerger.md), [text delnings kunskaper](cognitive-search-skill-textsplit.md) |
| Omfattande dokument | En intern överförings struktur, som genereras under bearbetningen, med slutliga utdata som avspeglas i ett sökindex. En färdigheter bestämmer vilka-anrikningar som utförs. Fält mappningar avgör vilka data element som läggs till i indexet. Du kan också skapa ett kunskaps lager för att hålla kvar och utforska omfattande dokument med verktyg som Storage Explorer, Power BI eller andra verktyg som ansluter till Azure Blob Storage. | Se [Knowledge Store (för hands version)](knowledge-store-concept-intro.md). |
| Indexerare |  En Crawler som extraherar sökbara data och metadata från en extern data källa och fyller i ett index baserat på fält-till-fält-mappningar mellan indexet och data källan för dokument sprickor. För kognitiva sökanrikninger anropar indexeraren en färdigheter och innehåller fält mappningarna som kopplar berikade utdata till mål fält i indexet. Index definitions definitionen innehåller alla instruktioner och referenser för pipeline-åtgärder och pipelinen anropas när du kör indexeraren. | [Indexerare](search-indexer-overview.md) |
| Datakälla  | Ett objekt som används av en indexerare för att ansluta till en extern data källa med typer som stöds i Azure. | Se [indexerare](search-indexer-overview.md) |
| Index | Ett beständigt sökindex i Azure Search som bygger på ett index schema som definierar fält strukturen och användningen. | [Index i Azure Search](search-what-is-an-index.md) | 

<a name="where-do-i-start"></a>

## <a name="where-do-i-start"></a>Vad ska jag börja med?

**Steg 1: [Skapa en Azure Search resurs](search-create-service-portal.md)** 

**Steg 2: Prova några snabb starter och exempel för praktisk upplevelse**

+ [Snabb start (portal)](cognitive-search-quickstart-blob.md)
+ [Självstudie (HTTP-förfrågningar)](cognitive-search-tutorial-blob.md)
+ [Exempel: Skapa en anpassad färdighet för kognitiv sökning (C#)](cognitive-search-create-custom-skill-example.md)

Vi rekommenderar den kostnads fria tjänsten i inlärnings syfte, men antalet kostnads fria transaktioner är begränsat till 20 dokument per dag. Om du vill köra både snabb starten och självstudien på en dag, använder du en mindre fil uppsättning (10 dokument) så att du kan få plats i båda övningarna eller ta bort den indexerare som du använde i snabb starten eller självstudien.

**Steg 3: Granska API: et**

Du kan använda rest `api-version=2019-05-06` on-begäranden eller .NET SDK. 

I det här steget används REST-API: er för att bygga en kognitiv Sök lösning. Endast två API: er har lagts till eller utökas för kognitiv sökning. Andra API: er har samma syntax som de allmänt tillgängliga versionerna.

| REST-API | Beskrivning |
|-----|-------------|
| [Skapa datakälla](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | En resurs som identifierar en extern data källa som tillhandahåller källdata som används för att skapa dokument som har omfattande data.  |
| [Skapa färdigheter (API-version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | Detta API är speciellt för kognitiv sökning. Det är en resurs som koordinerar användningen av [fördefinierade kunskaper](cognitive-search-predefined-skills.md) och [anpassade kognitiva kunskaper](cognitive-search-custom-skill-interface.md) som används i en anriknings pipeline vid indexering. |
| [Skapa index](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Ett schema som uttrycker ett Azure Search-index. Fält i index kartan till fält i källdata eller till fält som tillverkas under anriknings fasen (till exempel ett fält för organisations namn som skapats av enhets igenkänning). |
| [Skapa indexerare (API-version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)  | En resurs som definierar komponenter som används vid indexering: inklusive en data källa, en färdigheter, fält associationer från käll-och mellanliggande data strukturer till mål index och själva indexet. Att köra indexeraren är utlösaren för data inmatning och berikning. Utdata är ett sökindex baserat på index schemat, ifyllt med källdata, berikade med färdighetsuppsättningar. Detta befintliga API utökas för kognitiva Sök scenarier med inkludering av en färdigheter-egenskap. |

**Initialiseringschecklistan Ett typiskt arbets flöde**

1. Delmängd dina Azure-källdata i ett representativt exempel. Indexeringen tar tid så att den börjar med en liten, representativ data uppsättning och sedan skapar den stegvis i takt med att din lösning är vuxen.

1. Skapa ett [data käll objekt](https://docs.microsoft.com/rest/api/searchservice/create-data-source) i Azure Search för att tillhandahålla en anslutnings sträng för data hämtning.

1. Skapa en [färdigheter](https://docs.microsoft.com/rest/api/searchservice/create-skillset) med hjälp av anriknings steg.

1. Definiera [index schemat](https://docs.microsoft.com/rest/api/searchservice/create-index). *Fält* samlingen innehåller fält från källdata. Du bör också utvärderar ytterligare fält för att innehålla genererade värden för innehåll som skapas under anrikning.

1. Definiera [indexeraren](https://docs.microsoft.com/rest/api/searchservice/create-skillset) som refererar till data källan, färdigheter och indexet.

1. I indexeraren lägger du till *outputFieldMappings*. Det här avsnittet mappar utdata från färdigheter (i steg 3) till indata-fälten i index schemat (i steg 4).

1. Skicka *skapa indexerare* -begäran som du nyss skapade (en post-begäran med en indexerare-definition i begär ande texten) för att uttrycka indexeraren i Azure Search. I det här steget ska du köra indexeraren, anropa pipelinen.

1. Kör frågor för att utvärdera resultat och ändra kod för att uppdatera färdighetsuppsättningar, schema eller Indexer-konfiguration.

1. [Återställ indexeraren](search-howto-reindex.md) innan du skapar pipelinen igen.

Mer information om vissa frågor eller problem finns i [fel söknings tips](cognitive-search-concept-troubleshooting.md).

## <a name="next-steps"></a>Nästa steg

+ [Dokumentation om kognitiv sökning](cognitive-search-resources-documentation.md)
+ [Snabbstart: Prova kognitiv sökning i en portal genom gång](cognitive-search-quickstart-blob.md)
+ [Självstudier: Lär dig de kognitiva Sök-API: erna](cognitive-search-tutorial-blob.md)
+ [Översikt över kunskaps lager](knowledge-store-concept-intro.md)
+ [Genom gång av kunskaps lager](knowledge-store-howto.md)
