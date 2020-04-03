---
title: Skapa diagnostikinställning för att samla in loggar och mått i Azure
description: Skapa diagnostikinställningar för att vidarebefordra Azure-plattformsloggar till Azure Monitor Logs, Azure Storage eller Azure Event Hubs.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 6ff9e62ed145b7892d2997193dae8e171ae49c98
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585388"
---
# <a name="create-diagnostic-setting-to-collect-platform-logs-and-metrics-in-azure"></a>Skapa diagnostikinställning för att samla in plattformsloggar och mått i Azure
[Plattformsloggar](platform-logs-overview.md) i Azure, inklusive Azure Activity-logg och resursloggar, tillhandahåller detaljerad diagnostik- och granskningsinformation för Azure-resurser och den Azure-plattform de är beroende av. Den här artikeln innehåller information om hur du skapar och konfigurerar diagnostikinställningar för att skicka plattformsloggar till olika destinationer.

> [!IMPORTANT]
> Innan du skapar en diagnostikinställning för att samla in aktivitetsloggen bör du först inaktivera alla äldre konfigurationer. Mer information [finns i Logga in Azure-aktivitet med äldre inställningar.](diagnostic-settings-legacy.md)

Varje Azure-resurs kräver en egen diagnostikinställning, som definierar följande:

- Kategorier av loggar och måttdata som skickas till de mål som definierats i inställningen. De tillgängliga kategorierna kan variera för olika resurstyper.
- En eller flera destinationer att skicka loggarna till. Aktuella destinationer är Log Analytics-arbetsyta, Event Hubs och Azure Storage.
 
En enda diagnostikinställning kan definiera högst ett av varje mål. Om du vill skicka data till fler än en av en viss typ av mål (till exempel två olika Log Analytics-arbetsytor) skapar du flera inställningar. Varje resurs kan ha upp till fem diagnostiska inställningar.


> [!NOTE]
> [Plattformsmått](metrics-supported.md) samlas in automatiskt till [Azure Monitor Metrics](data-platform-metrics.md). Diagnostikinställningar kan användas för att samla in mått för vissa Azure-tjänster i Azure Monitor Logs för analys med andra övervakningsdata med hjälp av [loggfrågor](../log-query/log-query-overview.md).

## <a name="destinations"></a>Mål 
Plattformsloggar kan skickas till destinationerna i följande tabell. Konfigurationen för varje mål utförs med samma process för att skapa diagnostikinställningar som beskrivs i den här artikeln. Följ varje länk i följande tabell för mer information om hur du skickar data till den destinationen.

| Mål | Beskrivning |
|:---|:---|
| [Log Analytics-arbetsyta](resource-logs-collect-workspace.md) | Genom att samla in loggar till en Log Analytics-arbetsyta kan du analysera dem med andra övervakningsdata som samlas in av Azure Monitor med hjälp av kraftfulla loggfrågor och även utnyttja andra Azure Monitor-funktioner som aviseringar och visualiseringar. |
| [Händelsehubbar](resource-logs-stream-event-hubs.md) | Genom att skicka loggar till eventhubbar kan du strömma data till externa system som SIEM-tillverkare från tredje part och andra logganalyslösningar. |
| [Azure-lagringskonto](resource-logs-collect-storage.md) | Arkivering av loggar till ett Azure-lagringskonto är användbart för granskning, statisk analys eller säkerhetskopiering. |

## <a name="create-diagnostic-settings-in-azure-portal"></a>Skapa diagnostikinställningar i Azure Portal
Du kan konfigurera diagnostikinställningar i Azure-portalen antingen från Azure Monitor-menyn eller från menyn för resursen.

