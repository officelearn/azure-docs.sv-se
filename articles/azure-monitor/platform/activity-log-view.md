---
title: Visa Azure aktivitets logg händelser i Azure Monitor
description: Visa Azures aktivitets logg i Azure Monitor och hämta med PowerShell, CLI och REST API.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/07/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 3327f0bdaff641055cf84ab205d847f0fb73bfe8
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834619"
---
# <a name="view-and-retrieve-azure-activity-log-events"></a>Visa och hämta Azure aktivitets logg händelser

[Azure aktivitets loggen](platform-logs-overview.md) ger inblick i händelser på prenumerations nivå som har inträffat i Azure. Den här artikeln innehåller information om olika metoder för att visa och hämta aktivitets logg händelser.

## <a name="azure-portal"></a>Azure Portal
Visa aktivitets loggen för alla resurser på **Monitor** -menyn i Azure Portal. Visa aktivitets loggen för en viss resurs från alternativet **aktivitets logg** på den resurs menyn.

![Visa aktivitets logg](./media/activity-logs-overview/view-activity-log.png)

Du kan filtrera aktivitets logg händelser efter följande fält:

* **TimeSpan**: Start-och slut tid för händelser.
* **Kategori**: händelse kategorin enligt beskrivningen i [Kategorier i aktivitets loggen](activity-log-view.md#categories-in-the-activity-log).
* **Prenumeration**: ett eller flera namn på en Azure-prenumeration.
* **Resurs grupp**: en eller flera resurs grupper inom de valda prenumerationerna.
* **Resurs (namn)**:-namnet på en enskild resurs.
* **Resurs typ**: resurs typ, till exempel _Microsoft. Compute/virtualmachines_.
* **Åtgärds namn** – namnet på en Azure Resource Manager-åtgärd, till exempel _Microsoft. SQL/Server/Write_.
* **Allvarlighets**grad: händelsens allvarlighets grad. Tillgängliga värden är _information_, _Varning_, _fel_, _kritisk_.
* **Händelse som initieras av**: den användare som utförde åtgärden.
* **Öppna sökning**: öppna text söknings rutan som söker efter strängen i alla fält i alla händelser.

## <a name="categories-in-the-activity-log"></a>Kategorier i aktivitets loggen
Varje händelse i aktivitets loggen har en viss kategori som beskrivs i följande tabell. Fullständig information om scheman för dessa kategorier finns i [händelse schema för Azure aktivitets logg](activity-log-schema.md). 

| Kategori | Description |
|:---|:---|
| Administrativ | Innehåller posten över alla åtgärder för att skapa, uppdatera, ta bort och åtgärd som utförs via Resource Manager. Exempel på administrativa händelser är att _skapa en virtuell dator_ och _ta bort nätverks säkerhets grupp_.<br><br>Varje åtgärd som utförs av en användare eller ett program som använder Resource Manager är modellerad som en åtgärd på en viss resurs typ. Om åtgärds typen är _Skriv_, _ta bort_eller _åtgärd_registreras posterna för både start och lyckad eller misslyckad åtgärd i den administrativa kategorin. Administrativa händelser inkluderar även eventuella ändringar av rollbaserad åtkomst kontroll i en prenumeration. |
| Service Health | Innehåller posten för eventuella service Health-incidenter som har inträffat i Azure. Ett exempel på en Service Health händelse _SQL Azure i östra USA drabbas av drift stopp_. <br><br>Service Health-händelser finns på sex sorter: _åtgärd krävs_, _stöd för återställning_, _incident_, _Underhåll_, _information_eller _säkerhet_. Dessa händelser skapas endast om du har en resurs i prenumerationen som skulle påverkas av händelsen.
| Resource Health | Innehåller posten för eventuella resurs hälso händelser som har inträffat på dina Azure-resurser. Ett exempel på en Resource Health-händelse är _hälso statusen för den virtuella datorn har ändrats till otillgänglig_.<br><br>Resource Health händelser kan representera en av fyra hälso status: _tillgänglig_, otillgänglig _,_ _degraderad_och _okänd_. Dessutom kan Resource Health händelser kategoriseras som plattform som _initieras_ eller _användaren initieras_. |
| Varning | Innehåller posten för aktiveringar för Azure-aviseringar. Ett exempel på en varnings händelse är _CPU% på myVM har varit över 80 under de senaste 5 minuterna_.|
| Automatisk skalning | Innehåller posten för alla händelser som rör driften av autoskalning-motorn baserat på de inställningar för autoskalning som du har definierat i din prenumeration. Ett exempel på en autoskalning-händelse är autoskalning- _åtgärden misslyckades_. |
| Rekommendation | Innehåller rekommendations händelser från Azure Advisor. |
| Säkerhet | Innehåller posten för eventuella aviseringar som genererats av Azure Security Center. Ett exempel på en säkerhets händelse är _misstänkt dubbel tilläggs fil som körs_. |
| Policy | Innehåller poster med åtgärder som utförs av alla åtgärder som utförs av Azure Policy. Exempel på princip händelser är _granskning_ och _neka_. Varje åtgärd som utförs av principen är modellerad som en åtgärd på en resurs. |

## <a name="view-change-history"></a>Visa ändrings historik

När du läser aktivitets loggen kan det hjälpa dig att se vilka ändringar som skett under den tidpunkten. Du kan visa den här informationen med **ändrings historik**. Välj en händelse från aktivitets loggen som du vill se djupare i. Välj fliken **ändrings historik (förhands granskning)** om du vill visa alla associerade ändringar med händelsen.

![Lista över ändrings historik för en händelse](media/activity-logs-overview/change-history-event.png)

Om det finns några associerade ändringar i händelsen visas en lista över ändringar som du kan välja. Då öppnas sidan **ändrings historik (förhands granskning)** . På den här sidan visas ändringarna i resursen. Som du kan se i följande exempel kan vi inte bara se att den virtuella datorn har ändrat storlek, men att den tidigare storleken på den virtuella datorn var före ändringen och vad den ändrades till.

![Sidan ändrings historik visar skillnader](media/activity-logs-overview/change-history-event-details.png)

Läs mer om ändrings historik i [Hämta resurs ändringar](../../governance/resource-graph/how-to/get-resource-changes.md).






## <a name="powershell"></a>PowerShell
Använd cmdleten [Get-AzLog](https://docs.microsoft.com/powershell/module/az.monitor/get-azlog) för att hämta aktivitets loggen från PowerShell. Här följer några vanliga exempel.

> [!NOTE]
> `Get-AzLog`innehåller endast 15 dagars historik. Använd parametern **-MaxRecord** för att fråga de senaste N händelserna efter 15 dagar. Använd REST API eller SDK för att få åtkomst till händelser som är äldre än 15 dagar. Om du inte inkluderar **StartTime**, är standardvärdet slut **tid** minus en timme. Om du **inte inkluderar slut tid är**standardvärdet aktuell tid. Alla tider är i UTC-tid.


Hämta logg poster som skapats efter en viss datum tid:

```powershell
Get-AzLog -StartTime 2016-03-01T10:30
```

Hämta logg poster mellan ett datum tidsintervall:

```powershell
Get-AzLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Hämta logg poster från en angiven resurs grupp:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Hämta logg poster från en speciell resurs leverantör mellan ett datum tidsintervall:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Hämta logg poster med en angiven anropare:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Hämta de senaste 1000 händelserna:

```powershell
Get-AzLog -MaxRecord 1000
```


## <a name="cli"></a>CLI
Använd [AZ övervaka aktivitet – logg](../samples/cli-samples.md#view-activity-log-for-a-subscription) för att hämta aktivitets loggen från cli. Här följer några vanliga exempel.


Visa alla tillgängliga alternativ.

```azurecli
az monitor activity-log list -h
```

Hämta logg poster från en angiven resurs grupp:

```azurecli
az monitor activity-log list --resource-group <group name>
```

Hämta logg poster med en angiven anropare:

```azurecli
az monitor activity-log list --caller myname@company.com
```

Hämta loggar efter anropare på en resurs typ inom ett datum intervall:

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="rest-api"></a>REST-API
Använd [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/) för att hämta aktivitets loggen från en rest-klient. Här följer några vanliga exempel.

Hämta aktivitets loggar med filter:

``` HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2018-01-21T20:00:00Z' and eventTimestamp le '2018-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'
```

Hämta aktivitets loggar med filter och välj:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '2015-01-21T20:00:00Z' and eventTimestamp le '2015-01-23T20:00:00Z' and resourceGroupName eq 'MSSupportGroup'&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Hämta aktivitets loggar med Select:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&$select=eventName,id,resourceGroupName,resourceProviderName,operationName,status,eventTimestamp,correlationId,submissionTimestamp,level
```

Hämta aktivitets loggar utan filter eller välj:

```HTTP
GET https://management.azure.com/subscriptions/089bd33f-d4ec-47fe-8ba5-0753aa5c5b33/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01
```


## <a name="next-steps"></a>Nästa steg

* [Läs en översikt över plattforms loggar](platform-logs-overview.md)
* [Skapa diagnostisk inställning för att skicka aktivitets loggar till andra destinationer](diagnostic-settings.md)
