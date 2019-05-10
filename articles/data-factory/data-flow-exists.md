---
title: Azure Data Factory mappning av Flow finns Data-transformering
description: Hur du kontrollerar för befintliga rader med data factory mappade data flödar genom att omvandla Exists
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: b98b7afb21f2f50d44ba93ed793b6efb20f75164
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2019
ms.locfileid: "65235983"
---
# <a name="mapping-data-flow-exists-transformation"></a>Mappning av dataflödet finns omvandling

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Exists-transformeringen är en rad som filtrering omvandling som hindrar eller tillåter rader i dina data kan flöda via. Finns transformera liknar ```SQL WHERE EXISTS``` och ```SQL WHERE NOT EXISTS```. Efter omvandlingen finns de resulterande raderna från din dataström antingen innehåller alla rader där kolumnvärden från källa 1 finns i källan 2 eller finns inte i källan 2.

![Det finns inställningar](media/data-flow/exists.png "finns 1")

Välj andra källan för dina Exists så att dataflöde kan jämföra värden från Stream 1 mot Stream 2.

Välj kolumnen från källa 1 och från källan 2 vars värden du vill kontrollera mot Exists eller finns inte.

## <a name="multiple-exists-conditions"></a>Flera finns villkor

Bredvid varje rad i din kolumn villkor för Exists hittar du en + inloggning som är tillgängliga när du hovrar över nå raden. Detta kan du lägga till flera rader för Exists-villkor. Varje ytterligare villkor är en ”och”.

## <a name="custom-expression"></a>Anpassat uttryck

![Det finns anpassade inställningar](media/data-flow/exists1.png "finns anpassade")

Du kan klicka på ”anpassade uttryck” om du vill skapa i stället ett booleskt uttryck som din finns eller inte finns villkor. Markera den här rutan kan du ange ett eget uttryck som ett villkor.

## <a name="next-steps"></a>Nästa steg

Liknande transformationer är [Lookup](data-flow-lookup.md) och [ansluta](data-flow-join.md).