1. Var du konfigurerar diagnostikinställningar i Azure-portalen beror på resursen.

   - För en enskild resurs klickar du på **Diagnostikinställningar** under **Övervaka** på resursens meny.

        ![Diagnostikinställningar](media/diagnostic-settings/menu-resource.png)

    - För en eller flera resurser klickar du på **Diagnostikinställningar** under **Inställningar** på Azure Monitor-menyn och klickar sedan på resursen.
    
        ![Diagnostikinställningar](media/diagnostic-settings/menu-monitor.png)

    - För aktivitetsloggen klickar du på **Aktivitetslogg** på **Azure Monitor-menyn** och sedan **diagnostikinställningar**. Se till att du inaktiverar alla äldre konfigurationer för aktivitetsloggen. Mer information finns i [Inaktivera befintliga inställningar.](/azure/azure-monitor/platform/activity-log-collect#collecting-activity-log)

        ![Diagnostikinställningar](media/diagnostic-settings/menu-activity-log.png)

2. Om det inte finns några inställningar för den resurs som du har valt uppmanas du att skapa en inställning. Klicka på **Lägg till diagnostikinställning**.

   ![Lägg till diagnostikinställning - inga befintliga inställningar](media/diagnostic-settings/add-setting.png)

   Om det finns befintliga inställningar på resursen visas en lista över inställningar som redan har konfigurerats. Klicka antingen på **Lägg till diagnostikinställning** om du vill lägga till en ny inställning eller **Redigera inställning** om du vill redigera en befintlig. Varje inställning får inte ha mer än en av måltyperna.

   ![Lägg till diagnostikinställning - befintliga inställningar](media/diagnostic-settings/edit-setting.png)

3. Ge din inställning ett namn om den inte redan har ett.
4. Markera rutan för varje mål för att skicka loggarna. Klicka på **Konfigurera** om du vill ange deras inställningar enligt beskrivningen i följande tabell.

    | Inställning | Beskrivning |
    |:---|:---|
    | Log Analytics-arbetsyta | Namn på arbetsyta. |
    | Lagringskonto | Namn på lagringskonto. |
    | Namnområde för händelsehubb | Namnområdet där händelsehubben skapas (om det här är första gången du direktuppspelar loggar) eller strömmas till (om det redan finns resurser som strömmar loggkategorin till det här namnområdet).
    | Namn på händelsehubb | Du kan också ange ett händelsenavnamn om du vill skicka alla data i inställningen. Om du inte anger ett namn skapas en händelsehubb för varje loggkategori. Om du skickar flera kategorier kanske du vill ange ett namn för att begränsa antalet händelsehubbar som skapats. Mer information finns i [Azure Event Hubs-kvoter och begränsningar.](../../event-hubs/event-hubs-quotas.md) |
    | Principnamn för händelsenav | Definierar de behörigheter som direktuppspelningsmekanismen har. |

    ![Lägg till diagnostikinställning - befintliga inställningar](media/diagnostic-settings/setting-details.png)

5. Markera rutan för var och en av de datakategorier som ska skickas till de angivna destinationerna. Listan över kategorier varierar för varje Azure-tjänst.

   > [!NOTE]
   > Det går för närvarande inte att skicka flerdimensionella mätvärden via diagnostikinställningar. Mått med dimensioner exporteras som tillplattade endimensionella mått som aggregeras över dimensionsvärden.
   >
   > *Till exempel*: Måttet för inkommande meddelanden i en händelsehubb kan utforskas och läggas till på per-kö-nivå. När måttet exporteras via diagnostikinställningar visas det dock som alla inkommande meddelanden i alla köer i händelsehubben.

6. Klicka på **Spara**.

Efter en stund visas den nya inställningen i listan med inställningar för den här resursen och loggar strömmas till de angivna destinationerna när nya händelsedata genereras. Observera att det kan vara upp till femton minuter mellan när en händelse avges och när den [visas på en Log Analytics-arbetsyta](data-ingestion-time.md).



## <a name="create-diagnostic-settings-using-powershell"></a>Skapa diagnostikinställningar med PowerShell
Använd [cmdlet set-azdiagnosticsetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) för att skapa en diagnostikinställning med [Azure PowerShell](powershell-quickstart-samples.md). Se dokumentationen för den här cmdleten för beskrivningar av dess parametrar.

> [!IMPORTANT]
> Du kan inte använda den här metoden för Azure Activity-loggen. Använd i stället [skapa diagnostikinställning i Azure Monitor med hjälp av en Resource Manager-mall](diagnostic-settings-template.md) för att skapa en Resource Manager-mall och distribuera den med PowerShell.

Följande är ett exempel PowerShell cmdlet för att skapa en diagnostikinställning med hjälp av alla tre destinationer.


```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```


## <a name="create-diagnostic-settings-using-azure-cli"></a>Skapa diagnostikinställningar med Azure CLI
Använd kommandot [az monitor diagnostic-settings create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) för att skapa en diagnostikinställning med Azure [CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). Se dokumentationen för det här kommandot för beskrivningar av dess parametrar.

> [!IMPORTANT]
> Du kan inte använda den här metoden för Azure Activity-loggen. Använd i stället [skapa diagnostikinställning i Azure Monitor med hjälp av en Resource Manager-mall](diagnostic-settings-template.md) för att skapa en Resource Manager-mall och distribuera den med CLI.

Följande är ett exempel CLI kommando för att skapa en diagnostisk inställning med hjälp av alla tre destinationer.



```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

### <a name="configure-diagnostic-settings-using-rest-api"></a>Konfigurera diagnostikinställningar med REST API
Se [Diagnostikinställningar](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) för att skapa eller uppdatera diagnostikinställningar med hjälp av [AZURE Monitor REST API](https://docs.microsoft.com/rest/api/monitor/).


### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>Konfigurera diagnostikinställningar med hjälp av Resource Manager-mall
Se [Skapa diagnostikinställning i Azure Monitor med hjälp av en Resource Manager-mall](diagnostic-settings-template.md) för att skapa eller uppdatera diagnostikinställningar med en Resource Manager-mall.

## <a name="next-steps"></a>Nästa steg

* [Läs mer om Azure-plattformsloggar](platform-logs-overview.md)
