---
title: ta med fil
description: ta med fil
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: 3cc0e521e43f6855397a19fe34fce99da3e20494
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60408320"
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

