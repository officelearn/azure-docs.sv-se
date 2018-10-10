---
title: ta med fil
description: ta med fil
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 9280790f6692096a0b3909c9d1dfab2e94a8c0d7
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2018
ms.locfileid: "48904660"
---
Med den [Avvikelseidentifiering Finder API](https://labs.cognitive.microsoft.com/en-us/project-anomaly-finder), du kan ladda upp time series-data i JSON-format till API-slutpunkt och läs sedan resultatet från API-svaret. Du kan ladda upp time series-data, innehåller varje datapunkt:  
* Tidsstämpel - tidsstämpel för datapunkten. Kontrollera att den använder en UTC DateTime-sträng, till exempel ”2017-08-01T00:00:00Z”
* Värdet - mätning av den datapunkten

Resultaten består av:
* Period - periodicitet som API: et använder för att identifiera avvikelser
* WarningText - möjliga varningsinformation
* ExpectedValue - förutsägelsevärdet genom utbildningsresurser-baserad modell
* IsAnomaly - resultatet på om datapunkter är avvikelser eller inte i båda riktningarna (toppar och dalar)
* IsAnomaly_Neg - resultatet på om datapunkter är avvikelser i negativ riktning (dips)
* IsAnomaly_Pos - resultatet på om datapunkter är avvikelser i positiva riktning (toppar)
* UpperMargin - summan av ExpectedValue och UpperMargin anger den övre gränsen som datapunkt är fortfarande betraktas som vanligt
* LowerMargin - (ExpectedValue - LowerMargin) avgör undre gräns att datapunkt fortfarande betraktas som vanligt

Information om datakontrakt kan hittas [här](../apiref.md).

