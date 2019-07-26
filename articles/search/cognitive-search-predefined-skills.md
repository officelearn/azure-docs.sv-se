---
title: Inbyggd data extrahering, naturligt språk, bild bearbetning – Azure Search
description: Data extrahering, naturligt språk, kognitiva bild bearbetnings kunskaper Lägg till semantik och struktur till rå data i en Azure Search pipeline.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 3d0c7c10b03169e5d78b833df5a26d83b04b9240
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347837"
---
# <a name="predefined-skills-for-content-enrichment-azure-search"></a>Fördefinierade kunskaper för innehålls anrikning (Azure Search)

I den här artikeln får du lära dig om kognitiva färdigheter som medföljer Azure Search. En *kognitiv kunskap* är en åtgärd som transformerar innehåll på ett visst sätt. Ofta är det en komponent som extraherar data eller härleder struktur, och därmed förstärker vår förståelse av indata. Nästan alltid är utdatan text-baserad. En *färdigheter* är en samling kunskaper som definierar anriknings pipelinen. 

> [!NOTE]
> När du utökar omfattningen genom att öka frekvensen för bearbetning, lägga till fler dokument eller lägga till fler AI-algoritmer måste du [koppla en fakturerbar Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md). Avgifterna påförs när API: er anropas i Cognitive Services, och för avbildnings extrahering som en del av dokument-cracking-fasen i Azure Search. Det finns inga kostnader för text extrahering från dokument.
>
> Körningen av inbyggda kunskaper debiteras enligt den befintliga [Cognitive Services betala per](https://azure.microsoft.com/pricing/details/cognitive-services/)användning-pris. Priser för avbildnings extrahering beskrivs på [sidan Azure Search priser](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="predefined-skills"></a>Fördefinierade kunskaper

Flera färdigheter är flexibla i vad de använder eller producerar. I allmänhet baseras de flesta kunskaper på förtränade modeller, vilket innebär att du inte kan träna modellen med dina egna tränings data. I följande tabell räknas och beskrivs de kunskaper som tillhandahålls av Microsoft. 

| Kvalifikation | Beskrivning |
|-------|-------------|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Den här kunskapen använder en förtränad modell för att identifiera viktiga fraser baserade på placering av termer, språk regler, närhet till andra villkor och hur ovanlig termen är i käll data. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Den här kunskapen använder en förtränad modell för att identifiera vilket språk som används (ett språk-ID per dokument). När flera språk används i samma text segment, är utdata LCID för det mest använda språket.|
| [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md) | Konsoliderar text från en samling fält till ett enda fält.  |
| [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | Den här kunskapen använder en förtränad modell för att upprätta entiteter för en fast uppsättning kategorier: personer, plats, organisation, e-post, URL: er, DateTime-fält. |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | Den här kunskapen använder en förtränad modell för att räkna upp positiva eller negativa sentiment på en post efter post. Poängen är mellan 0 och 1. Neutrala Poäng inträffar för både null-fall när sentiment inte kan identifieras och för text som betraktas som neutral.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Delar upp text i sidor så att du kan utöka eller utöka innehållet stegvis. |
| [Microsoft. färdigheter. text. TranslationSkill (för hands version)](cognitive-search-skill-text-translation.md) | Den här kunskapen använder en förtränad modell för att översätta inmatad text till en rad olika språk för normalisering eller lokalisering av användnings fall. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Den här kunskapen använder en algoritm för avbildnings avkänning för att identifiera innehållet i en bild och generera en text beskrivning. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Optisk typsnitts igenkänning. |
| [Microsoft. färdigheter. util. ConditionalSkill](cognitive-search-skill-conditional.md) | Tillåter filtrering, tilldelar ett standardvärde och sammanfogar data baserat på ett villkor.|
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Mappar utdata till en komplex typ (en data typ med flera delar, som kan användas för ett fullständigt namn, en rad med flera rader eller en kombination av efter namn och personligt ID.) |
| [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md) | Tillåter utökning av kognitiv Sök pipelines genom att göra ett HTTP-anrop till ett anpassat webb-API |


Vägledning om hur du skapar en [anpassad färdighet](cognitive-search-custom-skill-web-api.md)finns i [så här definierar du ett anpassat gränssnitt](cognitive-search-custom-skill-interface.md) och [exempel: Skapa en anpassad färdighet för kognitiv sökning](cognitive-search-create-custom-skill-example.md).

## <a name="see-also"></a>Se också

+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
+ [Definition av anpassat kompetens gränssnitt](cognitive-search-custom-skill-interface.md)
+ [Självstudier: Omfattande indexering med kognitiv sökning](cognitive-search-tutorial-blob.md)
