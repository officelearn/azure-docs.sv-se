---
title: Kom igång med roller, behörigheter och säkerhet med Azure Monitor
description: Lär dig hur du använder Azure Monitor inbyggda roller och behörigheter för att begränsa åtkomsten till övervakning av resurser.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: bac57b18ec5474cfe3c27ad1079c5af7e1d2c451
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60453122"
---
# <a name="get-started-with-roles-permissions-and-security-with-azure-monitor"></a>Kom igång med roller, behörigheter och säkerhet med Azure Monitor

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Många team behöver strikt reglera åtkomst till övervakningsdata och inställningar. Till exempel om du har som fungerar endast om hur du övervakar (support-tekniker, DevOps-tekniker) eller om du använder en leverantör av hanterade tjänster kan du behöva ge dem åtkomst till endast övervakningsdata samtidigt begränsa deras möjlighet att skapa, ändra, eller ta bort resurser. Den här artikeln visar hur du snabbt gäller en inbyggd övervakning RBAC-roll för en användare i Azure eller skapa en egen anpassad roll för en användare behöver begränsade behörigheter för övervakning. Det diskuterar sedan säkerhetsaspekter för dina Azure Monitor-relaterade resurser och hur du kan begränsa åtkomsten till den data de innehåller.

## <a name="built-in-monitoring-roles"></a>Inbyggd övervakning roller
Azure Monitor inbyggda roller är avsedda att begränsa åtkomsten till resurser i en prenumeration samtidigt som de som ansvarar för övervakning av infrastruktur för att hämta och konfigurera de data de behöver. Azure Monitor innehåller två out-of the box-roller: En övervakning läsare och deltagare övervakning.

### <a name="monitoring-reader"></a>Övervakningsläsare
Personer som har tilldelats rollen övervakning läsare kan visa alla övervakningsdata i en prenumeration men det går inte att ändra alla resurser eller redigera alla inställningar som rör övervakning av resurser. Den här rollen är lämplig för användare i en organisation, till exempel support eller åtgärder tekniker som behöver för att kunna:

