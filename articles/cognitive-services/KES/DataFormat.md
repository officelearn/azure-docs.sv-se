---
title: Dataformatet i Knowledge utforskning Service API | Microsoft Docs
description: Läs mer om dataformatet i den kunskap utforskning Service (KES) API i kognitiva Services.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: a763505ac6458d68df74ae73e71029b81202ec8b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351477"
---
# <a name="data-format"></a>Dataformatet
Datafilen beskriver listan över objekt att indexera.
Varje rad i filen anger attributvärden för ett objekt i [JSON-format](http://json.org/) med UTF-8-kodning.
Utöver de attribut som definierats i den [schemat](SchemaFormat.md), varje objekt har ett valfritt ”logprob”-attribut som anger den relativa logg sannolikheten bland objekt.
När tjänsten returnerar objekt i fallande sannolikhet, använder vi ”logprob” för att ange returnerar ordning matchande objekt.
Få en sannolikhet *p* mellan 0 och 1 sannolikheten för motsvarande loggen kan beräknas som loggen (*p*), där log() är funktionen den naturliga logaritmen.
När inget värde har angetts för logprob, används standardvärdet 0.

```json
{"logprob":-5.3, "Title":"latent dirichlet allocation", "Year":2003, "Author":{"Name":"david m blei", "Affiliation":"uc berkeley"}, "Author":{"Name":"andrew y ng", "Affiliation":"stanford"}, "Author":{"Name":"michael i jordan", "Affiliation":"uc berkeley"}}
{"logprob":-6.1, "Title":"probabilistic latent semantic indexing", "Year":1999, "Author":{"Name":"thomas hofmann", "Affiliation":"uc berkeley"}}
...
```

Värdet representeras som en JSON-sträng inom citattecken för String, GUID och Blob-attribut.  För numeriska attribut (Int32, Int64, Double) representeras värdet som ett JSON-nummer.  Värdet är ett JSON-objekt som anger de underordnade attributvärdena för sammansatta attribut.  För snabbare index versioner presort objekt genom att minska sannolikheten för loggen.

I allmänhet kan ett attribut ha 0 eller fler värden.  Om ett attribut inte har något värde, vi helt enkelt ta bort den från JSON.  Ett attribut har 2 eller fler värden, kan vi Upprepa attribut-värde-par i JSON-objekt.  Vi kan också tilldela en JSON-matris som innehåller flera värden till attributet.

```json
{"logprob":0, "Title":"0 keyword"}
{"logprob":0, "Title":"1 keyword", "Keyword":"foo"}
{"logprob":0, "Title":"2 keywords", "Keyword":"foo", "Keyword":"bar"}
{"logprob":0, "Title":"2 keywords (alt)", "Keyword":["foo", "bar"]}
```
