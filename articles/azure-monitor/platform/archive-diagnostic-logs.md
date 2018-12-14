---
title: Arkivera Azure diagnostikloggar
description: Lär dig att arkivera dina Azure-diagnostikloggar för långsiktig kvarhållning i ett lagringskonto.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: johnkem
ms.component: logs
ms.openlocfilehash: dbdfdfc76ec0174411851503353155f266e25726
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53389116"
---
# <a name="archive-azure-diagnostic-logs"></a>Arkivera Azure diagnostikloggar

I den här artikeln visar vi hur du kan använda Azure portal, PowerShell-Cmdlets, CLI eller REST API för att arkivera dina [Azure diagnostikloggar](../../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) i ett lagringskonto. Det här alternativet är användbart om du vill behålla dina diagnostikloggar med en princip för valfri kvarhållningstid för granskning, statiska analys eller säkerhetskopiering. Storage-kontot behöver inte finnas i samma prenumeration som resursen loggarna så länge som den användare som konfigurerar inställningen har lämplig RBAC-åtkomst till båda prenumerationerna.

> [!WARNING]
> Formatet för loggdata i lagringskontot ändras till JSON Lines den 1 november 2018. [Den här artikeln beskriver effekten av den här ändringen samt hur du uppdaterar dina verktyg för att hantera det nya formatet.](./../../monitoring-and-diagnostics/monitor-diagnostic-logs-append-blobs.md) 
>
> 

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar måste du [skapa ett lagringskonto](../../storage/common/storage-quickstart-create-account.md) som du kan arkivera dina diagnostikloggar. Vi rekommenderar starkt att du inte använder ett befintligt lagringskonto som har andra, icke-övervakning av data som lagras i den så att du kan få bättre kontroll över åtkomsten till övervakningsdata. Om du också arkivera din aktivitetslogg och diagnostikmått till ett lagringskonto, men kan det vara klokt du använder att lagringskonto för diagnostikloggar samt så att alla övervakningsdata på en central plats.

> [!NOTE]
>  Du kan inte arkivera data till en storage-konto som bakom ett skyddat virtuellt nätverk.

## <a name="diagnostic-settings"></a>Diagnostikinställningar

