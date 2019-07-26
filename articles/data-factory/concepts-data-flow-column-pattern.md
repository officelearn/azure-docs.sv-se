---
title: Azure Data Factory mappa data flödes kolumn mönster
description: Skapa generaliserade data omvandlings mönster med Azure Data Factory kolumn mönster i data flödena för mappning
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 076c3318a68a50e6bd1b4f9f2a4a4b9a034533c6
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68346565"
---
# <a name="azure-data-factory-mapping-data-flows-column-patterns"></a>Kolumn mönster för mappning av Azure Data Factory-data flöden

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Flera Azure Data Factory data flödes transformationer stöder idén med "kolumn mönster" så att du kan skapa mallgrupper baserat på mönster i stället för hårdkodade kolumn namn. Du kan använda den här funktionen i uttrycks verktyget för att definiera mönster som matchar kolumner för omvandling i stället för att kräva exakta, specifika fält namn. Mönster är användbara om inkommande käll fält ändras ofta, särskilt när det gäller att ändra kolumner i textfiler eller NoSQL-databaser. Det här villkoret kallas ibland "schema avvikelse".

![kolumn mönster](media/data-flow/columnpattern2.png "Kolumn mönster")

Kolumn mönster är användbara för att hantera både schema drift scenarier och allmänna scenarier. Det är lämpligt för villkor där du inte kan se varje kolumn namn fullständigt. Du kan mönster matchning för kolumn namn och kolumn data typ och skapa ett uttryck för omvandling som utför åtgärden mot ett fält i data strömmen som matchar dina `name`  &  `type` mönster.

När du lägger till ett uttryck i en transformering som accepterar mönster, väljer du Lägg till kolumn mönster. Med kolumn mönster kan du matcha matchnings mönster för schema riktnings kolumner.

När du skapar mallens kolumn mönster `$$` använder du i uttrycket för att representera en referens till varje matchat fält från indata-dataströmmen.

Om du väljer att använda en av uttrycks Byggareets regex-funktioner kan du därefter använda $1, $2, $3... för att referera till de under mönster som matchas från ditt regex-uttryck.

Ett exempel på ett kolumn mönster scenario använder SUM med en serie inkommande fält. De aggregerade SUM-beräkningarna finns i den sammanställda omvandlingen. Du kan sedan använda SUM på varje matchande fält typ som matchar "Integer" och sedan använda $ $ för att referera till varje matchning i ditt uttryck.

## <a name="match-columns"></a>Matcha kolumner
![kolumn mönster typer](media/data-flow/pattern2.png "Mönster typer")

Om du vill bygga mönster baserade på kolumner kan du matcha kolumn namn, typ, ström eller position och använda valfri kombination av dem med uttrycks funktioner och reguljära uttryck.

![kolumn position](media/data-flow/position.png "Kolumn position")

## <a name="next-steps"></a>Nästa steg
Läs mer om data flödes uttrycks [språket](https://aka.ms/dataflowexpressions) för ADF-mappning för data transformationer
