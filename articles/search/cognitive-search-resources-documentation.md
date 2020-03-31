---
title: Dokumentationslänkar för AI-anrikning
titleSuffix: Azure Cognitive Search
description: En kommenterad lista med artiklar, självstudier, exempel och blogginlägg relaterade till AI-anrikningsarbetsbelastningar i Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: d2b25fb93a1e35ffa82cf49c60d181b841b1692d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616183"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Dokumentationsresurser för AI-anrikning i Azure Cognitive Search

AI-anrikning är en funktion för Azure Cognitive Search-indexering som hittar latent information i icke-textkällor och odifferentierad text, omvandla den till fulltextsökbart innehåll i Azure Cognitive Search.

Följande artiklar är den fullständiga dokumentationen för AI-anrikning.

## <a name="getting-started"></a>Komma igång
+ [Introduktion till AI i Azure Cognitive Search](cognitive-search-concept-intro.md)
+ [Snabbstart: Skapa en kognitiv kompetens i Azure-portalen](cognitive-search-quickstart-blob.md)
+ [Självstudiekurs: Berikad indexering med AI](cognitive-search-tutorial-blob.md)
+ [Exempel: Skapa en anpassad färdighet för AI-anrikning](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Så här får du vägledning
+ [Hur man definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Referera till anteckningar i en kompetensuppsättning](cognitive-search-concept-annotations-syntax.md)
+ [Så här mappar du fält till ett index](cognitive-search-output-field-mapping.md)
+ [Så här bearbetar och extraherar du information från bilder](cognitive-search-concept-image-scenarios.md)
+ [Så här återskapar du ett Azure Cognitive Search-index](search-howto-reindex.md)
+ [Så här definierar du ett anpassat kompetensgränssnitt](cognitive-search-custom-skill-interface.md)
+ [Felsökningstips](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Referens

+ [Inbyggda färdigheter](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.skills.text.entityRecognitionskill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft.Skills.text.mergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)
  + [Microsoft.Skills.text.splitskill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.text.sentimentskill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.text.translationskill](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.vision.imageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.util.conditionalskill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.documentExtractionSkill](cognitive-search-skill-document-extraction.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ Anpassade färdigheter
  + [Microsoft.Skills.Custom.webapiskill](cognitive-search-custom-skill-web-api.md)

+ [Inaktuella färdigheter](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

+ [REST API](https://docs.microsoft.com/rest/api/searchservice/)
  + [Skapa Skillset (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Skapa indexerare (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Se även

+ [REST-API:er för Azure Cognitive Search](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indexerare i Azure Cognitive Search](search-indexer-overview.md)
+ [Vad är Azure Cognitive Search?](search-what-is-azure-search.md)
