---
title: Entitetsattribut för konferensen instans – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Läs om de attribut som du kan använda med Konferensinstans entiteten i Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: db025f377a3fab2f788252db0c8e3555837a6de8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55196114"
---
# <a name="conference-instance-entity"></a>Konferensen instans entitet

<sub> * Följande attribut är specifika för konferensen instans entitet. (Ty = ”4”) </sub>

Name    |Beskrivning                            |Type       | Åtgärder
------- | ------------------------------------- | --------- | ----------------------------
Id      |Enhets-id                              |Int64      |Lika med
CIN     |Konferensen normaliserade Instansnamn ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})        |Sträng     |Lika med
DCN     |Visningsnamn för konferensen instansen ({ConferenceSeriesName}: {ConferenceInstanceYear})       |Sträng     |inga
CIL     |Platsen för konferensinstans    |Sträng     |Är lika med,<br/>StartsWith
CISD    |Startdatum för konferensinstans  |Date       |Är lika med,<br/>IsBetween
CIED    |Slutdatum för konferensinstans    |Date       |Är lika med,<br/>IsBetween
CIARD   |Abstrakt registrering förfallodatumet för konferensinstans  |Date       |Är lika med,<br/>IsBetween
CISDD   |Skicka förfallodatumet för konferensinstans     |Date       |Är lika med,<br/>IsBetween
CIFVD   |Slutversionen förfallodatumet för konferensinstans  |Date       |Är lika med,<br/>IsBetween
CINDD   |Meddelande datumet för konferensinstans   |Date       |Är lika med,<br/>IsBetween
CD.T    |Rubrik för en konferens instans-händelse   |Date       |Är lika med,<br/>IsBetween
CD.D    |Datum för en konferens instans-händelse    |Date       |Är lika med,<br/>IsBetween
PCS.CN  |Konferensen serienamn på-instansen |Sträng     |Lika med
PCS.CId |Konferensen serien ID för instansen |Int64    |Lika med
Kopia      |Totalt antal citat för konferensen instansantalet           |Int32      |inga  
ECC     |Konferensen instansantalet totala uppskattade citat |Int32      |inga


## <a name="extended-metadata-attributes"></a>Utökade attribut Metadata ##

Name    | Beskrivning               
--------|---------------------------    
FN      | Konferensinstans fullständigt namn
