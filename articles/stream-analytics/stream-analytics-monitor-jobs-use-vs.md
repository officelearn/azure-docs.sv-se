---
title: Övervaka och hantera Azure Stream Analytics med Visual Studio
description: I den här artikeln beskrivs hur du använder Visual Studio för att övervaka och hantera Azure Stream Analytics-jobb.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: e43a8d157baaf7e02ab7f8db4c777009d2a0abe9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431692"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-visual-studio"></a>Övervaka och hantera Stream Analytics-jobb med Visual Studio

Den här artikeln visar hur du övervakar ditt Stream Analytics-jobb i Visual Studio. Azure Stream Analytics-verktyg för Visual Studio ger en övervakningsupplevelse som liknar Azure-portalen utan att behöva lämna IDE. Du kan börja övervaka ett jobb så fort du **skickar till Azure** från ditt **Script.asaql**eller så kan du övervaka befintliga jobb oavsett hur de skapades. 

## <a name="job-summary"></a>Sammanfattning av jobb

**Jobbsammanfattningen** och **jobbmåtten** ger en snabb ögonblicksbild av ditt jobb. Du kan snabbt bestämma ett jobbs status och händelseinformation.]

<img src="./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-job-summary-metrics.png" alt="Stream Analytics job summary and job metrics" width="300px"/> 


## <a name="job-metrics"></a>Projektmått

Du kan komprimera **jobbsammanfattningen** och klicka på fliken **Jobbmått** för att visa ett diagram med viktiga mått. Kontrollera och avmarkera måtttyper för att lägga till och ta bort dem från diagrammet.

![Stream Analytics-mätvärden i Visual Studio](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-metrics.png)


## <a name="error-monitoring"></a>Felövervakning

Du kan också övervaka fel genom att klicka på fliken **Fel.**

![Stream Analytics-fel i Visual Studio](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-errors.png)


## <a name="get-support"></a>Få support
Om du vill ha mer hjälp kan du prova vårt [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics). 

## <a name="next-steps"></a>Nästa steg
* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Skapa ett Azure Stream Analytics-jobb med Visual Studio](stream-analytics-quick-create-vs.md)
* [Installera Azure Stream Analytics-verktyg för Visual Studio](stream-analytics-tools-for-visual-studio-install.md) 


