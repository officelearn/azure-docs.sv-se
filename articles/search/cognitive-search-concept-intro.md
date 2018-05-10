---
title: Kognitiva söka efter data extrahering naturligt språk som bearbetas i Azure Search | Microsoft Docs
description: Extrahering av data, bearbetning av naturligt språk (NLP) och avbildningen bearbetning för att skapa sökbara innehåll i Azure Search indexering med kognitiva kunskaper.
manager: cgronlun
author: HeidiSteen
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: heidist
ms.openlocfilehash: 853e8fbc0c7044dc97a29254bfd4130e7f81bbbd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="what-is-cognitive-search"></a>Vad är kognitiva sökningen?

Kognitiva search är en förhandsversion av funktionen för Azure Search tillgängliga på alla nivåer i södra centrala USA och Västeuropa som lägger till AI indexering arbetsbelastningar. Extrahering av data, bearbetning av naturligt språk och avbildningen bearbetning under indexeringen hittar Latenta informationen i Ostrukturerade eller icke-sökbart innehåll och gör det sökbara i Azure Search.

AI-integrering är via *kognitiva kunskaper* som utöka källdokument via sekventiella processer i vägen till ett sökindex. 

![Kognitiva Sök pipeline diagram](./media/cognitive-search-intro/cogsearch-architecture.png "kognitiva Sök pipeline-översikt")

Kunskaper som används under indexeringen är fördefinierade eller anpassade:

+ [Prefined kunskaper](cognitive-search-predefined-skills.md) baseras på samma AI-algoritmer som används i kognitiva Services API: er: [med namnet entitet Recognition](cognitive-search-skill-named-entity-recognition.md), [nyckel frasen extrahering](cognitive-search-skill-keyphrases.md), och [OCR](cognitive-search-skill-ocr.md) är bara några få. 

+ [Anpassade kunskaper](cognitive-search-create-custom-skill-example.md) kan utvecklas av du för någon särskild bearbetning som krävs. Exempel på anpassade kunskaper kan vara en kundentitet modul eller dokumentet klassificerare som mål för en specifik domän, till exempel Ekonomi, vetenskapliga publikationer eller medicinska.

> [!NOTE]
> Cognitive Search är en offentlig förhandsversion och kompetenskörning ges för närvarande kostnadsfritt. Priser för den här funktionen meddelas vid ett senare tillfälle.

## <a name="components-of-cognitive-search"></a>Komponenter i kognitiva sökning

Kognitiva Sök pipeline baseras på [Azure Search *indexerare* ](search-indexer-overview.md) som crawlas datakällor och ange bearbetning av index för slutpunkt till slutpunkt. Kunskaper är nu ansluten till indexerare som fångar upp och berika dokument enligt kunskaper som du definierar. När indexerad, kan du komma åt innehåll via search-begäranden genom alla [fråga typer som stöds av Azure Search](search-query-overview.md).  Om du är nybörjare på indexerare vägleder dig genom stegen i det här avsnittet.

### <a name="source-data-and-document-cracking-phase"></a>Källdata och dokumentera knäcka fas

I början av pipelinen har Ostrukturerade text- eller icke-textinnehåll (till exempel avbildningen och skannade dokument JPEG-filer). Data måste finnas i en Azure data storage-tjänst som kan användas av en indexerare. Indexerare kan ”knäcka” källdokument för att extrahera text från datakällan.

