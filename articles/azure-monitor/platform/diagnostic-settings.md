---
title: Använd diagnostikinställningar för att samla in plattformsmått och loggar och i Azure
description: Skicka Azure Monitor-plattformsmått och loggar till Azure Monitor-loggar, Azure-lagring eller Azure Event Hubs med hjälp av en diagnostikinställning.
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 04/15/2020
ms.subservice: logs
ms.openlocfilehash: edb34b1456efae4d06465cfa2e64e546f621c6da
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681226"
---
# <a name="create-diagnostic-setting-to-collect-resource-logs-and-metrics-in-azure"></a>Skapa diagnostikinställning för att samla in resursloggar och mått i Azure

[Plattformsloggar](platform-logs-overview.md) i Azure, inklusive Azure Activity-loggen och resursloggarna, tillhandahåller detaljerad diagnostik- och granskningsinformation för Azure-resurser och den Azure-plattform de är beroende av. [Plattformsmått](data-platform-metrics.md) samlas in som standard och lagras vanligtvis i Azure Monitor-måttdatabasen.

Den här artikeln innehåller information om hur du skapar och konfigurerar diagnostikinställningar för att skicka plattformsmått och plattformsloggar till olika destinationer.

> [!IMPORTANT]
> Innan du skapar en diagnostikinställning för att samla in aktivitetsloggen bör du först inaktivera alla äldre konfigurationer. Mer information [finns i Logga in Azure-aktivitet med äldre inställningar.](diagnostic-settings-legacy.md)

Varje Azure-resurs kräver en egen diagnostikinställning, som definierar följande villkor:

- Kategorier av loggar och måttdata som skickas till de mål som definierats i inställningen. De tillgängliga kategorierna kan variera för olika resurstyper.
- En eller flera destinationer att skicka loggarna till. Aktuella destinationer är Log Analytics-arbetsyta, Event Hubs och Azure Storage.

En enda diagnostikinställning kan definiera högst ett av varje mål. Om du vill skicka data till fler än en av en viss typ av mål (till exempel två olika Log Analytics-arbetsytor) skapar du flera inställningar. Varje resurs kan ha upp till fem diagnostiska inställningar.

> [!NOTE]
> [Plattformsmått](metrics-supported.md) samlas in automatiskt till [Azure Monitor Metrics](data-platform-metrics.md). Diagnostikinställningar kan användas för att samla in mått för vissa Azure-tjänster i Azure Monitor Logs för analys med andra övervakningsdata med hjälp av [loggfrågor](../log-query/log-query-overview.md).

## <a name="destinations"></a>Mål

Plattformsloggar och mått kan skickas till destinationerna i följande tabell. Följ varje länk i följande tabell för mer information om hur du skickar data till den destinationen.

| Mål | Beskrivning |
|:---|:---|
| [Log Analytics-arbetsyta](resource-logs-collect-workspace.md) | Genom att samla in loggar och mått i en Log Analytics-arbetsyta kan du analysera dem med andra övervakningsdata som samlas in av Azure Monitor med hjälp av kraftfulla loggfrågor och även utnyttja andra Azure Monitor-funktioner som aviseringar och visualiseringar. |
| [Händelsehubbar](resource-logs-stream-event-hubs.md) | Genom att skicka loggar och mått till eventhubbar kan du strömma data till externa system som SIEM-tillverkare från tredje part och andra logganalyslösningar. |
| [Azure-lagringskonto](resource-logs-collect-storage.md) | Arkivering av loggar och mått till ett Azure-lagringskonto är användbart för granskning, statisk analys eller säkerhetskopiering. Jämfört med Azure Monitor Logs och en Log Analytics-arbetsyta är Azure-lagring billigare och loggar kan sparas där på obestämd tid. |

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

    ![Lägg till diagnostikinställning](media/diagnostic-settings/setting-new-blank.png)

