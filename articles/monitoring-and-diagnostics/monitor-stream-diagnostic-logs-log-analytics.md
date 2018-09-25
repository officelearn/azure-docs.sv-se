---
title: Stream Azure diagnostikloggar till Log Analytics
description: Lär dig mer om att strömma Azure diagnostikloggar till Log Analytics-arbetsytan.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: johnkem
ms.component: logs
ms.openlocfilehash: c419a3c44a38f72d56f2b7b362c62e683fc20c7f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993025"
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics"></a>Stream Azure diagnostikloggar till Log Analytics

**[Azure diagnostikloggar](monitoring-overview-of-diagnostic-logs.md)**  kan strömmas i nära realtid till Azure Log Analytics-portalen, PowerShell-cmdletar eller Azure CLI.

## <a name="what-you-can-do-with-diagnostics-logs-in-log-analytics"></a>Vad du kan göra med diagnostik loggar i Log Analytics

Azure Log Analytics är en flexibel sökning och analys loggverktyget som hjälper dig att få insyn i obearbetad data som genereras från Azure-resurser. Vissa funktioner är följande:

* **Loggsökning** -skrivning avancerade frågor över dina loggdata, korrelera loggar från olika källor, och även generera diagram som kan fästas på instrumentpanelen i Azure.
* **Aviseringar** – identifiera när en eller flera händelser som matchar en viss fråga och få ett meddelande med en e-post eller webhook-anrop.
* **Lösningar** -använda fördefinierade vyer och instrumentpaneler som ger dig omedelbar insyn i dina loggdata.
* **Avancerade analyser** – Använd maskininlärning och mönstret matchande algoritmer för att identifiera eventuella problem som visas med dina loggar.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics"></a>Kunna strömma diagnostikloggar till Log Analytics

Du kan aktivera strömning av diagnostikloggar programmässigt, via portalen, eller med hjälp av den [Azure Monitor REST API: er](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). Oavsett hur du skapar en diagnostikinställning i som du anger en Log Analytics-arbetsyta och loggkategorier och mått som du vill skicka i till arbetsytan. En diagnostik **loggkategori** är en typ av logg som en resurs kan ge.

Log Analytics-arbetsytan behöver inte finnas i samma prenumeration som resursen loggarna så länge som den användare som konfigurerar inställningen har lämplig RBAC-åtkomst till båda prenumerationerna.

> [!NOTE]
> Det går för närvarande inte att skicka flerdimensionella mätvärden via diagnostikinställningar. Mått med dimensioner exporteras som tillplattade endimensionella mått som aggregeras över dimensionsvärden.
>
> *Till exempel*: Måttet för inkommande meddelanden i en händelsehubb kan utforskas och läggas till på per-kö-nivå. När måttet exporteras via diagnostikinställningar visas det dock som alla inkommande meddelanden i alla köer i händelsehubben.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Stream diagnostikloggar med hjälp av portalen
1. I portalen, gå till Azure Monitor och klicka på **diagnostikinställningar**

    ![Avsnittet av Azure Monitor övervakning](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-blade.png)

2. Du kan också filtrera listan efter resursgruppen eller resursen. Klicka sedan på den resurs som du vill ange en diagnostikinställning.

3. Om inga inställningar finns på resursen har du valt, uppmanas du för att skapa en inställning. Klicka på ”Slå på diagnostik”.

   ![Lägg till diagnostikinställning - inga befintliga inställningar](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-none.png)

   Om det finns befintliga inställningarna på resursen, visas en lista över inställningar som redan har konfigurerats på den här resursen. Klicka på ”Lägg till diagnostikinställning”.

   ![Lägg till diagnostikinställning - befintliga inställningar](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-multiple.png)

3. Ge din ställa in ett namn och markera kryssrutan för **skicka till Log Analytics**, Välj en Log Analytics-arbetsyta.

   ![Lägg till diagnostikinställning - befintliga inställningar](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-configure.png)

4. Klicka på **Spara**.

Den nya inställningen visas i din lista över inställningar för den här resursen efter en liten stund och diagnostiska loggar strömmas till arbetsytan när nya händelsedata genereras. Observera att det kan ta upp till 15 minuter mellan när en händelse genereras och när den visas i Log Analytics.

### <a name="via-powershell-cmdlets"></a>Via PowerShell-cmdletar
Strömning den [Azure PowerShell-Cmdlets](insights-powershell-samples.md), du kan använda den `Set-AzureRmDiagnosticSetting` cmdlet med följande parametrar:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

Observera att egenskapen workspaceID tar fullständiga Azure-resurs-ID för arbetsytan kan inte arbetsytan ID-/ nyckel visas i Log Analytics-portalen.

### <a name="via-azure-cli"></a>Via Azure CLI

Strömning den [Azure CLI](insights-cli-samples.md), du kan använda den [az monitor diagnostic-settings skapa](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) kommando.

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --workspace <log analytics name or object ID> \
    --resource <target resource object ID> \
    --resource-group <log analytics workspace resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

Du kan lägga till ytterligare kategorier diagnostikloggen genom att lägga till ordlistor JSON-matris som skickas som den `--logs` parametern.

Den `--resource-group` argumentet är bara krävs om `--workspace` är inte ett objekt-ID.

## <a name="how-do-i-query-the-data-in-log-analytics"></a>Hur jag för att fråga data i Log Analytics?

På bladet Loggsökning i portalen eller Advanced Analytics-upplevelse som en del av Log Analytics kan du fråga diagnostikloggar som en del av Log Management-lösningen under tabellen AzureDiagnostics. Det finns även [flera lösningar för Azure-resurser](../log-analytics/log-analytics-add-solutions.md) du kan installera för att få omedelbar insyn i loggdata som skickas till Log Analytics.

## <a name="next-steps"></a>Nästa steg

* [Läs mer om Azure-diagnostikloggar](monitoring-overview-of-diagnostic-logs.md)
