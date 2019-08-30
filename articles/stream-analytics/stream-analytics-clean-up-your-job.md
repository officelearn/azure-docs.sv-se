---
title: Rensa upp din Azure Stream Analytics-jobb
description: Den här artikeln beskrivs olika metoder för att ta bort Azure Stream Analytics-jobb.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: 093ada2b2751540b986154be3a2f333784c1259f
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173294"
---
# <a name="stop-or-delete-your-azure-stream-analytics-job"></a>Stoppa eller ta bort ditt Azure Stream Analytics jobb

Azure Stream Analytics-jobb kan stoppas eller tas bort via Azure Portal, Azure PowerShell, Azure SDK för .net eller REST API. Ett Stream Analytics jobb kan inte återställas när det har tagits bort.

>[!NOTE] 
>När du avbryter ditt Stream Analytics-jobb, kvarstår data endast i den inkommande och utgående lagringen, till exempel Event Hubs eller Azure SQL Database. Om du behöver ta bort data från Azure ska du följa den borttagning av processen för inkommande och utgående resurser på ditt Stream Analytics-jobb.

## <a name="stop-a-job-in-azure-portal"></a>Stoppa ett jobb i Azure-portalen

När du stoppar ett jobb avetableras resurserna och den stoppar bearbetningen av händelser. Avgifter som rör detta jobb stoppas också. Men all konfiguration behålls och du kan starta om jobbet senare 

1. Logga in på [Azure Portal](https://portal.azure.com). 

2. Leta upp ditt körs Stream Analytics-jobb och markera den.

3. På sidan Stream Analytics-jobb väljer **stoppa** att stoppa jobbet. 

   ![Stoppa Azure Stream Analytics-jobb](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Ta bort ett jobb i Azure-portalen

>[!WARNING] 
>Ett Stream Analytics jobb kan inte återställas när det har tagits bort.

1. Logga in på Azure Portal. 

2. Leta upp ditt befintliga Stream Analytics-jobb och markera den.

3. På sidan Stream Analytics-jobb väljer **ta bort** ta bort jobbet. 

   ![Ta bort Azure Stream Analytics-jobb](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Stoppa eller ta bort ett jobb med hjälp av PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Använd cmdleten [Stop-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) om du vill stoppa ett jobb med PowerShell. Om du vill ta bort ett jobb med PowerShell använder du cmdleten [Remove-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob) .

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Stoppa eller ta bort ett jobb med Azure SDK för .NET

Om du vill stoppa ett jobb med Azure SDK för .NET, använda den [StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) metod. Att ta bort ett jobb med Azure SDK för .NET, [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet) metod.

## <a name="stop-or-delete-a-job-using-rest-api"></a>Stoppa eller ta bort ett jobb med hjälp av REST API

Om du vill stoppa ett jobb med hjälp av REST API, som avser den [stoppa](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop) metod. Om du vill ta bort ett jobb med hjälp av REST API, som avser den [ta bort](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete) metod.