![Dokumentet sprickbildning fasen](./media/cognitive-search-intro/document-cracking-phase-blowup.png "dokumentet knäcka")

 Källor som stöds är Azure blob storage, Azure table storage, Azure SQL Database och Azure Cosmos DB. Text-baserat innehåll kan extraheras från följande filtyper: PDF-filer, Word, PowerPoint, CSV-filer. En fullständig lista finns [stöds formaten](search-howto-indexing-azure-blob-storage.md#supported-document-formats).

### <a name="cognitive-skills-and-enrichment-phase"></a>Kognitiva kunskaper och berikande fas

Berikande är via *kognitiva kunskaper* utför atomiska åtgärder. Till exempel när du har textinnehåll från en PDF-fil kan kan du använda entiteten recognition språkidentifiering eller viktiga frasen extrahering att skapa nya fält i ditt index som inte är tillgängliga internt i källan. Hela samlingen av kunskaper som används i din pipeline kallas en *kunskaper*.  

![Berikande fasen](./media/cognitive-search-intro/enrichment-phase-blowup.png "berikande fas")

En kunskaper baseras på [fördefinierade kognitiva kunskaper](cognitive-search-predefined-skills.md) eller [anpassade kunskaper](cognitive-search-create-custom-skill-example.md) du anger och ansluta till kunskaper. En kunskaper kan vara minimal eller mycket komplex och anger inte bara typ av bearbetning, men också utförs. En kunskaper plus fältmappningarna definierats som en del av en indexerare anger fullständigt berikande pipeline. Mer information om hur du drar alla dessa uppgifter tillsammans finns [definiera en kunskaper](cognitive-search-defining-skillset.md).

Pipelinen genereras internt, en samling av utökade dokument. Du kan välja vilka delar av utökade dokument ska mappas till indexeras fält i search-index. Till exempel om du har använt viktiga fraser uppackningen och kunskaper som entiteten recognition sedan dessa nya fält skulle bli en del av utökade dokumentet och de kan mappas till fält i ditt index. Se [anteckningar](cognitive-search-concept-annotations-syntax.md) lära dig mer om i/o-formationer.

### <a name="search-index-and-query-based-access"></a>Sökindex och frågebaserade åtkomst

När bearbetningen är klar har du en sökning Kristi som består av utökade dokument, text-sökbara i Azure Search. [Frågor till indexet](search-query-overview.md) är hur utvecklare och användare kan komma åt det utökade innehåll som genereras av pipelinen. 

![Index med sökikonen](./media/cognitive-search-intro/search-phase-blowup.png "Index med sökikonen")

Indexet är som andra du kan skapa för Azure Search: du kan komplettera med anpassade analyzers anropa fuzzy sökfrågor, lägga till filtrerade sökning eller experimentera med bedömningen profiler för att omforma sökresultatet.

Index genereras från ett indexeringsschema som definierar fälten attribut, och andra konstruktioner kopplad till ett visst index, till exempel bedömningen profiler och synonymen mappar. När ett index är definierat och fyllts, kan du indexera inkrementellt för att hämta upp nya och uppdaterade källdokument. Vissa ändringar kräver återskapa. Du bör använda en mindre mängd data tills schemat designen är stabil. Mer information finns i [så att bygga om index](search-howto-reindex.md).

<a name="feature-concepts"></a>

## <a name="key-features-and-concepts"></a>Huvudfunktioner och koncept

| Begrepp | Beskrivning| Länkar |
|---------|------------|-------|
| Kunskaper | En översta namngivna resurs som innehåller en samling kunskaper. En kunskaper är berikande pipeline. Den anropas under indexering av en indexerare. | [Definiera en kunskaper](cognitive-search-defining-skillset.md) |
| Kognitiva kunskaper | En atomisk omvandling i en berikande pipeline. Det är ofta en komponent som extraherar eller härleder struktur och därför förstärker din förståelse för indata. Nästan alltid utdata är textbaserade och bearbetning är naturligt språk bearbetning eller avbildning bearbetning som extraherar eller genererar text från avbildningen indata. Utdata från en kunskap kan mappas till ett fält i ett index eller används som indata för en underordnad berikande. En kunskap antingen fördefinierade och tillhandahålls av Microsoft eller anpassat: skapas och distribueras av du. | [Fördefinierade kunskaper](cognitive-search-predefined-skills.md) |
| Extrahering av data | Omfattar ett brett spektrum av bearbetning, men rör kognitiva sökning, namngiven enhet recognition kunskaper används normalt för att extrahera data (en entitet) från en källa som inte har denna information internt. | [Namngiven enhet Recognition kunskaper](cognitive-search-skill-named-entity-recognition.md)| 
| Bearbetning av avbildning | Härleder text från en avbildning, till exempel möjligheten att identifiera en Landmärke eller hämtar text från en avbildning. Vanliga exempel OCR för att lyfta tecken från en skannade dokument (JPEG)-fil eller upptäcker en gatuadress i ett foto som innehåller en gatuadress. | [Bild analys kunskaper](cognitive-search-skill-image-analysis.md) eller [OCR kunskaper](cognitive-search-skill-ocr.md)
| Bearbetning av naturligt språk | Text för insikter och information om text indata bearbetades. Identifiera språk, sentiment analys och viktiga frasen extrahering är kunskaper som faller under bearbetning av naturligt språk.  | [Nyckeln frasen extrahering kunskaper](cognitive-search-skill-keyphrases.md), [språk identifiering kunskaper](cognitive-search-skill-language-detection.md), [Sentiment analys kunskaper](cognitive-search-skill-sentiment.md) |
| Dokumentet hudsprickor | Processen för att extrahera eller skapa textinnehåll från icke-text källor under indexeringen. OCR (OCR) är ett exempel, men den avser vanligen indexeraren kärnfunktioner som indexeraren packar upp innehåll från filer. Datakällan som tillhandahåller källfilens plats och indexeraren definitionen tillhandahåller fältmappningar är både viktiga faktorer i dokumentet sprickbildning. | Se [indexerare](search-indexer-overview.md) |
| Shaping | Konsolidera textfragment i en större struktur eller omvänt redovisar större delar som text i en hanterbar storlek för ytterligare nedströms bearbetning. | [Formaren kunskaper](cognitive-search-skill-shaper.md), [Text fusion kunskaper](cognitive-search-skill-textmerger.md), [Text dela kunskaper](cognitive-search-skill-textsplit.md) |
| Utökade dokument | Ett övergående intern struktur, inte direkt åtkomliga i kod. Utökade dokument genereras under bearbetningen, men endast slutliga utdata sparas i en sökindex. Fältmappningar avgör vilka data läggs till i indexet. | Se [åtkomst till utökade dokument](cognitive-search-tutorial-blob.md#access-enriched-document). |
| Indexerare |  En crawler som sökbara data och metadata från en extern datakälla och fyller i ett index baserat på fältet till mappningar mellan index och datakälla för dokumentet knäcka. Kognitiva Sök enrichments indexeraren anropar en kunskaper och innehåller fältmappningarna associera berikande utdata till målfält i indexet. Pipelinen ska anropas när du kör indexeraren indexeraren definition innehåller alla instruktioner och referenser för pipeline-åtgärder. | [Indexerare](search-indexer-overview.md) |
| Datakälla  | Ett objekt som används av en indexerare för att ansluta till en extern datakälla av typer som stöds på Azure. | Se [indexerare](search-indexer-overview.md) |
| Index | En sparad sökning Kristi i Azure Search skapas från en indexeringsschema som definierar fältet struktur och användning. | [Index i Azure Search](search-what-is-an-index.md) | 


## <a name="where-do-i-start"></a>Vad ska jag börja med?

**Steg 1: Skapa en söktjänst i en region som tillhandahåller de API: er** 

+ Södra centrala USA
+ Västra Europa

**Steg 2: Praktisk erfarenhet till huvudserver arbetsflödet**

+ [Snabbstart (portal)](cognitive-search-quickstart-blob.md)
+ [Kursen (HTTP-begäranden)](cognitive-search-tutorial-blob.md)
+ [Exempel anpassade kunskaper (C#)](cognitive-search-create-custom-skill-example.md)

**Steg 3: Granska API (REST)**

För närvarande finns endast REST API: er. Använd `api-version=2017-11-11-Preview` för alla förfrågningar. Använd följande API: er för att skapa en lösning för kognitiva sökning. Bara två API: er läggs till eller utökad för kognitiva sökning. Andra API: er har samma syntax som de allmänt tillgängliga versionerna.

| REST-API | Beskrivning |
|-----|-------------|
| [Skapa datakälla](https://docs.microsoft.com/rest/api/searchservice/create-data-source)  | En resurs som identifierar en extern datakälla tillhandahåller källdata som används för att skapa utökade dokument.  |
| [Skapa kunskaper (api-version = 2017-11-11-Preview)](ref-create-skillset.md)  | En resurs som koordinera bruket av [fördefinierade kunskaper](cognitive-search-predefined-skills.md) och [anpassade kognitiva kunskaper](cognitive-search-custom-skill-interface.md) används i en pipeline berikande under indexeringen. |
| [Skapa Index](https://docs.microsoft.com/rest/api/searchservice/create-index)  | Ett schema som uttrycka ett Azure Search-index. Fält i indexet mappa till fält i datakällan eller ett fält som har tillverkats under fasen berikande (t.ex, ett fält för organisationsnamn som skapats av entiteten recognition). |
| [Skapa indexerare (api-version = 2017-11-11-Preview)](ref-create-skillset.md)  | En resurs som definierar komponenter som används vid indexering: en datakälla, en kunskaper, fältet kopplingar från käll- och mellanliggande datastrukturer till målet index och indexet sig själv. Köra indexeraren är utlösare för datapåfyllning och berikande. Utdata är en sökning Kristi baserat på schemat index, fylls i med källdata utökat via kunskaper.  |

**Checklista: Ett vanligt arbetsflöde**

1. Delmängd källdata Azure i ett representativt urval. Indexering tar tid så börja med en liten, representativ datauppsättning och sedan skapa det inkrementellt som din lösning utvecklas.

1. Skapa en [datakällobjektet](https://docs.microsoft.com/rest/api/searchservice/create-data-source) i Azure-sökning för att ange en anslutningssträng för hämtning av data.

1. Skapa en [kunskaper](ref-create-skillset.md) berikande steg.

1. Definiera den [indexeringsschema](https://docs.microsoft.com/rest/api/searchservice/create-index). Den *fält* samlingen innehåller fält från datakällan. Du bör också stub-ut ytterligare fält som ska innehålla genererade värden för innehåll som skapats under berikande.

1. Definiera den [indexeraren](ref-create-skillset.md) refererar till datakällan, kunskaper och index.

1. Inom indexeraren, lägger du till *outputFieldMappings*. Det här avsnittet mappar utdata från kunskaper (i steg 3) till indata fälten i indexeringsschema (i steg 4).

1. Skicka *skapa indexeraren* begär du just skapat (en POST-begäran med en definition av indexerare i frågans brödtext) för indexerare i Azure Search. Det här steget är hur köra indexeraren, anropar pipeline.

1. Köra frågor för att utvärdera resultaten och ändra koden för att uppdatera kunskaper, schemat eller indexerare konfiguration.

1. [Återställa indexeraren](search-howto-reindex.md) innan du återskapar pipeline.

Mer information om specifika frågor eller problem finns [felsökningstips](cognitive-search-concept-troubleshooting.md).

## <a name="next-steps"></a>Nästa steg

+ [Kognitiva Sök dokumentation](cognitive-search-resources-documentation.md)
+ [Snabbstart: Försök kognitiva sökning i en portal genomgång](cognitive-search-quickstart-blob.md)
+ [: Självstudier kognitiva sökningen API: er](cognitive-search-tutorial-blob.md)