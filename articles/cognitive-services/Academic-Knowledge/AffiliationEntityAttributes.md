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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61340527"
---
# <a name="affiliation-entity"></a>Anknytning till entitet

<sub> * Följande attribut är specifika för anknytning till entiteten. (Ty = '5') </sub>

Namn    |Beskrivning                            |Typ       | Åtgärder
------- | ------------------------------------- | --------- | ----------------------------
Id      |Entitets-ID                              |Int64      |Lika med
AfN     |Anknytning normaliserade namn        |String     |Lika med
DAfN    |Visningsnamn för anknytning       |String     |Ingen
CC      |Antalet för anknytning totala källhänvisningar           |Int32      |Ingen  
ECC     |Antalet för anknytning totala uppskattade källhänvisningar |Int32      |Ingen

## <a name="extended-metadata-attributes"></a>Utökade attribut Metadata ##

Namn    | Beskrivning               
--------|---------------------------    
PC      |Anknytnings papper antal
