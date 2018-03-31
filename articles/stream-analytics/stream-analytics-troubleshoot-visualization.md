---
title: Visualisera och felsöka Stream Analytics-jobb | Microsoft Docs
description: Lär dig mer om att visualisera ett Stream Analytics-jobbet försäljningsförlopp för självbetjäning felsökning med hjälp av funktionen diagnostik diagram.
keywords: ''
documentationcenter: ''
services: stream-analytics
author: jseb225
manager: ryanw
ms.assetid: d87841cd-c59f-4a46-b46e-8b904fdc12e9
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeanb
ms.openlocfilehash: eae43a6a444514855229af760de6aa1cbec7840a
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="visualize-and-troubleshoot-stream-analytics-jobs"></a>Visualisera och felsöka Stream Analytics-jobb
I Stream Analytics, precis som med andra molnbaserad teknik behövs felsökning ibland för att undersöka varför ett jobb inte ger utdatan som förväntas (eller inga utdata att). Med detta begrepp i åtanke kan Stream Analytics du visualisera ett direktuppspelningsjobb. Detta är också användbar som ett verktyg för modellering och har sida-förmån för dessa kräver dokumentationen för sitt arbete.

Indata är synliga samt frågan utförs och sedan alla utdata konfigurerats på panelen visualiseringen. Problem med nätverksanslutningen eller konfiguration kan bli tydligare och det kan också vara praktiskt att visa en bild av din konfiguration.

## <a name="using-the-diagnosis-diagram-tool"></a>Med hjälp av verktyget diagnos diagram
Klicka på knappen ”diagnos diagram” i området ”Inställningar” i Stream Analytics-jobbet för att komma åt den här visualizer.

![Stream-Analytics-Troubleshoot-Visualization-Diagnosis-diagram](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-diagnosis-diagram1.png)

Varje inkommande och utgående är färgkodade för att ange aktuellt tillståndet för komponent, enligt följande.

![stream-analytics-troubleshoot-visualization-input-map](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-input-map.png)

När användaren vill titta på mellanliggande frågesteg att förstå flödet datamönster inuti ett jobb, innehåller verktyget visualiseringen en uppdelning av frågan till komponenten steg och flödet sekvensen. Klicka på varje frågesteg visar motsvarande avsnitt i en fråga Redigera fönstret som visas. 

![stream-analytics-troubleshoot-visualization-intermediate-steps](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-intermediate-steps.png)

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referens för Azure Stream Analytics Management REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

