---
title: Visualisera och felsöka Azure Stream Analytics-jobb
description: Den här artikeln beskriver hur du visualisera ett Stream Analytics-jobb med hjälp av funktionen diagnostik diagram om du vill felsöka självbetjäning.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 523802f1f9a1dda19c5b6a66da7bc26fee851bd2
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
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