För att arkivera dina diagnostiska loggar med någon av metoderna nedan som du anger en **diagnostikinställning** för en viss resurs. En diagnostikinställning för en resurs definierar kategorier av loggar och måttdata som skickas till ett mål (storage-konto, Event Hubs-namnområdet eller Log Analytics). Den definierar även bevarandeprincipen (antal dagar) för händelser med varje loggkategori och måttdata som lagras i ett lagringskonto. Om en kvarhållningsprincip har angetts till noll lagras händelser för den loggkategori på obestämd tid (det vill säga att säga alltid). En bevarandeprincip kan annars vara valfritt antal dagar mellan 1 och 2147483647. [Du kan läsa mer om diagnostikinställningar här](../../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#diagnostic-settings). Principer för kvarhållning är tillämpad per dag, så i slutet av en dag (UTC) loggar från den dag som är nu utöver kvarhållning principen tas bort. Till exempel om du har en bevarandeprincip för en dag skulle i början av dagen idag loggar från dag innan igår tas bort. Ta bort börjar vid midnatt UTC-tid, men Observera att det kan ta upp till 24 timmar innan loggarna som ska tas bort från ditt lagringskonto. 

> [!NOTE]
> Det går för närvarande inte att skicka flerdimensionella mätvärden via diagnostikinställningar. Mått med dimensioner exporteras som tillplattade endimensionella mått som aggregeras över dimensionsvärden.
>
> *Till exempel*: ”Inkommande meddelanden'-mått i en Händelsehubb kan utforskas och läggas till på en per kö-nivå. När måttet exporteras via diagnostikinställningar visas det dock som alla inkommande meddelanden i alla köer i händelsehubben.
>
>

## <a name="archive-diagnostic-logs-using-the-portal"></a>Arkivera diagnostikloggar med hjälp av portalen

1. I portalen, gå till Azure Monitor och klicka på **diagnostikinställningar**

    ![Avsnittet av Azure Monitor övervakning](media/archive-diagnostic-logs/diagnostic-settings-blade.png)

2. Du kan också filtrera listan efter resursgruppen eller resursen. Klicka sedan på den resurs som du vill ange en diagnostikinställning.

3. Om inga inställningar finns på resursen har du valt, uppmanas du för att skapa en inställning. Klicka på ”Slå på diagnostik”.

   ![Lägg till diagnostikinställning - inga befintliga inställningar](media/archive-diagnostic-logs/diagnostic-settings-none.png)

   Om det finns befintliga inställningarna på resursen, visas en lista över inställningar som redan har konfigurerats på den här resursen. Klicka på ”Lägg till diagnostikinställning”.

   ![Lägg till diagnostikinställning - befintliga inställningar](media/archive-diagnostic-logs/diagnostic-settings-multiple.png)

3. Ge din ställa in ett namn och markera kryssrutan för **exportera till Lagringskonto**, Välj ett lagringskonto. Du kan också ange ett antal dagar att behålla dessa loggar med hjälp av den **Kvarhållning (dagar)** skjutreglage. En kvarhållning av noll dagar lagras loggarna på obestämd tid.

   ![Lägg till diagnostikinställning - befintliga inställningar](media/archive-diagnostic-logs/diagnostic-settings-configure.png)

4. Klicka på **Spara**.

Den nya inställningen visas i din lista över inställningar för den här resursen efter en liten stund och diagnostikloggar arkiveras till det lagringskontot när nya händelsedata genereras.

## <a name="archive-diagnostic-logs-via-azure-powershell"></a>Arkivera diagnostikloggar via Azure PowerShell

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg -StorageAccountId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Categories networksecuritygroupevent,networksecuritygrouprulecounter -Enabled $true -RetentionEnabled $true -RetentionInDays 90
```

| Egenskap  | Krävs | Beskrivning |
| --- | --- | --- |
| Resurs-ID |Ja |Resurs-ID för den resurs som du vill ange en diagnostikinställning. |
| StorageAccountId |Nej |Resurs-ID för det Lagringskonto där diagnostikloggar ska sparas. |
| Kategorier |Nej |Kommaavgränsad lista över loggkategorier för att aktivera. |
| Enabled |Ja |Booleskt värde som anger om diagnostik är aktiverade eller inaktiverade på den här resursen. |
| RetentionEnabled |Nej |Booleskt värde som anger om en bevarandeprincip är aktiverade på den här resursen. |
| RetentionInDays |Nej |Antal dagar som händelser ska behållas mellan 1 och 2147483647. Värdet noll lagras loggarna på obestämd tid. |

## <a name="archive-diagnostic-logs-via-the-azure-cli"></a>Arkivera diagnostikloggar via Azure CLI

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

Du kan lägga till ytterligare kategorier diagnostikloggen genom att lägga till ordlistor JSON-matris som skickas som den `--logs` parametern.

Den `--resource-group` argumentet är bara krävs om `--storage-account` är inte ett objekt-ID. Läs den fullständiga dokumentationen för arkivering diagnostikloggar till lagring i [CLI kommandoreferens](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create).

## <a name="archive-diagnostic-logs-via-the-rest-api"></a>Arkivera diagnostikloggar via REST API

[Se det här dokumentet](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) information om hur du ställer in en diagnostikinställning med hjälp av REST-API i Azure Monitor.

## <a name="schema-of-diagnostic-logs-in-the-storage-account"></a>Schemat för diagnostikloggar i storage-konto

När du har konfigurerat arkivering, skapas en lagringsbehållare i lagringskontot när en händelse inträffar i någon av loggkategorier som du har aktiverat. Blobbar i behållaren följer samma namngivningskonvention för aktivitetsloggar och diagnostiska loggar som visas här:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Till exempel kan ett blobbnamn vara:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Varje PT1H.json-blob innehåller en JSON-blob med händelser som inträffade inom den angivna timmen i blob-URL (till exempel h=12). Under den aktuella timmen läggs händelser till i filen PT1H.json allt eftersom de inträffar. Minutvärdet (m = 00) är alltid 00, eftersom diagnostiska logghändelser delas upp i enskilda blobar per timme.

Varje händelse som lagras i filen pt1h.JSON i matrisen ”poster” efter det här formatet:

``` JSON
{
    "records": [
        {
            "time": "2016-07-01T00:00:37.2040000Z",
            "systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1",
            "category": "NetworkSecurityGroupRuleCounter",
            "resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG",
            "operationName": "NetworkSecurityGroupCounters",
            "properties": {
                "vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}",
                "subnetPrefix": "10.3.0.0/24",
                "macAddress": "000123456789",
                "ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp",
                "direction": "In",
                "type": "allow",
                "matchedConnections": 1988
            }
        }
    ]
}
```

| Elementnamn | Beskrivning |
| --- | --- |
| time |Tidsstämpel när händelsen skapades av tjänsten Azure behandlingen av begäran som motsvarande händelsen. |
| resourceId |Resurs-ID för resursen som påverkas. |
| operationName |Åtgärdens namn. |
| category |Loggkategori för händelsen. |
| properties |Uppsättning `<Key, Value>` par (d.v.s. ordlista) som beskriver informationen om händelsen. |

> [!NOTE]
> Egenskaper och användningen av dessa egenskaper kan variera beroende på resurs.

## <a name="next-steps"></a>Nästa steg

* [Ladda ned blobar för analys](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Stream diagnostikloggar till Event Hubs-namnområdet](../../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Arkivera loggar för Azure Active Directory med Azure Monitor](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)
* [Läs mer om diagnostikloggar](../../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)
