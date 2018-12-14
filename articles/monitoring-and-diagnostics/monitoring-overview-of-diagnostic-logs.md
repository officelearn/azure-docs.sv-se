---
title: Översikt över Azure diagnostikloggar
description: Lär dig vad Azure diagnostikloggar är och hur du kan använda dem för att förstå händelser som inträffar inom en Azure-resurs.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: johnkem
ms.component: logs
ms.openlocfilehash: 89e9b7190a3c419c256513e477f85313e4f9f3bd
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384895"
---
# <a name="collect-and-consume-log-data-from-your-azure-resources"></a>Samla in och använda loggdata från resurserna i Azure

## <a name="what-are-azure-monitor-diagnostic-logs"></a>Vad är Azure Monitor diagnostikloggar

**Azure Monitor-diagnostikloggar** är loggar som genereras av en Azure-tjänst som tillhandahåller omfattande, frekventa data om driften av tjänsten. Azure Monitor tillhandahåller finns två typer av diagnostikloggar:
* **Klient loggar** -loggarna som kommer från på klientnivå tjänster som finns utanför en Azure-prenumeration som Azure Active Directory-loggar.
* **Resursen loggar** -loggarna som kommer från Azure-tjänster som distribuera resurser inom en Azure-prenumeration, till exempel Nätverkssäkerhetsgrupper eller Storage-konton.

    ![Resurs-diagnostics-loggar jämfört med andra typer av loggar ](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_vs_other_logs_v5.png)

Innehållet i de här loggarna varierar beroende på vilken Azure-tjänsten och resursen. Till exempel finns Nätverkssäkerhetsgrupp kopplad till regeln räknare och Key Vault granskningar två typer av diagnostikloggar.

Dessa loggar skiljer sig från den [aktivitetsloggen](monitoring-overview-activity-logs.md). Aktivitetsloggen ger insikt i de åtgärder som utförts på resurser i din prenumeration med hjälp av Resource Manager, till exempel skapar en virtuell dator eller ta bort en logikapp. Aktivitetsloggen är en prenumerationsnivå logg. Resursnivå diagnostikloggar ger information om åtgärder som utförts i den här resursen, till exempel hämta en hemlighet från Key Vault.

Dessa loggar är också skiljer sig från diagnostikloggar för gäst-OS-nivå. Gäst-OS-diagnostikloggar är dessa som samlats in av en agent som körs i en virtuell dator eller andra stöds resurstyp. Resursnivå diagnostikloggar kräver inga agenten och avbilda resurs-specifika data från Azure-plattformen, diagnostikloggar för gäst-OS-nivå samla in data från det operativsystem och program som körs på en virtuell dator.

Inte alla tjänster som stöder diagnostikloggar som beskrivs här. [Den här artikeln innehåller en lista för avsnittet vilka tjänster stöder diagnostikloggar](./../azure-monitor/platform/tutorial-dashboards.md).

## <a name="what-you-can-do-with-diagnostic-logs"></a>Vad du kan göra med diagnostikloggar
Här följer några av de saker som du kan göra med diagnostikloggar:

![Logiska placeringen av diagnostikloggar](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_Actions.png)

* Sparar dem till en [ **Lagringskonto** ](../azure-monitor/platform/archive-diagnostic-logs.md) för granskning eller manuell granskning. Du kan ange kvarhållning tid (i dagar) med **resursdiagnostikinställningar**.
* [Stream att **Händelsehubbar** ](monitoring-stream-diagnostic-logs-to-event-hubs.md) för inmatning av en tjänst från tredje part eller anpassade analyslösning till exempel Power BI.
* Analysera dem med [Log Analytics](../azure-monitor/platform/collect-azure-metrics-logs.md), där data skrivs direkt till Log Analytics och behöver inte först skriva data till lagring.  

Du kan använda ett lagringskonto eller Event Hubs-namnområde som inte är i samma prenumeration som det genererar loggar. Den användare som konfigurerar inställningen måste ha lämplig RBAC-åtkomst till båda prenumerationerna.

> [!NOTE]
>  Du kan för närvarande inte arkivera flödesloggar för nätverk till ett lagringskonto som ligger bakom ett skyddat virtuellt nätverk.

> [!WARNING]
> Formatet för loggdata i lagringskontot ändras till JSON Lines den 1 november 2018. [Den här artikeln beskriver effekten av den här ändringen samt hur du uppdaterar dina verktyg för att hantera det nya formatet.](./monitor-diagnostic-logs-append-blobs.md) 
>
> 

## <a name="diagnostic-settings"></a>Diagnostikinställningar

