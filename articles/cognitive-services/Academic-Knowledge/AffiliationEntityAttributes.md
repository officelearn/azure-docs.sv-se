---
title: Anknytning entitetsattribut i Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Läs om de attribut som du kan använda med anknytning till entiteten i Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 82e6a5b66342e58e62da029d617cbd1d74c28149
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61340527"
---
# <a name="affiliation-entity"></a>Anknytning till entitet

<sub> * Följande attribut är specifika för anknytning till entiteten. (Ty = '5') </sub>

Namn    |Beskrivning                            |Typ       | Åtgärder
------- | ------------------------------------- | --------- | ----------------------------
Id      |Enhets-id                              |Int64      |Lika med
AfN     |Anknytning normaliserade namn        |String     |Lika med
DAfN    |Visningsnamn för anknytning       |String     |inga
Kopia      |Antalet för anknytning totala källhänvisningar           |Int32      |inga  
ECC     |Antalet för anknytning totala uppskattade källhänvisningar |Int32      |inga

## <a name="extended-metadata-attributes"></a>Utökade attribut Metadata ##

Namn    | Beskrivning               
--------|---------------------------    
PC      |Anknytnings papper antal
