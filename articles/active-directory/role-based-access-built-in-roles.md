---
title: "Åtgärder och NotActions - Azure rollbaserad åtkomstkontroll (RBAC) | Microsoft Docs"
description: "Det här avsnittet beskriver den inbyggda i roller för rollbaserad åtkomstkontroll (RBAC). Rollerna läggs till kontinuerligt, så kontrollera dokumentationen uppdateringen."
services: active-directory
documentationcenter: 
author: rolyon
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 02/23/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: it-pro
ms.openlocfilehash: e49f555b2ae972cd3a0437fc44d2331aaeb5e955
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/24/2018
---
# <a name="built-in-roles-for-azure-role-based-access-control"></a>Inbyggda roller för rollbaserad åtkomstkontroll i Azure
Azure rollbaserad åtkomstkontroll (RBAC) levereras med följande inbyggda roller som kan tilldelas användare, grupper och tjänster. Du kan inte ändra definitionerna av inbyggda roller. Du kan dock skapa [anpassade roller i Azure RBAC](role-based-access-control-custom-roles.md) så att den passar de specifika behoven i din organisation.

## <a name="roles-in-azure"></a>Roller i Azure
Följande tabell innehåller en kort beskrivning av de inbyggda rollerna. Klicka på rollnamnet att se en detaljerad lista över **åtgärder** och **notactions** för rollen. Den **åtgärder** egenskapen anger tillåtna åtgärder på Azure-resurser. Åtgärden strängar kan använda jokertecken. Den **notactions** egenskapen anger vilka åtgärder som är exkluderade från de tillåtna åtgärderna.

Åtgärden definierar vilken typ av åtgärder som du kan utföra på en viss resurstyp. Exempel:
- **Skriva** kan du utföra PUT, POST, KORRIGERINGSFIL och DELETE-åtgärder.
- **Läs** kan du utföra GET-åtgärder.

Den här artikeln tar endast de olika roller som finns idag. När du tilldelar en roll till en användare, men kan du begränsa de tillåtna åtgärderna ytterligare genom att definiera ett omfång. Det här är användbart om du vill ange någon webbplats deltagare, men endast för en resursgrupp.

> [!NOTE]
> Azure rolldefinitioner utvecklas hela tiden. Den här artikeln är uppdaterade som som möjligt, men du kan alltid hitta de senaste definitionerna för roller i Azure PowerShell. Använd den [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) för att visa en lista över alla aktuella roller. Du kan sätta igång till en viss roll med hjälp av `(get-azurermroledefinition "<role name>").actions` eller `(get-azurermroledefinition "<role name>").notactions` i tillämpliga fall. Använd [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) till Liståtgärder av specifika Azure-resurs-providers.


