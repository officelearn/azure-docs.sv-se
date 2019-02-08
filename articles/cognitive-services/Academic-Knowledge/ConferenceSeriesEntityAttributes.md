---
title: Konferensen serien entitetsattribut – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Läs mer om de attribut som du kan använda med entitet som Konferensserie.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 38b4aa4c899668a68041f042ce6981ddd8c58219
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884927"
---
# <a name="conference-series-entity"></a>Konferensen serien entitet

<sub> * Följande attribut är specifika för konferensen serien entitet. (Ty = ”3”) </sub>

Namn    |Beskrivning                            |Type       | Åtgärder
------- | ------------------------------------- | --------- | ----------------------------
Id      |Enhets-id                              |Int64      |Lika med
CN      |Konferensserie normalized namn      |String     |Lika med
DCN     |Visningsnamn för konferens-serien         |String     |inga
Kopia      |Konferensserie totala antalet källhänvisningar         |Int32      |inga  
ECC     |Antalet för konferensen serien totala uppskattade källhänvisningar   |Int32      |inga
F.FId   |Fältet för studier entitets-ID som är associerade med konferensserie |Int64  | Lika med
F.FN    |Fältet för studier namnet som associeras med konferensserie  | Är lika med,<br/>StartsWith