* Visa övervakning instrumentpaneler i portalen och skapa sina egna privata instrumentpaneler för övervakning.
* Visa Varningsregler som definierats i [Azure-aviseringar](../../azure-monitor/platform/alerts-overview.md)
* Fråga om mått med hjälp av den [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931930.aspx), [PowerShell-cmdletar](../../azure-monitor/platform/powershell-quickstart-samples.md), eller [plattformsoberoende CLI](../../azure-monitor/platform/cli-samples.md).
* Fråga med hjälp av portalen, Azure Monitor REST API, PowerShell-cmdletar eller plattformsoberoende CLI aktivitetsloggen.
* Visa den [diagnostikinställningar](../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings) för en resurs.
* Visa den [loggprofilen](../../azure-monitor/platform/activity-logs-overview.md#export-the-activity-log-with-a-log-profile) för en prenumeration.
* Visa inställningarna för automatisk skalning.
* Visa Aviseringsaktivitet och inställningar.
* Få åtkomst till Application Insights-data och visa data i AI-Analytics.
* Söka efter Log Analytics-arbetsytedata, inklusive användningsdata för arbetsytan.
* Visa Log Analytics-hanteringsgrupper.
* Hämta sökschema i Log Analytics-arbetsyta.
* Lista över Övervakningspaket i Log Analytics-arbetsyta.
* Hämta och köra sparade sökningar i Log Analytics-arbetsyta.
* Hämta lagringskonfiguration för Log Analytics-arbetsytan.

> [!NOTE]
> Den här rollen ger inte läsbehörighet för loggdata som strömmas till en event hub eller lagras i ett lagringskonto. [Se nedan](#security-considerations-for-monitoring-data) information om hur du konfigurerar åtkomst till dessa resurser.
> 
> 

### <a name="monitoring-contributor"></a>Övervakningsdeltagare
Personer som har tilldelats rollen övervakning deltagare kan visa alla övervakningsdata i en prenumeration och skapa eller ändra inställningar för övervakning, men kan inte ändra andra resurser. Den här rollen är en supermängd rollen Läsare för övervakning och är lämplig för medlemmar i en organisation övervakning team eller leverantörer av hanterade tjänster som, utöver behörigheterna som ovan, måste också kunna:

* Publicera övervakning instrumentpaneler som en delad instrumentpanel.
* Ange [diagnostikinställningar](../../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings) för en resurs.\*
* Ange den [loggprofilen](../../azure-monitor/platform/activity-logs-overview.md#export-the-activity-log-with-a-log-profile) för en prenumeration.\*
* Ställ in Varningsregler aktivitet och inställningar via [Azure Alerts](../../azure-monitor/platform/alerts-overview.md).
* Skapa webbtester med Application Insights och komponenter.
* Lista Log Analytics-arbetsyta delade nycklar.
* Aktivera eller inaktivera Övervakningspaket i Log Analytics-arbetsyta.
* Skapa och ta bort och köra sparade sökningar i Log Analytics-arbetsyta.
* Skapa och ta bort lagringskonfiguration för Log Analytics-arbetsytan.

\*användaren måste också ha behörigheten Listnycklar på målresursen (lagring eller event hub namnrymden) att ange en av loggprofil eller diagnostiska inställningen.

> [!NOTE]
> Den här rollen ger inte läsbehörighet för loggdata som strömmas till en event hub eller lagras i ett lagringskonto. [Se nedan](#security-considerations-for-monitoring-data) information om hur du konfigurerar åtkomst till dessa resurser.
> 
> 

## <a name="monitoring-permissions-and-custom-rbac-roles"></a>Övervaka behörigheter och anpassad RBAC-roller
Om ovanstående inbyggda roller inte uppfyller de specifika behoven för ditt team, kan du [skapa en anpassad RBAC-roll](../../role-based-access-control/custom-roles.md) med mer detaljerade behörigheter. Nedan visas vanliga Azure Monitor RBAC-åtgärder med deras beskrivningar.

| Åtgärd | Beskrivning |
| --- | --- |
| Microsoft.Insights/ActionGroups/[Read, Write, Delete] |Läs/Skriv/ta bort åtgärdsgrupper. |
| Microsoft.Insights/ActivityLogAlerts/[Read, Write, Delete] |Läs/Skriv/ta bort aktivitetsloggaviseringar. |
| Microsoft.Insights/AlertRules/[Read, Write, Delete] |Läs/Skriv/ta bort aviseringsregler (från klassiska varningar). |
| Microsoft.Insights/AlertRules/Incidents/Read |Lista över incidenter (historik över aviseringsregeln utlöses) för Varningsregler. Detta gäller endast till portalen. |
| Microsoft.Insights/AutoscaleSettings/[Read, Write, Delete] |Läs/Skriv/ta bort autoskalningsinställningar. |
| Microsoft.Insights/DiagnosticSettings/[Read, Write, Delete] |Läs/Skriv/ta bort diagnostikinställningar. |
| Microsoft.Insights/EventCategories/Read |Räkna upp alla kategorier som är möjliga i aktivitetsloggen. Används av Azure-portalen. |
| Microsoft.Insights/eventtypes/digestevents/Read |Den här behörigheten krävs för användare som behöver åtkomst till aktivitetsloggar via portalen. |
| Microsoft.Insights/eventtypes/values/Read |Lista över aktivitetslogghändelser (av hanteringshändelser) i en prenumeration. Den här behörigheten gäller för både program- och portalen åtkomst till aktivitetsloggen. |
| Microsoft.Insights/ExtendedDiagnosticSettings/[Read, skriva, ta bort] | Läs/Skriv/ta bort diagnostikinställningar för flödesloggar för nätverket. |
| Microsoft.Insights/LogDefinitions/Read |Den här behörigheten krävs för användare som behöver åtkomst till aktivitetsloggar via portalen. |
| Microsoft.Insights/LogProfiles/[Read, skriva, ta bort] |Läs/Skriv/ta bort loggprofiler (strömma aktivitetsloggen till event hub eller storage-konto). |
| Microsoft.Insights/MetricAlerts/[Read, Write, Delete] |Läs/Skriv/ta bort aviseringar i nära realtid mått |
| Microsoft.Insights/MetricDefinitions/Read |Läs måttdefinitionerna (lista över tillgängliga typer av mått för en resurs). |
| Microsoft.Insights/Metrics/Read |Läsa måtten för en resurs. |
| Microsoft.Insights/Register/Action |Registrera resursprovidern Azure Monitor. |
| Microsoft.Insights/ScheduledQueryRules/[Read, Write, Delete] |Läs/Skriv/ta bort loggaviseringar i Azure Monitor. |



> [!NOTE]
> Åtkomst till aviseringar, diagnostikinställningar och statistik för en resurs som kräver att användaren har läsbehörighet till resurstyp och omfånget för den här resursen. En inställning eller log diagnostikprofil som arkiverar till ett lagringskonto eller dataströmmar till event hubs kräver skapar (”skriva”) att användaren också behörighet listnycklar för målresursen.
> 
> 

Till exempel med tabellen ovan som du kan skapa en anpassad RBAC-roll för en ”aktivitet Log Reader” så här:

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>Säkerhetsöverväganden för övervakning av data
Övervakning av data – särskilt loggfiler – kan innehålla känslig information, till exempel IP-adresser eller användarnamn. Övervakning av data från Azure levereras i tre grundläggande formulär:

1. Aktivitetsloggen som beskriver alla kontrollplanet åtgärder på din Azure-prenumeration.
2. Diagnostiska loggar är loggar som genereras av en resurs.
3. Mått som genereras av resurser.

Alla tre av följande datatyper kan lagras i ett lagringskonto eller strömma till Event Hub, som båda är allmänna Azure-resurser. Eftersom dessa är allmänna resurser, är skapa, ta bort och komma åt dem en privilegierad åtgärd som reserverats för en administratör. Vi rekommenderar att du använder följande metoder för övervakning-relaterade resurser för att förhindra missbruk:

* Använd ett enda, särskilt storage-konto för övervakning av data. Om du vill separera övervakningsdata i flera lagringskonton kan dela aldrig användning av ett lagringskonto mellan övervakning och icke-övervakning av data, eftersom det kan oavsiktligt ge dem som bara behöver åtkomst till övervakningsdata (till exempel en SIEM från tredje part) åtkomst till icke-övervakning av data.
* Använd ett enda, särskilt Service Bus eller Event Hub-namnområde i alla diagnostikinställningar av samma skäl som ovan.
* Begränsa åtkomsten till relaterade till prestandaövervakning storage-konton eller event hubs genom att lagra dem i en separat resursgrupp och [använda omfång](../../role-based-access-control/overview.md#scope) på din övervakning roller för att begränsa åtkomsten till endast den resursgruppen.
* Tilldela behörigheten Listnycklar för storage-konton eller händelsehubbar prenumerationsomfånget aldrig när en användare bara behöver åtkomst till övervakningsdata. I stället ge behörigheterna till användaren vid en resurs eller resursgrupp (om du har en dedikerad övervakning resursgrupp) omfång.

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Begränsa åtkomsten till relaterade till prestandaövervakning storage-konton
När en användare eller program behöver tillgång till övervakning av data i ett lagringskonto, bör du [Generera en konto-SAS](https://msdn.microsoft.com/library/azure/mt584140.aspx) för lagringskontot som innehåller övervakningsdata med tjänstnivå skrivskyddad åtkomst till blob storage. I PowerShell, kan det se ut:

```powershell
$context = New-AzStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Du kan sedan ge token till enheten som måste för att läsa från den storage-konto och den kan lista och läsa från alla blobbar i det lagringskontot.

Du kan också om du vill styra den här behörigheten med RBAC kan kan du ge entiteten Microsoft.Storage/storageAccounts/listkeys/action behörighet på det specifika lagringskontot. Detta är nödvändigt för användare som behöver för att kunna ange en diagnostikinställning eller loggprofilen att arkivera till ett lagringskonto. Du kan till exempel skapa följande anpassade RBAC roll för en användare eller program som behöver bara läsa från ett lagringskonto:

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzRoleDefinition -Role $role 
```

> [!WARNING]
> Listnycklar tillåter användaren att lista de primära och sekundära lagringskontonycklarna. De här nycklarna ge användaren alla signerade behörigheter (läsa, skriva, skapa BLOB-objekt, ta bort blobbar osv) för alla signerade tjänster (blob, kö, tabell, fil) i det lagringskontot. Vi rekommenderar att du använder en SAS-konto som beskrivs ovan när det är möjligt.
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Begränsa åtkomsten till relaterade till prestandaövervakning händelsehubbar
Ett liknande mönster kan följas med event hubs, men först måste du skapa en dedikerad Listen auktoriseringsregeln. Om du vill bevilja åtkomst till ett program som endast ska lyssna på relaterade till prestandaövervakning händelsehubbar kan du göra följande:

1. Skapa en princip för delad åtkomst på den event Hub som har skapats för direktuppspelning övervakningsdata med endast Listen anspråk. Detta kan göras i portalen. Exempel: du kan anropa den ”monitoringReadOnly”. Om möjligt ska du ge nyckeln direkt till konsumenten och hoppa över nästa steg.
2. Om användaren behöver för att kunna hämta nyckel för ad hoc-, bevilja användaren listnycklar för åtgärden för den händelsehubben. Detta är också nödvändigt för användare som behöver för att kunna ange en diagnostikinställning eller loggprofilen strömma till event hubs. Du kan till exempel skapa en regel för RBAC:
   
   ```powershell
   $role = Get-AzRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzRoleDefinition -Role $role 
   ```

## <a name="monitoring-within-a-secured-virtual-network"></a>Övervakning i ett skyddat virtuellt nätverk

Azure Monitor behöver åtkomst till dina Azure-resurser för att tillhandahålla tjänster som du aktiverar. Om du vill övervaka dina Azure-resurser när du fortfarande skydda dem från åtkomst till det offentliga Internet, kan du aktivera följande inställningar.

### <a name="secured-storage-accounts"></a>Skyddade konton 

Övervakning av data skrivs ofta till ett lagringskonto. Du kanske vill kontrollera att de data som kopieras till ett Lagringskonto inte kan nås av obehöriga användare. För ytterligare säkerhet, kan du låsa nätverksåtkomst för att endast tillåta dina behöriga resurser och betrodda Microsoft-tjänster åtkomst till ett lagringskonto genom att begränsa ett storage-konto om du vill använda ”valda nätverk”.
![Azure Storage-inställningsdialogrutan](./media/roles-permissions-security/secured-storage-example.png) Azure Monitor anses vara en av dessa ”betrodda Microsoft-tjänster” om du tillåter att betrodda Microsoft-tjänster åtkomst till dina skyddade lagring, Azure monitor får åtkomst till skyddade Storage-kontot; att aktivera skriver diagnostikloggar för Azure Monitor, aktivitetsloggen och mått till ditt Storage-konto med dessa skyddade villkor. Detta gör även att Log Analytics för att läsa loggar från säker lagring.   


Mer information finns i [Network security och Azure Storage](../../storage/common/storage-network-security.md)

## <a name="next-steps"></a>Nästa steg
* [Läs mer om RBAC och behörigheter i Resource Manager](../../role-based-access-control/overview.md)
* [Läs en översikt över övervakning i Azure](../../azure-monitor/overview.md)


