---
title: Entitetsattribut för konferensen instans – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Läs om de attribut som du kan använda med Konferensinstans entiteten i Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 183a307159adb5dfdb248eb0cf4862462a626db6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60498754"
---
# <a name="conference-instance-entity"></a>Konferensen instans entitet

<sub> * Följande attribut är specifika för konferensen instans entitet. (Ty = ”4”) </sub>

Namn    |Beskrivning                            |Typ       | Åtgärder
------- | ------------------------------------- | --------- | ----------------------------
Id      |Entitets-ID                              |Int64      |Lika med
CIN     |Konferensen normaliserade Instansnamn ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})        |String     |Lika med
DCN     |Visningsnamn för konferensen instansen ({ConferenceSeriesName}: {ConferenceInstanceYear})       |String     |Ingen
CIL     |Platsen för konferensinstans    |String     |Är lika med,<br/>StartsWith
CISD    |Startdatum för konferensinstans  |Date       |Är lika med,<br/>IsBetween
CIED    |Slutdatum för konferensinstans    |Date       |Är lika med,<br/>IsBetween
CIARD   |Abstrakt registrering förfallodatumet för konferensinstans  |Date       |Är lika med,<br/>IsBetween
CISDD   |Skicka förfallodatumet för konferensinstans     |Date       |Är lika med,<br/>IsBetween
CIFVD   |Slutversionen förfallodatumet för konferensinstans  |Date       |Är lika med,<br/>IsBetween
CINDD   |Meddelande datumet för konferensinstans   |Date       |Är lika med,<br/>IsBetween
CD.T    |Rubrik för en konferens instans-händelse   |Date       |Är lika med,<br/>IsBetween
CD.D    |Datum för en konferens instans-händelse    |Date       |Är lika med,<br/>IsBetween
PCS.CN  |Konferensen serienamn på-instansen |String     |Lika med
PCS.CId |Konferensen serien ID för instansen |Int64    |Lika med
CC      |Totalt antal citat för konferensen instansantalet           |Int32      |Ingen  
ECC     |Konferensen instansantalet totala uppskattade citat |Int32      |Ingen


## <a name="extended-metadata-attributes"></a>Utökade attribut Metadata ##

Namn    | Beskrivning               
--------|---------------------------    
FN      | Konferensinstans fullständigt namn
