---
title: Azure Data Factory mappning Dataomvandling Flow fönster
description: Azure Data Factory mappning Dataomvandling Flow fönster
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 6f3f06ff54fc76416ba63f4f09835897d546f8dc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61349964"
---
# <a name="azure-data-factory-window-transformation"></a>Azure Data Factory-fönstret transformering

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Fönstret omvandlingen är där du ska definiera Windows-baserad aggregeringar av kolumner i din dataströmmar. I Uttrycksverktyget, kan du definiera olika typer av aggregeringar som baseras på data eller tid windows (SQL OVER-satsen), till exempel LEADS, FÖRDRÖJNING, NTILE, CUMEDIST, RANGORDNING osv.). Ett nytt fält ska genereras i dina utdata som innehåller dessa aggregeringar. Du kan även innehålla valfria att gruppera efter fält.

![Alternativ för migreringsintervall](media/data-flow/windows1.png "windows 1")

## <a name="over"></a>Över
Ange partitionering av kolumndata för din fönstret omvandling. Motsvarande SQL-uttryck är den ```Partition By``` i Over-satsen i SQL. Om du vill skapa en beräkning eller skapa ett uttryck som ska användas för partitionering kan du göra det genom att hovra över kolumnens namn och välja ”beräknad kolumn”.

![Alternativ för migreringsintervall](media/data-flow/windows4.png "windows 4")

## <a name="sort"></a>Sortera
En annan del av Over-satsen kan du ställa den ```Order By```. Detta anger data sorteringen ordning. Du kan också skapa ett uttryck för ett calculate-värde i den här kolumnfält för sortering.

![Alternativ för migreringsintervall](media/data-flow/windows5.png "windows 5")

## <a name="range-by"></a>Intervall av
Nu ska vi konfigurera fönsterramen som Ramavgränsare eller begränsad. Om du vill ange en obundna fönsterram, ställer du in skjutreglaget till Ramavgränsare i bägge ändar. Om du väljer en inställning mellan Ramavgränsare och Current Row, måste du ange Offset-start och sluta värden. Båda värdena ska vara positiva heltal. Du kan använda relativa siffror eller värden från dina data.

Fönstret skjutreglaget har två värden för att ange: värdena före den aktuella raden och värdena efter den aktuella raden. Start- och slut-förskjutning matchar två väljare på skjutreglaget.

![Alternativ för migreringsintervall](media/data-flow/windows6.png "windows 6")

## <a name="window-columns"></a>Fönstret kolumner
Använd uttrycksverktyget definiera aggregeringar som du vill använda med data som windows, till exempel RANKNING, COUNT, MIN, MAX, KOMPAKTA RANKNING, LEAD, FÖRDRÖJNING, osv.

![Alternativ för migreringsintervall](media/data-flow/windows7.png "windows 7")

En fullständig lista över aggregering och analytiska funktioner som är tillgängliga för dig att använda i den ADF Data flöda Uttrycksspråk via Uttrycksverktyget visas här: https://aka.ms/dataflowexpressions.

## <a name="next-steps"></a>Nästa steg

Om du letar efter en enkel att gruppera efter-aggregering, använder du den [aggregera omvandling](data-flow-aggregate.md)
