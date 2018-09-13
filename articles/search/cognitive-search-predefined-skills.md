---
title: Fördefinierade extrahering av data, naturligt språk, bildbearbetning kunskaper (Azure Search) | Microsoft Docs
description: Extrahering av data, naturligt språk, kognitiva funktioner för bildbearbetning lägga till semantik och struktur till rå innehåll i en Azure Sök-pipeline.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: cf02946c772ce2dfd04fcd0ae478a560d095c092
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35964062"
---
# <a name="predefined-skills-for-content-enrichment-azure-search"></a>Fördefinierade kunskaper för innehåll berikande (Azure Search)

I den här artikeln får du lära dig om kognitiva kunskaper som medföljer Azure Search. En *kognitiva kunskaper* är en åtgärd som transformerar innehållet på något sätt. Ofta är det en komponent som extraherar data eller härleder struktur och därför förstärker vår förståelse för indata. Utdata är nästan alltid textbaserade. En *kompetens* är samling färdigheter som definierar berikande pipeline. 

## <a name="predefined-skills"></a>Fördefinierade kunskaper

Flera kunskaper är flexibla i vad de använda eller skapa. I allmänhet baseras de flesta kunskaper på förtränade modeller, vilket innebär att du kan träna modellen med hjälp av dina egna träningsdata. Anvisningar om hur du skapar en anpassad färdighet finns i [hur du definierar ett anpassat gränssnitt](cognitive-search-custom-skill-interface.md) och [exempel: skapa en anpassad färdighet](cognitive-search-create-custom-skill-example.md). I följande tabell räknar upp och beskriver de färdigheter som tillhandahålls av Microsoft. 

| Färdighet | Beskrivning |
|-------|-------------|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Kompetensen använder en pretrained modell för att identifiera viktiga fraser som baserat på termen placering, språkliga regler, närhet till andra villkor och hur ovanlig termen är i källdata. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Denna färdighet använder en pretrained modell att identifiera vilka språk är används (ett språk-ID per dokument). När flera språk används i samma text segment, är utdata LCID för de främst används.|
| [Microsoft.Skills.Text.MergerSkill](cognitive-search-skill-textmerger.md) | Konsoliderar text från en samling av fält till ett fält.  |
| [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md) | Kompetensen använder en pretrained modell för att upprätta entiteter för en fast uppsättning kategorier: personer, plats, organisation. |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | Kompetensen använder en pretrained modell för att bedöma positiv eller negativ attityd på basis av post med. Poängen är mellan 0 och 1. Neutrala resultat inträffar för både null fallet när sentiment inte kan identifieras och som anses neutral för text.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Delar upp text i sidor så att du kan utöka eller förbättra innehållet inkrementellt. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Kompetensen använder en algoritm för avbildning för att identifiera innehållet i en bild och generera en textbeskrivning. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Optisk teckenläsning. |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Maps-utdata till en komplex typ (en flerdelad datatyp, som kan användas för ett fullständigt namn, en flerradig-adress eller en kombination av efternamn och ett personligt ID.) |

## <a name="see-also"></a>Se också

+ [Hur du definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Anpassade egenskapsdefinition gränssnitt](cognitive-search-custom-skill-interface.md)
+ [Självstudie: Utökad indexering med kognitiva search](cognitive-search-tutorial-blob.md)