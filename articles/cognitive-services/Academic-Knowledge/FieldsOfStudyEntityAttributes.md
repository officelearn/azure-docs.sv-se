---
title: Fältet för studier entitetsattribut – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Läs om de attribut som du kan använda med fältet fallstudien om entiteten i Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: 793b35d9c6412c40a87f3f91fcd772476d57584f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55154575"
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
FP.FN   |Huvudfält för studier namn             |Sträng     |Lika med
FP.FId  |Överordnade fält av studie-ID               |Int64      |Lika med
FC.FN   |Underordnade fält av studie namn              |Sträng     |Lika med
FC.FId  |Underordnade fält av studie-ID                |Int64      |Lika med
