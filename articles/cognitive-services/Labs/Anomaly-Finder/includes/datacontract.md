---
title: ta med fil
description: ta med fil
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: e37d3ef5b6f65ad31bc19f9f8c15350014d1c9ad
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353355"
---
Med den [Avvikelseidentifiering Finder API](https://labs.cognitive.microsoft.com/en-us/project-anomaly-finder), kan du överföra tid series-data i JSON-format till API-slutpunkt och läses sedan resultatet från det API-svaret. Du kan ladda upp tid series-data, innehåller varje datapunkt:  
* Tidsstämpel - tidstämpel för datapunkten. Kontrollera att den använder en UTC-datum tid-sträng, till exempel ”2017-08-01T00:00:00Z”
* Värde: mätning av den datapunkten

Resultaten består av:
* Period - periodiciteten API: et används för att identifiera avvikelser
* WarningText - möjliga varningsinformation
* ExpectedValue - förutsägelsevärdet med inlärning baserat modellen
* IsAnomaly - resultatet om datapunkter är avvikelser eller inte i båda riktningarna (toppar eller korta)
* IsAnomaly_Neg - resultatet om datapunkter är avvikelser i negativ riktning (korta)
* IsAnomaly_Pos - resultatet om datapunkter är avvikelser i positiv riktning (toppar)
* UpperMargin - summan av ExpectedValue och UpperMargin anger den övre gränsen för datapunkt kan fortfarande anses som vanligt
* LowerMargin - (ExpectedValue - LowerMargin) avgör undre gränsvärde att datapunkt fortfarande tänkte som vanligt

Information om datakontrakt hittar [här](../apiref.md).

