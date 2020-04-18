---
title: Omformning av dataflödesfönster
description: Azure Data Factory-mappning av dataflödesfönster
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: 1f440a5877dcefeca8c1baa82fad4299b5ce7bce
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606231"
---
# <a name="azure-data-factory-window-transformation"></a>Omvandling av Azure Data Factory-fönster

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Fönsteromvandlingen är där du definierar fönsterbaserade aggregeringar av kolumner i dina dataströmmar. I Uttrycksverktyget kan du definiera olika typer av aggregeringar som baseras på data eller tidsfönster (SQL OVER-satsen), till exempel LEAD, LAG, NTILE, CUMEDIST, RANK, etc.). Ett nytt fält genereras i utdata som innehåller dessa aggregeringar. Du kan också inkludera valfria grupp-för-fält.

![Fönsteralternativ](media/data-flow/windows1.png "windows 1")

## <a name="over"></a>Över
Ange partitionering av kolumndata för fönsteromvandlingen. SQL-motsvarigheten ```Partition By``` är i Över-satsen i SQL. Om du vill skapa en beräkning eller skapa ett uttryck som ska användas för partitioneringen kan du göra det genom att hovra över kolumnnamnet och välja "beräknad kolumn".

![Fönsteralternativ](media/data-flow/windows4.png "windows 4")

## <a name="sort"></a>Sortera
En annan del av över-satsen är att ange ```Order By```. Detta anger datasorteringsordningen. Du kan också skapa ett uttryck för ett beräknat värde i det här kolumnfältet för sortering.

![Fönsteralternativ](media/data-flow/windows5.png "windows 5")

## <a name="range-by"></a>Intervall efter
Ställ sedan in fönsterramen som Obunden eller Avgränsad. Om du vill ange en obunden fönsterram ställer du in skjutreglaget på Obunden i båda ändar. Om du väljer en inställning mellan Obunden och Aktuell rad måste du ange värdena För förskjutningsstart och. Båda värdena är positiva heltal. Du kan använda antingen relativa tal eller värden från dina data.

Fönsterreglaget har två värden att ange: värdena före den aktuella raden och värdena efter den aktuella raden. Förskjutningen Start och matchar de två väljarena på skjutreglaget.

![Fönsteralternativ](media/data-flow/windows6.png "windows 6")

## <a name="window-columns"></a>Fönsterkolumner
Slutligen kan du använda Uttrycksverktyget för att definiera de aggregeringar som du vill använda med datafönstren som RANK, COUNT, MIN, MAX, DENSE RANK, LEAD, LAG, etc.

![Fönsteralternativ](media/data-flow/windows7.png "windows 7")

Den fullständiga listan över aggregerings- och analysfunktioner som är tillgängliga för dig att använda https://aka.ms/dataflowexpressionsi ADF-dataflödesuttrycksspråket via Uttrycksverktyget visas här: .

## <a name="next-steps"></a>Nästa steg

Om du letar efter en enkel grupp-för-aggregering använder du [den sammanlagda omvandlingen](data-flow-aggregate.md)
