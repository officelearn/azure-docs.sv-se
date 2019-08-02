---
title: Attribut för entiteten konferens instans – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Lär dig de attribut som du kan använda med entiteten konferens instans i Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: 37a353fbb86ca199b2316dcfba5904f4b46b0276
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705065"
---
# <a name="conference-instance-entity"></a>Konferens instans entitet

<sub>* Följande attribut är speciella för en konferens instans-entitet. (Ty = ' 4 ')</sub>

Namn    |Beskrivning                            |type       | Åtgärder
------- | ------------------------------------- | --------- | ----------------------------
ID      |Enhets-id                              |Int64      |Lika med
CIN     |Konferens instans normaliserat namn ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})        |Sträng     |Lika med
DCN     |Visnings namn för konferens instans ({ConferenceSeriesName}: {ConferenceInstanceYear})       |Sträng     |inga
CIL     |Plats för konferens instansen    |Sträng     |Är lika med<br/>StartsWith
CISD    |Start datum för konferens instansen  |Date       |Är lika med<br/>IsBetween
CIED    |Sista datum för konferens instansen    |Date       |Är lika med<br/>IsBetween
CIARD   |Förfallo datum för abstrakt registrering för konferens instansen  |Date       |Är lika med<br/>IsBetween
CISDD   |Förfallo datum för konferens instans     |Date       |Är lika med<br/>IsBetween
CIFVD   |Slut versionens förfallo datum för konferens instansen  |Date       |Är lika med<br/>IsBetween
CINDD   |Meddelande datum för konferens instansen   |Date       |Är lika med<br/>IsBetween
INSTALLATIONS. TERA    |Rubrik för en konferens instans händelse   |Date       |Är lika med<br/>IsBetween
INSTALLATIONS. STYR    |Datum för händelse av konferens instans    |Date       |Är lika med<br/>IsBetween
DATORER. NOMENKLATUR  |Konferens seriens namn på instansen |Sträng     |Lika med
Datorer. CId |ID för instansen för konferens serien |Int64    |Lika med
Kopia      |Totalt antal käll hänvisningar i en konferens instans           |Int32      |inga  
ECC     |Konferens instans totalt antal beräknade antal hänvisningar |Int32      |inga


## <a name="extended-metadata-attributes"></a>Utökade metadata-attribut ##

Namn    | Beskrivning               
--------|---------------------------    
FN      | Fullständigt namn på konferens instans
