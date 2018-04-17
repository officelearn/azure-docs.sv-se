---
title: Kom igång med roller, behörigheter och säkerhet med Azure-Monitor | Microsoft Docs
description: Lär dig hur du använder Azure-Monitor inbyggda roller och behörigheter för att begränsa åtkomsten till övervakning resurser.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 2686e53b-72f0-4312-bcd3-3dc1b4a9b912
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: johnkem
ms.openlocfilehash: 248d45a59fa2769c4cfcc4b169bd9e61059f11b0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="get-started-with-roles-permissions-and-security-with-azure-monitor"></a>Kom igång med roller, behörigheter och säkerhet med Azure-Monitor
Många grupper behöver strikt reglera åtkomst till övervakningsdata och inställningar. Till exempel om du har gruppmedlemmar som arbetar enbart om hur du övervakar (supporttekniker, devops engineers) eller om du använder en leverantör av hanterade tjänster, kanske du vill bevilja dem åtkomst till endast övervakningsdata samtidigt begränsa deras möjlighet att skapa, ändra, eller ta bort resurser. Den här artikeln visar hur du snabbt vill använda en inbyggd övervakning RBAC roll till en användare i Azure eller skapa egna anpassade roll för en användare behöver begränsade behörigheter för övervakning. Sedan diskuterar det säkerhetsaspekter för dina Azure-Monitor-relaterade resurser och hur du kan begränsa åtkomst till de data som de innehåller.

## <a name="built-in-monitoring-roles"></a>Inbyggda övervakning roller
Azure övervakaren inbyggda roller är utformade för att begränsa åtkomsten till resurser i en prenumeration samtidigt som de ansvariga för övervakning av infrastruktur för att hämta och konfigurera data de behöver. Azure övervakning ger två out box-roller: en övervakning läsare och deltagare övervakning.

### <a name="monitoring-reader"></a>Övervaka läsare
Personer som har rollen som läsare övervakning kan visa alla övervakningsdata i en prenumeration men kan inte ändra en resurs eller redigera några inställningar som rör övervakning resurser. Den här rollen är lämplig för användare i en organisation, till exempel support eller åtgärder tekniker, som behöver kunna:

