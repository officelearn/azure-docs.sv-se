---
title: Konferens instans entitetsattribut i Academic Knowledge API | Microsoft Docs
description: Läs om de attribut som du kan använda med entiteten konferens instans i Academic Knowledge API i kognitiva Services.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: ef2bca4346a4666905f3dfb7bd448720f3b0ef8b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351396"
---
# <a name="conference-instance-entity"></a>Konferens instans entitet

<sub> * Följande attribut är specifika för konferens instans entitet. (Ty = ”4”) </sub>

Namn    |Beskrivning                            |Typ       | Åtgärder
------- | ------------------------------------- | --------- | ----------------------------
Id      |Enhets-id                              |Int64      |Lika med
CIN     |Konferens normaliserade Instansnamn ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})        |Sträng     |Lika med
DCN     |Visningsnamn för konferens-instans ({ConferenceSeriesName}: {ConferenceInstanceYear})       |Sträng     |inga
CIL     |Platsen för konferens-instans    |Sträng     |Är lika med,<br/>StartsWith
CISD    |Startdatum i konferens-instans  |Date       |Är lika med,<br/>IsBetween
CIED    |Slutdatum för konferens-instans    |Date       |Är lika med,<br/>IsBetween
CIARD   |Abstrakt registrering förfallodatumet för konferens-instans  |Date       |Är lika med,<br/>IsBetween
CISDD   |Skicka förfallodatumet för konferens-instans     |Date       |Är lika med,<br/>IsBetween
CIFVD   |Slutversionen förfallodatumet för konferens-instans  |Date       |Är lika med,<br/>IsBetween
CINDD   |Datum för konferens-instansen   |Date       |Är lika med,<br/>IsBetween
CD: N. T    |Rubrik på en händelse för konferens-instans   |Date       |Är lika med,<br/>IsBetween
CD: N. D    |Datum för en instans konferens-händelse    |Date       |Är lika med,<br/>IsBetween
DATORER. CN  |Konferens serienamn av instansen |Sträng     |Lika med
DATORER. CId |ID för konferens series av instansen |Int64    |Lika med
Kopia      |Konferens totala citat instanser           |Int32      |inga  
ECC     |Konferens totala uppskattade citat instanser |Int32      |inga


## <a name="extended-metadata-attributes"></a>Utökade Metadataattribut ##

Namn    | Beskrivning               
--------|---------------------------    
FN      | Konferens instans fullständigt namn