---
title: Roller, behörigheter och säkerhet i Azure Monitor
description: Lär dig hur du använder Azure Monitors inbyggda roller och behörigheter för att begränsa åtkomsten till övervakningsresurser.
author: johnkemnetz
services: azure-monitor
ms.topic: conceptual
ms.date: 11/27/2017
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: 81309f0b5781e6302887a5b079ed359e70659834
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658990"
---
# <a name="roles-permissions-and-security-in-azure-monitor"></a>Roller, behörigheter och säkerhet i Azure Monitor

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Många team måste strikt reglera åtkomsten till övervakningsdata och inställningar. Om du till exempel har gruppmedlemmar som arbetar uteslutande med övervakning (supporttekniker, DevOps-tekniker) eller om du använder en hanterad tjänsteleverantör, kanske du vill ge dem åtkomst till endast övervakningsdata samtidigt som de begränsar deras möjligheter att skapa, ändra eller ta bort resurser. Den här artikeln visar hur du snabbt använder en inbyggd övervaknings-RBAC-roll på en användare i Azure eller skapar din egen anpassade roll för en användare som behöver begränsade övervakningsbehörigheter. Den diskuterar sedan säkerhetsaspekter för dina Azure Monitor-relaterade resurser och hur du kan begränsa åtkomsten till de data de innehåller.

## <a name="built-in-monitoring-roles"></a>Inbyggda övervakningsroller
Azure Monitors inbyggda roller är utformade för att begränsa åtkomsten till resurser i en prenumeration samtidigt som de som ansvarar för övervakning av infrastrukturen kan hämta och konfigurera de data de behöver. Azure Monitor innehåller två färdiga roller: En övervakningsläsare och en övervakningsdeltagare.

### <a name="monitoring-reader"></a>Övervakningsläsare
Personer som tilldelats rollen Övervakningsläsare kan visa alla övervakningsdata i en prenumeration men kan inte ändra någon resurs eller redigera inställningar som är relaterade till övervakningsresurser. Den här rollen är lämplig för användare i en organisation, till exempel support- eller drifttekniker, som behöver kunna:

* Visa övervakningsinstrumentpaneler i portalen och skapa egna privata övervakningsinstrumentpaneler.
* Visa varningsregler som definierats i [Azure-aviseringar](alerts-overview.md)
* Fråga efter mått med hjälp av [AZURE Monitor REST API](https://msdn.microsoft.com/library/azure/dn931930.aspx), [PowerShell-cmdlets](powershell-quickstart-samples.md)eller [CLI över flera plattformar](cli-samples.md).
* Fråga aktivitetsloggen med hjälp av portalen, AZURE Monitor REST API, PowerShell-cmdlets eller CLI över flera plattformar.
* Visa [diagnostikinställningarna](diagnostic-settings.md) för en resurs.
* Visa [loggprofilen](activity-log-export.md) för en prenumeration.
* Visa inställningar för automatisk skalning.
* Visa varningsaktivitet och inställningar.
* Få tillgång till programstatistikdata och visa data i AI Analytics.
* Sök logganalysarbetsytadata inklusive användningsdata för arbetsytan.
* Visa hanteringsgrupper för logganalys.
* Hämta sökschemat på Logganalysarbetsytan.
* Lista övervakningspaket i Log Analytics-arbetsytan.
* Hämta och kör sparade sökningar på Log Analytics-arbetsytan.
* Hämta lagringskonfigurationen för Logganalysarbetsyta.

> [!NOTE]
> Den här rollen ger inte läsbehörighet till loggdata som har strömmats till en händelsenav eller lagrats i ett lagringskonto. [Se nedan](#security-considerations-for-monitoring-data) för information om hur du konfigurerar åtkomst till dessa resurser.
> 
> 

### <a name="monitoring-contributor"></a>Övervakningsbidragsgivare
Personer som tilldelats rollen Övervakningsdeltagare kan visa alla övervakningsdata i en prenumeration och skapa eller ändra övervakningsinställningar, men kan inte ändra några andra resurser. Den här rollen är en superuppsättning av rollen Övervakningsläsare och är lämplig för medlemmar i en organisations övervakningsteam eller hanterade tjänsteleverantörer som, utöver behörigheterna ovan, också måste kunna:

* Publicera övervakningsinstrumentpaneler som en delad instrumentpanel.
* Ange [diagnostikinställningar](diagnostic-settings.md) för en resurs.\*
* Ange [loggprofilen](activity-log-export.md) för en prenumeration.\*
* Ange aktivitet och inställningar för varningsregler via [Azure-aviseringar](alerts-overview.md).
* Skapa programinsikter webbtester och komponenter.
* Delade nycklar för listlogganalys.
* Aktivera eller inaktivera övervakningspaket på Log Analytics-arbetsytan.
* Skapa och ta bort sparade sökningar på Log Analytics-arbetsytan.
* Skapa och ta bort lagringskonfigurationen för Logganalysarbetsyta.

\*användaren måste också beviljas listnycklar behörighet för separat för målresursen (lagringskonto eller händelsehubbnamnområde) för att ange en loggprofil eller diagnostikinställning.

> [!NOTE]
> Den här rollen ger inte läsbehörighet till loggdata som har strömmats till en händelsenav eller lagrats i ett lagringskonto. [Se nedan](#security-considerations-for-monitoring-data) för information om hur du konfigurerar åtkomst till dessa resurser.
> 
> 

## <a name="monitoring-permissions-and-custom-rbac-roles"></a>Övervakningsbehörigheter och anpassade RBAC-roller
Om ovanstående inbyggda roller inte uppfyller teamets exakta behov kan du [skapa en anpassad RBAC-roll](../../role-based-access-control/custom-roles.md) med mer detaljerade behörigheter. Nedan följer de vanliga RBAC-åtgärderna för Azure Monitor med sina beskrivningar.

| Åtgärd | Beskrivning |
| --- | --- |
| Microsoft.Insights/ActionGroups/[Läs, skriv, ta bort] |Läs/skriv/ta bort åtgärdsgrupper. |
| Microsoft.Insights/ActivityLogAlerts/[Läs, Skriv, Ta bort] |Läs/skriv/ta bort aktivitetsloggvarningar. |
| Microsoft.Insights/AlertRules/[Läs, skriv, ta bort] |Läs/skriv/ta bort varningsregler (från aviseringar klassiska). |
| Microsoft.Insights/AlertRules/Incidents/Read |Lista incidenter (historik över varningsregeln som utlöses) för varningsregler. Detta gäller endast portalen. |
| Microsoft.Insights/AutoscaleSettings/[Läs, Skriv, Ta bort] |Läs/skriv/ta bort inställningar för automatisk skalning. |
| Microsoft.Insights/DiagnosticSettings/[Läs, Skriv, Ta bort] |Läs/skriv/ta bort diagnostikinställningar. |
| Microsoft.Insights/EventCategories/Läs |Räkna upp alla kategorier som är möjliga i aktivitetsloggen. Används av Azure-portalen. |
| Microsoft.Insights/eventtypes/digestevents/Read |Den här behörigheten är nödvändig för användare som behöver åtkomst till aktivitetsloggar via portalen. |
| Microsoft.Insights/eventtypes/values/Read |Lista aktivitetslogghändelser (hanteringshändelser) i en prenumeration. Den här behörigheten gäller både programmatisk och portalåtkomst till aktivitetsloggen. |
| Microsoft.Insights/ExtendedDiagnosticSettings/[Läs, Skriv, Ta bort] | Läs/skriv/ta bort diagnostikinställningar för nätverksflödesloggar. |
| Microsoft.Insights/LogDefinitions/Läs |Den här behörigheten är nödvändig för användare som behöver åtkomst till aktivitetsloggar via portalen. |
| Microsoft.Insights/LogProfiles/[Läs, Skriv, Ta bort] |Läs/skriv/ta bort loggprofiler (strömmande aktivitetslogg till händelsenav eller lagringskonto). |
| Microsoft.Insights/MetricAlerts/[Läs, Skriv, Ta bort] |Läs/skriva/ta bort måttaviseringar i nära realtid |
| Microsoft.Insights/MetricDefinitions/Read |Läs måttdefinitioner (lista över tillgängliga måtttyper för en resurs). |
| Microsoft.Insights/Metrics/Read |Läs mått för en resurs. |
| Microsoft.Insights/Register/Åtgärd |Registrera Azure Monitor-resursleverantören. |
| Microsoft.Insights/ScheduledQueryRules/[Läs, skriv, ta bort] |Läs/skriv/ta bort loggaviseringar i Azure Monitor. |



> [!NOTE]
> Åtkomst till aviseringar, diagnostikinställningar och mått för en resurs kräver att användaren har läsbehörighet till resurstypen och resursens omfattning. Skapa ("skriv") en diagnostisk inställning eller loggprofil som arkiv till ett lagringskonto eller strömmar till händelsehubbar kräver att användaren också har ListKeys-behörighet på målresursen.
> 
> 

Med hjälp av tabellen ovan kan du till exempel skapa en anpassad RBAC-roll för en "Aktivitetsloggläsare" så här:

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

## <a name="security-considerations-for-monitoring-data"></a>Säkerhetsöverväganden vid övervakning av data
Övervakningsdata – särskilt loggfiler – kan innehålla känslig information, till exempel IP-adresser eller användarnamn. Övervakning av data från Azure finns i tre grundläggande formulär:

1. Aktivitetsloggen, som beskriver alla kontrollplansåtgärder på din Azure-prenumeration.
2. resursloggar, som är loggar som avges av en resurs.
3. Mått, som släpps ut av resurser.

Alla tre av dessa datatyper kan lagras i ett lagringskonto eller strömmas till Event Hub, som båda är Azure-resurser för allmänt syfte. Eftersom det här är allmänna resurser är det en privilegierad åtgärd som är reserverad för en administratör att skapa, ta bort och komma åt dem. Vi föreslår att du använder följande metoder för övervakningsrelaterade resurser för att förhindra missbruk:

* Använd ett enda dedikerat lagringskonto för övervakning av data. Om du behöver separera övervakningsdata i flera lagringskonton, dela aldrig användningen av ett lagringskonto mellan övervaknings- och icke-övervakningsdata, eftersom detta av misstag kan ge dem som bara behöver åtkomst till övervakningsdata (till exempel en tredje parts SIEM) tillgång till icke-övervakningsdata.
* Använd ett enda, dedikerat servicebuss- eller eventhubbnamnområde över alla diagnostikinställningar av samma anledning som ovan.
* Begränsa åtkomsten till övervakningsrelaterade lagringskonton eller händelsehubbar genom att behålla dem i en separat resursgrupp och [använd scope](../../role-based-access-control/overview.md#scope) på dina övervakningsroller för att begränsa åtkomsten till endast den resursgruppen.
* Bevilja aldrig ListKeys-behörigheten för antingen lagringskonton eller händelsehubbar i prenumerationsomfattning när en användare bara behöver åtkomst till övervakningsdata. Ge i stället dessa behörigheter till användaren på en resurs eller resursgrupp (om du har ett dedikerat övervakningsresursgrupp) omfång.

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Begränsa åtkomsten till övervakningsrelaterade lagringskonton
När en användare eller ett program behöver åtkomst till övervakning av data i ett lagringskonto bör du [generera ett konto-SAS](https://msdn.microsoft.com/library/azure/mt584140.aspx) på lagringskontot som innehåller övervakningsdata med skrivskyddad åtkomst på tjänstnivå till blob-lagring. I PowerShell kan det se ut som:

```powershell
$context = New-AzStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Du kan sedan ge token till entiteten som behöver läsa från det lagringskontot, och den kan lista och läsa från alla blobbar i det lagringskontot.

Om du behöver styra den här behörigheten med RBAC kan du också bevilja den entiteten microsoft.storageAccounts/listkeys/action-behörigheten för just det lagringskontot. Detta är nödvändigt för användare som behöver kunna ställa in en diagnostikinställning eller loggprofil för att arkivera ett lagringskonto. Du kan till exempel skapa följande anpassade RBAC-roll för en användare eller ett program som bara behöver läsa från ett lagringskonto:

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
> Med behörigheten ListKeys kan användaren visa de primära och sekundära lagringskontonycklarna. Dessa nycklar ger användaren alla signerade behörigheter (läsa, skriva, skapa blobbar, ta bort blobbar, etc.) över alla signerade tjänster (blob, kö, tabell, fil) i det lagringskontot. Vi rekommenderar att du använder ett konto SAS som beskrivs ovan när det är möjligt.
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Begränsa åtkomsten till övervakningsrelaterade händelsehubbar
Ett liknande mönster kan följas med händelsehubbar, men först måste du skapa en dedikerad lyssna auktoriseringsregel. Om du vill bevilja, tillgång till ett program som bara behöver lyssna på övervakningsrelaterade händelsehubbar gör du följande:

1. Skapa en princip för delad åtkomst på de händelsehubbar som skapades för strömmande övervakningsdata med endast Lyssna anspråk. Detta kan göras i portalen. Du kan till exempel kalla det "monitoringReadOnly". Om möjligt kommer du vill ge den nyckeln direkt till konsumenten och hoppa över nästa steg.
2. Om konsumenten behöver kunna hämta nyckeln ad hoc ger du användaren åtgärden ListKeys för händelsehubben. Detta är också nödvändigt för användare som behöver kunna ställa in en diagnostikinställning eller loggprofil för att strömma till händelsehubbar. Du kan till exempel skapa en RBAC-regel:
   
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

## <a name="monitoring-within-a-secured-virtual-network"></a>Övervakning inom ett skyddat virtuellt nätverk

Azure Monitor behöver åtkomst till dina Azure-resurser för att tillhandahålla de tjänster du aktiverar. Om du vill övervaka dina Azure-resurser samtidigt som du skyddar dem från åtkomst till det offentliga Internet kan du aktivera följande inställningar.

### <a name="secured-storage-accounts"></a>Konton för skyddad lagring 

Övervakningsdata skrivs ofta till ett lagringskonto. Du kanske vill vara säker på att data som kopieras till ett lagringskonto inte kan nås av obehöriga användare. För ytterligare säkerhet kan du låsa nätverksåtkomsten så att endast dina auktoriserade resurser och betrodda Microsoft-tjänster får åtkomst till ett lagringskonto genom att begränsa ett lagringskonto så att det använder "valda nätverk".
![Azure Storage](./media/roles-permissions-security/secured-storage-example.png) Settings Dialog Azure Monitor anses vara en av dessa "betrodda Microsoft-tjänster" Om du tillåter betrodda Microsoft-tjänster att komma åt din säkra lagringsskärm har Azure-övervakaren åtkomst till ditt säkra lagringskonto. aktivera skrivning av Azure Monitor-resursloggar, aktivitetslogg och mått till ditt lagringskonto under dessa skyddade villkor. Detta gör det också möjligt för Log Analytics att läsa loggar från säker lagring.   


Mer information finns i [Nätverkssäkerhet och Azure Storage](../../storage/common/storage-network-security.md)

## <a name="next-steps"></a>Nästa steg
* [Läs om RBAC och behörigheter i Resource Manager](../../role-based-access-control/overview.md)
* [Läs översikten över övervakning i Azure](../../azure-monitor/overview.md)