Resursdiagnostikloggar är konfigurerade med resursdiagnostikinställningar. Klient-diagnostikloggar är konfigurerade med en diagnostikinställning för klienten. **Diagnostikinställningar** för en kontroll av tjänsten:

* Diagnostikloggar och mått mottagna (Storage-konto, Händelsehubbar och/eller Log Analytics).
* Vilka loggkategorier skickas och om måttdata skickas också.
* Hur länge varje loggkategori ska behållas i ett lagringskonto
    - En kvarhållning av noll dagar innebär loggar hålls alltid. I annat fall kan värdet vara valfritt antal dagar mellan 1 och 2147483647.
    - Om principerna för kvarhållning har angetts men lagra loggar i ett Lagringskonto är inaktiverad (till exempel om det bara Event Hubs eller Log Analytics-alternativen är markerade), påverkar principerna för kvarhållning inte.
    - Principer för kvarhållning är tillämpad per dag, så i slutet av en dag (UTC) loggar från den dag som är nu utöver kvarhållning principen tas bort. Till exempel om du har en bevarandeprincip för en dag skulle i början av dagen idag loggar från dag innan igår tas bort. Ta bort börjar vid midnatt UTC-tid, men Observera att det kan ta upp till 24 timmar innan loggarna som ska tas bort från ditt lagringskonto.

Dessa inställningar konfigureras enkelt från diagnostikinställningar i portalen, med Azure PowerShell och CLI-kommandon eller med hjälp av den [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/).

> [!NOTE]
> Det går för närvarande inte att skicka flerdimensionella mätvärden via diagnostikinställningar. Mått med dimensioner exporteras som tillplattade endimensionella mått som aggregeras över dimensionsvärden.
>
> *Till exempel*: ”Inkommande meddelanden'-mått i en Händelsehubb kan utforskas och läggas till på en per kö-nivå. När måttet exporteras via diagnostikinställningar visas det dock som alla inkommande meddelanden i alla köer i händelsehubben.
>
>

## <a name="how-to-enable-collection-of-diagnostic-logs"></a>Hur du aktiverar insamling av diagnostikloggar

Insamling av diagnostikloggar kan aktiveras [som en del av en resurs i en Resource Manager-mall](./../azure-monitor/platform/diagnostic-logs-stream-template.md) eller när en resurs har skapats via den resurssida i portalen. Du kan också aktivera samling när som helst med Azure PowerShell eller CLI-kommandon eller med Azure Monitor REST-API.

> [!TIP]
> De här anvisningarna kan inte direkt till varje resurs. Finns i schemat länkarna längst ned på sidan för att förstå särskilda åtgärder som kan gälla för vissa typer av resurser.

### <a name="enable-collection-of-diagnostic-logs-in-the-portal"></a>Aktivera insamling av diagnostikloggar i portalen

Du kan aktivera insamling av resursdiagnostikloggar i Azure-portalen när en resurs har skapats genom att gå till en specifik resurs eller genom att gå till Azure Monitor. För att aktivera detta via Azure Monitor:

