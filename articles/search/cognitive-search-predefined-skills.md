---
title: Inbyggda dataextrahering, naturligt språk, bildbearbetning – Azure Search
description: Extrahering av data, naturligt språk, kognitiva funktioner för bildbearbetning lägga till semantik och struktur till rå innehåll i en Azure Search-pipeline.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: bc1353ffb4514622ce0ef6e5c3ced76adc7f999f
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2018
ms.locfileid: "53314796"
---
# <a name="predefined-skills-for-content-enrichment-azure-search"></a>Fördefinierade kunskaper för innehåll berikande (Azure Search)

I den här artikeln får du lära dig om kognitiva kunskaper som medföljer Azure Search. En *kognitiva kunskaper* är en åtgärd som transformerar innehållet på något sätt. Ofta är det en komponent som extraherar data eller härleder struktur och därför förstärker vår förståelse för indata. Utdata är nästan alltid textbaserade. En *kompetens* är samling färdigheter som definierar berikande pipeline. 

> [!NOTE]
> Från och med den 21 December 2018 kan du associera en resurs för Cognitive Services med ett Azure Search-kompetens. Detta gör att vi börjar debitera för körning av kompetens. På det här datumet måste också börjar vi debitera för extrahering av avbildningen som en del av dokumentknäckning fasen. Textextrahering från dokument fortsätter att erbjudas utan extra kostnad.
>
> Körningen av inbyggda kunskaper debiteras med den befintliga [Cognitive Services betala-som-du gå pris](https://azure.microsoft.com/pricing/details/cognitive-services/) . Bild extrahering priser kommer att debiteras enligt priserna för förhandsversionen och beskrivs i den [Azure Search sidan med priser](https://go.microsoft.com/fwlink/?linkid=2042400). Lär dig [mer](cognitive-search-attach-cognitive-services.md).

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
+ [Självstudiekurs: Utökad indexering med kognitiva search](cognitive-search-tutorial-blob.md)