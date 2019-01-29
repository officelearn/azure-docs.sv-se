---
title: Konferensen serien entitetsattribut – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Läs mer om de attribut som du kan använda med entitet som Konferensserie.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: ff71b489cce01d8d6ea29e09905d7d3ac8429e34
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155616"
---
# <a name="conference-series-entity"></a>Konferensen serien entitet

<sub> * Följande attribut är specifika för konferensen serien entitet. (Ty = ”3”) </sub>

Name    |Beskrivning                            |Type       | Åtgärder
------- | ------------------------------------- | --------- | ----------------------------
Id      |Enhets-id                              |Int64      |Lika med
CN      |Konferensserie normalized namn      |Sträng     |Lika med
DCN     |Visningsnamn för konferens-serien         |Sträng     |inga
Kopia      |Konferensserie totala antalet källhänvisningar         |Int32      |inga  
ECC     |Antalet för konferensen serien totala uppskattade källhänvisningar   |Int32      |inga
F.FId   |Fältet för studier entitets-ID som är associerade med konferensserie |Int64  | Lika med
F.FN    |Fältet för studier namnet som associeras med konferensserie  | Är lika med,<br/>StartsWith
