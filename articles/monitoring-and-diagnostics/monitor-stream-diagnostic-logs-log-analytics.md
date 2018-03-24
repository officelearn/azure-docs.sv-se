---
title: Strömma Azure diagnostiska loggar till Log Analytics | Microsoft Docs
description: Lär dig mer om att strömma Azure diagnostiska loggar till en logganalys-arbetsyta.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: johnkem
ms.openlocfilehash: 517ce3547f471dd1b40c79b2f087b02ad7f51b85
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics"></a>Strömma Azure diagnostiska loggar till logganalys
**[Azure diagnostikloggar](monitoring-overview-of-diagnostic-logs.md)**  kan strömmas i nära realtid till Azure Log Analytics med hjälp av portalen, PowerShell-cmdlets eller Azure CLI.

## <a name="what-you-can-do-with-diagnostics-logs-in-log-analytics"></a>Vad du kan göra med diagnostiken loggar i logganalys

Azure Log Analytics är en flexibel sökning och analyser för loggfiler som gör att du kan få insyn i raw loggdata som genereras från Azure-resurser. Vissa funktioner är:

* **Sök i loggfilen** -skrivåtgärder avancerade frågor via din loggdata, korrelera loggar från olika källor, och även generera diagram som kan vara fäst på instrumentpanelen i Azure.
* **Aviseringar** -identifiera när en eller flera händelser som matchar en viss fråga och bli ett meddelande med en e-post eller webhook-anrop.
* **Lösningar** -använda fördefinierade vyer och instrumentpaneler som ger dig direkta insikter om dina loggdata.
* **Avancerade analyser** – tillämpa maskininlärning och mönstret matchande algoritmer för att identifiera eventuella problem som visas av loggarna.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics"></a>Strömning av diagnostiska loggar till logganalys
Du kan aktivera strömning av diagnostikloggar programmässigt via portalen eller med hjälp av den [Azure övervakaren REST API: er](https://docs.microsoft.com/rest/api/monitor/servicediagnosticsettings). Oavsett hur du skapar en diagnostikinställningen där du anger en logganalys-arbetsytan och logg kategorier och mått som du vill skicka i till arbetsytan. En diagnostik **loggen kategori** är en typ av logg som en resurs kan ge.

Logganalys-arbetsytan behöver inte finnas i samma prenumeration som resursen avger loggar så länge som den användare som konfigurerar inställningen har lämplig RBAC åtkomst till båda prenumerationer.

## <a name="stream-diagnostic-logs-using-the-portal"></a>Dataströmmen diagnostikloggar med hjälp av portalen
1. Gå till Azure-Monitor i portalen och klicka på **diagnostikinställningar**

    ![Avsnittet av Azure-Monitor övervakning](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-blade.png)

2. Om du vill filtrera listan efter resursgrupp eller resurstyp, och klicka sedan på resursen som du vill ange en diagnostikinställningen.

3. Om det finns inga inställningar på resursen har du valt, uppmanas du för att skapa en inställning. Klicka på ”Aktivera diagnostik”.

   ![Lägg till diagnostikinställningen - inga befintliga inställningar](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-none.png)

   Om det finns befintliga inställningarna på resursen, visas en lista över inställningar som redan har konfigurerats på den här resursen. Klicka på ”Lägg till diagnostikinställningen”.

   ![Lägg till diagnostikinställningen - befintliga inställningar](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-multiple.png)

3. Ge din ange ett namn och markera kryssrutan för **skicka till logganalys**, välj sedan en logganalys-arbetsytan.
   
   ![Lägg till diagnostikinställningen - befintliga inställningar](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-configure.png)

4. Klicka på **Spara**.

Den nya inställningen visas i din lista över inställningar för den här resursen efter en liten stund och diagnostiska loggar strömmas till arbetsytan när nya händelsedata genereras. Observera att det kan vara upp till femton minuter mellan när en händelse genereras och när den visas i logganalys.

### <a name="via-powershell-cmdlets"></a>Via PowerShell-Cmdlets
Strömning den [Azure PowerShell-Cmdlets](insights-powershell-samples.md), du kan använda den `Set-AzureRmDiagnosticSetting` med följande parametrar:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

Observera att egenskapen workspaceID tar kompletta Azure-resurs-ID för arbetsytan inte arbetsytan ID/nyckeln i logganalys-portalen.

### <a name="via-azure-cli"></a>Via Azure CLI
Strömning den [Azure CLI](insights-cli-samples.md), du kan använda den `insights diagnostic set` kommandot så här:

```azurecli
azure insights diagnostic set --resourceId <resourceID> --workspaceId <workspace resource ID> --categories <list of categories> --enabled true
```

Observera att egenskapen workspaceId tar kompletta Azure-resurs-ID för arbetsytan inte arbetsytan ID/nyckeln i logganalys-portalen.

## <a name="how-do-i-query-the-data-in-log-analytics"></a>Hur jag för att fråga efter data i Log Analytics?

Du kan fråga diagnostikloggar som en del av loggen hanteringslösningen under tabellen AzureDiagnostics i bladet loggen Sök i portalen eller Advanced Analytics upplevelse som en del av logganalys. Det finns också [flera lösningar för Azure-resurser](../log-analytics/log-analytics-add-solutions.md) du kan installera för att få direkta insikter om loggdata som du skickar till logganalys.


## <a name="next-steps"></a>Nästa steg
* [Läs mer om Azure diagnostikloggar](monitoring-overview-of-diagnostic-logs.md)