* Visa övervakning instrumentpaneler i portalen och skapa sina egna privata övervakning instrumentpaneler.
* Visa Varningsregler som definierats i [Azure aviseringar](monitoring-overview-unified-alerts.md)
* Frågan för mått med hjälp av den [REST-API för Azure-Monitor](https://msdn.microsoft.com/library/azure/dn931930.aspx), [PowerShell-cmdlets](insights-powershell-samples.md), eller [plattformsoberoende CLI](insights-cli-samples.md).
* Fråga aktivitetsloggen med hjälp av portalen, Azure övervakaren REST API: et, PowerShell-cmdlets eller plattformsoberoende CLI.
* Visa den [diagnostikinställningar](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) för en resurs.
* Visa den [logga profil](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile) för en prenumeration.
* Visa Autoskala inställningar.
* Visa avisering aktivitet och inställningar.
* Åtkomst till Application Insights-data och visa data i AI Analytics.
* Sök logganalys arbetsytan data inklusive användningsdata för arbetsytan.
* Visa logganalys hanteringsgrupper.
* Hämta schemat för logganalys-sökning.
* Lista över logganalys intelligence Pack.
* Hämta och köra logganalys sparade sökningar.
* Hämta konfigurationen för logganalys-lagring.

> [!NOTE]
> Den här rollen ger inte läsbehörighet till loggdata som strömmas till en händelsehubb eller lagras i ett lagringskonto. [Se nedan](#security-considerations-for-monitoring-data) information om hur du konfigurerar åtkomst till dessa resurser.
> 
> 

### <a name="monitoring-contributor"></a>Övervaka deltagare
Personer som har tilldelats rollen övervakning deltagare kan visa alla övervakningsdata i en prenumeration och skapa eller ändra inställningar för övervakning, men det går inte att ändra andra resurser. Den här rollen är en supermängd övervakning läsare och är lämplig för medlemmar i en organisation övervakning team eller hanterade tjänsteleverantörer, förutom behörigheterna som ovan, måste också kunna:

* Publicera övervakning instrumentpaneler som en delad instrumentpanel.
* Ange [diagnostikinställningar](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) för en resource.*
* Ange den [logga profil](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile) för en subscription.*
* Ställ in Varningsregler aktivitet och inställningar via [Azure aviseringar](monitoring-overview-unified-alerts.md).
* Skapa webbtester med Application Insights och komponenter.
* Lista logganalys-arbetsytan delade nycklar.
* Aktivera eller inaktivera logganalys intelligence Pack.
* Skapa och ta bort och köra logganalys sparade sökningar.
* Skapa och ta bort konfigurationen för logganalys-lagring.

* användaren måste också ha behörigheten ListKeys på målresursen (storage-konto eller händelse hubb namnområde) att ställa in en logg profil eller diagnostikinställningen.

> [!NOTE]
> Den här rollen ger inte läsbehörighet till loggdata som strömmas till en händelsehubb eller lagras i ett lagringskonto. [Se nedan](#security-considerations-for-monitoring-data) information om hur du konfigurerar åtkomst till dessa resurser.
> 
> 

## <a name="monitoring-permissions-and-custom-rbac-roles"></a>Övervaka behörigheter och anpassade RBAC-roller
Om ovanstående inbyggda roller inte passar exakt ditt team, kan du [skapa en anpassad roll RBAC](../role-based-access-control/custom-roles.md) med mer detaljerade behörigheter. Nedan visas vanliga Azure övervakaren RBAC-åtgärder med deras beskrivningar.

| Åtgärd | Beskrivning |
| --- | --- |
| Microsoft.Insights/ActionGroups/[Read, Write, Delete] |Läs/Skriv/ta bort åtgärdsgrupper. |
| Microsoft.Insights/ActivityLogAlerts/[Read, Write, Delete] |Läs/Skriv/ta bort aktivitet loggen aviseringar. |
| Microsoft.Insights/AlertRules/[Read, Write, Delete] |Läs/Skriv/ta bort Varningsregler (från aviseringar klassiska). |
| Microsoft.Insights/AlertRules/Incidents/Read |Lista över incidenter (historik över varningsregeln som utlöses) för Varningsregler. Detta gäller endast för portalen. |
| Microsoft.Insights/AutoscaleSettings/[Read, Write, Delete] |Läs/Skriv/ta bort Autoskala inställningar. |
| Ta bort Microsoft.Insights/DiagnosticSettings/[Read, skriva] |Diagnostikinställningar för Läs/Skriv/ta bort. |
| Microsoft.Insights/EventCategories/Read |Räkna upp alla kategorier som är möjliga i aktivitetsloggen. Används av Azure-portalen. |
| Microsoft.Insights/eventtypes/digestevents/Read |Den här behörigheten krävs för användare som behöver åtkomst till aktivitetsloggar via portalen. |
| Microsoft.Insights/eventtypes/values/Read |Lista över aktivitetsloggen händelser (management-händelser) i en prenumeration. Den här behörigheten gäller både programmässiga och portal åtkomst till aktivitetsloggen. |
| Ta bort Microsoft.Insights/ExtendedDiagnosticSettings/[Read, skriva] | Läs/Skriv/ta bort diagnostikinställningar för nätverket flödet loggar. |
| Microsoft.Insights/LogDefinitions/Read |Den här behörigheten krävs för användare som behöver åtkomst till aktivitetsloggar via portalen. |
| Ta bort Microsoft.Insights/LogProfiles/[Read, skriva] |Läs/Skriv/ta bort loggen profiler (streaming aktivitetsloggen till händelse-hubb eller lagring). |
| Ta bort Microsoft.Insights/MetricAlerts/[Read, skriva] |Läs/Skriv/ta bort nära realtid mått aviseringar |
| Microsoft.Insights/MetricDefinitions/Read |Läs måttdefinitionerna (lista över tillgängliga mått typer för en resurs). |
| Microsoft.Insights/Metrics/Read |Läsa måtten för en resurs. |
| Microsoft.Insights/Register/Action |Registerresursleverantören Azure-Monitor. |
| Microsoft.Insights/ScheduledQueryRules/[Read, Write, Delete] |Läs/Skriv/ta bort loggen aviseringar för Application Insights. |



> [!NOTE]
> Åtkomst till aviseringar, diagnostikinställningar och mått för en resurs som kräver att användaren har läsbehörighet till resurstyp och omfånget för den här resursen. En inställning eller loggfil diagnostikprofil som arkiveras till ett lagringskonto eller dataströmmar i händelsehubbar kräver skapar (”write”) att användaren har behörighet att ListKeys även på målresursen.
> 
> 

Till exempel använder tabellen ovan som du kan skapa en anpassad RBAC-roll för en ”aktivitet Händelseloggläsare” så här:

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzureRmRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>Säkerhetsaspekter för övervakning av data
Övervakningsdata – särskilt loggfiler, kan innehålla känslig information, till exempel IP-adresser eller användarnamn. Övervakningsdata från Azure levereras i tre grundläggande formulär:

1. Aktivitetsloggen, som beskriver alla kontroll-plan åtgärder på Azure-prenumerationen.
2. Diagnostikloggar som loggar som orsakat av en resurs.
3. Mått som orsakat av resurser.

Alla tre av följande datatyper kan lagras i ett lagringskonto eller strömmas till Event Hub, vilka båda allmänna Azure-resurser. Eftersom de är allmänna resurser är att skapa, ta bort och att nå dem en privilegierad åtgärd som reserverats för en administratör. Vi rekommenderar att du använder följande metoder för övervakning-relaterade resurser för att förhindra missbruk:

* Använd ett enda, särskilt storage-konto för övervakningsdata. Om du behöver dela övervakningsdata i flera lagringskonton Dela aldrig användning av ett lagringskonto mellan övervakning och icke-övervakning av data, som det av misstag kan ge dem som bara behöver åtkomst till övervakningsdata (till exempel en SIEM från tredje part) åtkomst till icke-övervakning av data.
* Använd ett enda, särskilt Service Bus eller Event Hub namnområde i alla diagnostikinställningar av samma skäl som ovan.
* Begränsa åtkomsten till relaterade till prestandaövervakning storage-konton eller event hubs genom att hålla dem i en separat resursgrupp och [använda omfång](../role-based-access-control/overview.md#basics-of-access-management-in-azure) på din övervakning roller för att begränsa åtkomsten till endast resursgruppen.
* Tilldela behörigheten ListKeys för storage-konton eller händelsehubbar definitionsområdet prenumerationen aldrig när en användare bara behöver åtkomst till övervakningsdata. I stället ge dessa behörigheter till användare på en resurs eller resursgrupp (om du har en särskild övervakning resursgrupp) omfång.

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Begränsa åtkomsten till relaterade till prestandaövervakning storage-konton
När en användare eller ett program behöver tillgång till övervakningsdata i ett lagringskonto, bör du [Generera en konto-SAS](https://msdn.microsoft.com/library/azure/mt584140.aspx) på lagringskontot som innehåller övervakningsdata med servicenivåer skrivskyddad åtkomst till blob storage. Detta kan se ut i PowerShell:

```powershell
$context = New-AzureStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzureStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Du kan ge token till entiteten att måste för att läsa från den lagringen konto och den kan listan och läsa från alla blobbar i det storage-kontot.

Alternativt, om du vill kontrollera den här behörigheten med RBAC du kan ge entiteten Microsoft.Storage/storageAccounts/listkeys/action behörighet för att viss storage-konto. Detta är nödvändigt för användare som behöver för att kunna ställa in en diagnostiska inställning eller logga profilen för att arkivera till ett lagringskonto. Du kan till exempel skapa följande anpassade RBAC roll för en användare eller program som behöver bara läsa från ett lagringskonto:

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzureRmRoleDefinition -Role $role 
```

> [!WARNING]
> ListKeys tillåter användare att visa en lista med primära och sekundära lagringskontonycklar. De här nycklarna bevilja användaren alla signerade behörigheter (läsa, skriva, skapa BLOB, ta bort blobbar osv) för alla signerade tjänster (blob, kön, tabell, fil) i detta lagringskonto. Vi rekommenderar att du använder en konto-SAS ovan när det är möjligt.
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Begränsa åtkomsten till relaterade till prestandaövervakning händelsehubbar
Ett liknande mönster kan följas av händelsehubbar, men du måste först skapa en dedikerad lyssna auktoriseringsregeln. Om du vill bevilja åtkomst till ett program som endast ska lyssna på relaterade till prestandaövervakning händelsehubbar gör du följande:

1. Skapa en princip för delad åtkomst på de händelse-nav som har skapats för strömning övervakningsdata med endast lyssna anspråk. Detta kan göras i portalen. T.ex, kan du anropa den ”monitoringReadOnly”. Om det är möjligt ska du ge nyckeln direkt till konsumenten och hoppa över nästa steg.
2. Om klienten behöver för att kunna hämta den viktiga ad hoc-, bevilja användaren ListKeys åtgärd för att händelsehubb. Detta är också nödvändigt för användare som behöver för att kunna ställa in en diagnostiska inställning eller logga profil till en dataström i händelsehubbar. Du kan till exempel skapa en regel för RBAC:
   
   ```powershell
   $role = Get-AzureRmRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzureRmRoleDefinition -Role $role 
   ```

## <a name="next-steps"></a>Nästa steg
* [Läs mer om RBAC och behörigheter i Resource Manager](../role-based-access-control/overview.md)
* [Läs översikt över övervakning i Azure](monitoring-overview.md)

