---
title: Fält för entitetens attribut för undersökning – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Lär dig de attribut som du kan använda med fältet för studie entiteten i Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: d54f3c72462e6702b09068092b7c18ea50f12048
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704984"
---
# <a name="field-of-study-entity"></a>Fält för studie enhet

<sub>* Följande attribut är speciella för en entitets-understudie. (Ty = ' 6 ')</sub>

Namn    |Beskrivning                            |type       | Åtgärder
------- | ------------------------------------- | --------- | ----------------------------
ID      |Enhets-id                              |Int64      |Lika med
FN      |Fält för förnormaliserat studie namn         |Sträng     |Lika med
DFN     |Fält för visnings namn för studie            |Sträng     |inga
Kopia      |Fält för antal undersökningar totalt antal citat    |Int32      |inga  
ECC     |Fält med totalt antal beräknade antal hänvisningar|Int32      |inga
FLORIDA      |Nivå i fält för studie hierarki     |Int32      |Är lika med <br/>IsBetween
FP. FN   |Överordnat fält för studie namn             |Sträng     |Lika med
FP. FId  |Överordnat fält för studie-ID               |Int64      |Lika med
BETRAKTAS. FN   |Underordnat namn på studiens namn              |Sträng     |Lika med
Betraktas. FId  |Underordnat studie-ID                |Int64      |Lika med
