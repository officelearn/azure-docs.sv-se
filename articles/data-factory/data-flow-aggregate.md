---
title: Aggregera omvandling i mappning dataflöde – Azure Data Factory | Microsoft Docs
description: Lär dig mer om att samla data i stor skala i Azure Data Factory med mappning av Data flöda sammanställd transformeringen.
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 21135b26d4bc840b3fcb091e675e5e6bd24d8548
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312138"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Sammanställd omvandling i mappning av dataflöde 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Sammanställd omvandlingen är där du ska definiera aggregeringar av kolumner i din dataströmmar. Du kan definiera olika typer av aggregeringar som SUM, MIN, MAX och antal som kan grupperas efter befintliga eller beräknade kolumner med Uttrycksverktyget.

## <a name="group-by"></a>Gruppera efter
Välj en befintlig kolumn eller skapa en ny beräknad kolumn som används som en grupp by-sats för din aggregering. Välj den önskade kolumnen i listrutan om du vill använda en befintlig kolumn. Hovra över en sats för att skapa en ny beräknad kolumn, och klicka på ”beräknad kolumn'. Då öppnas det [Data flöda Uttrycksverktyget](concepts-data-flow-expression-builder.md). Ange kolumnnamnet utdata under fältet ”namn som” när du har skapat din beräknad kolumn. Om du vill lägga till ytterligare en grupp av satsen hovra över en befintlig instruktion och klickar på ”+”.

![Sammanställd omvandling Gruppera efter inställningar](media/data-flow/agg.png "sammanställd omvandling Gruppera efter inställningar")

> [!NOTE]
> En group by-sats är valfri i en sammanställd transformering.

## <a name="aggregate-column"></a>Sammanställningskolumnen 
Välj fliken ”aggregeringar” för att skapa mängduttryck. Du kan antingen välja en befintlig kolumn och skriva över värdet med aggregering eller skapa ett nytt fält med ett nytt namn. Uttryck för aggregering anges i den högra rutan bredvid namnet kolumnväljaren. Redigera uttrycket genom att klicka på textrutan så att du öppnar Uttrycksverktyget. Att lägga till en ytterligare aggregering, hovrar du över ett befintligt uttryck och klicka på ”+” Skapa en ny kolumn för aggregering eller [mönster för kolumnen](concepts-data-flow-column-pattern.md).

![Aggregera omvandling sammanställd inställningar](media/data-flow/agg2.png "aggregera omvandling sammanställd inställningar")

> [!NOTE]
> Varje aggregering-uttryck måste innehålla minst en mängdfunktion.

> [!NOTE]
> I felsökningsläge och kan inte Uttrycksverktyget producera förhandsgranskningar av data med mängdfunktioner. Om du vill visa förhandsgranskningar av data för sammanställd transformationer att Stäng Uttrycksverktyget och visa data via fliken ”Förhandsgranskning”.

## <a name="next-steps"></a>Nästa steg

-Ange Windows-baserad aggregering med hjälp av den [fönstret omvandling](data-flow-window.md)