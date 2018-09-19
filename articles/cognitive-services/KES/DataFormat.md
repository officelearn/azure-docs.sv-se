---
title: Dataformatet - Knowledge API för tjänst för Kunskapsutveckling
titlesuffix: Azure Cognitive Services
description: Läs mer om dataformatet i den kunskap utforskning Service (KES) API.
services: cognitive-services
author: bojunehsu
manager: cgronlun
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 2c67ff1f7a3713b9418458bb7904a35808532293
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129292"
---
# <a name="data-format"></a>Dataformat

Datafilen beskriver listan med objekt som ska indexeras.
Varje rad i filen anger attributvärden för ett objekt i [JSON-format](http://json.org/) med UTF-8-kodning.
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
