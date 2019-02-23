---
title: Rensa upp din Azure Stream Analytics-jobb
description: Den här artikeln beskrivs olika metoder för att ta bort Azure Stream Analytics-jobb.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: cfc01667f1fee7e57929e1990b5cf8c99a0fd595
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56737509"
---
# <a name="clean-up-your-azure-stream-analytics-job"></a>Rensa upp din Azure Stream Analytics-jobb

Azure Stream Analytics-jobb kan enkelt tas bort via Azure portal, Azure PowerShell, Azure SDK för .net eller REST API.

>[!NOTE] 
>När du avbryter ditt Stream Analytics-jobb, kvarstår data endast i den inkommande och utgående lagringen, till exempel Event Hubs eller Azure SQL Database. Om du behöver ta bort data från Azure ska du följa den borttagning av processen för inkommande och utgående resurser på ditt Stream Analytics-jobb.

## <a name="stop-a-job-in-azure-portal"></a>Stoppa ett jobb i Azure-portalen

1. Logga in på [Azure Portal](https://portal.azure.com). 

2. Leta upp ditt körs Stream Analytics-jobb och markera den.

3. På sidan Stream Analytics-jobb väljer **stoppa** att stoppa jobbet. 

   ![Stoppa Azure Stream Analytics-jobb](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Ta bort ett jobb i Azure-portalen

1. Logga in på Azure Portal. 

2. Leta upp ditt befintliga Stream Analytics-jobb och markera den.

3. På sidan Stream Analytics-jobb väljer **ta bort** ta bort jobbet. 

   ![Ta bort Azure Stream Analytics-jobb](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Stoppa eller ta bort ett jobb med hjälp av PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du vill stoppa ett jobb med hjälp av PowerShell, Använd den [Stop-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) cmdlet. Om du vill ta bort ett jobb med hjälp av PowerShell, Använd den [Remove-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob) cmdlet.

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Stoppa eller ta bort ett jobb med Azure SDK för .NET

Om du vill stoppa ett jobb med Azure SDK för .NET, använda den [StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) metod. Att ta bort ett jobb med Azure SDK för .NET, [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet) metod.

## <a name="stop-or-delete-a-job-using-rest-api"></a>Stoppa eller ta bort ett jobb med hjälp av REST API

Om du vill stoppa ett jobb med hjälp av REST API, som avser den [stoppa](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop) metod. Om du vill ta bort ett jobb med hjälp av REST API, som avser den [ta bort](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete) metod.
