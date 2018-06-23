---
title: Anknytningen entitetsattribut i Academic Knowledge API | Microsoft Docs
description: 'Läs om de attribut som du kan använda med anknytning entiteten i Academic Knowledge API: et i kognitiva Services.'
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: a0ec67cb811ca207b3d038028491da2516028f0b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351351"
---
# <a name="affiliation-entity"></a>Anknytningen entitet

<sub> * Följande attribut är specifika för anknytningen entitet. (Ty = '5') </sub>

Namn    |Beskrivning                            |Typ       | Åtgärder
------- | ------------------------------------- | --------- | ----------------------------
Id      |Enhets-id                              |Int64      |Lika med
AfN     |Anknytningen normaliserat namn        |Sträng     |Lika med
DAfN    |Anknytningen visningsnamn       |Sträng     |inga
Kopia      |Anknytningen totala citat antal           |Int32      |inga  
ECC     |Anknytningen totala uppskattade citat antal |Int32      |inga

## <a name="extended-metadata-attributes"></a>Utökade Metadataattribut ##

Namn    | Beskrivning               
--------|---------------------------    
PC      |Anknytningens papper antal