4. **Kategoriinformation (vad du ska cirkulera)** - Markera rutan för varje datakategori som du vill skicka till destinationer som anges senare. Listan över kategorier varierar för varje Azure-tjänst.

     - **AllMetrics** dirigerar en resurs plattformsmått till Azure Logs-arkivet, men i loggform. Dessa mått skickas vanligtvis endast till Azure Monitor-måttens tidsseriedatabas. Skicka dem till Azure Monitor Logs Store (som är sökbar via Log Analytics) kan du integrera dem i frågor som söker över andra loggar. Det här alternativet kanske inte är tillgängligt för alla resurstyper. När det stöds visar [Azure Monitor mått som stöds](metrics-supported.md) vilka mått som samlas in för vilka resurstyper.

       > [!NOTE]
       > Det går för närvarande inte att skicka flerdimensionella mätvärden via diagnostikinställningar. Mått med dimensioner exporteras som tillplattade endimensionella mått som aggregeras över dimensionsvärden.
       >
       > *Till exempel:* IOReadBytes-måttet på en blockchain kan utforskas och kartläggas på en per nodnivå. Men när det exporteras via diagnostikinställningar representerar det exporterade måttet som alla läsbyten för alla noder.

     - **Loggar visar** de olika kategorier som är tillgängliga beroende på resurstypen. Kontrollera alla kategorier som du vill dirigera till en destination.

5. **Målinformation** - Markera rutan för varje destination. När du markerar varje ruta visas alternativ så att du kan lägga till ytterligare information.

      ![Skicka till logganalys- eller händelsehubbar](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics** - Ange prenumeration och arbetsyta.  Om du inte har en arbetsyta måste du [skapa en innan du fortsätter](../learn/quick-create-workspace.md).

    1. **Händelsehubbar** - Ange följande villkor:
       - Prenumerationen som händelsehubben ingår i
       - Namnområdet För händelsehubben - Om du ännu inte har någon måste du [skapa ett](../../event-hubs/event-hubs-create.md)
       - Ett händelsenavnamn (valfritt) som alla data ska skickas till. Om du inte anger ett namn skapas en händelsehubb för varje loggkategori. Om du skickar flera kategorier kanske du vill ange ett namn för att begränsa antalet händelsehubbar som skapats. Mer information finns i [Azure Event Hubs-kvoter och begränsningar.](../../event-hubs/event-hubs-quotas.md)
       - En händelsehubbprincip (valfritt) En princip definierar de behörigheter som direktuppspelningsmekanismen har. Mer information finns i [Event-hubs-funktioner](../../event-hubs/event-hubs-features.md#publisher-policy).

    1. **Lagring** – Välj prenumerations-, lagringskonto- och bevarandeprincip.

        ![Skicka till lagring](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > Överväg att ställa in lagringsprincipen till 0 och manuellt ta bort data från lagring med ett schemalagt jobb för att undvika eventuell förvirring i framtiden.
        >
        > Om du använder lagring för arkivering vill du i allmänhet ha dina data i mer än 365 dagar. För det andra, om du väljer en bevarandeprincip som är större än 0, är utgångsdatumet kopplat till loggarna vid tidpunkten för lagringen. Du kan inte ändra datumet för loggarna när de har lagrats.
        >
        > Om du till exempel anger bevarandeprincipen för *WorkflowRuntime* till 180 dagar och sedan 24 timmar senare ställer in den på 365 dagar, tas loggarna som lagrats under de första 24 timmarna automatiskt bort efter 180 dagar, medan alla efterföljande loggar av den typen tas bort automatiskt efter 365 dagar. Om du ändrar bevarandeprincipen senare blir inte de första 24 timmarna av loggarna kvar i 365 dagar.

6. Klicka på **Spara**.

Efter en stund visas den nya inställningen i listan med inställningar för den här resursen och loggar strömmas till de angivna destinationerna när nya händelsedata genereras. Det kan ta upp till 15 minuter mellan när en händelse avges och när den [visas på en Log Analytics-arbetsyta](data-ingestion-time.md).

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

- [Läs mer om Azure-plattformsloggar](platform-logs-overview.md)
