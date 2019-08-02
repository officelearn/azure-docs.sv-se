---
title: Attribut för entiteten anknytning i Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Lär dig de attribut som du kan använda med entiteten anknytning i Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 333875472d9b859196c4d828061301b9d32c4d5a
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705103"
---
# <a name="affiliation-entity"></a>Anknytnings enhet

<sub>* Följande attribut är speciella för en anknytnings enhet. (Ty = ' 5 ')</sub>

Namn    |Beskrivning                            |type       | Åtgärder
------- | ------------------------------------- | --------- | ----------------------------
ID      |Enhets-id                              |Int64      |Lika med
AfN     |Namn på anknytning, normaliserad        |Sträng     |Lika med
DAfN    |Visnings namn för anknytning       |Sträng     |inga
Kopia      |Totalt antal hänvisningar i anknytning           |Int32      |inga  
ECC     |Sammanlagt antal beräknade antal hänvisningar i anknytning |Int32      |inga

## <a name="extended-metadata-attributes"></a>Utökade metadata-attribut ##

Namn    | Beskrivning               
--------|---------------------------    
PC      |Anknytningens antal papper
