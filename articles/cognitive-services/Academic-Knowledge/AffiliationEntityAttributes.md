---
title: Anknytning entitetsattribut i Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Läs om de attribut som du kan använda med anknytning till entiteten i Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 177fe9da8bbe821a69eae02d89a225e5d4009331
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900486"
---
# <a name="affiliation-entity"></a>Anknytning till entitet

<sub> * Följande attribut är specifika för anknytning till entiteten. (Ty = '5') </sub>

Namn    |Beskrivning                            |Typ       | Åtgärder
------- | ------------------------------------- | --------- | ----------------------------
Id      |Enhets-id                              |Int64      |Lika med
AfN     |Anknytning normaliserade namn        |Sträng     |Lika med
DAfN    |Visningsnamn för anknytning       |Sträng     |inga
Kopia      |Antalet för anknytning totala källhänvisningar           |Int32      |inga  
ECC     |Antalet för anknytning totala uppskattade källhänvisningar |Int32      |inga

## <a name="extended-metadata-attributes"></a>Utökade attribut Metadata ##

Namn    | Beskrivning               
--------|---------------------------    
PC      |Anknytnings papper antal