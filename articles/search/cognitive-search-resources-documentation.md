---
title: Dokumentations Länkar för AI-anrikning
titleSuffix: Azure Cognitive Search
description: En kommenterad lista över artiklar, självstudier, exempel och blogg inlägg som rör AI-arbetsbelastningar i Azure Kognitiv sökning.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 11/04/2019
ms.openlocfilehash: e73e69f90b1228154d7f209c54c6b52cc03d5eb4
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76837798"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Dokumentations resurser för AI-anrikning i Azure Kognitiv sökning

AI-anrikning är en funktion i Azure Kognitiv sökning indexering som hittar latend information i icke-text källor och en icke-differentierad text, som omvandlar den till text sökbar innehåll i Azure Kognitiv sökning.

Följande artiklar är den fullständiga dokumentationen för AI-berikning.

## <a name="getting-started"></a>Komma igång
+ [Introduktion till AI i Azure Kognitiv sökning](cognitive-search-concept-intro.md)
+ [Snabb start: skapa en kognitiv färdigheter i Azure Portal](cognitive-search-quickstart-blob.md)
+ [Självstudie: omfattande indexering med AI](cognitive-search-tutorial-blob.md)
+ [Exempel: skapa en anpassad färdighet för AI-anrikning](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Instruktions vägledning
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
+ [Referera till anteckningar i en kompetensuppsättning](cognitive-search-concept-annotations-syntax.md)
+ [Så här mappar du fält till ett index](cognitive-search-output-field-mapping.md)
+ [Så här bearbetar och extraherar du information från bilder](cognitive-search-concept-image-scenarios.md)
+ [Återskapa ett Azure Kognitiv sökning-index](search-howto-reindex.md)
+ [Definiera ett anpassat kunskaps gränssnitt](cognitive-search-custom-skill-interface.md)
+ [Felsökningstips](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Referens

+ [Inbyggda kunskaper](cognitive-search-predefined-skills.md)
  + [Microsoft. färdigheter. text. KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft. färdigheter. text. PIIDetectionSkill](cognitive-search-skill-pii-detection.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft. färdigheter. text. TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft. färdigheter. util. ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft. färdigheter. util. DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ Anpassade färdigheter
  + [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Föråldrade kunskaper](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

+ [REST-API](https://docs.microsoft.com/rest/api/searchservice/)
  + [Skapa färdigheter (API-version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Skapa indexerare (API-version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Se också

+ [Azure Kognitiv sökning REST API](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indexerare i Azure Cognitive Search](search-indexer-overview.md)
+ [Vad är Azure Kognitiv sökning?](search-what-is-azure-search.md)
