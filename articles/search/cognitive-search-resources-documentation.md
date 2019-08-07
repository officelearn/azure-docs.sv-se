---
title: Kognitiv Sök dokumentations resurser – Azure Search
description: En kommenterad lista över artiklar, självstudier, exempel och blogg inlägg som rör kognitiva Sök arbets belastningar i Azure Search.
services: search
manager: cgronlun
author: HeidiSteen
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.subservice: cognitive-search
ms.openlocfilehash: c7c0d22944726959ae3eaf036d30bcde3ca86900
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68841137"
---
# <a name="documentation-resources-for-cognitive-search-workloads"></a>Dokumentations resurser för kognitiva Sök arbets belastningar

Kognitiv sökning, som nu är allmänt tillgängligt, är ett nytt berikande lager i Azure Search indexering som hittar latend information i icke-text källor och icke-differentierad text, omvandla den till text sökbar innehåll i Azure Search.

I följande artiklar finns fullständig dokumentation för kognitiv sökning.

## <a name="getting-started"></a>Komma igång
+ [Vad är kognitiv sökning?](cognitive-search-concept-intro.md)
+ [Snabbstart: Prova kognitiv sökning i portalen](cognitive-search-quickstart-blob.md)
+ [Självstudier: Lär dig de kognitiva Sök-API: erna](cognitive-search-tutorial-blob.md)
+ [Exempel: Skapa en anpassad färdighet för kognitiv sökning](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Instruktions vägledning
+ [Så här definierar du en färdigheter](cognitive-search-defining-skillset.md)
+ [Så här refererar du till anteckningar i en färdigheter](cognitive-search-concept-annotations-syntax.md)
+ [Så här mappar du fält till ett index](cognitive-search-output-field-mapping.md)
+ [Så här bearbetar och extraherar du information från bilder](cognitive-search-concept-image-scenarios.md)
+ [Återskapa ett Azure Search-index](search-howto-reindex.md)
+ [Definiera ett anpassat kunskaps gränssnitt](cognitive-search-custom-skill-interface.md)
+ [Felsökningstips](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Referens

+ [Fördefinierade kunskaper](cognitive-search-predefined-skills.md)
  + [Microsoft. färdigheter. text. KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft. färdigheter. text. TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft. färdigheter. util. ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ Anpassade färdigheter
  + [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Föråldrade kunskaper](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

+ [REST-API](https://docs.microsoft.com/rest/api/searchservice/)
  + [Skapa färdigheter (API-version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Skapa indexerare (API-version = 2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Se också

+ [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indexerare i Azure Search](search-indexer-overview.md)
+ [Vad är Azure Search?](search-what-is-azure-search.md)
