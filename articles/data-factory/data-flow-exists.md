---
title: Azure Data Factory mappning dataflödet finns omvandling
description: Azure Data Factory mappning dataflödet finns omvandling
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 6ce27ba699ae766ed4d2428f67d91379464bb9f1
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006135"
---
# <a name="azure-data-factory-mapping-data-flow-exists-transformation"></a>Azure Data Factory mappning dataflödet finns omvandling

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Exists-transformeringen är en rad som filtrering omvandling som hindrar eller tillåter rader i dina data kan flöda via. Finns transformera liknar ```SQL WHERE EXISTS``` och ```SQL WHERE NOT EXISTS```. När du har en Filter-omvandling de resulterande raderna från din dataström antingen innehåller alla rader där kolumnvärden från källa 1 finns i källan 2 eller finns inte i källan 2.

![Det finns inställningar](media/data-flow/exsits.png "finns 1")

Välj andra källan för dina Exists så att dataflöde kan jämföra värden från Stream 1 mot Stream 2.

Välj kolumnen från källa 1 och från källan 2 vars värden du vill kontrollera mot Exists eller finns inte.

## <a name="multiple-exists-conditions"></a>Flera finns villkor

Bredvid varje rad i din kolumn villkor för Exsits hittar du en + inloggning som är tillgängliga när du hovrar över nå raden. Detta kan du lägga till flera rader för Exists-villkor.

## <a name="next-steps"></a>Nästa steg

