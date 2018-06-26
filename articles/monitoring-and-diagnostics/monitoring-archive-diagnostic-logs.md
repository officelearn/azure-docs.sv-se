---
title: Arkivera Azure diagnostikloggar
description: Lär dig mer om att arkivera dina Azure diagnostikloggar för långsiktig kvarhållning i ett lagringskonto.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: johnkem
ms.component: logs
ms.openlocfilehash: d48828c8d2ec439f389fe4eddabb59599cc1680b
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36752834"
---
# <a name="archive-azure-diagnostic-logs"></a>Arkivera Azure diagnostikloggar

I den här artikeln visar vi hur du kan använda Azure-portalen, PowerShell-Cmdlets, CLI eller REST API för att arkivera dina [Azure diagnostikloggar](monitoring-overview-of-diagnostic-logs.md) i ett lagringskonto. Det här alternativet är användbart om du vill behålla dina diagnostikloggar med ett valfritt bevarandeprincipen för granskning, statiska analys eller säkerhetskopiering. Storage-konto behöver inte finnas i samma prenumeration som resursen avger loggar så länge som den användare som konfigurerar inställningen har lämplig RBAC åtkomst till båda prenumerationer.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar måste du [skapa ett lagringskonto](../storage/storage-create-storage-account.md) som du kan arkivera dina diagnostikloggar. Vi rekommenderar starkt att du inte använder ett befintligt lagringskonto som har andra, icke-övervakning data som lagras i den så att du bättre kan styra åtkomsten till övervakningsdata. Men om du arkiverar även din aktivitetsloggen och diagnostik mått till ett lagringskonto, kan det vara bra att använda detta lagringskonto för dina diagnostikloggar för att hålla alla övervakningsdata på en central plats. Storage-konto som du använder måste vara ett allmänt lagringskonto inte ett blob storage-konto.

> [!NOTE]
>  Du kan inte arkivera data till en lagringsplats för kontot som bakom skyddade virtuella nätverk.

## <a name="diagnostic-settings"></a>Diagnostikinställningar

Om du vill arkivera dina diagnostikloggar med någon av metoderna nedan kan du ange en **diagnostikinställningen** för en viss resurs. Diagnostikinställningen för en resurs definierar kategorier av loggar och mått data som skickas till ett mål (storage-konto, Händelsehubbar namnområde eller logganalys). Den definierar även bevarandeprincip (antal dagar att behålla) för händelser för varje logg kategori och mått data som lagras i ett lagringskonto. Om en bevarandeprincip har angetts till noll lagras händelser för den logg kategorin på obestämd tid (dvs, oändligt). En bevarandeprincip kan annars vara valfritt antal dagar mellan 1 och 2147483647. [Du kan läsa mer om diagnostikinställningar här](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings). Bevarandeprinciper är tillämpade per dag, så i slutet av dagen (UTC) loggar från den dagen är nu utöver kvarhållning princip kommer att tas bort. Till exempel om du har en bevarandeprincip på en dag skulle i början av dagen idag loggar från dag före igår tas bort. Ta bort börjar vid midnatt UTC-tid, men Observera att det kan ta upp till 24 timmar för loggar som ska tas bort från ditt lagringskonto. 

> [!NOTE]
> Det går för närvarande inte att skicka flerdimensionella mätvärden via diagnostikinställningar. Mått med dimensioner exporteras som tillplattade endimensionella mått som aggregeras över dimensionsvärden.
>
> *Till exempel*: Måttet för inkommande meddelanden i en händelsehubb kan utforskas och läggas till på per-kö-nivå. När måttet exporteras via diagnostikinställningar visas det dock som alla inkommande meddelanden i alla köer i händelsehubben.
>
>

## <a name="archive-diagnostic-logs-using-the-portal"></a>Arkivera diagnostikloggar med hjälp av portalen

1. Gå till Azure-Monitor i portalen och klicka på **diagnostikinställningar**

    ![Avsnittet av Azure-Monitor övervakning](media/monitoring-archive-diagnostic-logs/diagnostic-settings-blade.png)

2. Om du vill filtrera listan efter resursgrupp eller resurstyp, och klicka sedan på resursen som du vill ange en diagnostikinställningen.

3. Om det finns inga inställningar på resursen har du valt, uppmanas du för att skapa en inställning. Klicka på ”Aktivera diagnostik”.

   ![Lägg till diagnostikinställningen - inga befintliga inställningar](media/monitoring-archive-diagnostic-logs/diagnostic-settings-none.png)

   Om det finns befintliga inställningarna på resursen, visas en lista över inställningar som redan har konfigurerats på den här resursen. Klicka på ”Lägg till diagnostikinställningen”.

   ![Lägg till diagnostikinställningen - befintliga inställningar](media/monitoring-archive-diagnostic-logs/diagnostic-settings-multiple.png)

