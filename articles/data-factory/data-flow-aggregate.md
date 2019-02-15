---
title: Azure Data Factory mappning sammanställd Dataomvandling för Flow
description: Azure Data Factory flöde sammanställd Dataomvandling
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 3793c49da32845d559d73faa25c571d3f86e062f
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56272040"
---
# <a name="azure-data-factory-mapping-data-flow-aggregate-transformation"></a>Azure Data Factory mappning sammanställd Dataomvandling för Flow

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Sammanställd omvandlingen är där du ska definiera aggregeringar av kolumner i din dataströmmar. I Uttrycksverktyget, kan du definiera olika typer av aggregeringar (t.ex. SUM, MIN, MAX, COUNT, etc.) och skapa ett nytt fält i dina utdata som innehåller dessa aggregeringar med valfritt att gruppera efter fält.

![Aggregera omvandling alternativ](media/data-flow/agg.png "aggregera 1")

## <a name="group-by"></a>Gruppera efter
(Valfritt) Välj en Group by-sats för din aggregering och använda namnet på en befintlig kolumn eller ett nytt namn. Använd ”Lägg till kolumn” Lägg till mer group by-satser och klicka på rutan bredvid kolumnnamnet på för att starta Uttrycksverktyget för att antingen välja en befintlig kolumn, kombination av kolumner eller uttryck för grupperingen.

## <a name="the-aggregate-column-tab"></a>Fliken Sammanställningskolumnen 
(Krävs) Välj fliken Sammanställningskolumnen att skapa mängduttryck. Du kan antingen välja en befintlig kolumn att skriva över värdet med aggregering eller skapa ett nytt fält med det nya namnet för sammanställning. Det uttryck som du vill använda för sammanställning ska skrivas in i den högra rutan bredvid namnet kolumnväljaren. När du klickar på textrutan öppnas Uttrycksverktyget.

![Aggregera omvandling alternativ](media/data-flow/agg2.png "aggregator")

## <a name="data-preview-in-expression-builder"></a>Förhandsgranskning i Uttrycksverktyget

I felsökningsläge och kan inte Uttrycksverktyget producera förhandsgranskningar av data med mängdfunktioner. Om du vill visa förhandsgranskningar av data för sammanställd transformationer att Stäng Uttrycksverktyget och visa data från flow-designern data.
