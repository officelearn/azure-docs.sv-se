---
title: Transformering av data flödes fönster
description: Transformering av Azure Data Factory mappning av data flödes fönster
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: fa34def67d91332a00bf0ee92b365957a47f9616
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931485"
---
# <a name="azure-data-factory-window-transformation"></a>Transformering av Azure Data Factory fönster



Fönster omvandlingen är den plats där du ska definiera Window-baserade agg regeringar för kolumner i dina data strömmar. I uttrycks verktyget kan du definiera olika typer av agg regeringar som baseras på data eller tidsfönster (SQL över-sats) som LEAD, fördröjning, NTILE, CUMEDIST, rang osv.). Ett nytt fält skapas i utdata som innehåller dessa agg regeringar. Du kan även ta med valfria grupp fält.

![Fönster alternativ](media/data-flow/windows1.png "Windows 1")

## <a name="over"></a>Över
Ange partitionering för kolumn data för fönster omvandlingen. SQL-motsvarigheten är ```Partition By``` i över-satsen i SQL. Om du vill skapa en beräkning eller skapa ett uttryck som ska användas för partitionering kan du göra det genom att hovra över kolumn namnet och välja "beräknad kolumn".

![Fönster alternativ](media/data-flow/windows4.png "Windows 4")

## <a name="sort"></a>Sortera
En annan del av över-satsen anger ```Order By```. Detta anger sorterings ordningen för data. Du kan också skapa ett uttryck för att beräkna värdet i det här kolumn fältet för sortering.

![Fönster alternativ](media/data-flow/windows5.png "Windows 5")

## <a name="range-by"></a>Intervall av
Ange sedan fönster ramen som obunden eller gräns. Om du vill ange en obunden fönster ram anger du skjutreglaget till obundet i båda ändar. Om du väljer en inställning mellan en obunden och aktuell rad måste du ange start-och slut värden för offset. Båda värdena är positiva heltal. Du kan använda antingen relativa tal eller värden från dina data.

Skjutreglaget fönster har två värden att ange: värdena före den aktuella raden och värdena efter den aktuella raden. Start-och slut förskjutningen matchar de två markeringarna i skjutreglaget.

![Fönster alternativ](media/data-flow/windows6.png "Windows 6")

## <a name="window-columns"></a>Fönster kolumner
Använd slutligen uttrycks verktyget för att definiera de agg regeringar som du vill använda med data Fönstren som rang, COUNT, MIN, MAX, tätare rang, LEAD, fördröjning osv.

![Fönster alternativ](media/data-flow/windows7.png "windows 7")

En fullständig lista över agg regerings-och analys funktioner som du kan använda i uttrycks språket för ADF-dataflödet via uttrycks verktyget finns här: https://aka.ms/dataflowexpressions.

## <a name="next-steps"></a>Nästa steg

Om du letar efter en enkel samlings grupp, använder du den [sammanställda omvandlingen](data-flow-aggregate.md)
