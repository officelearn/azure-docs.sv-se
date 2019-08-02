---
title: Entitetsattribut för konferens serie – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Lär dig mer om de attribut som du kan använda med entiteten konferens serie.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: feed324202f6a75ceb7e9089875b899c51cd8ae6
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705050"
---
# <a name="conference-series-entity"></a>Enhet för konferens serie

<sub>* Följande attribut är speciella för en entitet i konferens serien. (Ty = ' 3 ')</sub>

Namn    |Beskrivning                            |type       | Åtgärder
------- | ------------------------------------- | --------- | ----------------------------
ID      |Enhets-id                              |Int64      |Lika med
CN      |Namn på konferens seriens normaliserade      |Sträng     |Lika med
DCN     |Visnings namn för konferens serien         |Sträng     |inga
Kopia      |Totalt antal käll hänvisningar i konferens serien         |Int32      |inga  
ECC     |Totalt antal uppskattade antal hänvisningar i konferens serien   |Int32      |inga
F. FId   |Fält för entitet-ID för undersökning som är associerat med konferens serien |Int64  | Lika med
F.FN    |Fält för studie namn som är associerat med konferens serien  | Är lika med<br/>StartsWith
