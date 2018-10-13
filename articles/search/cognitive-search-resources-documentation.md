---
title: Dokumentation för kognitiv sökning (Azure Search) | Microsoft Docs
description: En kommenterade lista över artiklar, självstudier, exempel och blogg inlägg relaterade till cognitive search arbetsbelastningar i Azure Search.
services: search
manager: cgronlun
author: HeidiSteen
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: heidist
ms.openlocfilehash: bad73d2bf8bdf4adee4ce6e40550e506a35a2083
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2018
ms.locfileid: "49165689"
---
# <a name="documentation-resources-for-cognitive-search-workloads"></a>Dokumentation för kognitiv sökning arbetsbelastningar

Cognitive search nu är i offentlig förhandsversion, ett nytt berikande-lager i Azure Search indexering som söker efter latent information i icke-text källor och odifferentierade text, omvandla det till fulltext sökbart innehåll i Azure Search.

I följande artiklar finns fullständig dokumentation för kognitiv sökning.

## <a name="getting-started"></a>Komma igång
+ [Vad är cognitive search?](cognitive-search-concept-intro.md)
+ [Snabbstart: Prova cognitive search i portalen](cognitive-search-quickstart-blob.md)
+ [: Självstudier kognitiv sökning API: er](cognitive-search-tutorial-blob.md)
+ [Exempel: skapa en anpassad kunskap](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Vägledning
+ [Hur du definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Hur du refererar till kommentarer i en kompetens](cognitive-search-concept-annotations-syntax.md)
+ [Mappning till ett index](cognitive-search-output-field-mapping.md)
+ [Hur du bearbetar och extrahera information från bilder](cognitive-search-concept-image-scenarios.md)
+ [Återskapar ett Azure Search-index](search-howto-reindex.md)
+ [Hur du definierar ett gränssnitt för anpassade funktioner](cognitive-search-custom-skill-interface.md)
+ [Felsökningstips](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Referens

+ [Fördefinierade kunskaper](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ [Förhandsversion av REST-API](search-api-2017-11-11-preview.md)
  + [Skapa kompetens (api-version = 2017-11-11-förhandsversion)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Skapa indexerare (api-version = 2017-11-11-förhandsversion)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Se också

+ [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indexerare i Azure Search](search-indexer-overview.md)
+ [Vad är Azure Search?](search-what-is-azure-search.md)
