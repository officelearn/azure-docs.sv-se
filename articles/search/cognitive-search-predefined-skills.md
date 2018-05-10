---
title: Fördefinierade extrahering av data, naturligt språk, bild bearbetning kunskaper (Azure Search) | Microsoft Docs
description: Data extrahering naturligt språk avbildningen bearbetning kognitiva kunskaper lägga till semantik och struktur rådata innehållet i en Azure Seach pipeline.
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 870cf9629c7af8faee0ce5709199b64910b27ffb
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="predefined-skills-for-content-enrichment-azure-search"></a>Fördefinierade kunskaper för innehåll berikande (Azure Search)

I den här artikeln får information du om kognitiva kunskaper som medföljer kognitiva sökning. En *kognitiva kunskaper* är en åtgärd som omvandlar innehåll på något sätt. Det är ofta en komponent som hämtar data eller härleder struktur och därför förstärker våra förståelse av indata. Utdata är nästan alltid textbaserade. En *kunskaper* är samling kunskaper som definierar berikande pipeline. 

## <a name="predefined-skills"></a>Fördefinierade kunskaper

Flera kunskaper är flexibla i vad de använder eller skapa. I allmänhet baseras de flesta kunskaper på före tränade modeller, vilket innebär att du inte kan träna modellen med hjälp av utbildningsdata. Anvisningar om hur du skapar en anpassad kunskap finns [hur du definierar ett anpassat gränssnitt](cognitive-search-custom-skill-interface.md) och [exempel: skapa en anpassad kunskap](cognitive-search-create-custom-skill-example.md). I följande tabell räknar och beskriver de kunskaper som tillhandahålls av Microsoft. 

| Kunskaper | Beskrivning |
|-------|-------------|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Kompetensen använder en pretrained modell för att identifiera viktiga fraser baserat på termen placering, språkliga regler, närhet till andra villkor och hur ovanliga termen ligger inom källdata. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Denna kunskaper använder en pretrained modell för att identifiera vilka språk är användas (ett språk-ID per dokument). När flera språk används i samma text segment, är utdata LCID för språket som främst används.|
| [Microsoft.Skills.Text.MergerSkill](cognitive-search-skill-textmerger.md) | Konsoliderar text från en uppsättning fält i ett enda fält.  |
| [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md) | Kompetensen använder en pretrained modell för att upprätta entiteter för en fast mängd kategorier: personer, plats, organisation. |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | Kompetensen använder en pretrained modell för att poängsätta positiva eller negativa sentiment på grundval av post med. Poängen är mellan 0 och 1. Språkneutralt resultat inträffar för null fallet när sentiment inte kan identifieras och som anses neutral för text.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Delar upp text i sidor så att du kan förbättra eller utöka innehåll inkrementellt. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Kompetensen använder en algoritm för avbildning för att identifiera innehållet i en avbildning och generera en beskrivning. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | OCR. |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Maps utdata till en komplex typ (en flerdelade datatyp, som kan användas för ett fullständigt namn, en flerradig-adress eller en kombination av efternamn och ett personligt ID.) |

## <a name="see-also"></a>Se också

+ [Hur du definierar en kunskaper](cognitive-search-defining-skillset.md)
+ [Anpassade egenskapsdefinition gränssnitt](cognitive-search-custom-skill-interface.md)
+ [Självstudier: Utökat indexering med kognitiva search](cognitive-search-tutorial-blob.md)