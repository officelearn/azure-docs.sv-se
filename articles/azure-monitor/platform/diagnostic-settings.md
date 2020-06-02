---
title: Använd diagnostikinställningar för att samla in plattforms mått och loggar och i Azure
description: Skicka Azure Monitor plattforms mått och loggar till Azure Monitor loggar, Azure Storage eller Azure Event Hubs med hjälp av en diagnostisk inställning.
author: bwren
ms.author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 04/27/2020
ms.subservice: logs
ms.openlocfilehash: 46dd7949dde1890035053a7a985f2f1d921e141e
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266669"
---
# <a name="create-diagnostic-setting-to-collect-resource-logs-and-metrics-in-azure"></a>Skapa en diagnostisk inställning för att samla in resurs loggar och mått i Azure

[Plattforms loggar](platform-logs-overview.md) i Azure, inklusive Azure aktivitets logg och resurs loggar, ger detaljerad diagnostik och gransknings information för Azure-resurser och Azure-plattformen som de är beroende av. [Plattforms mått](data-platform-metrics.md) samlas in som standard och lagras vanligt vis i Azure Monitor Metrics-databasen.

Den här artikeln innehåller information om hur du skapar och konfigurerar diagnostikinställningar för att skicka plattforms mått och plattforms loggar till olika mål.

> [!IMPORTANT]
> Innan du skapar en diagnostisk inställning för att samla in aktivitets loggen bör du först inaktivera all äldre konfiguration. Mer information finns i [samla in Azure aktivitets logg med äldre inställningar](diagnostic-settings-legacy.md) .

Varje Azure-resurs kräver en egen diagnostisk inställning som definierar följande kriterier:

- Kategorier av loggar och måttdata som skickas till de mål som definierats i inställningen. De tillgängliga kategorierna kan variera för olika resurstyper.
- En eller flera destinationer att skicka loggarna till. Aktuella destinationer är Log Analytics-arbetsyta, Event Hubs och Azure Storage.

En enda diagnostisk inställning kan definiera högst en av varje mål. Om du vill skicka data till fler än en av en viss typ av mål (till exempel två olika Log Analytics-arbetsytor) skapar du flera inställningar. Varje resurs kan ha upp till fem diagnostiska inställningar.