3. Ge din ange ett namn och markera kryssrutan för **exportera till Lagringskontot**, Välj ett lagringskonto. Du kan också ange ett antal dagar att behålla dessa loggar med hjälp av den **bevarande (dagar)** skjutreglagen. En kvarhållning av noll dagar lagrar loggarna på obestämd tid.

   ![Lägg till diagnostikinställningen - befintliga inställningar](media/monitoring-archive-diagnostic-logs/diagnostic-settings-configure.png)

4. Klicka på **Spara**.

Den nya inställningen visas i din lista över inställningar för den här resursen efter en liten stund och diagnostikloggar arkiveras till att lagringskontot när nya händelsedata genereras.

## <a name="archive-diagnostic-logs-via-azure-powershell"></a>Arkivera diagnostikloggar via Azure PowerShell

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg -StorageAccountId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Categories networksecuritygroupevent,networksecuritygrouprulecounter -Enabled $true -RetentionEnabled $true -RetentionInDays 90
```

| Egenskap  | Krävs | Beskrivning |
| --- | --- | --- |
| resurs-ID |Ja |Resurs-ID för den resurs som du vill ange en diagnostikinställningen. |
| storageAccountId |Nej |Resurs-ID för det Lagringskonto där diagnostikloggar ska sparas. |
| Kategorier |Nej |Kommaavgränsad lista över loggen kategorier för att aktivera. |
| Enabled |Ja |Booleskt värde som anger om diagnostik är aktiverade eller inaktiverade på den här resursen. |
| RetentionEnabled |Nej |Booleskt värde som anger om en bevarandeprincip är aktiverade på den här resursen. |
| RetentionInDays |Nej |Antal dagar som händelser ska behållas mellan 1 och 2147483647. Värdet noll lagrar loggarna på obestämd tid. |

## <a name="archive-diagnostic-logs-via-the-azure-cli-20"></a>Arkivera diagnostikloggar via Azure CLI 2.0

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

Du kan lägga till ytterligare kategorier diagnostiska loggen genom att lägga till ordlistor JSON-matris som skickades i `--logs` parameter.

Den `--resource-group` argumentet är bara krävs om `--storage-account` är inte ett objekt-ID. Fullständig dokumentation för arkivering diagnostiska loggar till lagring finns i [CLI kommandoreferens](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create).

## <a name="archive-diagnostic-logs-via-the-rest-api"></a>Arkivera diagnostikloggar via REST API

[Det här dokumentet finns](https://docs.microsoft.com/en-us/rest/api/monitor/diagnosticsettings) information om hur du ställer in en diagnostikinställningen med hjälp av REST API för Azure-Monitor.

## <a name="schema-of-diagnostic-logs-in-the-storage-account"></a>Schemat för diagnostikloggar i storage-konto

När du har konfigurerat arkivering, skapas en lagringsbehållare i lagringskontot när en händelse inträffar i en av logg-kategorier som du har aktiverat. Blobbar i behållaren följer samma format över diagnostikloggar och aktivitetsloggen. Strukturen för de här blobbar är:

> insikter - loggar-{kategori loggnamn} / resourceId = / PRENUMERATIONER / {prenumerations-ID} /RESOURCEGROUPS/ {resursgruppens namn} /PROVIDERS/ {resurs providernamn} / {resurstyp} / {resursnamn} / y = {numeriskt årtal} / m = {tvåsiffrig numeriska month} / d = {tvåsiffrig kalenderdag} / tim = {tvåsiffrig 24-timmarsklocka hour}/m=00/PT1H.json

Eller fler bara

> insikter - loggar-{kategori loggnamn} / resourceId = / {resursens Id} / y = {numeriskt årtal} / m = {tvåsiffrig numeriska month} / d = {tvåsiffrig kalenderdag} / tim = {tvåsiffrig 24-timmarsklocka hour}/m=00/PT1H.json

Till exempel kan en blobbnamnet vara:

> insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json

Varje PT1H.json-blob innehåller en JSON-blob med händelser som inträffade inom den angivna timmen i blob-URL (till exempel h=12). Under den aktuella timmen läggs händelser till i filen PT1H.json allt eftersom de inträffar. Minuten (m = 00) är alltid 00, eftersom diagnostiska logghändelser delas upp i enskilda blobbar per timme.

Varje händelse lagras i filen PT1H.json i matrisen ”innehåller” följa det här formatet:

```
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
| time |Tidsstämpel när händelsen skapades av tjänsten Azure motsvarande händelsen begäran bearbetades. |
| resourceId |Resurs-ID för resursen påverkas. |
| operationName |Åtgärdens namn. |
| category |Loggen kategori för händelsen. |
| properties |En uppsättning `<Key, Value>` par (d.v.s. ordlista) som beskriver information om händelsen. |

> [!NOTE]
> Egenskaper och användning av dessa egenskaper kan variera beroende på resursen.

## <a name="next-steps"></a>Nästa steg

* [Ladda ned blobbar för analys](../storage/storage-dotnet-how-to-use-blobs.md)
* [Dataströmmen diagnostiska loggar till ett namnområde för Händelsehubbar](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Läs mer om diagnostikloggar](monitoring-overview-of-diagnostic-logs.md)
