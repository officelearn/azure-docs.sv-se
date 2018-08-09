---
title: Övervaka och hantera Azure Stream Analytics-jobb med hjälp av Visual Studio
description: Den här artikeln beskriver hur du använder Visual Studio för att övervaka och hantera Azure Stream Analytics-jobb.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/10/2018
ms.openlocfilehash: 9cc65fc5119bedc816f825b7aae6120f4bca3b12
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39717035"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-visual-studio"></a>Övervaka och hantera Stream Analytics-jobb med Visual Studio

Den här artikeln visar hur du övervakar ditt Stream Analytics-jobb i Visual Studio. Azure Stream Analytics-verktyg för Visual Studio tillhandahåller en övervakningsupplevelse som liknar Azure-portalen utan att behöva lämna din IDE. Du kan börja övervaka ett jobb så snart som du **skicka till Azure** från din **Script.asaql**, eller du kan övervaka befintliga jobb oavsett hur de skapades. 

## <a name="job-summary"></a>Jobbsammanfattning

Den **jobbsammanfattning** och **Jobbmått** ge en ögonblicksbild av jobbet. På ett ögonblick, kan du fastställa ett jobb samt händelseinformation information.]

<img src="./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-job-summary-metrics.png" alt="Stream Analytics job summary and job metrics" width="300px"/> 


## <a name="job-metrics"></a>Jobbmått

Du kan dölja den **jobbsammanfattning** och klicka på den **Jobbmått** fliken för att visa ett diagram med viktiga mått. Kontrollera och avmarkera mått typer för att lägga till och ta bort dem från diagrammet.

![Mätvärden för Stream Analytics](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-metrics.png)


## <a name="error-monitoring"></a>Fel vid övervakning

Du kan också övervaka fel genom att klicka på den **fel** fliken.

![Stream Analytics-fel](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-errors.png)


## <a name="get-support"></a>Få support
För mer hjälp kan du prova vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics). 

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Skapa ett Azure Stream Analytics-jobb med Visual Studio](stream-analytics-quick-create-vs.md)
* [Installera Azure Stream Analytics-verktyg för Visual Studio](stream-analytics-tools-for-visual-studio-install.md) 


