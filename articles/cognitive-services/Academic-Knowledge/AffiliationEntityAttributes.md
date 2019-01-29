---
title: Anknytning entitetsattribut i Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Läs om de attribut som du kan använda med anknytning till entiteten i Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 344b26b16f74cd44982e3c93fa69295792daa9a0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55190655"
---
# <a name="affiliation-entity"></a>Anknytning till entitet

<sub> * Följande attribut är specifika för anknytning till entiteten. (Ty = '5') </sub>

Name    |Beskrivning                            |Type       | Åtgärder
------- | ------------------------------------- | --------- | ----------------------------
Id      |Enhets-id                              |Int64      |Lika med
AfN     |Anknytning normaliserade namn        |Sträng     |Lika med
DAfN    |Visningsnamn för anknytning       |Sträng     |inga
Kopia      |Antalet för anknytning totala källhänvisningar           |Int32      |inga  
ECC     |Antalet för anknytning totala uppskattade källhänvisningar |Int32      |inga

## <a name="extended-metadata-attributes"></a>Utökade attribut Metadata ##

Name    | Beskrivning               
--------|---------------------------    
PC      |Anknytnings papper antal
