---
title: Konferensen serien entitetsattribut – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Läs mer om de attribut som du kan använda med entitet som Konferensserie.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: f9f28afd7005d7a61aa0d2f4dba69ca598034b52
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900756"
---
# <a name="conference-series-entity"></a>Konferensen serien entitet

<sub> * Följande attribut är specifika för konferensen serien entitet. (Ty = ”3”) </sub>

Namn    |Beskrivning                            |Typ       | Åtgärder
------- | ------------------------------------- | --------- | ----------------------------
Id      |Enhets-id                              |Int64      |Lika med
CN      |Konferensserie normalized namn      |Sträng     |Lika med
DCN     |Visningsnamn för konferens-serien         |Sträng     |inga
Kopia      |Konferensserie totala antalet källhänvisningar         |Int32      |inga  
ECC     |Antalet för konferensen serien totala uppskattade källhänvisningar   |Int32      |inga
F.FId   |Fältet för studier entitets-ID som är associerade med konferensserie |Int64  | Lika med
F.FN    |Fältet för studier namnet som associeras med konferensserie  | Är lika med,<br/>StartsWith