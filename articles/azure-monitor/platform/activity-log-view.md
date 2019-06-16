---
title: Visa händelser i aktivitetsloggen för Azure i Azure Monitor
description: Visa Azure-aktivitetsloggen i Azure Monitor och hämta med PowerShell, CLI och REST API.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 32578f77f2b3f30d80953bdd1099d22c945c640b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66248121"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>Visa och hämta händelser i aktivitetsloggen för Azure

Den [Azure-aktivitetsloggen](activity-logs-overview.md) ger information om händelser på prenumerationsnivå som har inträffat i Azure. Den här artikeln innehåller information om olika metoder för att visa och hämta händelser i aktivitetsloggen.

## <a name="azure-portal"></a>Azure Portal
Se aktivitetsloggen för alla resurser från den **övervakaren** menyn i Azure-portalen. Se aktivitetsloggen för en viss resurs från den **aktivitetsloggen** alternativ i den resurs-menyn.

![Visa aktivitetslogg](./media/activity-logs-overview/view-activity-log.png)

Du kan filtrera aktivitetsloggen händelser efter följande fält:

* **TimeSpan**: Start- och tid för händelser.
* **Kategori**: Händelsekategori enligt beskrivningen i [kategorier i aktivitetsloggen](activity-logs-overview.md#categories-in-the-activity-log).
* **Prenumeration**: Ett eller flera Azure-prenumeration-namn.
* **Resursgrupp**: En eller flera resursgrupper i de valda prenumerationerna.
* **Resurs (namn)** :-namnet på en viss resurs.
* **Resurstyp**: Typ av resurs, till exempel _Microsoft.Compute/virtualmachines_.
* **Åtgärdens namn** -namnet på en Azure Resource Manager-åtgärd, till exempel _Microsoft.SQL/servers/Write_.
* **Allvarlighetsgrad**: Allvarlighetsgrad för händelsen. Tillgängliga värden är _information_, _varning_, _fel_, _kritisk_.
* **Händelsen initierad av**: Den användare som utförde åtgärden.
* **Öppna sökning**: Öppna textruta för sökning som söker efter strängen i alla fält i alla händelser.

### <a name="view-change-history"></a>Visa ändringshistorik

När du granskar aktivitetsloggen, det kan bidra till att se vad som hände ändringar under den tiden. Du kan visa den här informationen med **ändringshistorik**. Välj en händelse i aktivitetsloggen som du vill söka djupet i. Välj den **ändringshistorik (förhandsversion)** fliken för att visa alla kopplade händelsen ändringar.

![Ändra listan med kommandohistorik för en händelse](media/activity-logs-overview/change-history-event.png)

Om det finns några associerade ändringar till händelsen, visas en lista över ändringar som du kan välja. Gör det öppnas den **ändringshistorik (förhandsversion)** sidan. På den här sidan visas ändringarna till resursen. Vi kan inte bara se att den virtuella datorn ändras storlekar, men den föregående VM-storleken har innan ändringen och ändrades till som du ser i följande exempel.

![Ändra historik-sidan som visar skillnader](media/activity-logs-overview/change-history-event-details.png)

Läs mer om ändra historik i [hämta resursändringar](../../governance/resource-graph/how-to/get-resource-changes.md).


## <a name="log-analytics-workspace"></a>Log Analytics-arbetsyta
Klicka på **loggar** överst i den **aktivitetsloggen** kan du öppna den [Activity Log Analytics övervakningslösning](activity-log-collect.md) för prenumerationen. Detta gör det möjligt att visa analysinformation för aktivitetsloggen och köra [logga frågor](../log-query/log-query-overview.md) med den **AzureActivity** tabell. Om din aktivitetslogg inte är ansluten till en Log Analytics-arbetsyta, uppmanas du att utföra den här konfigurationen.



## <a name="powershell"></a>PowerShell
Använd den [Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) cmdlet för att hämta aktivitetsloggen från PowerShell. Följande är några vanliga exempel.

> [!NOTE]
> `Get-AzLog` endast ger 15 dagar tidigare. Använd den **- MaxEvents** parametern till de senaste N-händelser efter 15 dagar. Använda REST API eller SDK för att komma åt händelser som är äldre än 15 dagar. Om du inte använder **StartTime**, och standardvärdet är **EndTime** minus en timme. Om du inte använder **EndTime**, och standardvärdet är aktuell tid. Hela tiden är i UTC.


Hämta loggposter som skapats efter ett visst datum-tid:

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

Hämta loggposter mellan ett datum tidsintervall:

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Hämta poster från en specifik resursgrupp:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Hämta poster från en viss resursprovider mellan ett datum tidsintervall:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Hämta poster med en specifik anropare:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Hämta de senaste 1 000 händelserna:

```powershell
Get-AzLog -MaxEvents 1000
```


## <a name="cli"></a>CLI
Använd [az monitor-aktivitetsloggen](cli-samples.md#view-activity-log-for-a-subscription) att hämta aktivitetsloggen från CLI. Följande är några vanliga exempel.


Visa alla tillgängliga alternativ.

```azurecli
az monitor activity-log list -h
```

Hämta poster från en specifik resursgrupp:

```azurecli
az monitor activity-log list --resource-group <group name>
```

Hämta poster med en specifik anropare:

```azurecli
az monitor activity-log list --caller myname@company.com
```

Hämta loggar av anroparen på en resurstyp i ett datumintervall:

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>REST-API
Använd den [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/) att hämta aktivitetsloggen från en REST-klient. Följande är några vanliga exempel.

Hämta aktivitetsloggar med filter:

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

Hämta aktivitetsloggar med filter och välja:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Hämta aktivitetsloggar med särskilda:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Hämta aktivitetsloggar utan filter eller välj:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="next-steps"></a>Nästa steg

* [Läs en översikt över aktivitetsloggen](activity-logs-overview.md)
* [Arkivera aktivitetsloggen till lagring eller strömma till Event Hubs](activity-log-export.md)
* [Stream Azure-aktivitetsloggen till Event Hubs](activity-logs-stream-event-hubs.md)
* [Arkivera Azure-aktivitetsloggen till storage](archive-activity-log.md)

