---
title: 'Analyzer-metod - API: T med Lingistic analys'
titlesuffix: Azure Cognitive Services
description: 'Analysverktyg REST API innehåller en lista över analysverktyg som för närvarande stöds av API: T för Lingvistisk analys.'
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 06/30/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 1b33b60f674eebb15fdc6112e1d630b93b98494b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60404733"
---
# <a name="analyzers-method"></a>Analyzer-metod

> [!IMPORTANT]
> Förhandsversionen av Språkanalys upphörde den 9 augusti 2018. Vi rekommenderar att du använder [Azure Machine Learning-textanalysmoduler](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) för textbearbetning och -analys.

Den **analysverktyg** REST API innehåller en lista över analysverktyg som för närvarande stöds av tjänsten.
Svaret innehåller sina [namn](Analyzer-Names.md) och de språk som stöds av varje (till exempel ”SV” för engelska).

## <a name="request-parameters"></a>Begäranparametrar
Ingen

<br>

## <a name="response-parameters"></a>Svarsparametrar

Namn | Typ | Beskrivning
-----|------|--------------
Språk | lista med strängar | lista över två bokstaven ISO språkkoder som som den här analyzer kan användas.
id   | string | unikt ID för den här analyzer
typ | string | bred typ av analyzer här
Specifikationen | string | namnet på den specifikation som används för den här analyzer
Implementering | string | Beskrivning av modellen och/eller algoritmen bakom den här analyzer

<br>

## <a name="example"></a>Exempel
Hämta /analyzers

Svar: JSON
```json
[
    {
        "id": "22A6B758-420F-4745-8A3C-46835A67C0D2",
        "languages": ["en"],
        "kind": "Constituency_Tree",  
        "specification": "PennTreebank3",
        "implementation": "SplitMerge"
    },
    {
        "id" : "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
        "languages": ["en"],
        "kind": "POS_Tags",
        "specification": "PennTreebank3",
        "implementation": "cmm"
    },
    {
        "id" : "08EA174B-BFDB-4E64-987E-602F85DA7F72",
        "languages": ["en"],
        "kind": "Tokens",
        "specification":"PennTreebank3",
        "implementation": "regexes"
    }
]
```
