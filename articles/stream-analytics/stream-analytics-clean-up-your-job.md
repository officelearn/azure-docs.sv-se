---
title: Rensa Azure Stream Analytics jobbet
description: Den här artikeln visar olika metoder för att ta bort dina Azure Stream Analytics-jobb.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: 124d2cf4380153bd3ea68de9233f6a1b5eb6ba78
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130705"
---
# <a name="stop-or-delete-your-azure-stream-analytics-job"></a>Stoppa eller ta bort ditt Azure Stream Analytics jobb

Azure Stream Analytics-jobb kan stoppas eller tas bort via Azure Portal, Azure PowerShell, Azure SDK för .net eller REST API. Ett Stream Analytics jobb kan inte återställas när det har tagits bort.

>[!NOTE] 
>När du stoppar ditt Stream Analytics jobb sparas bara data i lagrings utrymmet för indata och utdata, till exempel Event Hubs eller Azure SQL Database. Om du måste ta bort data från Azure måste du följa borttagnings processen för indata-och utmatnings resurser för ditt Stream Analytics jobb.

## <a name="stop-a-job-in-azure-portal"></a>Stoppa ett jobb i Azure Portal

När du stoppar ett jobb avetableras resurserna och den stoppar bearbetningen av händelser. Avgifter som rör detta jobb stoppas också. Men all konfiguration behålls och du kan starta om jobbet senare 

1. Logga in på [Azure-portalen](https://portal.azure.com). 

2. Leta upp Stream Analytics jobb som körs och markera det.

3. På sidan Stream Analytics jobb väljer du **stoppa** för att stoppa jobbet. 

   ![Stoppa Azure Stream Analytics jobb](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Ta bort ett jobb i Azure Portal

>[!WARNING] 
>Ett Stream Analytics jobb kan inte återställas när det har tagits bort.

1. Logga in på Azure-portalen. 

2. Leta upp ditt befintliga Stream Analytics-jobb och markera det.

3. På sidan Stream Analytics jobb väljer du **ta bort** för att ta bort jobbet. 

   ![Ta bort Azure Stream Analytics jobb](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Stoppa eller ta bort ett jobb med PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Använd cmdleten [Stop-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob) om du vill stoppa ett jobb med PowerShell. Om du vill ta bort ett jobb med PowerShell använder du cmdleten [Remove-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob) .

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Stoppa eller ta bort ett jobb med Azure SDK för .NET

Om du vill stoppa ett jobb med Azure SDK för .NET använder du metoden [StreamingJobsOperationsExtensions. BeginStop](/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet) . Ta bort ett jobb med hjälp av Azure SDK för .NET, metoden [StreamingJobsOperationsExtensions. BeginDelete](/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet) .

## <a name="stop-or-delete-a-job-using-rest-api"></a>Stoppa eller ta bort ett jobb med REST API

Om du vill stoppa ett jobb med hjälp av REST API, se [stopp](/powershell/module/azurerm.streamanalytics/stop-azurermstreamanalyticsjob#stop) metoden. Om du vill ta bort ett jobb med hjälp av REST API, se [Delete](/powershell/module/azurerm.streamanalytics/stop-azurermstreamanalyticsjob#delete) -metoden.