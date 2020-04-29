---
title: Dokumentations Länkar för AI-anrikning
titleSuffix: Azure Cognitive Search
description: En kommenterad lista över artiklar, självstudier, exempel och blogg inlägg som rör AI-arbetsbelastningar i Azure Kognitiv sökning.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: d2b25fb93a1e35ffa82cf49c60d181b841b1692d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77616183"
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

+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
  + [Microsoft. färdigheter. text. KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft. färdigheter. text. LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft. färdigheter. text. EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft. färdigheter. text. MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft. färdigheter. text. PIIDetectionSkill](cognitive-search-skill-pii-detection.md)
  + [Microsoft. färdigheter. text. SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft. färdigheter. text. SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft. färdigheter. text. TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft. färdigheter. vision. ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft. färdigheter. vision. OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft. färdigheter. util. ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft. färdigheter. util. DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)
  + [Microsoft. färdigheter. util. ShaperSkill](cognitive-search-skill-shaper.md)

+ Anpassade färdigheter
  + [Microsoft. färdigheter. Custom. WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Inaktuella färdigheter](cognitive-search-skill-deprecated.md)
  + [Microsoft. färdigheter. text. NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

+ [REST-API](https://docs.microsoft.com/rest/api/searchservice/)
  + [Skapa färdigheter (API-version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Skapa indexerare (API-version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Se även

+ [REST-API:er för Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indexerare i Azure Cognitive Search](search-indexer-overview.md)
+ [Vad är Azure Cognitive Search?](search-what-is-azure-search.md)
