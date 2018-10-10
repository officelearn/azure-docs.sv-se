---
title: Entitetsattribut för konferensen instans – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Läs om de attribut som du kan använda med Konferensinstans entiteten i Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 6111ad00044943f12b2e098c4fd07ffb40185799
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902420"
---
# <a name="conference-instance-entity"></a>Konferensen instans entitet

<sub> * Följande attribut är specifika för konferensen instans entitet. (Ty = ”4”) </sub>

Namn    |Beskrivning                            |Typ       | Åtgärder
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
CD. T    |Rubrik för en konferens instans-händelse   |Date       |Är lika med,<br/>IsBetween
CD. D    |Datum för en konferens instans-händelse    |Date       |Är lika med,<br/>IsBetween
DATORER. CN  |Konferensen serienamn på-instansen |Sträng     |Lika med
DATORER. CId |Konferensen serien ID för instansen |Int64    |Lika med
Kopia      |Totalt antal citat för konferensen instansantalet           |Int32      |inga  
ECC     |Konferensen instansantalet totala uppskattade citat |Int32      |inga


## <a name="extended-metadata-attributes"></a>Utökade attribut Metadata ##

Namn    | Beskrivning               
--------|---------------------------    
FN      | Konferensinstans fullständigt namn