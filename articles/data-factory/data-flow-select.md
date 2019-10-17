---
title: Azure Data Factory mappa data flöde Välj omvandling
description: Azure Data Factory mappa data flöde Välj omvandling
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 6ef9712dd2fd6b8d53fd4ad2c3e07e1d6c8f1aec
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387204"
---
# <a name="mapping-data-flow-select-transformation"></a>Mappa data flöde Välj omvandling


Använd den här omvandlingen för kolumn urval (minska antalet kolumner), Ali Aset och Stream Names och ändra ordning på kolumner.

## <a name="how-to-use-select-transformation"></a>Använda Välj omvandling
Med alternativet Välj transformering kan du ange alias för en hel ström eller kolumner i den data strömmen, tilldela olika namn (alias) och sedan referera till de nya namnen senare i ditt data flöde. Den här transformeringen är användbar för självkopplings scenarier. Sättet att implementera en själv koppling i ett ADF-dataflöde är att ta en ström, förgrena den med "ny gren", sedan omedelbart efteråt, lägga till en "Välj"-transformering. Den data strömmen kommer nu att ha ett nytt namn som du kan använda för att koppla tillbaka till den ursprungliga strömmen och skapa en själv koppling:

![Själv koppling](media/data-flow/selfjoin.png "Själv koppling")

I diagrammet ovan är Select Transform överst. Detta är alias för den ursprungliga strömmen till "OrigSourceBatting". I den markerade kopplings transformeringen nedan kan du se att vi använder den här Välj Ali strömmen som höger koppling, så att vi kan referera till samma nyckel i både vänster & högra sida av den inre kopplingen.

Välj kan också användas på ett sätt som avmarkerar kolumner från ditt data flöde. Om du t. ex. har 6 kolumner som definierats i din mottagare, men du bara vill välja en viss 3 att transformera och sedan flöda till mottagaren, kan du bara välja de tre genom att använda alternativet Välj transformering.

![Välj omvandling](media/data-flow/newselect1.png "Välj alias")

## <a name="options"></a>Alternativ
* Standardvärdet för "Välj" är att inkludera alla inkommande kolumner och behålla de ursprungliga namnen. Du kan ange ett alias för strömmen genom att ange namnet på Välj transformering.
* Om du vill ange alias för enskilda kolumner avmarkerar du "Markera alla" och använder kolumn mappningen längst ned.
* Välj hoppa över dubbletter för att eliminera dubblerade kolumner från indata-eller utdata-metadata.

![Hoppa över dubbletter](media/data-flow/select-skip-dup.png "Hoppa över dubbletter")

* När du väljer att hoppa över dubbletter visas resultaten på fliken Granska. ADF behåller den första förekomsten av kolumnen och du ser att varje efterföljande förekomst av samma kolumn har tagits bort från ditt flöde.

> [!NOTE]
> Om du vill rensa mappnings reglerna trycker du på knappen **Återställ** .

## <a name="mapping"></a>Mellan
Som standard mappar Select-omvandlingen alla kolumner automatiskt, vilket kommer att gå igenom alla inkommande kolumner till samma namn i utdata. Namnet på den utgående strömmen som anges i Välj inställningar definierar ett nytt aliasnamn för data strömmen. Om du behåller Välj uppsättningen för automatisk mappning, kan du ge alias för hela data strömmen med alla kolumner samma.

![Välj omvandlings regler](media/data-flow/rule2.png "Regel baserad mappning")

Om du vill skapa alias för, ta bort, byta namn på eller ordna om kolumner måste du först stänga av "Auto-Map". Som standard visas en standard regel som anges för dig som heter "alla kolumner". Du kan lämna den här regeln på plats om du alltid vill att alla inkommande kolumner ska mappas till samma namn i sina utdata.

Men om du vill lägga till anpassade regler klickar du på Lägg till mappning. Fält mappning ger dig en lista över inkommande och utgående kolumn namn för mappning och alias. Välj "regel baserad mappning" om du vill skapa mönster matchnings regler.

## <a name="rule-based-mapping"></a>Regel baserad mappning
När du väljer regelbaserade mappning instruerar du ADF att utvärdera matchnings uttrycket så att det matchar inkommande mönster regler och definierar de utgående fält namnen. Du kan lägga till valfri kombination av både fält-och regelbaserade mappningar. Fält namn genereras sedan vid körning med ADF baserat på inkommande metadata från källan. Du kan visa namnen på de genererade fälten under fel sökning och i fönstret data förhands granskning.

Mer information om mönster matchning finns i [kolumn mönster dokumentationen](concepts-data-flow-column-pattern.md).

## <a name="next-steps"></a>Nästa steg
* När du har använt Välj för att byta namn på, ändra ordning och alias använder du [omvandling av mottagare](data-flow-sink.md) för att använda data i ett data lager.
