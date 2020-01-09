---
title: Övervaka och hantera Azure Stream Analytics med Visual Studio
description: Den här artikeln beskriver hur du använder Visual Studio för att övervaka och hantera Azure Stream Analytics-jobb.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: e43a8d157baaf7e02ab7f8db4c777009d2a0abe9
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431692"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-visual-studio"></a>Övervaka och hantera Stream Analytics-jobb med Visual Studio

Den här artikeln visar hur du övervakar ditt Stream Analytics-jobb i Visual Studio. Azure Stream Analytics-verktyg för Visual Studio tillhandahåller en övervakningsupplevelse som liknar Azure-portalen utan att behöva lämna din IDE. Du kan börja övervaka ett jobb så snart som du **skicka till Azure** från din **Script.asaql**, eller du kan övervaka befintliga jobb oavsett hur de skapades. 

## <a name="job-summary"></a>Jobbsammanfattning

Den **jobbsammanfattning** och **Jobbmått** ge en ögonblicksbild av jobbet. På ett ögonblick, kan du fastställa ett jobb samt händelseinformation information.]

<img src="./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-job-summary-metrics.png" alt="Stream Analytics job summary and job metrics" width="300px"/> 


## <a name="job-metrics"></a>Jobbmått

Du kan dölja den **jobbsammanfattning** och klicka på den **Jobbmått** fliken för att visa ett diagram med viktiga mått. Kontrollera och avmarkera mått typer för att lägga till och ta bort dem från diagrammet.

![Stream Analytics-mått i Visual Studio](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-metrics.png)


## <a name="error-monitoring"></a>Fel vid övervakning

Du kan också övervaka fel genom att klicka på den **fel** fliken.

![Stream Analytics-fel i Visual Studio](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-errors.png)


## <a name="get-support"></a>Få support
För mer hjälp kan du prova vår [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics). 

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Skapa ett Azure Stream Analytics-jobb med Visual Studio](stream-analytics-quick-create-vs.md)
* [Installera Azure Stream Analytics-verktyg för Visual Studio](stream-analytics-tools-for-visual-studio-install.md) 


