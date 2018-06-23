---
title: Fältet för undersökning entitetsattribut i Academic Knowledge API | Microsoft Docs
description: 'Läs om de attribut som du kan använda med fältet undersökning entiteten i Academic Knowledge API: et i kognitiva Services.'
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: a8176370f5b2f63b7741f7e892ed5a8c775f19c1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351360"
---
# <a name="field-of-study-entity"></a>Fältet för undersökning entitet

<sub> * Följande attribut är specifika för fältet av undersökning entitet. (Ty = '6') </sub>

Namn    |Beskrivning                            |Typ       | Åtgärder
------- | ------------------------------------- | --------- | ----------------------------
Id      |Enhets-id                              |Int64      |Lika med
FN      |Fältet för undersökning normaliserat namn         |Sträng     |Lika med
DFN     |Fältet för undersökning visningsnamn            |Sträng     |inga
Kopia      |Fältet för undersökning totala citat antal    |Int32      |inga  
ECC     |Fältet för antalet totala uppskattade källhänvisningar|Int32      |inga
FL      |Nivå i fälten för undersökning hierarki     |Int32      |Är lika med, <br/>IsBetween
RP. FN   |Överordnade fältet undersökning namn             |Sträng     |Lika med
RP. FId  |Överordnat fält för undersökning ID               |Int64      |Lika med
FC. FN   |Underordnade fältet för undersökning namn              |Sträng     |Lika med
FC. FId  |Underordnade fältet för undersökning-ID                |Int64      |Lika med