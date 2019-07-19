---
title: Azure Data Factory mappa data flöde Välj omvandling
description: Azure Data Factory mappa data flöde Välj omvandling
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 15c74637a2dc42ec44f582878b5505d94637cd7b
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314194"
---
# <a name="azure-data-factory-mapping-data-flow-select-transformation"></a>Azure Data Factory mappa data flöde Välj omvandling
[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Använd den här omvandlingen för kolumn urval (minska antalet kolumner), Ali Aset och Stream Names och ändra ordning på kolumner.

## <a name="how-to-use-select-transformation"></a>Använda Välj omvandling
Med alternativet Välj transformering kan du ange alias för en hel ström eller kolumner i den data strömmen, tilldela olika namn (alias) och sedan referera till de nya namnen senare i ditt data flöde. Den här transformeringen är användbar för självkopplings scenarier. Sättet att implementera en själv koppling i ett ADF-dataflöde är att ta en ström, förgrena den med "ny gren", sedan omedelbart efteråt, lägga till en "Välj"-transformering. Den data strömmen kommer nu att ha ett nytt namn som du kan använda för att koppla tillbaka till den ursprungliga strömmen och skapa en själv koppling:

![Själv koppling](media/data-flow/selfjoin.png "Själv koppling")

I diagrammet ovan är Select Transform överst. Detta är alias för den ursprungliga strömmen till "OrigSourceBatting". I den markerade kopplings transformeringen nedan kan du se att vi använder den här Välj Ali strömmen som höger koppling, så att vi kan referera till samma nyckel i både vänster & högra sida av den inre kopplingen.

Välj kan också användas på ett sätt som avmarkerar kolumner från ditt data flöde. Om du t. ex. har 6 kolumner som definierats i din mottagare, men du bara vill välja en viss 3 att transformera och sedan flöda till mottagaren, kan du bara välja de tre genom att använda alternativet Välj transformering.

> [!NOTE]
> Du måste stänga av "Markera alla" om du bara vill välja vissa kolumner

![Välj omvandling](media/data-flow/select001.png "Välj alias")

## <a name="options"></a>Alternativ
* Standardvärdet för "Välj" är att inkludera alla inkommande kolumner och behålla de ursprungliga namnen. Du kan ange ett alias för strömmen genom att ange namnet på Välj transformering.
* Om du vill ange alias för enskilda kolumner avmarkerar du "Markera alla" och använder kolumn mappningen längst ned.
* Välj hoppa över dubbletter för att eliminera dubblerade kolumner från indata-eller utdata-metadata.

![Hoppa över dubbletter](media/data-flow/select-skip-dup.png "Hoppa över dubbletter")

## <a name="next-steps"></a>Nästa steg
* När du har använt Välj för att byta namn på, ändra ordning och alias använder du [omvandling av mottagare](data-flow-sink.md) för att använda data i ett data lager.
