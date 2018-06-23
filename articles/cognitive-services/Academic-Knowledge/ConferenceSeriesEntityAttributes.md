---
title: Konferens serien entitetsattribut i Academic Knowledge API | Microsoft Docs
description: Lär dig mer om de attribut som du kan använda med entiteten konferens serien i kognitiva Services.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 332736c927bdaa00334546f626a6eabb8e11d3b5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351363"
---
# <a name="conference-series-entity"></a>Konferens serien entitet

<sub> * Följande attribut är specifika för konferens serien entitet. (Ty = ”3”) </sub>

Namn    |Beskrivning                            |Typ       | Åtgärder
------- | ------------------------------------- | --------- | ----------------------------
Id      |Enhets-id                              |Int64      |Lika med
CN      |Konferens normaliserade serienamn      |Sträng     |Lika med
DCN     |Visningsnamn för konferens serien         |Sträng     |inga
Kopia      |Konferens serien totalt antalet källhänvisningar         |Int32      |inga  
ECC     |Antalet för konferens serien totala uppskattade källhänvisningar   |Int32      |inga
F.FId   |Fältet för undersökning enhets-ID som är associerade med konferens-serien |Int64  | Lika med
F.FN    |Fältet för undersökning namnet som associeras med konferens-serien  | Är lika med,<br/>StartsWith