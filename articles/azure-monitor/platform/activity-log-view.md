---
title: Visa Azure Activity-logghändelser i Azure Monitor
description: Visa Azure Activity-loggen i Azure Monitor och hämta med PowerShell, CLI och REST API.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: d2423d04ead9040cce53d847d24efe75be680d94
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397309"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>Visa och hämta Azure Activity-logghändelser

[Azure-aktivitetsloggen](platform-logs-overview.md) ger insikt i händelser på prenumerationsnivå som har inträffat i Azure. Den här artikeln innehåller information om olika metoder för att visa och hämta aktivitetslogghändelser.

## <a name="azure-portal"></a>Azure Portal
Visa aktivitetsloggen för alla resurser från **Monitor-menyn** i Azure-portalen. Visa aktivitetsloggen för en viss resurs från alternativet **Aktivitetslogg** på resursens meny.

![Visa aktivitetslogg](./media/activity-logs-overview/view-activity-log.png)

Du kan filtrera aktivitetslogghändelser efter följande fält:

* **Tidsintervall:** Start- och sluttid för händelser.
* **Kategori**: Händelsekategorin som beskrivs i [Kategorier i aktivitetsloggen](activity-log-view.md#categories-in-the-activity-log).
* **Prenumeration**: Ett eller flera Azure-prenumerationsnamn.
* **Resursgrupp**: En eller flera resursgrupper inom de valda prenumerationerna.
* **Resurs (namn)**: - Namnet på en viss resurs.
* **Resurstyp**: Resurstyp, till exempel _Microsoft.Compute/virtualmachines_.
* **Åtgärdsnamn** - Namnet på en Azure Resource Manager-åtgärd, till exempel _Microsoft.SQL/servers/Write_.
* **Allvarlighetsgrad**: Allvarlighetsgraden för händelsen. Tillgängliga värden är _Informativa_, _Varning_, _Fel_, _Kritisk_.
* **Händelse initierad av**: Användaren som utförde åtgärden.
* **Öppna sökning:** Öppna textsökrutan som söker efter strängen i alla fält i alla händelser.

## <a name="categories-in-the-activity-log"></a>Kategorier i aktivitetsloggen
Varje händelse i aktivitetsloggen har en viss kategori som beskrivs i följande tabell. Fullständig information om schemat för dessa kategorier finns i [Azure Activity Log händelseschema](activity-log-schema.md). 

| Kategori | Beskrivning |
|:---|:---|
| Administrativ | Innehåller posten för alla åtgärder för att skapa, uppdatera, ta bort och vidta åtgärder som utförs via Resource Manager. Exempel på administrativa händelser är _att skapa virtuell dator_ och ta bort _nätverkssäkerhetsgrupp_.<br><br>Varje åtgärd som vidtas av en användare eller ett program med Resurshanteraren modelleras som en åtgärd på en viss resurstyp. Om operationstypen är _Skriv,_ _Ta bort_eller _Åtgärd_registreras posterna för både start och lyckad eller misslyckad åtgärd i kategorin Administration. Administrativa händelser innehåller även eventuella ändringar av rollbaserad åtkomstkontroll i en prenumeration. |
| Service Health | Innehåller posten över alla tjänsthälsoincidenter som har inträffat i Azure. Ett exempel på en Service _Health-händelse SQL Azure i östra USA upplever driftstopp_. <br><br>Service Health händelser finns i sex sorter: _Åtgärd krävs,_ _Assisterad återhämtning,_ _Incident,_ _Underhåll,_ _Information_eller _Säkerhet_. Dessa händelser skapas bara om du har en resurs i prenumerationen som skulle påverkas av händelsen.
| Resource Health | Innehåller posten för alla resurshälsohändelser som har inträffat till dina Azure-resurser. Ett exempel på en resurshälsahändelse är _hälsostatus för virtuell dator som har ändrats till otillgänglig_.<br><br>Resurshälsohändelser kan representera en av fyra hälsotillstånd: _Tillgänglig_, _Ej tillgänglig,_ _Försämrad_och _Okänd_. Dessutom kan Resource Health-händelser kategoriseras som _plattformsinitierade_ eller _användarinitierade_. |
| Varning | Innehåller posten aktiveringar för Azure-aviseringar. Ett exempel på en varningshändelse är _CPU% på myVM har varit över 80 under de senaste 5 minuterna._|
| Automatisk skalning | Innehåller en post för alla händelser som är relaterade till hur motorn för automatisk skalning fungerar baserat på alla inställningar för automatisk skalning som du har definierat i din prenumeration. Ett exempel på en händelse för automatisk skalning är _automatisk skalning upp åtgärden misslyckades_. |
| Rekommendation | Innehåller rekommendationshändelser från Azure Advisor. |
| Säkerhet | Innehåller posten för alla aviseringar som genereras av Azure Security Center. Ett exempel på en säkerhetshändelse är _Misstänkt dubbelnamn som körs_. |
| Princip | Innehåller poster för alla effektåtgärdsåtgärder som utförs av Azure Policy. Exempel på policyhändelser är _Granskning_ och _Neka_. Varje åtgärd som vidtas av principen modelleras som en åtgärd på en resurs. |

## <a name="view-change-history"></a>Visa ändringshistorik

När du granskar aktivitetsloggen kan det hjälpa dig att se vilka ändringar som hände under den händelsetiden. Du kan visa den här informationen med **ändringshistorik**. Välj en händelse i den aktivitetslogg som du vill titta djupare på. Välj fliken **Ändringshistorik (Förhandsgranska)** om du vill visa eventuella associerade ändringar med den händelsen.

![Ändra historiklista för en händelse](media/activity-logs-overview/change-history-event.png)

Om det finns några associerade ändringar med händelsen visas en lista över ändringar som du kan välja. Då öppnas sidan **Ändra historik (Förhandsgranska).** På den här sidan ser du ändringarna i resursen. Som du kan se av följande exempel kan vi inte bara se att den virtuella datorn ändrade storlekar, men vad den tidigare vm-storleken var före ändringen och vad den ändrades till.

![Sidan Ändra historik som visar skillnader](media/activity-logs-overview/change-history-event-details.png)

Mer information om ändringshistorik finns i [Hämta resursändringar](../../governance/resource-graph/how-to/get-resource-changes.md).






## <a name="powershell"></a>PowerShell
Använd [cmdleten Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) för att hämta aktivitetsloggen från PowerShell. Följande är några vanliga exempel.

> [!NOTE]
> `Get-AzLog`ger bara 15 dagars historia. Använd parametern **-MaxEvents** för att fråga de senaste N-händelserna efter 15 dagar. Om du vill komma åt händelser som är äldre än 15 dagar använder du REST API eller SDK. Om du inte inkluderar **StartTime**är standardvärdet **EndTime** minus en timme. Om du inte inkluderar **EndTime**är standardvärdet aktuell tid. Alla tider är i UTC.


Hämta loggposter som skapats efter en viss datumtid:

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

Hämta loggposter mellan ett datumtidsintervall:

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Hämta loggposter från en viss resursgrupp:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Hämta loggposter från en viss resursprovider mellan ett datumtidsintervall:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Hämta loggposter med en specifik uppringare:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Få de senaste 1000 händelserna:

```powershell
Get-AzLog -MaxEvents 1000
```


## <a name="cli"></a>CLI
Använd [az övervaka aktivitetslogg](cli-samples.md#view-activity-log-for-a-subscription) för att hämta aktivitetsloggen från CLI. Följande är några vanliga exempel.


Visa alla tillgängliga alternativ.

```azurecli
az monitor activity-log list -h
```

Hämta loggposter från en viss resursgrupp:

```azurecli
az monitor activity-log list --resource-group <group name>
```

Hämta loggposter med en specifik uppringare:

```azurecli
az monitor activity-log list --caller myname@company.com
```

Hämta loggar efter uppringare på en resurstyp, inom ett datumintervall:

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>REST-API
Använd [AZURE Monitor REST API](https://docs.microsoft.com/rest/api/monitor/) för att hämta aktivitetsloggen från en REST-klient. Följande är några vanliga exempel.

Hämta aktivitetsloggar med filter:

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

Hämta aktivitetsloggar med filtret och välj:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Hämta aktivitetsloggar med välj:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Hämta aktivitetsloggar utan filter eller välj:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="next-steps"></a>Nästa steg

* [Läs en översikt över plattformsloggar](platform-logs-overview.md)
* [Skapa diagnostikinställning för att skicka aktivitetsloggar till andra destinationer](diagnostic-settings.md)