1. I den [Azure-portalen](http://portal.azure.com), gå till Azure Monitor och klicka på **diagnostikinställningar**

    ![Avsnittet av Azure Monitor övervakning](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-blade.png)

2. Du kan också filtrera listan efter resursgruppen eller resursen. Klicka sedan på den resurs som du vill ange en diagnostikinställning.

3. Om inga inställningar finns på resursen har du valt, uppmanas du för att skapa en inställning. Klicka på ”Slå på diagnostik”.

   ![Lägg till diagnostikinställning - inga befintliga inställningar](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-none.png)

   Om det finns befintliga inställningarna på resursen, visas en lista över inställningar som redan har konfigurerats på den här resursen. Klicka på ”Lägg till diagnostikinställning”.

   ![Lägg till diagnostikinställning - befintliga inställningar](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-multiple.png)

3. Ger du ange ett namn, markerar du kryssrutorna för varje mål som du vill skicka data till och konfigurera vilken resurs som används för varje mål. Du kan också ange ett antal dagar att behålla dessa loggar med hjälp av den **Kvarhållning (dagar)** skjutreglagen (gäller endast för kontot lagringsplats). En kvarhållning av noll dagar lagras loggarna på obestämd tid.

   ![Lägg till diagnostikinställning - befintliga inställningar](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-configure.png)

4. Klicka på **Spara**.

Efter en liten stund visas den nya inställningen i din lista över inställningar för den här resursen och diagnostikloggar skickas till de angivna mål när nya händelsedata genereras.

Diagnostikinställningar för klient kan endast konfigureras på portalbladet för klient-service - inställningarna visas inte i bladet Azure Monitor diagnostikinställningar. Till exempel Azure Active Directory med granskningsloggar konfigureras genom att klicka på den **Exportera inställningar** på bladet granskningsloggarna.

![Diagnostikinställningar för AAD](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-aad.png)

### <a name="enable-collection-of-resource-diagnostic-logs-via-powershell"></a>Aktivera insamling av resursdiagnostikloggar via PowerShell

Om du vill aktivera insamling av resursdiagnostikloggar via Azure PowerShell använder du följande kommandon:

Använd följande kommando om du vill aktivera lagring av diagnostiska loggar i ett lagringskonto:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

Storage-konto-ID är resurs-ID för storage-konto som du vill skicka loggarna.

Om du vill aktivera strömning av diagnostiska loggar till en händelsehubb, Använd följande kommando:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your Service Bus rule id] -Enabled $true
```

Regel-ID för service bus är en sträng med det här formatet: `{Service Bus resource ID}/authorizationrules/{key name}`.

Om du vill aktivera utskick av diagnostikloggar till Log Analytics-arbetsytan, Använd följande kommando:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

Du kan hämta resurs-ID för Log Analytics-arbetsytan med följande kommando:

```powershell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId
```

Du kan kombinera dessa parametrar om du vill aktivera flera Utdataalternativ för.

Du kan inte för närvarande konfigurera inställningarna för diagnostik med Azure PowerShell.

### <a name="enable-collection-of-resource-diagnostic-logs-via-the-azure-cli"></a>Aktivera insamling av resursdiagnostikloggar via Azure CLI

Om du vill aktivera insamling av resursdiagnostikloggar via Azure CLI, använder du den [az monitor diagnostic-settings skapa](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) kommando.

Aktivera lagring av diagnostiska loggar i ett Lagringskonto:

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

Den `--resource-group` argumentet är bara krävs om `--storage-account` är inte ett objekt-ID.

Ska kunna strömma diagnostikloggar till en händelsehubb:

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --event-hub <event hub name> \
    --event-hub-rule <event hub rule ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

Regel-ID är en sträng med det här formatet: `{Service Bus resource ID}/authorizationrules/{key name}`.

För att aktivera sändning av diagnostikloggar till Log Analytics-arbetsyta:

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

Den `--resource-group` argumentet är bara krävs om `--workspace` är inte ett objekt-ID

Med alla kommandon du kan lägga till ytterligare kategorier diagnostikloggen genom att lägga till ordlistor JSON-matris som skickas som den `--logs` parametern. Du kan kombinera den `--storage-account`, `--event-hub`, och `--workspace` parametrar för att aktivera flera Utdataalternativ för.

Du kan inte för närvarande Konfigurera klient diagnostikinställningar med hjälp av CLI.

### <a name="enable-collection-of-resource-diagnostic-logs-via-rest-api"></a>Aktivera insamling av resursdiagnostikloggar via REST-API

Om du vill ändra diagnostikinställningar med Azure Monitor REST-API, se [det här dokumentet](https://docs.microsoft.com/rest/api/monitor/).

Du kan inte för närvarande Konfigurera klient diagnostikinställningar med Azure Monitor REST-API.

## <a name="manage-resource-diagnostic-settings-in-the-portal"></a>Hantera resursdiagnostikinställningar i portalen

Se till att alla dina resurser har ställts in med diagnostikinställningar. Gå till **övervakaren** i portalen och öppna **diagnostikinställningar**.

![Den diagnostiska loggar bladet i portalen](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-nav.png)

Du kanske måste klicka på ”alla tjänster” att hitta avsnittet Monitor.

Här kan du visa och filtrera alla resurser som har stöd för diagnostikinställningar för att se om de har aktiverat Diagnostikfunktionen. Du kan öka detaljnivån ned för att se om flera inställningar är inställda på en resurs och kontrollera vilka storage-konto, Event Hubs-namnområdet och/eller Log Analytics-arbetsyta som data som flödar in till.

![Den diagnostiska loggar resultat i portalen](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-blade.png)

Att lägga till en diagnostikinställning öppnar vyn diagnostikinställningar, där du kan aktivera, inaktivera eller ändra diagnostikinställningarna för den valda resursen.

## <a name="supported-services-categories-and-schemas-for-diagnostic-logs"></a>Tjänster som stöds, kategorier och scheman för diagnostikloggar

[Se den här artikeln](../azure-monitor/platform/tutorial-dashboards.md) för en fullständig lista över tjänster som stöds och loggkategorier och scheman som används av dessa tjänster.

## <a name="next-steps"></a>Nästa steg

* [Stream resursdiagnostikloggar till **Event Hubs**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Ändra resursdiagnostikinställningar med hjälp av Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Analysera loggar från Azure storage med Log Analytics](../azure-monitor/platform/collect-azure-metrics-logs.md)
