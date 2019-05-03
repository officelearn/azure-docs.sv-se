---
title: Inbyggda dataextrahering, naturligt språk, bildbearbetning – Azure Search
description: Extrahering av data, naturligt språk, kognitiva funktioner för bildbearbetning lägga till semantik och struktur till rå innehåll i en Azure Search-pipeline.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 7925f3aef4123fddd3a96c6e62971b881ae4cbc3
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65021860"
---
# <a name="predefined-skills-for-content-enrichment-azure-search"></a>Fördefinierade kunskaper för innehåll berikande (Azure Search)

I den här artikeln får du lära dig om kognitiva kunskaper som medföljer Azure Search. En *kognitiva kunskaper* är en åtgärd som transformerar innehållet på något sätt. Ofta är det en komponent som extraherar data eller härleder struktur och därför förstärker vår förståelse för indata. Utdata är nästan alltid textbaserade. En *kompetens* är samling färdigheter som definierar berikande pipeline. 

> [!NOTE]
> När du utökar omfattningen genom att öka frekvensen för bearbetning, att lägga till fler dokument eller att lägga till fler AI-algoritmer, måste du [bifoga en fakturerbar resurs för Cognitive Services](cognitive-search-attach-cognitive-services.md). Avgifter tillkommer när du anropar API: er i Cognitive Services och extrahering av avbildningen som en del av det dokumentknäckning steget i Azure Search. Det finns inga avgifter för textextrahering från dokument.
>
> Körningen av inbyggda färdigheter som ingår debiteras enligt den befintliga [Cognitive Services betala-som-du gå pris](https://azure.microsoft.com/pricing/details/cognitive-services/). Bild extrahering priser beskrivs i den [Azure Search sidan med priser](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="predefined-skills"></a>Fördefinierade kunskaper

Flera kunskaper är flexibla i vad de använda eller skapa. I allmänhet baseras de flesta kunskaper på förtränade modeller, vilket innebär att du kan träna modellen med hjälp av dina egna träningsdata. I följande tabell räknar upp och beskriver de färdigheter som tillhandahålls av Microsoft. 

| Färdighet | Beskrivning |
|-------|-------------|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Kompetensen använder en pretrained modell för att identifiera viktiga fraser som baserat på termen placering, språkliga regler, närhet till andra villkor och hur ovanlig termen är i källdata. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Denna färdighet använder en pretrained modell att identifiera vilka språk är används (ett språk-ID per dokument). När flera språk används i samma text segment, är utdata LCID för de främst används.|
| [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md) | Konsoliderar text från en samling av fält till ett fält.  |
| [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | Kompetensen använder en pretrained modell för att upprätta entiteter för en fast uppsättning kategorier: personer, plats, organisation, e-postmeddelanden, URL: er, datetime-fält. |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | Kompetensen använder en pretrained modell för att bedöma positiv eller negativ attityd på basis av post med. Poängen är mellan 0 och 1. Neutrala resultat inträffar för både null fallet när sentiment inte kan identifieras och som anses neutral för text.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Delar upp text i sidor så att du kan utöka eller förbättra innehållet inkrementellt. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Kompetensen använder en algoritm för avbildning för att identifiera innehållet i en bild och generera en textbeskrivning. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Optisk teckenläsning. |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Maps-utdata till en komplex typ (en flerdelad datatyp, som kan användas för ett fullständigt namn, en flerradig-adress eller en kombination av efternamn och ett personligt ID.) |
| [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md) | Tillåter utökningsbarhet för kognitiv sökning pipelinen genom att göra ett HTTP-anrop till ett anpassat webb-API |


Anvisningar om hur du skapar en [anpassade färdighet](cognitive-search-custom-skill-web-api.md), se [hur du definierar ett anpassat gränssnitt](cognitive-search-custom-skill-interface.md) och [exempel: skapa en anpassad färdighet](cognitive-search-create-custom-skill-example.md).

## <a name="see-also"></a>Se också

+ [Hur du definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Anpassade egenskapsdefinition gränssnitt](cognitive-search-custom-skill-interface.md)
+ [Självstudie: Utökad indexering med kognitiva search](cognitive-search-tutorial-blob.md)
