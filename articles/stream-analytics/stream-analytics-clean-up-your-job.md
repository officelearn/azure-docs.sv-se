---
title: Rensa Azure Stream Analytics-jobb
description: Den här artikeln är en guide för hur du tar bort Azure Stream Analytics-jobb.
services: stream-analytics
author: mamccrea
manager: kfile
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: 4773f542f12ae1773e881106bc8948c663bfd1e3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34661082"
---
# <a name="clean-up-your-azure-stream-analytics-job"></a>Rensa Azure Stream Analytics-jobb

Azure Stream Analytics-jobb kan enkelt tas bort via Azure-portalen, Azure PowerShell, Azure SDK för .net eller REST API.

>[!NOTE] 
>När du stoppar Stream Analytics-jobbet kvarstår data endast i inkommande och utgående lagringen, till exempel Händelsehubbar eller Azure SQL Database. Om du behöver ta bort data från Azure måste du följa borttagningsprocessen för inkommande och utgående resurser av Stream Analytics-jobbet.

## <a name="stop-a-job-in-azure-portal"></a>Stoppa ett jobb i Azure-portalen

1. Logga in på [Azure Portal](https://portal.azure.com). 

2. Leta upp din Stream Analytics-jobb som körs och markera den.

3. Välj på sidan Stream Analytics-jobbet **stoppa** att stoppa jobbet. 

   ![Stoppa jobb](./media/stream-analytics-clean-up-your-job/stop-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Ta bort ett jobb på Azure-portalen

1. Logga in på Azure Portal. 

2. Leta upp din befintliga Stream Analytics-jobbet och välja den.

3. Välj på sidan Stream Analytics-jobbet **ta bort** att ta bort jobbet. 

   ![Ta bort jobb](./media/stream-analytics-clean-up-your-job/delete-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Stoppa eller ta bort ett jobb med hjälp av PowerShell

Om du vill stoppa ett jobb med hjälp av PowerShell, Använd den [stoppa AzureRmStreamAnalyticsJob](https://docs.microsoft.com/en-us/powershell/module/azurerm.streamanalytics/stop-azurermstreamanalyticsjob?view=azurermps-5.7.0) cmdlet. Om du vill ta bort ett jobb med hjälp av PowerShell, Använd den [ta bort AzureRmStreamAnalyticsJob](https://docs.microsoft.com/en-us/powershell/module/azurerm.streamanalytics/Remove-AzureRmStreamAnalyticsJob?view=azurermps-5.7.0) cmdlet.

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Stoppa eller ta bort ett jobb med hjälp av Azure SDK för .NET

Om du vill stoppa ett jobb med hjälp av Azure SDK för .NET, Använd den [StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) metod. Ta bort ett jobb med hjälp av Azure SDK för .NET, [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet) metod.

## <a name="stop-or-delete-a-job-using-rest-api"></a>Stoppa eller ta bort ett jobb med hjälp av REST API

Om du vill stoppa ett jobb med hjälp av REST-API, referera till den [stoppa](https://docs.microsoft.com/en-us/rest/api/streamanalytics/stream-analytics-job#stop) metod. Om du vill ta bort ett jobb med hjälp av REST-API, referera till den [ta bort](https://docs.microsoft.com/en-us/rest/api/streamanalytics/stream-analytics-job#delete) metod.