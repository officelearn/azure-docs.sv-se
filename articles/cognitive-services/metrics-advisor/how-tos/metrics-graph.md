---
title: Mått diagram för mått rådgivare
titleSuffix: Azure Cognitive Services
description: Hur du konfigurerar mått diagram och visualiserar relaterade avvikelser i dina data.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: aahi
ms.openlocfilehash: 09ea16f07973757b169f21c7c3f909a24651daa4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90942097"
---
# <a name="how-to-build-a-metrics-graph-to-analyze-related-metrics"></a>Anvisningar: bygga ett mått diagram för att analysera relaterade mått

Varje mått i Metrics Advisor övervakas separat av en modell som lär sig från historiska data för att förutsäga framtida trender. Varje mått har en separat modell som tillämpas på den. I vissa fall kan dock flera mått relatera till varandra, och avvikelser måste analyseras över flera mått. **Mått diagrammet** hjälper dig med detta. 

Om du till exempel har olika typer av telemetri i skilda mått, övervakar Metric Advisor dem separat. Om avvikelser i ett mått orsakar avvikelser i andra, kan det vara bra att söka efter dessa relationer och rotor saken i dina data när de löser incidenter. Med mått diagrammet kan du skapa ett visuellt topologi diagram över påträffade avvikelser. 

## <a name="select-a-metric-to-put-the-first-node-to-the-graph"></a>Välj ett mått för att flytta den första noden till diagrammet

Klicka på fliken **mått diagram** i navigerings fältet. Det första steget i att skapa ett mått diagram är att placera en nod i grafen. Välj en datafeed och mått överst på sidan. En nod visas i den nedre panelen. 

:::image type="content" source="../media/graph/metrics-graph-select.png" alt-text="Välj mått":::

## <a name="add-a-noderelation-on-existing-node"></a>Lägg till en nod/relation på en befintlig nod

Därefter måste du lägga till en annan nod och ange en relation till en befintlig nod (er). Välj en befintlig nod och högerklicka på den. En snabb meny visas med flera alternativ. 

Klicka på **Lägg till relation**och du kommer att kunna välja ett annat mått och ange Relations typen mellan de två noderna. Du kan också använda vissa dimensions filter. 

:::image type="content" source="../media/graph/metrics-graph-node-action.png" alt-text="Välj mått":::

När du har upprepat stegen ovan kommer du att ha ett mått diagram som beskriver relationerna mellan alla relaterade mått.
**Tips om Node-färger**
> [!TIP]
> - När du väljer ett mått och dimensions filter, kommer alla noder med samma mått och dimensions filter i grafen att färgas som **<font color=blue>blå</font>**.
> - Omarkerade noder som representerar ett mått i grafen kommer att färgas **<font color=green>grönt</font>**.
> - Om det finns en avvikelse i det aktuella måttet, blir noden **<font color=red>röd</font>**.

## <a name="view-related-metrics-anomaly-status-in-incident-hub"></a>Visa relaterade mått avvikande status i incident hubben

När mått diagrammet skapas, när en avvikelse identifieras på mått i grafen, kan du Visa relaterade avvikelse status och få en överblick på hög nivå av incidenten. 

Klicka i en incident i diagrammet och rulla ned till **kors mått analys**, under diagnostikinformation.

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="Välj mått":::

## <a name="next-steps"></a>Nästa steg

- [Justera avvikelseidentifiering med hjälp av feedback](anomaly-feedback.md)
- [Diagnostisera en incident](diagnose-incident.md).
- [Konfigurera mått och finjustera konfiguration för identifiering](configure-metrics.md)