> [!NOTE]
> [Plattforms mått](metrics-supported.md) samlas in automatiskt för att [Azure Monitor mått](data-platform-metrics.md). Diagnostiska inställningar kan användas för att samla in mått för vissa Azure-tjänster i Azure Monitor loggar för analys med andra övervaknings data med hjälp av [logg frågor](../log-query/log-query-overview.md) med vissa begränsningar. 
>  
>  
> Det går för närvarande inte att skicka flerdimensionella mätvärden via diagnostikinställningar. Mått med dimensioner exporteras som tillplattade endimensionella mått som aggregeras över dimensionsvärden. *Exempel*: måttet ' IOReadBytes ' på en blockchain kan utforskas och ritas på en nivå per nod. Men när det exporteras via diagnostiska inställningar representerar det exporterade måttet som alla lästa byte för alla noder. Till följd av interna begränsningar kan inte alla mått exporteras till Azure Monitor loggar/Log Analytics. Mer information finns i [listan över exporterade mått](metrics-supported-export-diagnostic-settings.md). 
>  
>  
> För att komma runt de här begränsningarna för vissa mått rekommenderar vi att du extraherar dem manuellt med hjälp av [måtten REST API](https://docs.microsoft.com/rest/api/monitor/metrics/list) och importerar dem till Azure Monitor loggar med hjälp av [Azure Monitor data insamlings-API](data-collector-api.md).  

## <a name="destinations"></a>Mål

Plattforms loggar och-mått kan skickas till målen i följande tabell. Följ varje länk i följande tabell om du vill ha mer information om hur du skickar data till det målet.

| Mål | Beskrivning |
|:---|:---|
| [Log Analytics-arbetsyta](resource-logs-collect-workspace.md) | Genom att samla in loggar och mått i en Log Analytics arbets yta kan du analysera dem med andra övervaknings data som samlas in av Azure Monitor använda kraftfulla logg frågor och även använda andra Azure Monitor funktioner, till exempel aviseringar och visualiseringar. |
| [Event Hub](resource-logs-stream-event-hubs.md) | Genom att skicka loggar och mått till Event Hubs kan du strömma data till externa system, till exempel Siem för tredje part och andra Log Analytics-lösningar. |
| [Azure Storage-konto](resource-logs-collect-storage.md) | Arkivering av loggar och mått till ett Azure Storage-konto är användbart för granskning, statisk analys eller säkerhets kopiering. Jämfört med Azure Monitor loggar och en Log Analytics arbets yta är Azure Storage billigare och loggar kan sparas där på obestämd tid. |

## <a name="create-diagnostic-settings-in-azure-portal"></a>Skapa diagnostikinställningar i Azure-portalen

Du kan konfigurera diagnostikinställningar i Azure Portal antingen från Azure Monitor-menyn eller från menyn för resursen.

1. Var du konfigurerar diagnostikinställningar i Azure Portal är beroende av resursen.

   - För en enskild resurs klickar du på **diagnostikinställningar** under **övervaka** på resurs menyn.

        ![Diagnostikinställningar](media/diagnostic-settings/menu-resource.png)

   - För en eller flera resurser klickar du på **diagnostikinställningar** under **inställningar** på menyn Azure Monitor och klickar sedan på resursen.

      ![Diagnostikinställningar](media/diagnostic-settings/menu-monitor.png)

   - I aktivitets loggen klickar du på **aktivitets logg** på **Azure Monitor** -menyn och sedan på **diagnostikinställningar**. Se till att inaktivera all äldre konfiguration för aktivitets loggen. Se [inaktivera befintliga inställningar](/azure/azure-monitor/platform/activity-log-collect#collecting-activity-log) för mer information.

        ![Diagnostikinställningar](media/diagnostic-settings/menu-activity-log.png)

2. Om det inte finns några inställningar på den resurs du har valt uppmanas du att skapa en inställning. Klicka på **Lägg till diagnostisk inställning**.

   ![Lägg till diagnostisk inställning-inga befintliga inställningar](media/diagnostic-settings/add-setting.png)

   Om det finns befintliga inställningar på resursen visas en lista över inställningar som redan har kon figurer ATS. Klicka antingen på **Lägg till diagnostisk inställning** för att lägga till en ny inställning eller **Redigera inställning** för att redigera en befintlig. Varje inställning får inte ha fler än en av varje mål typ.

   ![Lägg till diagnostisk inställning – befintliga inställningar](media/diagnostic-settings/edit-setting.png)

3. Ange ett namn för inställningen om det inte redan har en.

    ![Lägg till diagnostisk inställning](media/diagnostic-settings/setting-new-blank.png)

4. **Kategori information (vad vill du dirigera)** – Markera kryss rutan för varje kategori av data som du vill skicka till destinationer som du har angett senare. Listan över kategorier varierar för varje Azure-tjänst.

     - **AllMetrics** dirigerar en resurss plattforms mått till Azure logs Store, men i logg formuläret. De här måtten skickas vanligt vis endast till Azure Monitor Metrics Time-Series-databas. Skickar dem till Azure Monitor loggar Store (som är sökbart via Log Analytics) du integrerar dem i frågor som söker i andra loggar. Det här alternativet kanske inte är tillgängligt för alla resurs typer. När det stöds visas [Azure Monitor mått som stöds](metrics-supported.md) för vilka mått som samlas in för de olika resurs typerna.

       > [!NOTE]
       > Se limitatation för routning av mått till Azure Monitor loggar tidigare i den här artikeln.  


     - **Loggar** visar de olika kategorierna som är tillgängliga beroende på resurs typen. Kontrol lera de kategorier som du vill dirigera till ett mål.

5. **Destinations information** – Markera kryss rutan för varje mål. När du markerar varje ruta visas alternativ som gör att du kan lägga till ytterligare information.

      ![Skicka till Log Analytics eller Event Hubs](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics** -ange prenumerationen och arbets ytan.  Om du inte har en arbets yta måste du [skapa en innan du fortsätter](../learn/quick-create-workspace.md).

    1. **Event Hub** – ange följande kriterier:
       - Den prenumeration som händelsehubben är en del av
       - Namn området för Händelsehubben – om du inte har något måste du [skapa ett](../../event-hubs/event-hubs-create.md)
       - Ett Event Hub-namn (valfritt) för att skicka alla data till. Om du inte anger ett namn skapas en Event Hub för varje logg kategori. Om du skickar flera kategorier kanske du vill ange ett namn för att begränsa antalet Event Hub som skapats. Mer information finns i [Azure Event Hubs kvoter och begränsningar](../../event-hubs/event-hubs-quotas.md) .
       - En princip för Event Hub (valfritt) en princip definierar de behörigheter som den strömmande mekanismen har. Mer information finns i [Event-Hub-features](../../event-hubs/event-hubs-features.md#publisher-policy).

    1. **Lagring** – Välj prenumeration, lagrings konto och bevarande princip.

        ![Skicka till lagring](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > Överväg att ange bevarande principen till 0 och manuellt ta bort dina data från lagringen med hjälp av ett schemalagt jobb för att undvika eventuell förvirring i framtiden.
        >
        > Först, om du använder lagring för arkivering, vill du vanligt vis att dina data ska vara runt i mer än 365 dagar. För det andra, om du väljer en bevarande princip som är större än 0, kopplas förfallo datumet till loggarna vid lagrings tiden. Du kan inte ändra datum för dessa loggar när de har lagrats.
        >
        > Om du till exempel ställer in bevarande principen för *WorkflowRuntime* till 180 dagar och sedan 24 timmar senare anger den till 365 dagar, tas loggarna som lagras under de första 24 timmarna automatiskt bort efter 180 dagar, medan alla efterföljande loggar av den typen tas bort automatiskt efter 365 dagar. Om du ändrar bevarande principen senare blir inte de första 24 timmarna med loggar kvar runt i 365 dagar.

6. Klicka på **Spara**.

Efter en liten stund visas den nya inställningen i listan med inställningar för den här resursen och loggarna strömmas till de angivna målen när nya händelse data genereras. Det kan ta upp till 15 minuter innan en händelse genereras och när den [visas i en Log Analytics-arbetsyta](data-ingestion-time.md).

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

## <a name="configure-diagnostic-settings-using-rest-api"></a>Konfigurera diagnostikinställningar med REST API

Se [diagnostikinställningar](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) för att skapa eller uppdatera diagnostikinställningar med hjälp av [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/).

## <a name="configure-diagnostic-settings-using-resource-manager-template"></a>Konfigurera diagnostikinställningar med Resource Manager-mall

Se [skapa diagnostisk inställning i Azure monitor att använda en Resource Manager-mall](diagnostic-settings-template.md) för att skapa eller uppdatera diagnostikinställningar med en Resource Manager-mall.

## <a name="next-steps"></a>Nästa steg

- [Läs mer om Azures plattforms loggar](platform-logs-overview.md)
