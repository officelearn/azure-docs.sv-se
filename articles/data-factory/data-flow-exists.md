---
title: Transformering av Azure Data Factory mappnings data flödet finns
description: Så här söker du efter befintliga rader med hjälp av Data Factory-mappning av data flöden med exists-transformering
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 6048a6d30d37b9d2b46c3105c5f8eac0a9ca41c0
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387841"
---
# <a name="mapping-data-flow-exists-transformation"></a>Transformering av mappnings data flödet finns



Exists-omvandlingen är en omvandling för rad filtrering som stoppar eller tillåter att rader i dina data flödar genom. Transformeringen exists liknar ```SQL WHERE EXISTS``` och ```SQL WHERE NOT EXISTS```. När omvandlingen finns kommer de resulterande raderna från din data ström att inkludera alla rader där kolumn värden från källa 1 finns i källa 2 eller inte finns i källa 2.

![Finns inställningar](media/data-flow/exists.png "finns 1")

Välj den andra källan så att data flödet kan jämföra värden från data strömmen 1 mot Stream 2.

Välj kolumnen från källa 1 och från källa 2 vars värden du vill kontrol lera mot finns eller som inte finns.

## <a name="multiple-exists-conditions"></a>Det finns flera villkor

Bredvid varje rad i dina kolumn villkor finns ett +-tecken tillgängligt när du hovrar över Reach-raden. På så sätt kan du lägga till flera rader för exists-villkor. Varje ytterligare villkor är "och".

## <a name="custom-expression"></a>Anpassat uttryck

![Finns anpassade inställningar](media/data-flow/exists1.png "finns anpassad")

Du kan klicka på "anpassat uttryck" för att i stället skapa ett kostnads fritt uttryck som villkoret finns eller inte finns. Genom att markera den här rutan kan du ange ett eget uttryck som ett villkor.

## <a name="next-steps"></a>Nästa steg

Liknande omvandlingar är [Lookup](data-flow-lookup.md) och [Join](data-flow-join.md).
