---
title: Skapa en diagnostisk inställning för insamling av loggar och mått i Azure
description: Skapa diagnostikinställningar för att vidarebefordra Azure Platform-loggar till Azure Monitor loggar, Azure Storage eller Azure Event Hubs.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 22932121b97c1b0fe91c46b5eea0222a022a4e61
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75751082"
---
# <a name="create-diagnostic-setting-to-collect-platform-logs-and-metrics-in-azure"></a>Skapa en diagnostisk inställning för att samla in plattforms loggar och mått i Azure
[Plattforms loggar](platform-logs-overview.md) i Azure, inklusive Azure aktivitets logg och resurs loggar, ger detaljerad diagnostik och gransknings information för Azure-resurser och Azure-plattformen som de är beroende av. Den här artikeln innehåller information om hur du skapar och konfigurerar diagnostikinställningar för att skicka plattforms loggar till olika mål.

> [!IMPORTANT]
> Innan du skapar en diagnostisk inställning för att samla in aktivitets loggen bör du först inaktivera all äldre konfiguration. Mer information finns i [samla in Azure aktivitets logg med äldre inställningar](diagnostic-settings-legacy.md) .

Varje Azure-resurs kräver en egen diagnostisk inställning som definierar följande:

- Kategorier av loggar och måttdata som skickas till de mål som definierats i inställningen. De tillgängliga kategorierna kan variera för olika resurstyper.
- En eller flera destinationer att skicka loggarna till. Aktuella destinationer är Log Analytics-arbetsyta, Event Hubs och Azure Storage.
 
En enda diagnostisk inställning kan definiera högst en av varje mål. Om du vill skicka data till fler än en av en viss typ av mål (till exempel två olika Log Analytics-arbetsytor) skapar du flera inställningar. Varje resurs kan ha upp till fem diagnostiska inställningar.


> [!NOTE]
> [Plattforms mått](metrics-supported.md) samlas in automatiskt för att [Azure Monitor mått](data-platform-metrics.md). Diagnostiska inställningar kan användas för att samla in mått för vissa Azure-tjänster i Azure Monitor loggar för analys med andra övervaknings data med hjälp av [logg frågor](../log-query/log-query-overview.md).

## <a name="destinations"></a>Mål 
Plattforms loggar kan skickas till målen i följande tabell. Konfigurationen för varje mål utförs med samma process för att skapa diagnostiska inställningar som beskrivs i den här artikeln. Följ varje länk i följande tabell om du vill ha mer information om hur du skickar data till det målet.

| Mål | Beskrivning |
|:---|:---|
| [Log Analytics-arbetsyta](resource-logs-collect-workspace.md) | Genom att samla in loggar i en Log Analytics arbets yta kan du analysera dem med andra övervaknings data som samlas in av Azure Monitor med hjälp av kraftfulla logg frågor och även använda andra Azure Monitor funktioner, till exempel aviseringar och visualiseringar. |
| [Event Hub](resource-logs-stream-event-hubs.md) | Genom att skicka loggar till Event Hubs kan du strömma data till externa system som Siem i tredje part och andra Log Analytics-lösningar. |
| [Azure Storage-konto](resource-logs-collect-storage.md) | Arkivering av loggar till ett Azure Storage-konto är användbart för granskning, statisk analys eller säkerhets kopiering. |

## <a name="create-diagnostic-settings-in-azure-portal"></a>Skapa diagnostikinställningar i Azure Portal
Du kan konfigurera diagnostikinställningar i Azure Portal antingen från Azure Monitor-menyn eller från menyn för resursen.

