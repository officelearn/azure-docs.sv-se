---
title: Azure Data Factory mappning dataflödet finns omvandling
description: Azure Data Factory mappning dataflödet finns omvandling
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 9d21b304f55ec746da4b7b42194fe0d168261b53
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56272106"
---
# <a name="azure-data-factory-mapping-data-flow-exists-transformation"></a>Azure Data Factory mappning dataflödet finns omvandling

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Exists-transformeringen är en rad som filtrering omvandling som hindrar eller tillåter rader i dina data kan flöda via. Finns transformera liknar ```SQL WHERE EXISTS``` och ```SQL WHERE NOT EXISTS```. När du har en Filter-omvandling de resulterande raderna från din dataström antingen innehåller alla rader där kolumnvärden från källa 1 finns i källan 2 eller finns inte i källan 2.

![Det finns inställningar](media/data-flow/exsits.png "finns 1")

Välj andra källan för dina Exists så att dataflöde kan jämföra värden från Stream 1 mot Stream 2.

Välj kolumnen från källa 1 och från källan 2 vars värden du vill kontrollera mot Exists eller finns inte.
