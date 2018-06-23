---
title: Metoden Analyzers i Lingistic analys API | Microsoft Docs
description: Analyzers REST API innehåller en lista över analyzers som stöds av tjänsten i kognitiva Microsoft-tjänster.
services: cognitive-services
author: RichardSunMS
manager: wkwok
ms.service: cognitive-services
ms.component: linguistic-analysis
ms.topic: article
ms.date: 06/30/2016
ms.author: lesun
ms.openlocfilehash: 3fc243a0da77c5bae9009929f2b82e1353347752
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351462"
---
# <a name="analyzers-method"></a>Analyzers-metoden

Den **analyzers** REST API innehåller en lista över analyzers som för närvarande stöds av tjänsten.
Svaret innehåller sina [namn](Analyzer-Names.md) och de språk som stöds av varje (till exempel ”SV” för engelska).

## <a name="request-parameters"></a>Begäranparametrar
Ingen

<br>

## <a name="response-parameters"></a>Svarsparametrar
Namn | Typ | Beskrivning
-----|------|--------------
språk | lista med strängar | lista över två bokstav ISO språkkoder som som den här analyzer kan användas.
id   | sträng | unikt ID för den här analyzer
typ | sträng | bred typ av analyzer här
specifikationen | sträng | namnet på den specifikation som används för den här analyzer
implementering | sträng | Beskrivning av modellen och/eller algoritmen bakom det här analyzer

<br>
## <a name="example"></a>Exempel
Hämta /analyzers

Svaret: JSON
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