1. Var du konfigurerar diagnostikinställningar i Azure Portal är beroende av resursen.

   - För en enskild resurs klickar du på **diagnostikinställningar** under **övervaka** på resurs menyn.

        ![Diagnostikinställningar](media/diagnostic-settings/menu-resource.png)

    - För en eller flera resurser klickar du på **diagnostikinställningar** under **inställningar** på menyn Azure Monitor och klickar sedan på resursen.
    
        ![Diagnostikinställningar](media/diagnostic-settings/menu-monitor.png)

    - I aktivitets loggen klickar du på **aktivitets logg** på **Azure Monitor** -menyn och sedan på **diagnostikinställningar**. Se till att inaktivera all äldre konfiguration för aktivitets loggen. Se [inaktivera befintliga inställningar](diagnostic-settings-legacy.md#disable-existing-settings) för mer information.

        ![Diagnostikinställningar](media/diagnostic-settings/menu-activity-log.png)

2. Om inga inställningar finns på resursen har du valt, uppmanas du för att skapa en inställning. Klicka på **Lägg till diagnostisk inställning**.

   ![Lägg till diagnostikinställning - inga befintliga inställningar](media/diagnostic-settings/add-setting.png)

   Om det finns befintliga inställningar på resursen visas en lista över inställningar som redan har kon figurer ATS. Klicka antingen på **Lägg till diagnostisk inställning** för att lägga till en ny inställning eller **Redigera inställning** för att redigera en befintlig. Varje inställning får inte ha fler än en av varje mål typ.

   ![Lägg till diagnostikinställning - befintliga inställningar](media/diagnostic-settings/edit-setting.png)

3. Ange ett namn för inställningen om det inte redan har en.
4. Markera kryss rutan för varje mål för att skicka loggarna. Klicka på **Konfigurera** för att ange inställningarna enligt beskrivningen i följande tabell.

    | Inställning | Beskrivning |
    |:---|:---|
    | Log Analytics-arbetsyta | Namn på arbets yta. |
    | Lagringskonto | Namn på lagrings konto. |
    | Namnområde för händelsehubb | Namn området där Event Hub skapas (om det här är din första gången strömnings loggar) eller strömmas till (om det redan finns resurser som är strömmande till den här namn rymden).
    | Namn på händelsehubb | Du kan också ange ett namn på händelsehubben för att skicka alla data i inställningen. Om du inte anger ett namn skapas en Event Hub för varje logg kategori. Om du skickar flera kategorier kanske du vill ange ett namn för att begränsa antalet Event Hub som skapats. Mer information finns i [Azure Event Hubs kvoter och begränsningar](../../event-hubs/event-hubs-quotas.md) . |
    | Principnamn för Event hub | Definierar de behörigheter som den strömmande mekanismen har. |

    ![Lägg till diagnostikinställning - befintliga inställningar](media/diagnostic-settings/setting-details.png)

5. Markera kryss rutan för var och en av de data kategorier som ska skickas till de angivna målen. Listan över kategorier varierar för varje Azure-tjänst.

   > [!NOTE]
   > Det går för närvarande inte att skicka flerdimensionella mätvärden via diagnostikinställningar. Mått med dimensioner exporteras som tillplattade endimensionella mått som aggregeras över dimensionsvärden.
   >
   > *Till exempel*: Måttet för inkommande meddelanden i en händelsehubb kan utforskas och läggas till på per-kö-nivå. När måttet exporteras via diagnostikinställningar visas det dock som alla inkommande meddelanden i alla köer i händelsehubben.

6. Klicka på **Spara**.

Efter en liten stund visas den nya inställningen i listan med inställningar för den här resursen och loggarna strömmas till de angivna målen när nya händelse data genereras. Observera att det kan finnas upp till femton minuter mellan när en händelse genereras och när den [visas i en Log Analytics-arbetsyta](data-ingestion-time.md).



## <a name="create-diagnostic-settings-using-powershell"></a>Skapa diagnostikinställningar med PowerShell
Använd cmdleten [set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) för att skapa en diagnostisk inställning med [Azure PowerShell](powershell-quickstart-samples.md). I dokumentationen för den här cmdleten finns beskrivningar av parametrarna.

> [!IMPORTANT]
> Du kan inte använda den här metoden för Azures aktivitets logg. Använd i stället [skapa diagnostisk inställning i Azure monitor att använda en Resource Manager-mall](diagnostic-settings-template.md) för att skapa en Resource Manager-mall och distribuera den med PowerShell.

Följande är ett exempel på en PowerShell-cmdlet för att skapa en diagnostisk inställning med alla tre mål.


```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```


## <a name="create-diagnostic-settings-using-azure-cli"></a>Skapa diagnostikinställningar med Azure CLI
Använd kommandot [AZ Monitor Diagnostic-Settings Create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) för att skapa en diagnostisk inställning med [Azure CLI](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). I dokumentationen för det här kommandot finns beskrivningar av parametrarna.

> [!IMPORTANT]
> Du kan inte använda den här metoden för Azures aktivitets logg. Använd i stället [skapa diagnostisk inställning i Azure monitor att använda en Resource Manager-mall](diagnostic-settings-template.md) för att skapa en Resource Manager-mall och distribuera den med cli.

Följande är ett exempel på CLI-kommando för att skapa en diagnostisk inställning med alla tre mål.



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
Se [diagnostikinställningar](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) för att skapa eller uppdatera diagnostikinställningar med hjälp av [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/).


### <a name="configure-diagnostic-settings-using-resource-manager-template"></a>Konfigurera diagnostikinställningar med Resource Manager-mall
Se [skapa diagnostisk inställning i Azure monitor att använda en Resource Manager-mall](diagnostic-settings-template.md) för att skapa eller uppdatera diagnostikinställningar med en Resource Manager-mall.

## <a name="next-steps"></a>Nästa steg

* [Läs mer om Azures plattforms loggar](platform-logs-overview.md)