| Rollnamn | Beskrivning |
| --- | --- |
| [API Management-tjänsten deltagare](#api-management-service-contributor) |Kan hantera API Management-tjänsten och de API: er |
| [Operatörsrollen för API Management-tjänsten](#api-management-service-operator-role) | Kan hantera API Management-tjänsten, men inte själva API |
| [Rollen för API Management-tjänsten läsare](#api-management-service-reader-role) | Skrivskyddad åtkomst till API Management-tjänsten och API: er |
| [Application Insights Component Contributor](#application-insights-component-contributor) |Kan hantera Application Insights-komponenter |
| [Automation-operatorn](#automation-operator) |Kan starta, stoppa, pausa och återuppta jobb |
| [Säkerhetskopiering deltagare](#backup-contributor) | Kan hantera säkerhetskopiering i Recovery Services-valvet |
| [Ansvarig för säkerhetskopiering](#backup-operator) | Kan hantera säkerhetskopia, förutom att ta bort säkerhetskopieringen i Recovery Services-valvet |
| [Säkerhetskopiering läsare](#backup-reader) | Kan visa alla tjänster för hantering av säkerhetskopiering  |
| [Fakturering läsare](#billing-reader) | Kan visa alla faktureringsinformationen  |
| [BizTalk-deltagare](#biztalk-contributor) |Kan hantera BizTalk-tjänst |
| [ClearDB MySQL DB-deltagare](#cleardb-mysql-db-contributor) |Kan hantera ClearDB MySQL-databaser |
| [Deltagare](#contributor) |Kan hantera allt utom åtkomst. |
| [Rollen för läsare av cosmos DB-konto](#cosmos-db-account-reader-role) |Kan läsa data i Azure DB som Cosmos-konto |
| [Data Factory deltagare](#data-factory-contributor) |Kan skapa och hantera datafabriker och underordnade resurser i dem. |
| [DevTest Labs användare](#devtest-labs-user) |Kan visa allt och ansluta, starta, -omstart och -avstängning virtuella datorer |
| [DNS-zonen deltagare](#dns-zone-contributor) |Kan hantera DNS-zoner och poster |
| [DocumentDB-konto deltagare](#documentdb-account-contributor) |Kan hantera Azure DB som Cosmos-konton |
| [Intelligenta System-kontot deltagare](#intelligent-systems-account-contributor) |Kan hantera intelligenta system konton |
| Logic App-deltagare | Kan hantera alla aspekter av en Logikapp, men inte skapa en ny. |
| Logic App-operatör |Kan starta och stoppa arbetsflöden som definierats inom en Logikapp. |
| [Övervaka läsare](#monitoring-reader) |Kan läsa övervakningsdata för alla |
| [Övervakning av deltagare](#monitoring-contributor) |Kan läsa övervakningsdata och redigera inställningarna för övervakning |
| [Nätverksdeltagare](#network-contributor) |Kan hantera alla nätverksresurser |
| [Ägare](#owner) |Kan hantera allt, inklusive åtkomst |
| [Reader](#reader) |Kan visa allt, men det går inte att göra ändringar |
| [Redis-Cache deltagare](#redis-cache-contributor) |Kan hantera Redis-cache |
| [Schemaläggaren jobbet samlingar deltagare](#scheduler-job-collections-contributor) |Kan hantera scheduler-jobbsamlingar |
| [Sök Service deltagare](#search-service-contributor) |Kan hantera search-tjänster |
| [Säkerhet Admin](#security-administrator) | I Security Center endast: Visa säkerhetsprinciper, visa säkerhetsstatus, redigera säkerhetsprinciper, Visa aviseringar och rekommendationerna, stänga aviseringar och rekommendationer |
| [Säkerhetshantering](#security-manager) | Kan hantera säkerhetskomponenter, säkerhetsprinciper och virtuella datorer |
| [Säkerhet läsare](#security-reader) | I Security Center endast: Visa rekommendationer och aviseringar, visa säkerhetsprinciper, visa säkerhetsstatus, men det går inte att göra ändringar |
| [Site Recovery-deltagare](#site-recovery-contributor) | Kan hantera Site Recovery på Recovery Services-valvet |
| [Site Recovery-operatorn](#site-recovery-operator) | Kan hantera redundans och återställning efter fel åtgärder Site Recovery i Recovery Services-valvet |
| [Site Recovery läsare](#site-recovery-reader) | Kan visa alla Site Recovery-hanteringsåtgärder  |
| [SQL DB-deltagare](#sql-db-contributor) |Kan hantera SQL-databaser, men inte deras säkerhetsrelaterade principer |
| [SQL-säkerhetsansvarig](#sql-security-manager) |Kan hantera de säkerhetsrelaterade principerna för SQL-servrar och databaser |
| [SQL Server-deltagare](#sql-server-contributor) |Kan hantera SQL-servrar och databaser, men inte deras säkerhetsrelaterade principer |
| [Klassiska Storage-konto deltagare](#classic-storage-account-contributor) |Kan hantera klassiska lagringskonton |
| [Storage-konto deltagare](#storage-account-contributor) |Kan hantera storage-konton |
| [Stöd för begäran deltagare](#support-request-contributor) | Skapa och hantera supportärenden |
| [Administratör för användaråtkomst](#user-access-administrator) |Kan hantera användarnas åtkomst till Azure-resurser |
| [Klassiska Virtual Machine-deltagare](#classic-virtual-machine-contributor) |Kan hantera klassiska virtuella datorer, men inte den virtuella nätverks- eller konto som de är anslutna |
| [Virtual Machine-deltagare](#virtual-machine-contributor) |Kan hantera virtuella datorer, men inte den virtuella nätverks- eller konto som de är anslutna |
| [Klassisk nätverksdeltagare](#classic-network-contributor) |Kan hantera klassiska virtuella nätverk och reserverade IP-adresser |
| [Web Plan deltagare](#web-plan-contributor) |Kan hantera web planer |
| [Webbplatsen deltagare](#website-contributor) |Kan hantera webbplatser, men inte alla web-planer som de är anslutna |

## <a name="role-permissions"></a>Rollbehörigheter
I följande tabeller beskrivs de specifika behörigheter som tilldelats varje roll. Detta kan inkludera **åtgärder**, som ger behörigheter och **NotActions**, som begränsar dem.

### <a name="api-management-service-contributor"></a>API Management-tjänstdeltagare
Kan hantera API Management-tjänster

| **Åtgärder** |  |
| --- | --- |
| Microsoft.ApiManagement/Service/* |Skapa och hantera API Management-tjänsten |
| Microsoft.Authorization/*/read |Läsa tillstånd |
| Microsoft.Insights/alertRules/* |Skapa och hantera aviseringsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read |Läs hälsotillståndet för resurser |
| Microsoft.Resources/deployments/* |Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read |Läs roller och rolltilldelningar |
| Microsoft.Support/* |Skapa och hantera supportärenden |

### <a name="api-management-service-operator-role"></a>Operatörsroll för API Management
Kan hantera API Management-tjänster

| **Åtgärder** |  |
| --- | --- |
| Microsoft.ApiManagement/Service/*/read | Läs API Management-tjänsten-instanser |
| Microsoft.ApiManagement/Service/backup/action | Säkerhetskopiera API Management-tjänsten till den angivna behållaren i en användare har angett storage-konto |
| Microsoft.ApiManagement/Service/delete | Ta bort en instans för API Management-tjänsten |
| Microsoft.ApiManagement/Service/managedeployments/action | Ändra SKU/enheter. Lägg till eller ta bort regionala distributioner av API Management-tjänsten |
| Microsoft.ApiManagement/Service/read | Läsa in metadata för en instans för API Management-tjänsten |
| Microsoft.ApiManagement/Service/restore/action | Återställa API Management-tjänsten från den angivna behållaren i en användare har angett storage-konto |
| Microsoft.ApiManagement/Service/updatehostname/action | Skapa, uppdatera eller ta bort anpassade domännamn för en API Management-tjänsten |
| Microsoft.ApiManagement/Service/write | Skapa en ny instans av API Management-tjänsten |
| Microsoft.Authorization/*/read |Läsa tillstånd |
| Microsoft.Insights/alertRules/* |Skapa och hantera aviseringsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read |Läs hälsotillståndet för resurser |
| Microsoft.Resources/deployments/* |Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read |Läs roller och rolltilldelningar |
| Microsoft.Support/* |Skapa och hantera supportärenden |

### <a name="api-management-service-reader-role"></a>Läsarroll för API Management-tjänst
Kan hantera API Management-tjänster

| **Åtgärder** |  |
| --- | --- |
| Microsoft.ApiManagement/Service/*/read | Läs API Management-tjänsten-instanser |
| Microsoft.ApiManagement/Service/read | Läsa in metadata för en instans för API Management-tjänsten |
| Microsoft.Authorization/*/read |Läsa tillstånd |
| Microsoft.Insights/alertRules/* |Skapa och hantera aviseringsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read |Läs hälsotillståndet för resurser |
| Microsoft.Resources/deployments/* |Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read |Läs roller och rolltilldelningar |
| Microsoft.Support/* |Skapa och hantera supportärenden |

### <a name="application-insights-component-contributor"></a>Application Insights-komponentdeltagare
Kan hantera Application Insights-komponenter

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Läs roller och rolltilldelningar |
| Microsoft.Insights/alertRules/* |Skapa och hantera aviseringsregler |
| Microsoft.Insights/components/* |Skapa och hantera Insights-komponenter |
| Microsoft.Insights/webtests/* |Skapa och hantera webbtest |
| Microsoft.ResourceHealth/availabilityStatuses/read |Läs hälsotillståndet för resurser |
| Microsoft.Resources/deployments/* |Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read |Läs resursgrupper |
| Microsoft.Support/* |Skapa och hantera supportärenden |

### <a name="automation-operator"></a>Automation-operatör
Kan starta, stoppa, pausa och återuppta jobb

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Läs roller och rolltilldelningar |
| Microsoft.Automation/automationAccounts/jobs/read |Läs automation-konto jobb |
| Microsoft.Automation/automationAccounts/jobs/resume/action |Återuppta ett jobb för automation-konto |
| Microsoft.Automation/automationAccounts/jobs/stop/action |Stoppa ett jobb för automation-konto |
| Microsoft.Automation/automationAccounts/jobs/streams/read |Läs automation konto jobbet strömmar |
| Microsoft.Automation/automationAccounts/jobs/suspend/action |Pausa ett jobb för automation-konto |
| Microsoft.Automation/automationAccounts/jobs/write |Skriva automation konto jobb |
| Microsoft.Automation/automationAccounts/jobSchedules/read |Läs ett Jobbschema för automation-konto |
| Microsoft.Automation/automationAccounts/jobSchedules/write |Läs ett Jobbschema för automation-konto |
| Microsoft.Automation/automationAccounts/read |Läs automation-konton |
| Microsoft.Automation/automationAccounts/runbooks/read |Läs automation-runbooks |
| Microsoft.Automation/automationAccounts/schedules/read |Läs automation-konto scheman |
| Microsoft.Automation/automationAccounts/schedules/write |Skriva automation konto scheman |
| Microsoft.Insights/components/* |Skapa och hantera Insights-komponenter |
| Microsoft.ResourceHealth/availabilityStatuses/read |Läs hälsotillståndet för resurser |
| Microsoft.Resources/deployments/* |Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read |Läs resursgrupper |
| Microsoft.Support/* |Skapa och hantera supportärenden |

### <a name="backup-contributor"></a>Säkerhetskopieringsmedarbetare
Kan hantera alla åtgärder för hantering av säkerhetskopiering, förutom att skapa Recovery Services-valvet och ge åtkomst till andra

| **Åtgärder** | |
| --- | --- |
| Microsoft.Network/virtualNetworks/read | Läs virtuella nätverk |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Hantera resultatet av åtgärden för hantering av säkerhetskopiering |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Skapa och hantera säkerhetskopiering behållare i säkerhetskopiering finns hos Recovery Services-valvet |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | Skapa och hantera säkerhetskopieringsjobb |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportera säkerhetskopieringsjobb till excel |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | Skapa och hantera meta-data som rör hantering av säkerhetskopiering |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Skapa och hantera resultaten av säkerhetskopiering hanteringsåtgärder |
| Microsoft.RecoveryServices/Vaults/backupPolicies/* | Skapa och hantera principer för säkerhetskopiering |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Skapa och hantera objekt som kan säkerhetskopieras |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Skapa och hantera säkerhetskopierade objekt |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Skapa och hantera behållare Säkerhetskopiera objekt |
| Microsoft.RecoveryServices/Vaults/certificates/* | Skapa och hantera certifikat som relaterar till säkerhetskopiering i Recovery Services-valvet |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | Skapa och hantera utökad information som rör valvet |
| Microsoft.RecoveryServices/Vaults/read | Läsa recovery services-valv |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | Hantera Identifieringsåtgärden för hämtning av nyskapad behållare |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Skapa och hantera registrerade identiteter |
| Microsoft.RecoveryServices/Vaults/usages/* | Skapa och hantera användningen av Recovery Services-valvet |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Läs resursgrupper |
| Microsoft.Storage/storageAccounts/read | Läs storage-konton |
| Microsoft.Support/* |Skapa och hantera supportärenden |

### <a name="backup-operator"></a>Säkerhetskopieringsoperatör
Kan hantera alla säkerhetskopiering hanteringsåtgärder förutom att skapa valv, ta bort säkerhetskopiering och ger åtkomst till andra

| **Åtgärder** | |
| --- | --- |
| Microsoft.Network/virtualNetworks/read | Läs virtuella nätverk |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Läs resultatet av åtgärden för hantering av säkerhetskopiering |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Läs åtgärden resulterar i skydd behållare |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Utföra säkerhetskopiering på begäran-åtgärden på ett säkerhetskopierade objekt |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Läs resultatet av åtgärden utförs på säkerhetskopierade objekt |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationStatus/read | Lässtatus för åtgärden utförs på säkerhetskopierade objekt |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Läs säkerhetskopierade objekt |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Läs återställningspunkt för ett säkerhetskopierade objekt |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Utför en återställning med hjälp av en återställningspunkt för ett säkerhetskopierade objekt |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Skapa ett objekt för säkerhetskopiering |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Läs behållare på Säkerhetskopiera objekt |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | Skapa och hantera säkerhetskopieringsjobb |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportera säkerhetskopieringsjobb till excel |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Läsa metadata som rör hantering av säkerhetskopiering |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Skapa och hantera resultaten av säkerhetskopiering hanteringsåtgärder |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Läs resultat av åtgärder som utförs på principer för säkerhetskopiering |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | Läsa principer för säkerhetskopiering |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Skapa och hantera objekt som kan säkerhetskopieras |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Läs säkerhetskopierade objekt |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Läs säkerhetskopierade behållare Säkerhetskopiera objekt |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Läsa utökade information som rör valvet |
| Microsoft.RecoveryServices/Vaults/extendedInformation/write | Skriva utökad information som rör valvet |
| Microsoft.RecoveryServices/Vaults/read | Läsa recovery services-valv |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | Hantera Identifieringsåtgärden för hämtning av nyskapad behållare |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Läs resultatet av åtgärden har utförts på objekten registrerad i valvet |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Läsa registrerade artiklar på valvet |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Skriva registrerade artiklar till valvet |
| Microsoft.RecoveryServices/Vaults/usages/read | Läs användning av Recovery Services-valvet |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Läs resursgrupper |
| Microsoft.Storage/storageAccounts/read | Läs storage-konton |
| Microsoft.Support/* | Skapa och hantera supportärenden |

### <a name="backup-reader"></a>Säkerhetskopieringsläsare
Kan övervaka säkerhetskopiering hantering i Recovery Services-valvet

| **Åtgärder** | |
| --- | --- |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read  | Läs resultatet av åtgärden för hantering av säkerhetskopiering |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read  | Läs åtgärden resulterar i skydd behållare |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read  | Läs resultatet av åtgärden utförs på säkerhetskopierade objekt |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationStatus/read  | Lässtatus för åtgärden utförs på säkerhetskopierade objekt |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read  | Läs säkerhetskopierade objekt |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read  | Läs behållare på Säkerhetskopiera objekt |
| Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read  | Läsa resultaten av säkerhetskopieringsjobb |
| Microsoft.RecoveryServices/Vaults/backupJobs/read  | Läs säkerhetskopieringsjobb |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportera säkerhetskopieringsjobb till excel |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read  | Läsa metadata som rör hantering av säkerhetskopiering |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/read  | Läs säkerhetskopiering hantering Åtgärdsresultat |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read  | Läs resultat av åtgärder som utförs på principer för säkerhetskopiering |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read  | Läsa principer för säkerhetskopiering |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read  |  Läs säkerhetskopierade objekt |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read  | Läs säkerhetskopierade behållare Säkerhetskopiera objekt |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read  | Läsa utökade information som rör valvet |
| Microsoft.RecoveryServices/Vaults/read  | Läsa recovery services-valv |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read  | Läsa resultatet av identifieringen för hämtning av nyskapad behållare |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read  | Läs resultatet av åtgärden har utförts på objekten registrerad i valvet |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read  | Läsa registrerade artiklar på valvet |
| Microsoft.RecoveryServices/Vaults/usages/read  |  Läs användning av Recovery Services-valvet |

### <a name="billing-reader"></a>Faktureringsläsare
Kan visa all information om fakturering

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Läs roller och rolltilldelningar |
| Microsoft.Billing/*/read |Läs faktureringsinformation |
| Microsoft.Support/* |Skapa och hantera supportärenden |

### <a name="biztalk-contributor"></a>BizTalk-deltagare
Kan hantera BizTalk-tjänst

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Läs roller och rolltilldelningar |
| Microsoft.BizTalkServices/BizTalk/* |Skapa och hantera BizTalk-tjänst |
| Microsoft.Insights/alertRules/* |Skapa och hantera aviseringsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read |Läs hälsotillståndet för resurser |
| Microsoft.Resources/deployments/* |Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read |Läs resursgrupper |
| Microsoft.Support/* |Skapa och hantera supportärenden |

### <a name="cleardb-mysql-db-contributor"></a>ClearDB MySQL DB-deltagare
Kan hantera ClearDB MySQL-databaser

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Läs roller och rolltilldelningar |
| Microsoft.Insights/alertRules/* |Skapa och hantera aviseringsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read |Läs hälsotillståndet för resurser |
| Microsoft.Resources/deployments/* |Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read |Läs resursgrupper |
| Microsoft.Support/* |Skapa och hantera supportärenden |
| successbricks.cleardb/databases/* |Skapa och hantera ClearDB MySQL-databaser |

### <a name="contributor"></a>Deltagare
Kan hantera allt utom åtkomst

| **Åtgärder** |  |
| --- | --- |
| * |Skapa och hantera resurser av alla typer av |

| **NotActions** |  |
| --- | --- |
| Microsoft.Authorization/*/Delete |Det går inte att ta bort roller och rolltilldelningar |
| Microsoft.Authorization/*/Write |Det går inte att skapa roller och rolltilldelningar |

### <a name="cosmos-db-account-reader-role"></a>Läsarroll för Cosmos DB-konto
Kan läsa Azure Cosmos DB kontodata. Se [DocumentDB-konto deltagare](#documentdb-account-contributor) för att hantera Azure DB som Cosmos-konton.

| **Åtgärder** |  |
| --- | --- |
|Microsoft.Authorization/*/read|Läs roller och rolltilldelningar kan läsbehörighet till alla användare|
|Microsoft.DocumentDB/*/read|Läs en samling|
|Microsoft.DocumentDB/databaseAccounts/readonlykeys/action|Läsa fönstret readonly-nycklar|
|Microsoft.Insights/Metrics/read|Läsa måtten för kontot|
|Microsoft.Insights/MetricDefinitions/read|Läs mått definitioner|
|Microsoft.Resources/subscriptions/resourceGroups/read|Läs resursgrupper|
|Microsoft.Support/*|Skapa och hantera supportärenden|

### <a name="data-factory-contributor"></a>Data Factory-deltagare
Skapa och hantera datafabriker och underordnade resurser i dem.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Läs roller och rollen tilldelningar |
| Microsoft.DataFactory/dataFactories/* |Skapa och hantera datafabriker och underordnade resurser i dem. |
| Microsoft.Insights/alertRules/* |Skapa och hantera aviseringsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read |Läs hälsotillståndet för resurser |
| Microsoft.Resources/deployments/* |Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read |Läs resursgrupper |
| Microsoft.Support/* |Skapa och hantera supportärenden |

### <a name="devtest-labs-user"></a>DevTest Labs-användare
Kan visa allt och ansluta, starta, -omstart och -avstängning virtuella datorer

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Läs roller och rollen tilldelningar |
| Microsoft.Compute/availabilitySets/read |Läsa egenskaperna för tillgänglighetsuppsättningar |
| Microsoft.Compute/virtualMachines/*/read |Läsa egenskaperna för en virtuell dator (VM-storlekar, Körningsstatus, VM-tillägg, etc.) |
| Microsoft.Compute/virtualMachines/deallocate/action |Frigöra virtuella datorer |
| Microsoft.Compute/virtualMachines/read |Läsa egenskaperna för en virtuell dator |
| Microsoft.Compute/virtualMachines/restart/action |Starta om virtuella datorer |
| Microsoft.Compute/virtualMachines/start/action |Starta virtuella datorer |
| Microsoft.DevTestLab/*/read |Läsa egenskaperna för ett labb |
| Microsoft.DevTestLab/labs/createEnvironment/action |Skapa en laboratoriemiljö |
| Microsoft.DevTestLab/labs/formulas/delete |Ta bort formler |
| Microsoft.DevTestLab/labs/formulas/read |Läs formler |
| Microsoft.DevTestLab/labs/formulas/write |Lägg till eller ändra formler |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action |Utvärdera principer för labbet |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action |Ansluta till en belastningsutjämnare adress serverdelspool |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action |Ansluta till en belastningsutjämnare inkommande NAT-regel |
| Microsoft.Network/networkInterfaces/*/read |Läsa egenskaperna för ett nätverksgränssnitt (till exempel alla belastningsutjämnare som nätverksgränssnittet tillhör) |
| Microsoft.Network/networkInterfaces/join/action |Anslut en virtuell dator till ett nätverksgränssnitt |
| Microsoft.Network/networkInterfaces/read |Läs nätverksgränssnitt |
| Microsoft.Network/networkInterfaces/write |Skriva nätverksgränssnitt |
| Microsoft.Network/publicIPAddresses/*/read |Läsa egenskaperna för en offentlig IP-adress |
| Microsoft.Network/publicIPAddresses/join/action |Ansluta till en offentlig IP-adress |
| Microsoft.Network/publicIPAddresses/read |Läs nätverket offentliga IP-adresser |
| Microsoft.Network/virtualNetworks/subnets/join/action |Ansluta ett virtuellt nätverk |
| Microsoft.Resources/deployments/operations/read |Läs distributionsåtgärder |
| Microsoft.Resources/deployments/read |Läs distributioner |
| Microsoft.Resources/subscriptions/resourceGroups/read |Läs resursgrupper |
| Microsoft.Storage/storageAccounts/listKeys/action |Lista nycklar för lagringskonto |

### <a name="dns-zone-contributor"></a>DNS-zondeltagare
Hantera DNS-zoner och poster.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/\*/read |Läs roller och rolltilldelningar |
| Microsoft.Insights/alertRules/\* |Skapa och hantera aviseringsregler |
| Microsoft.Network/dnsZones/\* |Skapa och hantera DNS-zoner och poster |
| Microsoft.ResourceHealth/availabilityStatuses/read |Läsa hälsotillståndet för resurser |
| Microsoft.Resources/deployments/\* |Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read |Läs resursgrupper |
| Microsoft.Support/\* |Skapa och hantera supportärenden |

### <a name="documentdb-account-contributor"></a>DocumentDB-kontodeltagare
Hantera Azure DB som Cosmos-konton. Azure Cosmos-DB är kallades DocumentDB.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Läs roller och rollen tilldelningar |
| Microsoft.DocumentDb/databaseAccounts/* |Skapa och hantera Azure DB som Cosmos-konton |
| Microsoft.Insights/alertRules/* |Skapa och hantera aviseringsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read |Läs hälsotillståndet för resurser |
| Microsoft.Resources/deployments/* |Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read |Läs resursgrupper |
| Microsoft.Support/* |Skapa och hantera supportärenden |

### <a name="intelligent-systems-account-contributor"></a>Intelligent Systems-kontodeltagare
Kan hantera intelligenta system konton

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Läs roller och rollen tilldelningar |
| Microsoft.Insights/alertRules/* |Skapa och hantera aviseringsregler |
| Microsoft.IntelligentSystems/accounts/* |Skapa och hantera intelligenta system konton |
| Microsoft.ResourceHealth/availabilityStatuses/read |Läs hälsotillståndet för resurser |
| Microsoft.Resources/deployments/* |Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read |Läs resursgrupper |
| Microsoft.Support/* |Skapa och hantera supportärenden |

### <a name="monitoring-reader"></a>Övervaka läsare
Kan läsa alla övervakningsdata (mått, loggar osv.). Se även [Kom igång med roller, behörigheter och säkerhet med Azure-Monitor](/monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles).

| **Åtgärder** |  |
| --- | --- |
| * / läsa |Läsa resurser av alla typer utom hemligheter. |
| Microsoft.OperationalInsights/workspaces/search/action |Search Log Analytics data |
| Microsoft.Support/* |Skapa och hantera supportärenden |

### <a name="monitoring-contributor"></a>Övervaka deltagare
Kan läsa alla övervakningsdata och redigera inställningarna för övervakning. Se även [Kom igång med roller, behörigheter och säkerhet med Azure-Monitor](/monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles).

| **Åtgärder** |  |
| --- | --- |
| * / läsa |Läsa resurser av alla typer utom hemligheter. |
| Microsoft.Insights/AlertRules/* |Läs/Skriv/ta bort Varningsregler. |
| Microsoft.Insights/components/* |Läs/Skriv/ta bort Application Insights-komponenter. |
| Microsoft.Insights/DiagnosticSettings/* |Diagnostikinställningar för Läs/Skriv/ta bort. |
| Microsoft.Insights/eventtypes/* |Lista över aktivitetsloggen händelser (management-händelser) i en prenumeration. Den här behörigheten gäller både programmässiga och portal åtkomst till aktivitetsloggen. |
| Microsoft.Insights/LogDefinitions/* |Den här behörigheten krävs för användare som behöver åtkomst till aktivitetsloggar via portalen. Lista loggen kategorier i aktivitetsloggen. |
| Microsoft.Insights/MetricDefinitions/* |Läs måttdefinitionerna (lista över tillgängliga mått typer för en resurs). |
| Microsoft.Insights/Metrics/* |Läsa måtten för en resurs. |
| Microsoft.Insights/Register/Action |Registrera providern Microsoft.Insights. |
| Microsoft.Insights/webtests/* |Läs/Skriv/ta bort Application Insights web tester. |
| Microsoft.OperationalInsights/workspaces/intelligencepacks/* |Läs/Skriv/ta bort logganalys-lösningen packs. |
| Microsoft.OperationalInsights/workspaces/savedSearches/* |Läs/Skriv/ta bort logganalys sparade sökningar. |
| Microsoft.OperationalInsights/workspaces/search/action |Sök logganalys arbetsytor. |
| Microsoft.OperationalInsights/workspaces/sharedKeys/action |Lista nycklar för logganalys-arbetsytan. |
| Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* |Läs/Skriv/ta bort logganalys insight lagringskonfigurationer. |

### <a name="network-contributor"></a>Nätverksdeltagare
Kan hantera alla nätverksresurser

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Läs roller och rollen tilldelningar |
| Microsoft.Insights/alertRules/* |Skapa och hantera aviseringsregler |
| Microsoft.Network/* |Skapa och hantera nätverk |
| Microsoft.ResourceHealth/availabilityStatuses/read |Läs hälsotillståndet för resurser |
| Microsoft.Resources/deployments/* |Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read |Läs resursgrupper |
| Microsoft.Support/* |Skapa och hantera supportärenden |

### <a name="owner"></a>Ägare
Kan hantera allt, inklusive åtkomst

| **Åtgärder** |  |
| --- | --- |
| * |Skapa och hantera resurser av alla typer av |

### <a name="reader"></a>Läsare
Kan visa allt, men det går inte att göra ändringar

| **Åtgärder** |  |
| --- | --- |
| * / läsa |Läsa resurser av alla typer utom hemligheter. |

### <a name="redis-cache-contributor"></a>Redis Cache-deltagare
Kan hantera Redis-cache

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Läs roller och rollen tilldelningar |
| Microsoft.Cache/redis/* |Skapa och hantera Redis-cache |
| Microsoft.Insights/alertRules/* |Skapa och hantera aviseringsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read |Läs hälsotillståndet för resurser |
| Microsoft.Resources/deployments/* |Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read |Läs resursgrupper |
| Microsoft.Support/* |Skapa och hantera supportärenden |

### <a name="scheduler-job-collections-contributor"></a>Scheduler-jobbsamlingsdeltagare
Kan hantera Scheduler-jobbsamlingar

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Läs roller och rollen tilldelningar |
| Microsoft.Insights/alertRules/* |Skapa och hantera aviseringsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read |Läs hälsotillståndet för resurser |
| Microsoft.Resources/deployments/* |Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read |Läs resursgrupper |
| Microsoft.Scheduler/jobcollections/* |Skapa och hantera jobbsamlingar |
| Microsoft.Support/* |Skapa och hantera supportärenden |

### <a name="search-service-contributor"></a>Söktjänstdeltagare
Kan hantera Search-tjänster

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Läs roller och rollen tilldelningar |
| Microsoft.Insights/alertRules/* |Skapa och hantera aviseringsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read |Läs hälsotillståndet för resurser |
| Microsoft.Resources/deployments/* |Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read |Läs resursgrupper |
| Microsoft.Search/searchServices/* |Skapa och hantera search-tjänster |
| Microsoft.Support/* |Skapa och hantera supportärenden |

### <a name="security-administrator"></a>Säkerhetsadministratör
I Security Center endast: Visa säkerhetsprinciper, visa säkerhetsstatus, redigera säkerhetsprinciper, Visa aviseringar och rekommendationerna, stänga aviseringar och rekommendationer

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Läs roller och rolltilldelningar |
| Microsoft.Authorization/policyAssignments/* | Skapa och hantera principtilldelningar |
| Microsoft.Authorization/policySetDefinitions/* | Skapa och hantera principen anger |
| Microsoft.Authorization/policyDefinitions/* | Skapa och hantera principdefinitioner |
| Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
| Microsoft.operationalInsights/workspaces/*/read | View Log Analytics data |
| Microsoft.Resources/deployments/* |Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read |Läs resursgrupper |
| Microsoft.Security/*/read | Läs säkerhetskomponenter och principer |
| Microsoft.Support/* |Skapa och hantera supportärenden |

### <a name="security-manager"></a>Säkerhetshanteraren
Kan hantera säkerhetskomponenter, säkerhetsprinciper och virtuella datorer

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Läs roller och rolltilldelningar |
| Microsoft.ClassicCompute/*/read |Läsa konfigurationsinformation klassiska virtuella datorer |
| Microsoft.ClassicCompute/virtualMachines/*/write |Spara konfigurationen för klassiska virtuella datorer |
| Microsoft.ClassicNetwork/*/read |Läsa konfigurationsinformation om klassiska nätverket |
| Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read |Läs hälsotillståndet för resurser |
| Microsoft.Resources/deployments/* |Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read |Läs resursgrupper |
| Microsoft.Security/* |Skapa och hantera säkerhetskomponenter och principer |
| Microsoft.Support/* |Skapa och hantera supportärenden |

### <a name="security-reader"></a>Säkerhetsläsare
I Security Center endast: Visa rekommendationer och aviseringar, visa säkerhetsprinciper, visa säkerhetsstatus, men det går inte att göra ändringar

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Läs roller och rolltilldelningar |
| Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
| Microsoft.operationalInsights/workspaces/*/read | View Log Analytics data |
| Microsoft.Resources/subscriptions/resourceGroups/read |Läs resursgrupper |
| Microsoft.Security/*/read | Läs säkerhetskomponenter och principer |
| Microsoft.Support/* |Skapa och hantera supportärenden |
| Microsoft.Resources/deployments/* |Skapa och hantera distributionen av resursgrupper |

### <a name="site-recovery-contributor"></a>Site Recovery-bidragsgivare
Kan hantera alla Site Recovery-hanteringsåtgärder, förutom att skapa Recovery Services-valvet och tilldela behörigheter till andra användare

| **Åtgärder** | |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
| Microsoft.Network/virtualNetworks/read | Läs virtuella nätverk |
| Microsoft.RecoveryServices/Vaults/certificates/write | Uppdaterar Autentiseringscertifikatet valvet |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | Skapa och hantera utökad information som rör valvet |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/*  | Läsa aviseringar för Recovery services-valvet |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/läsning  | Läs Recovery services-valvet meddelandekonfigurationen |
| Microsoft.RecoveryServices/Vaults/read | Läs Recovery Services-valv |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | Hantera Identifieringsåtgärden för hämtning av nyskapad behållare |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Skapa och hantera registrerade identiteter |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Skapa eller uppdatera replikeringsinställningarna för avisering |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Läsa replikeringshändelser |
| Microsoft.RecoveryServices/vaults/replicationFabrics/* | Skapa och hantera infrastrukturresurser för replikering |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | Skapa och hantera replikeringsjobb |
| Microsoft.RecoveryServices/vaults/replicationPolicies/* | Skapa och hantera replikeringsprinciper |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Skapa och hantera återställningsplaner |
| Microsoft.RecoveryServices/Vaults/storageConfig/* | Skapa och hantera konfigurationen av Recovery Services-valvet |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Läs Recovery Services-valvet tokeninformation |
| Microsoft.RecoveryServices/Vaults/usages/read | Läs användningsinformation om Recovery Services-valvet |
| Microsoft.ResourceHealth/availabilityStatuses/read | Läs hälsotillståndet för resurser |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Läs resursgrupper |
| Microsoft.Storage/storageAccounts/read | Läs storage-konton |
| Microsoft.Support/* |Skapa och hantera supportärenden |

### <a name="site-recovery-operator"></a>Site Recovery-operatör
Kan redundans och återställning efter fel kan, men inte utföra andra åtgärder för hantering av Site Recovery eller tilldela åtkomst till andra användare

| **Åtgärder** | |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
| Microsoft.Network/virtualNetworks/read | Läs virtuella nätverk |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Läsa utökade information som rör valvet |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/*  | Läsa aviseringar för Recovery services-valvet |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/läsning  | Läs Recovery services-valvet meddelandekonfigurationen |
| Microsoft.RecoveryServices/Vaults/read | Läs Recovery Services-valv |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | Hantera Identifieringsåtgärden för hämtning av nyskapad behållare |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Läser Åtgärdsstatus och resultat för en åtgärd som skickats |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Läsa behållare som har registrerats för en resurs |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Läsa replikering aviseringsinställningar |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Läsa replikeringshändelser |
| Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Kontrollera konsekvensen för fabric |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read | Läs replikering infrastrukturresurser |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ reassociateGateway/åtgärd | Koppla nytt replikerings-gateway |
| Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Förnya certifikat för replikering fabric |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Läsa replikering fabric-nätverk |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/läsning | Läs replikering fabric nätverksmappning |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/läsning | Läs skydd behållare |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/läsning | Hämta lista över alla skyddade objekt |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/åtgärd | Använda en specifik återställningspunkt |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/failoverCommit/åtgärd | Genomföra redundans för en misslyckad över objekt |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/plannedFailover/åtgärd | Starta planerad redundans för ett skyddat objekt |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/läsning | Hämta lista över alla skyddade objekt |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/läsning | Hämta lista över tillgängliga återställningspunkter |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/repairReplication/åtgärd | Reparera replikeringen av ett skyddat objekt |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/skyddar/åtgärd | Börja skydda igen för ett skyddat objekt|
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/testFailover/åtgärd | Starta testa redundans med ett skyddat objekt |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/åtgärd | Starta rensning av ett redundanstest |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/unplannedFailover/åtgärd | Starta oplanerad redundans för ett skyddat objekt |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/updateMobilityService/åtgärd | Uppdatera mobilitetstjänsten |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/läsning | Läsa skydd behållaren mappningar |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/läsning | Läs Recovery Services-providers |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/refreshProvider/åtgärd | Uppdatera Recovery Services-provider |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/läsning | Läs lagringsklassificeringar för replikering infrastrukturresurser |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/läsning | Läsa lagring klassificering mappningar |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Läs registrerad vCenter-information |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | Skapa och hantera replikeringsjobb |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read | Läs replikeringsprinciper |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/ failoverCommit/åtgärd | Genomföra redundans för redundant återställningsplan |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/ plannedFailover/åtgärd | Starta redundans för en återställningsplan |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Läs återställningsplaner |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Börja skydda igen av en återställningsplan |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Starta redundanstest av en återställningsplan |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/ testFailoverCleanup/åtgärd | Starta rensning av test för redundant återställningsplan |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/ unplannedFailover/åtgärd | Starta oplanerad redundans för en återställningsplan |
| Microsoft.RecoveryServices/Vaults/storageConfig/read | Läsa lagringskonfigurationen för av Recovery Services-valvet |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Läs Recovery Services-valvet tokeninformation |
| Microsoft.RecoveryServices/Vaults/usages/read | Läs användningsinformation om Recovery Services-valvet |
| Microsoft.ResourceHealth/availabilityStatuses/read | Läs hälsotillståndet för resurser |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Läs resursgrupper |
| Microsoft.Storage/storageAccounts/read | Läs storage-konton |
| Microsoft.Support/* | Skapa och hantera supportärenden |

### <a name="site-recovery-reader"></a>Site Recovery-läsare
Kan övervaka status för Site Recovery i Recovery Services-valvet och aktivera supportärenden

| **Åtgärder** | |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read  | Läsa utökade information som rör valvet |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read  | Läsa aviseringar för Recovery services-valvet |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/läsning  | Läs Recovery services-valvet meddelandekonfigurationen |
| Microsoft.RecoveryServices/Vaults/read  | Läs Recovery Services-valv |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read  | Hantera Identifieringsåtgärden för hämtning av nyskapad behållare |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read  | Läser Åtgärdsstatus och resultat för en åtgärd som skickats |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read  | Läsa behållare som har registrerats för en resurs |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Läsa replikering aviseringsinställningar |
| Microsoft.RecoveryServices/vaults/replicationEvents/read  | Läsa replikeringshändelser |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read  | Läs replikering infrastrukturresurser |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read  | Läsa replikering fabric-nätverk |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/läsning  | Läs replikering fabric nätverksmappning |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/läsning  |  Läs skydd behållare |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/läsning  | Hämta lista över alla skyddade objekt |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/läsning  | Hämta lista över alla skyddade objekt |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/läsning  | Hämta lista över tillgängliga återställningspunkter |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/läsning  | Läsa skydd behållaren mappningar |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/läsning  | Läs Recovery Services-providers |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/läsning  | Läs lagringsklassificeringar för replikering infrastrukturresurser |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/läsning  |  Läsa lagring klassificering mappningar |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read  |  Läs registrerad vCenter-information |
| Microsoft.RecoveryServices/vaults/replicationJobs/read  |  Lässtatus för replikeringsjobb |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read  |  Läs replikeringsprinciper |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read  |  Läs återställningsplaner |
| Microsoft.RecoveryServices/Vaults/storageConfig/read  |  Läsa lagringskonfigurationen för av Recovery Services-valvet |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read  |  Läs Recovery Services-valvet tokeninformation |
| Microsoft.RecoveryServices/Vaults/usages/read  |  Läs användningsinformation om Recovery Services-valvet |
| Microsoft.Support/*  |  Skapa och hantera supportärenden |

### <a name="sql-db-contributor"></a>SQL DB-deltagare
Kan hantera SQL-databaser, men inte deras säkerhetsrelaterade principer

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Läs roller och rollen tilldelningar |
| Microsoft.Insights/alertRules/* |Skapa och hantera aviseringsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read |Läs hälsotillståndet för resurser |
| Microsoft.Resources/deployments/* |Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read |Läs resursgrupper |
| Microsoft.Sql/servers/databases/* |Skapa och hantera SQL-databaser |
| Microsoft.Sql/servers/read |Läsa SQL-servrar |
| Microsoft.Support/* |Skapa och hantera supportärenden |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/databases/auditingPolicies/* |Det går inte att redigera granskningsprinciper |
| Microsoft.Sql/servers/databases/auditingSettings/* |Det går inte att redigera granskningsinställningar |
| Microsoft.Sql/servers/databases/auditRecords/read |Det går inte att läsa granskningsposter |
| Microsoft.Sql/servers/databases/connectionPolicies/* |Det går inte att redigera principer |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* |Det går inte att redigera datamaskning principer |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* |Det går inte att redigera avisering säkerhetsprinciper |
| Microsoft.Sql/servers/databases/securityMetrics/* |Det går inte att redigera säkerhet mått |

### <a name="sql-security-manager"></a>SQL-säkerhetshanteraren
Kan hantera de säkerhetsrelaterade principerna för SQL-servrar och databaser

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Läs Microsoft auktorisering |
| Microsoft.Insights/alertRules/* |Skapa och hantera insikter Varningsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read |Läs hälsotillståndet för resurser |
| Microsoft.Resources/deployments/* |Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read |Läs resursgrupper |
| Microsoft.Sql/servers/auditingPolicies/* |Skapa och hantera granskningsprinciper för SQL server |
| Microsoft.Sql/servers/auditingSettings/* |Skapa och hantera granskning inställning för SQL server |
| Microsoft.Sql/servers/databases/auditingPolicies/* |Skapa och hantera granskningsprinciper för SQL server-databas |
| Microsoft.Sql/servers/databases/auditingSettings/* |Skapa och hantera granskning inställningar för SQL server-databas |
| Microsoft.Sql/servers/databases/auditRecords/read |Läs granskningsposter |
| Microsoft.Sql/servers/databases/connectionPolicies/* |Skapa och hantera principer för SQL server-databas |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* |Skapa och hantera SQL server-databasen datamaskning principer |
| Microsoft.Sql/servers/databases/read |Läs SQL-databaser |
| Microsoft.Sql/servers/databases/schemas/read |Läs SQL server-databasen scheman |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read |Läs SQL server-databasen tabellkolumner |
| Microsoft.Sql/servers/databases/schemas/tables/read |Läs SQL server-databastabeller |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* |Skapa och hantera SQL server-databasen avisering säkerhetsprinciper |
| Microsoft.Sql/servers/databases/securityMetrics/* |Skapa och hantera mått för SQL server-databasen säkerhet |
| Microsoft.Sql/servers/read |Läsa SQL-servrar |
| Microsoft.Sql/servers/securityAlertPolicies/* |Skapa och hantera aviseringar principer för SQL server-säkerhet |
| Microsoft.Support/* |Skapa och hantera supportärenden |

### <a name="sql-server-contributor"></a>SQL Server-deltagare
Kan hantera SQL-servrar och databaser, men inte deras säkerhetsrelaterade principer

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Läs roller och rolltilldelningar |
| Microsoft.Insights/alertRules/* |Skapa och hantera insikter Varningsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read |Läs hälsotillståndet för resurser |
| Microsoft.Resources/deployments/* |Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read |Läs resursgrupper |
| Microsoft.Sql/servers/* |Skapa och hantera SQL-servrar |
| Microsoft.Support/* |Skapa och hantera supportärenden |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/auditingPolicies/* |Det går inte att redigera SQL server-granskningsprinciper |
| Microsoft.Sql/servers/auditingSettings/* |Det går inte att redigera SQL server-granskningsinställningar |
| Microsoft.Sql/servers/databases/auditingPolicies/* |Det går inte att redigera granskningsprinciper för SQL server-databas |
| Microsoft.Sql/servers/databases/auditingSettings/* |Det går inte att redigera granskningsinställningar för SQL server-databas |
| Microsoft.Sql/servers/databases/auditRecords/read |Det går inte att läsa granskningsposter |
| Microsoft.Sql/servers/databases/connectionPolicies/* |Det går inte att redigera principer för SQL server-databas |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* |Det går inte att redigera SQL server-databasen datamaskning principer |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* |Det går inte att redigera SQL server-databasen avisering säkerhetsprinciper |
| Microsoft.Sql/servers/databases/securityMetrics/* |Det går inte att redigera SQL server-databasen säkerhet mått |
| Microsoft.Sql/servers/securityAlertPolicies/* |Det går inte att redigera avisering principer för SQL server-säkerhet |

### <a name="classic-storage-account-contributor"></a>Klassisk lagringskontodeltagare
Kan hantera klassiska lagringskonton

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Läsa tillstånd |
| Microsoft.ClassicStorage/storageAccounts/* |Skapa och hantera storage-konton |
| Microsoft.Insights/alertRules/* |Skapa och hantera insikter Varningsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read |Läs hälsotillståndet för resurser |
| Microsoft.Resources/deployments/* |Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read |Läs resursgrupper |
| Microsoft.Support/* |Skapa och hantera supportärenden |

### <a name="storage-account-contributor"></a>Lagringskontodeltagare
Kan hantera storage-konton, men inte åtkomst till dem.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Läsa alla auktorisering |
| Microsoft.Insights/alertRules/* |Skapa och hantera insikter Varningsregler |
| Microsoft.Insights/diagnosticSettings/* |Hantera diagnostikinställningar |
| Microsoft.ResourceHealth/availabilityStatuses/read |Läs hälsotillståndet för resurser |
| Microsoft.Resources/deployments/* |Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read |Läs resursgrupper |
| Microsoft.Storage/storageAccounts/* |Skapa och hantera storage-konton |
| Microsoft.Support/* |Skapa och hantera supportärenden |

### <a name="support-request-contributor"></a>Supportförfrågningsdeltagare
Skapa och hantera supportärenden definitionsområdet prenumeration

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läsa tillstånd |
| Microsoft.Support/* | Skapa och hantera supportärenden |
| Microsoft.Resources/subscriptions/resourceGroups/read | Läs roller och rolltilldelningar |

### <a name="user-access-administrator"></a>Administratör för användaråtkomst
Kan hantera användarnas åtkomst till Azure-resurser

| **Åtgärder** |  |
| --- | --- |
| * / läsa |Läsa resurser av alla typer utom hemligheter. |
| Microsoft.Authorization/* |Hantera auktorisering |
| Microsoft.Support/* |Skapa och hantera supportärenden |

### <a name="classic-virtual-machine-contributor"></a>Klassisk virtuell datordeltagare
Kan hantera klassiska virtuella datorer, men inte den virtuella nätverks- eller konto som de är anslutna

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Läsa tillstånd |
| Microsoft.ClassicCompute/domainNames/* |Skapa och hantera klassiska beräkning domännamn |
| Microsoft.ClassicCompute/virtualMachines/* |Skapa och hantera virtuella datorer |
| Microsoft.ClassicNetwork/networkSecurityGroups/join/action |Anslut nätverkssäkerhetsgrupp |
| Microsoft.ClassicNetwork/reservedIps/link/action |Länka reserverade IP-adresser |
| Microsoft.ClassicNetwork/reservedIps/read |Läs reserverad IP-adress |
| Microsoft.ClassicNetwork/virtualNetworks/join/action |Ansluta till virtuella nätverk |
| Microsoft.ClassicNetwork/virtualNetworks/read |Läs virtuella nätverk |
| Microsoft.ClassicStorage/storageAccounts/disks/read |Läsa diskar med lagringsutrymme |
| Microsoft.ClassicStorage/storageAccounts/images/read |Läsa lagring konto bilder |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action |Lista nycklar för lagringskonto |
| Microsoft.ClassicStorage/storageAccounts/read |Läs klassiska lagringskonton |
| Microsoft.Insights/alertRules/* |Skapa och hantera insikter Varningsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read |Läs hälsotillståndet för resurser |
| Microsoft.Resources/deployments/* |Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read |Läs resursgrupper |
| Microsoft.Support/* |Skapa och hantera supportärenden |

### <a name="virtual-machine-contributor"></a>Virtuell datordeltagare
Kan hantera virtuella datorer, men inte den virtuella nätverks- eller konto som de är anslutna

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Läsa tillstånd |
| Microsoft.Compute/availabilitySets/* |Skapa och hantera beräknings-tillgänglighetsuppsättningar |
| Microsoft.Compute/locations/* |Skapa och hantera beräknings-platser |
| Microsoft.Compute/virtualMachines/* |Skapa och hantera virtuella datorer |
| Microsoft.Compute/virtualMachineScaleSets/* |Skapa och hantera virtuella datorer |
| Microsoft.Insights/alertRules/* |Skapa och hantera insikter Varningsregler |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action |Ansluta till nätverket programmet gateway serverdelsadresspooler |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action |Anslut load balancer serverdelsadresspooler |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action |Ansluta till belastningsutjämnaren inkommande NAT-pooler |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action |Ansluta till belastningsutjämnaren inkommande NAT-regler |
| Microsoft.Network/loadBalancers/read |Läs belastningsutjämnare |
| Microsoft.Network/locations/* |Skapa och hantera nätverksplatser |
| Microsoft.Network/networkInterfaces/* |Skapa och hantera nätverksgränssnitt |
| Microsoft.Network/networkSecurityGroups/join/action |Anslut nätverkssäkerhetsgrupp |
| Microsoft.Network/networkSecurityGroups/read |Läsa nätverkssäkerhetsgrupper |
| Microsoft.Network/publicIPAddresses/join/action |Ansluta till nätverket offentliga IP-adresser |
| Microsoft.Network/publicIPAddresses/read |Läs nätverket offentliga IP-adresser |
| Microsoft.Network/virtualNetworks/read |Läs virtuella nätverk |
| Microsoft.Network/virtualNetworks/subnets/join/action |Ansluta till virtuella undernät |
| Microsoft.ResourceHealth/availabilityStatuses/read |Läs hälsotillståndet för resurser |
| Microsoft.Resources/deployments/* |Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read |Läs resursgrupper |
| Microsoft.Storage/storageAccounts/listKeys/action |Lista nycklar för lagringskonto |
| Microsoft.Storage/storageAccounts/read |Läs storage-konton |
| Microsoft.Support/* |Skapa och hantera supportärenden |

### <a name="classic-network-contributor"></a>Klassisk nätverksdeltagare
Kan hantera klassiska virtuella nätverk och reserverade IP-adresser

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Läsa tillstånd |
| Microsoft.ClassicNetwork/* |Skapa och hantera klassiska nätverk |
| Microsoft.Insights/alertRules/* |Skapa och hantera insikter Varningsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read |Läs hälsotillståndet för resurser |
| Microsoft.Resources/deployments/* |Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read |Läs resursgrupper |
| Microsoft.Support/* |Skapa och hantera supportärenden |

### <a name="web-plan-contributor"></a>Webbplan-deltagare
Kan hantera web planer

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Läsa tillstånd |
| Microsoft.Insights/alertRules/* |Skapa och hantera insikter Varningsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read |Läs hälsotillståndet för resurser |
| Microsoft.Resources/deployments/* |Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read |Läs resursgrupper |
| Microsoft.Support/* |Skapa och hantera supportärenden |
| Microsoft.Web/serverFarms/* |Skapa och hantera servergrupper |

### <a name="website-contributor"></a>Webbplatsdeltagare
Kan hantera webbplatser, men inte alla web-planer som de är anslutna

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Läsa tillstånd |
| Microsoft.Insights/alertRules/* |Skapa och hantera insikter Varningsregler |
| Microsoft.Insights/components/* |Skapa och hantera Insights-komponenter |
| Microsoft.ResourceHealth/availabilityStatuses/read |Läs hälsotillståndet för resurser |
| Microsoft.Resources/deployments/* |Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read |Läs resursgrupper |
| Microsoft.Support/* |Skapa och hantera supportärenden |
| Microsoft.Web/certificates/* |Skapa och hantera webbplatscertifikat |
| Microsoft.Web/listSitesAssignedToHostName/read |Läs platser på ett värdnamn |
| Microsoft.Web/serverFarms/join/action |Ansluta till servergrupper |
| Microsoft.Web/serverFarms/read |Läs servergrupper |
| Microsoft.Web/sites/* |Skapa och hantera webbplatser (för att skapa även kräver behörighet att skriva till den associerade Apptjänstplan) |

## <a name="see-also"></a>Se också
* [Rollbaserad åtkomstkontroll](role-based-access-control-configure.md): komma igång med RBAC på Azure-portalen.
* [Anpassade roller i Azure RBAC](role-based-access-control-custom-roles.md): Lär dig att skapa anpassade roller som passar dina åtkomstbehov.
* [Skapa en profil över åtkomständringshistorik](role-based-access-control-access-change-history-report.md): hålla reda på hur du ändrar rolltilldelningar i RBAC.
* [Rollbaserad åtkomstkontroll felsökning](role-based-access-control-troubleshooting.md): få förslag om hur du löser vanliga problem.
* [Behörigheter i Azure Security Center](../security-center/security-center-permissions.md)
