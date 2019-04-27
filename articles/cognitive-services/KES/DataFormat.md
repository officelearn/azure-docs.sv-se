---
title: Dataformatet - Knowledge API för tjänst för Kunskapsutveckling
titlesuffix: Azure Cognitive Services
description: Läs mer om dataformatet i den kunskap utforskning Service (KES) API.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: bba257c61509d862bb3161625750f05a86af8770
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60815082"
---
# <a name="data-format"></a>Data Format

Datafilen beskriver listan med objekt som ska indexeras.
Varje rad i filen anger attributvärden för ett objekt i [JSON-format](https://json.org/) med UTF-8-kodning.
Förutom de attribut som definierats i den [schemat](SchemaFormat.md), varje objekt har ett valfritt ”logprob”-attribut som anger den relativa log sannolikheten bland objekten.
När tjänsten returnerar objekt i fallande sannolikheten, använder vi ”logprob” för att ange returnerade ordningen på matchande objekt.
Får en sannolikhet *p* mellan 0 och 1 motsvarande log sannolikheten kan beräknas som log (*p*), där log() är funktionen naturliga loggen.
När inget värde har angetts för logprob, används standardvärdet 0.

```json
{"logprob":-5.3, "Title":"latent dirichlet allocation", "Year":2003, "Author":{"Name":"david m blei", "Affiliation":"uc berkeley"}, "Author":{"Name":"andrew y ng", "Affiliation":"stanford"}, "Author":{"Name":"michael i jordan", "Affiliation":"uc berkeley"}}
{"logprob":-6.1, "Title":"probabilistic latent semantic indexing", "Year":1999, "Author":{"Name":"thomas hofmann", "Affiliation":"uc berkeley"}}
...
```

För String, GUID och Blob-attribut visas värdet som en JSON-sträng inom citattecken.  För numeriska attribut (Int32, Int64, Double) visas värdet som ett JSON-tal.  Värdet är ett JSON-objekt som anger de underordnade attributvärdena för sammansatt attribut.  För snabbare index versioner, presort objekten genom att minska sannolikheten för loggen.

I allmänhet är kan ett attribut ha 0 eller fler värden.  Om ett attribut inte har något värde, vi helt enkelt ta bort den från JSON-filen.  Om ett attribut har minst 2 värden, kan vi Upprepa attribut-värde-par i JSON-objekt.  Vi kan också tilldela en JSON-matris som innehåller flera värden till attributet.

```json
{"logprob":0, "Title":"0 keyword"}
{"logprob":0, "Title":"1 keyword", "Keyword":"foo"}
{"logprob":0, "Title":"2 keywords", "Keyword":"foo", "Keyword":"bar"}
{"logprob":0, "Title":"2 keywords (alt)", "Keyword":["foo", "bar"]}
```
