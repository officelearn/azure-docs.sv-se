---
title: Hur du startar Azure Stream Analytics-jobb
description: Den här artikeln beskrivs hur du startar ett Stream Analytics-jobb.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2019
ms.openlocfilehash: aa089ed53554ec697bd9430cc95a7cce78aabed2
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57411531"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Hur du startar Azure Stream Analytics-jobb

Du kan börja din Azure Stream Analytics-jobb med Azure portal, Visual Studio och PowerShell. När du startar ett jobb, väljer du en gång för att jobbet ska börja skapa utdata. Azure-portalen, Visual Studio och PowerShell har olika metoder för att ange starttiden. Dessa metoder beskrivs nedan.

## <a name="azure-portal"></a>Azure Portal

Gå till ditt jobb i Azure-portalen och välj **starta** på översiktssidan. Välj en **starttid för jobbutdata** och välj sedan **starta**.

Det finns tre alternativ för **starttid för jobbutdata**: *Nu*, *anpassade*, och *senast stoppad*. Att välja *nu* startar jobbet vid aktuell tid. Att välja *anpassade* kan du ange en anpassad tid i förflutna eller i framtiden för att jobbet ska börja. Välj om du vill återuppta ett stoppat jobb utan att förlora data. Senast stoppad *.

## <a name="visual-studio"></a>Visual Studio

I jobbvyn, väljer du den gröna pilen för att starta jobbet. Ange den **jobbet utdata starta läget** och välj **starta**. Jobbstatusen ändras till **kör**.

Det finns tre alternativ för **jobbet utdata starta läget**: *JobStartTime*, *CustomTime*, och *LastOutputEventTime*. Om den här egenskapen inte finns, är standardvärdet *JobStartTime*.

*JobStartTime* gör startpunkt händelsens utdata strömma samma som när jobbet startas.

*CustomTime* startar utdata vid en anpassad tid som anges i den *OutputStartTime* parametern.

*LastOutputEventTime* gör startpunkten för händelsen utdataströmmen samma som den sista händelsen utdata tid.

## <a name="powershell"></a>PowerShell

Använd följande cmdlet för att starta jobbet med hjälp av PowerShell:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Det finns tre alternativ för **OutputStartMode**: *JobStartTime*, *CustomTime*, och *LastOutputEventTime*. Om den här egenskapen inte finns, är standardvärdet *JobStartTime*.

*JobStartTime* gör startpunkt händelsens utdata strömma samma som när jobbet startas.

*CustomTime* startar utdata vid en anpassad tid som anges i den *OutputStartTime* parametern.

*LastOutputEventTime* gör startpunkten för händelsen utdataströmmen samma som den sista händelsen utdata tid.

Mer information om den `Start-AzStreamAnalyitcsJob` cmdlet, visa den [Start AzStreamAnalyticsJob referens](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob.md).

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure-portalen](stream-analytics-quick-create-portal.md)
* [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure Stream Analytics-verktyg för Visual Studio](stream-analytics-quick-create-vs.md)