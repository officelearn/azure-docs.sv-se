---
title: Inbyggd text- och bildbehandling under indexering
titleSuffix: Azure Cognitive Search
description: Dataextrahering, naturligt språk, bildbehandling kognitiva färdigheter lägga semantik och struktur till rått innehåll i en Azure Cognitive Search pipeline.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9e1f62dcdb122726fc1c08b7bea4e4c214ce7906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76933373"
---
# <a name="built-in-cognitive-skills-for-text-and-image-processing-during-indexing-azure-cognitive-search"></a>Inbyggda kognitiva färdigheter för text- och bildbehandling under indexering (Azure Cognitive Search)

I den här artikeln får du lära dig mer om de kognitiva färdigheter som medföljer Azure Cognitive Search som du kan inkludera i en kompetens för att extrahera innehåll och struktur. En *kognitiv färdighet* är en modul eller åtgärd som omvandlar innehåll på något sätt. Ofta är det en komponent som extraherar data eller härleder struktur, och därför ökar vår förståelse av indata. Nästan alltid är utdata textbaserad. En *kompetens* är samling av färdigheter som definierar anrikning pipeline. 

> [!NOTE]
> När du utökar omfattningen genom att öka bearbetningsfrekvensen, lägga till fler dokument eller lägga till fler AI-algoritmer måste du [bifoga en fakturerbar Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md). Avgifter ackumuleras när du anropar API:er i Cognitive Services och för bildutvinning som en del av dokumentsprickningsfasen i Azure Cognitive Search. Det finns inga avgifter för textextrahering från dokument.
>
> Utförande av inbyggda färdigheter debiteras på den befintliga [Cognitive Services pay-as-you go pris](https://azure.microsoft.com/pricing/details/cognitive-services/). Prissättning för avbildningsextrahering beskrivs på [prissidan](https://go.microsoft.com/fwlink/?linkid=2042400)för Azure Cognitive Search .


## <a name="built-in-skills"></a>Inbyggda färdigheter

Flera färdigheter är flexibla i vad de konsumerar eller producerar. I allmänhet är de flesta färdigheter baserade på förutbildade modeller, vilket innebär att du inte kan träna modellen med hjälp av dina egna träningsdata. I följande tabell räknas upp och beskrivs de kunskaper som tillhandahålls av Microsoft. 

| Skicklighet | Beskrivning |
|-------|-------------|
|[Microsoft.Skills.Text.CustomEntityLookupSkill](cognitive-search-skill-custom-entity-lookup.md)| Söker efter text från en anpassad, användardefinierad lista med ord och fraser.|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Denna färdighet använder en förtränad modell för att upptäcka viktiga fraser baserat på termplacering, språkliga regler, närhet till andra termer och hur ovanlig termen är inom källdata. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Den här färdigheten använder en förtränad modell för att identifiera vilket språk som används (ett språk-ID per dokument). När flera språk används inom samma textsegment är utdata LCID för det huvudsakligen använda språket.|
| [Microsoft.Skills.text.mergeSkill](cognitive-search-skill-textmerger.md) | Konsoliderar text från en samling fält till ett enda fält.  |
| [Microsoft.skills.text.entityRecognitionskill](cognitive-search-skill-entity-recognition.md) | Den här färdigheten använder en förtränad modell för att upprätta entiteter för en fast uppsättning kategorier: personer, plats, organisation, e-post, webbadresser, datumfält. |
| [Microsoft.Skills.Text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)  | Denna färdighet använder en förtränad modell för att extrahera personligt identifierbar information från en viss text. Färdigheten ger också olika alternativ för att maskera de identifierade PII-entiteterna i texten.  |
| [Microsoft.Skills.text.sentimentskill](cognitive-search-skill-sentiment.md)  | Den här färdigheten använder en förtränad modell för att få positiva eller negativa sentiment på post efter post. Ställningen är mellan 0 och 1. Neutrala poäng förekommer både för null-fallet när sentimentet inte kan identifieras och för text som anses neutral.  |
| [Microsoft.Skills.text.splitskill](cognitive-search-skill-textsplit.md) | Delar upp text på sidor så att du kan berika eller utöka innehåll stegvis. |
| [Microsoft.Skills.text.translationskill](cognitive-search-skill-text-translation.md) | Den här färdigheten använder en förtränad modell för att översätta indatatexten till en mängd olika språk för normalisering eller lokaliseringsanvändningsfall. |
| [Microsoft.Skills.vision.imageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Den här färdigheten använder en algoritm för bildidentifiering för att identifiera innehållet i en bild och generera en textbeskrivning. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Optisk teckenigenkänning. |
| [Microsoft.Skills.util.conditionalskill](cognitive-search-skill-conditional.md) | Gör det möjligt att filtrera, tilldela ett standardvärde och slå samman data baserat på ett villkor.|
| [Microsoft.Skills.Util.documentExtractionSkill](cognitive-search-skill-document-extraction.md) | Extraherar innehåll från en fil i anrikningspipelinen. |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Mappar utdata till en komplex typ (en datatyp i flera delar, som kan användas för ett fullständigt namn, en flerradsadress eller en kombination av efternamn och en personlig identifierare.) |
| [Microsoft.Skills.Custom.webapiskill](cognitive-search-custom-skill-web-api.md) | Tillåter utökningsbarhet för en AI-anrikningspipeline genom att göra ett HTTP-anrop till ett anpassat webb-API |


Information om hur du skapar en [anpassad färdighet](cognitive-search-custom-skill-web-api.md)finns i [Så här definierar du ett anpassat gränssnitt](cognitive-search-custom-skill-interface.md) och [exempel: Skapa en anpassad färdighet för AI-anrikning](cognitive-search-create-custom-skill-example.md).

## <a name="see-also"></a>Se även

+ [Hur man definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Definition av gränssnitt för anpassade färdigheter](cognitive-search-custom-skill-interface.md)
+ [Självstudiekurs: Berikad indexering med AI](cognitive-search-tutorial-blob.md)
