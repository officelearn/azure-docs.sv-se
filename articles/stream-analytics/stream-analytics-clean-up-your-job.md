---
title: Rensa ditt Azure Stream Analytics-jobb
description: I den här artikeln visas olika metoder för att ta bort dina Azure Stream Analytics-jobb.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: d99920417f20034da1001a821c02376ac19274d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426487"
---
# <a name="stop-or-delete-your-azure-stream-analytics-job"></a>Stoppa eller ta bort ditt Azure Stream Analytics-jobb

Azure Stream Analytics-jobb kan enkelt stoppas eller tas bort via Azure-portalen, Azure PowerShell, Azure SDK för .Net eller REST API. Det går inte att återställa ett Stream Analytics-jobb när det har tagits bort.

>[!NOTE] 
>När du stoppar ditt Stream Analytics-jobb kvarstår data endast i indata- och utdatalagringen, till exempel Event Hubs eller Azure SQL Database. Om du måste ta bort data från Azure måste du följa borttagningsprocessen för indata- och utdataresurserna för ditt Stream Analytics-jobb.

## <a name="stop-a-job-in-azure-portal"></a>Stoppa ett jobb i Azure-portalen

När du stoppar ett jobb avetableras resurserna och bearbetar händelser. Avgifter relaterade till det här jobbet stoppas också. Men all konfiguration behålls och du kan starta om jobbet senare 

1. Logga in på [Azure-portalen](https://portal.azure.com). 

2. Leta reda på ditt stream analytics-jobb som körs och välj det.

3. På jobbsidan Stream Analytics väljer du **Stoppa** för att stoppa jobbet. 

   ![Stoppa Azure Stream Analytics-jobbet](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Ta bort ett jobb i Azure Portal

>[!WARNING] 
>Det går inte att återställa ett Stream Analytics-jobb när det har tagits bort.

1. Logga in på Azure Portal. 

2. Leta reda på ditt befintliga Stream Analytics-jobb och välj det.

3. På jobbsidan Stream Analytics väljer du **Ta bort** för att ta bort jobbet. 

   ![Ta bort Azure Stream Analytics-jobb](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Stoppa eller ta bort ett jobb med PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du vill stoppa ett jobb med PowerShell använder du [cmdleten Stop-AzStreamAnalyticsJob.](https://docs.microsoft.com/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) Om du vill ta bort ett jobb med PowerShell använder du cmdleten [Remove-AzStreamAnalyticsJob.](https://docs.microsoft.com/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob)

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Stoppa eller ta bort ett jobb med Azure SDK för .NET

Om du vill stoppa ett jobb med Azure SDK för .NET använder du metoden [StreamingJobsOperationsExtensions.BeginStop.](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) Om du vill ta bort ett jobb med Azure SDK för .NET, [StreamingJobsOperationsExtensions.BeginDelete-metoden.](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet)

## <a name="stop-or-delete-a-job-using-rest-api"></a>Stoppa eller ta bort ett jobb med REST API

Om du vill stoppa ett jobb med REST API läser du [stop-metoden.](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop) Om du vill ta bort ett jobb med REST API läser du metoden [Ta bort.](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete)
