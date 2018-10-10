---
title: Fältet för studier entitetsattribut – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Läs om de attribut som du kan använda med fältet fallstudien om entiteten i Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: 862fd6d506d5f1ca6f7f532f80f53a29200f33db
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900435"
---
# <a name="field-of-study-entity"></a>Fältet för studier entitet

<sub> * Följande attribut är specifika för fältet för studier entitet. (Ty = '6') </sub>

Namn    |Beskrivning                            |Typ       | Åtgärder
------- | ------------------------------------- | --------- | ----------------------------
Id      |Enhets-id                              |Int64      |Lika med
FN      |Fältet för studier normaliserade namn         |Sträng     |Lika med
DFN     |Fältet för studier visningsnamn            |Sträng     |inga
Kopia      |Fältet för antalet för studien total källhänvisningar    |Int32      |inga  
ECC     |Fältet för antalet totala uppskattade källhänvisningar|Int32      |inga
FL      |Nivå i fälten för studier hierarki     |Int32      |Är lika med, <br/>IsBetween
FP. FN   |Huvudfält för studier namn             |Sträng     |Lika med
FP. FId  |Överordnade fält av studie-ID               |Int64      |Lika med
FC. FN   |Underordnade fält av studie namn              |Sträng     |Lika med
FC. FId  |Underordnade fält av studie-ID                |Int64      |Lika med