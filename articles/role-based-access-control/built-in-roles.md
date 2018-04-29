---
title: Inbyggda roller för Azure rollbaserad åtkomstkontroll (RBAC) | Microsoft Docs
description: Det här avsnittet beskriver den inbyggda i roller för rollbaserad åtkomstkontroll (RBAC). Rollerna läggs till kontinuerligt, så kontrollera dokumentationen uppdateringen.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 03/06/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: it-pro
ms.openlocfilehash: 4e413d921390cfb87c27c530c6870945c28c0df8
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2018
---
# <a name="built-in-roles-for-azure-role-based-access-control"></a>Inbyggda roller för rollbaserad åtkomstkontroll i Azure
Azure rollbaserad åtkomstkontroll (RBAC) levereras med följande inbyggda roller som kan tilldelas användare, grupper och tjänster. Du kan inte ändra definitionerna av inbyggda roller. Du kan dock skapa [anpassade roller i Azure RBAC](custom-roles.md) så att den passar de specifika behoven i din organisation.

## <a name="built-in-role-descriptions"></a>Beskrivningar av inbyggd roll
Följande tabell innehåller en kort beskrivning av de inbyggda rollerna. Klicka på rollnamnet att se en detaljerad lista över **åtgärder** och **notactions** för rollen. Den **åtgärder** egenskapen anger tillåtna åtgärder på Azure-resurser. Åtgärden strängar kan använda jokertecken. Den **notactions** egenskapen anger vilka åtgärder som är exkluderade från de tillåtna åtgärderna.

Åtgärden definierar vilken typ av åtgärder som du kan utföra på en viss resurstyp. Exempel:
- **Skriva** kan du utföra PUT, POST, KORRIGERINGSFIL och DELETE-åtgärder.
- **Läs** kan du utföra GET-åtgärder.

Den här artikeln tar endast de olika roller som finns idag. När du tilldelar en roll till en användare, men kan du begränsa de tillåtna åtgärderna ytterligare genom att definiera ett omfång. Det här är användbart om du vill ange någon webbplats deltagare, men endast för en resursgrupp.

> [!NOTE]
> Azure rolldefinitioner utvecklas hela tiden. Den här artikeln är uppdaterade som som möjligt, men du kan alltid hitta de senaste definitionerna för roller i Azure PowerShell. Använd den [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) för att visa en lista över alla aktuella roller. Du kan sätta igång till en viss roll med hjälp av `(get-azurermroledefinition "<role name>").actions` eller `(get-azurermroledefinition "<role name>").notactions` i tillämpliga fall. Använd [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) till Liståtgärder av specifika Azure-resurs-providers.


| Inbyggd roll | Beskrivning |
| --- | --- |
| [Ägare](#owner) | Låter dig hantera allt, inklusive åtkomst till resurser. |
| [Deltagare](#contributor) | Låter dig hantera allt, med undantag för åtkomst till resurser. |
| [läsare](#reader) | Låter dig visa allting, men låter dig inte göra några ändringar. |
| [API Management-tjänsten deltagare](#api-management-service-contributor) | Kan hantera tjänsten och samtliga API:er |
| [Operatörsrollen för API Management-tjänsten](#api-management-service-operator-role) | Kan hantera tjänsten men inte API:er |
| [Rollen för API Management-tjänsten läsare](#api-management-service-reader-role) | Skrivskyddad åtkomst till tjänst och API:er |
| [Application Insights Component Contributor](#application-insights-component-contributor) | Kan hantera Application Insights-komponenter |
| [Application Insights ögonblicksbild felsökare](#application-insights-snapshot-debugger) | Ger användarbehörighet för att använda felsökningsfunktionerna för ögonblicksbilder i Application Insights |
| [Operator för Automation-jobb](#automation-job-operator) | Skapa och hantera jobb med Automation Runbooks. |
| [Automation-operatorn](#automation-operator) | Automation-operatörer kan starta, stoppa, göra uppehåll i och återuppta jobb |
| [Automation Runbook-operatör](#automation-runbook-operator) | Läs runbook-egenskaperna för att kunna skapa jobb av den runbooken. |
| [Azure-stacken registrering ägare](#azure-stack-registration-owner) | Låter dig hantera Azure Stack-registreringar. |
| [Säkerhetskopiering deltagare](#backup-contributor) | Låter dig hantera säkerhetskopieringstjänsten, men låter dig inte skapa valv eller ge åtkomst till andra |
| [Ansvarig för säkerhetskopiering](#backup-operator) | Låter dig hantera säkerhetskopieringstjänster, med undantag för att ta bort säkerhetskopior, skapa valv eller ge åtkomst till andra |
| [Säkerhetskopiering läsare](#backup-reader) | Kan visa säkerhetskopieringstjänster, men inte göra några ändringar |
| [Fakturering läsare](#billing-reader) | Tillåter läsåtkomst till faktureringsdata |
| [BizTalk-deltagare](#biztalk-contributor) | Låter dig hantera BizTalk-tjänster, men ger dig inte tillgång till dem. |
| [CDN-slutpunkten deltagare](#cdn-endpoint-contributor) | Kan hantera CDN-slutpunkter, men kan inte bevilja åtkomst till andra användare. |
| [CDN-slutpunkten läsare](#cdn-endpoint-reader) | Kan visa CDN-slutpunkter, men kan inte göra ändringar. |
| [CDN-profilen deltagare](#cdn-profile-contributor) | Kan hantera CDN-profiler och deras slutpunkter, men kan inte bevilja åtkomst till andra användare. |
| [CDN-profilen läsare](#cdn-profile-reader) | Kan visa CDN-profiler och deras slutpunkter, men kan inte göra ändringar. |
| [Klassisk nätverksdeltagare](#classic-network-contributor) | Låter dig hantera klassiska nätverk, men ger dig inte tillgång till dem. |
| [Klassiska Storage-konto deltagare](#classic-storage-account-contributor) | Låter dig hantera klassiska lagringskonton, men ger dig inte åtkomst att hantera dem. |
| [Klassiska Storage-konto administratören rolltjänst](#classic-storage-account-key-operator-service-role) | Operatörer av klassiska lagringskontonycklar får lista och återskapa nycklar till klassiska lagringskonton |
| [Klassiska Virtual Machine-deltagare](#classic-virtual-machine-contributor) | Kan du hantera klassiska virtuella datorer, men inte åtkomst till dem, och inte virtuellt nätverk eller lagringskonto som de är anslutna till. |
| [ClearDB MySQL DB-deltagare](#cleardb-mysql-db-contributor) | Låter dig hantera ClearDB MySQL-databaser, men ger dig inte tillgång till dem. |
| [Rollen för läsare av cosmos DB-konto](#cosmos-db-account-reader-role) | Kan läsa Azure Cosmos DB kontodata. Se [DocumentDB-konto deltagare](#documentdb-account-contributor) för att hantera Azure DB som Cosmos-konton. |
| [Data Factory deltagare](#data-factory-contributor) | Skapa och hantera datafabriker och deras underordnade resurser. |
| [Data Lake Analytics-utvecklare](#data-lake-analytics-developer) | Låter dig skicka in, övervaka och hantera dina egna jobb, men inte skapa eller ta bort Data Lake Analytics-konton. |
| [DevTest Labs användare](#devtest-labs-user) | Låter dig ansluta, starta, starta om och stänga av dina virtuella datorer i din Azure DevTest Labs. |
| [DNS-zonen deltagare](#dns-zone-contributor) | Låter dig hantera DNS-zoner och postuppsättningar i Azure DNS, men låter dig inte kontrollera vem som har åtkomst till dem. |
| [DocumentDB-konto deltagare](#documentdb-account-contributor) | Hantera Azure DB som Cosmos-konton. Azure Cosmos-DB är kallades DocumentDB. |
| [Intelligenta System-kontot deltagare](#intelligent-systems-account-contributor) | Låter dig hantera Intelligent Systems-konton, men ger dig inte tillgång till dem. |
| [Key Vault deltagare](#key-vault-contributor) | Låter dig hantera nyckelvalv, men inte ha åtkomst till dem. |
| [Lab Creator](#lab-creator) | Kan du skapa, hantera, ta bort dina hanterade labs under din Azure Lab konton. |
| [Log Analytics deltagare](#log-analytics-contributor) | Log Analytics deltagare kan läsa alla övervakningsdata och redigera inställningarna för övervakning. Redigera inställningarna för övervakning innehåller lägger till VM-tillägget på virtuella datorer; läsa lagringskontonycklar för att kunna konfigurera samlingen loggar från Azure Storage; Skapa och konfigurera Automation-konton lägga till lösningar. och konfigurera Azure-diagnostik på alla Azure-resurser. |
| [Analytics Händelseloggläsare](#log-analytics-reader) | Log Analytics Reader kan visa och söka i alla övervakningsdata och dessutom visa övervakningsinställningar, bl.a. konfigurationen av Azure Diagnostics på alla Azure-resurser. |
| [Logik App deltagare](#logic-app-contributor) | Låter dig hantera logikappar, men du kan inte komma åt dem. |
| [Logik App Operator](#logic-app-operator) | Låter dig läsa, aktivera och inaktivera logikapp. |
| [Hanterade identitet deltagare](#managed-identity-contributor) | Skapa, läsa, uppdatera och ta bort användaren som har tilldelats identitet |
| [Hanterade identitet Operator](#managed-identity-operator) | Läsa och tilldela användaren som har tilldelats identitet |
| [Övervakning av deltagare](#monitoring-contributor) | Kan läsa alla övervakningsdata och redigera inställningarna för övervakning. Se även [Kom igång med roller, behörigheter och säkerhet med Azure-Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
| [Övervaka läsare](#monitoring-reader) | Kan läsa alla övervakningsdata (mått, loggar osv.). Se även [Kom igång med roller, behörigheter och säkerhet med Azure-Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
| [Nätverksdeltagare](#network-contributor) | Låter dig hantera nätverk, men ger dig inte tillgång till dem. |
| [Nya Relic APM konto deltagare](#new-relic-apm-account-contributor) | Låter dig hantera New Relic Application Performance Management-konton och program, men ger dig inte tillgång till dem. |
| [Redis-Cache deltagare](#redis-cache-contributor) | Låter dig hantera Redis-cacheminnen, men ger dig inte tillgång till dem. |
| [Schemaläggaren jobbet samlingar deltagare](#scheduler-job-collections-contributor) | Låter dig hantera Scheduler-jobbsystem, men ger dig inte tillgång till dem. |
| [Sök Service deltagare](#search-service-contributor) | Låter dig hantera söktjänster, men ger dig inte tillgång till dem. |
| [Säkerhet Admin](#security-admin) | I Security Center endast: Visa säkerhetsprinciper, visa säkerhetsstatus, redigera säkerhetsprinciper, Visa aviseringar och rekommendationerna, stänga aviseringar och rekommendationer |
| [Säkerhetshantering](#security-manager) | Låter dig hantera säkerhetskomponenter, säkerhetsprinciper och virtuella datorer |
| [Säkerhet läsare](#security-reader) | I Security Center endast: Visa rekommendationer och aviseringar, visa säkerhetsprinciper, visa säkerhetsstatus, men det går inte att göra ändringar |
| [Site Recovery-deltagare](#site-recovery-contributor) | Låter dig hantera Site Recovery-tjänsten förutom att skapa valv och tilldela roller |
| [Site Recovery-operatorn](#site-recovery-operator) | Låter dig växla vid fel och återställa men inte utföra andra Site Recovery-hanteringsåtgärder |
| [Site Recovery läsare](#site-recovery-reader) | Låter dig se Site Recovery-status men inte utföra andra hanteringsåtgärder |
| [SQL DB-deltagare](#sql-db-contributor) | Kan du hantera SQL-databaser, men inte åtkomst till dem. Du kan också hantera deras säkerhetsrelaterade principer eller sina överordnade SQL-servrar. |
| [SQL-säkerhetsansvarig](#sql-security-manager) | Tillåter dig att hantera säkerhetsrelaterade principer för SQL-servrar och databaser, men inte åtkomst till dem. |
| [SQL Server-deltagare](#sql-server-contributor) | Tillåter dig att hantera SQL-servrar och databaser, men inte åtkomst till dem eller deras säkerhetsrelaterade principer. |
| [Storage-konto deltagare](#storage-account-contributor) | Låter dig hantera lagringskonton, men ger dig inte åtkomst till dem. |
| [Storage-konto administratören rolltjänst](#storage-account-key-operator-service-role) | Operatörer av lagringskontonycklar får lista och återskapa nycklar till lagringskonton |
| [Stöd för begäran deltagare](#support-request-contributor) | Låter dig skapa och hantera supportförfrågningar |
| [Traffic Manager-deltagare](#traffic-manager-contributor) | Låter dig hantera Traffic Manager-profiler, men låter dig inte kontrollera vem som har åtkomst till dem. |
| [Administratör för användaråtkomst](#user-access-administrator) | Låter dig hantera användaråtkomst till Azure-resurser. |
| [Administratören för virtuella datorer inloggning](#virtual-machine-administrator-login) | -Användare med den här rollen har möjlighet att logga in på en virtuell dator med Windows-administratör eller användarbehörigheter för Linux-rot. |
| [Virtual Machine-deltagare](#virtual-machine-contributor) | Kan du hantera virtuella datorer, men inte åtkomst till dem, och inte virtuellt nätverk eller lagringskonto som de är anslutna till. |
| [Användarinloggning för virtuell dator](#virtual-machine-user-login) | Användare med den här rollen har möjlighet att logga in på en virtuell dator som en vanlig användare. |
| [Web Plan deltagare](#web-plan-contributor) | Låter dig hantera webbplaner för webbplatser, men ger dig inte tillgång till dem. |
| [Webbplatsen deltagare](#website-contributor) | Låter dig hantera webbplatser (men inte webbplaner), men ger dig inte tillgång till dem. |

I följande tabeller beskrivs de specifika behörigheter som tilldelats varje roll. Detta kan inkludera **åtgärder**, som ger behörigheter och **NotActions**, som begränsar dem.

## <a name="owner"></a>Ägare
Låter dig hantera allt, inklusive åtkomst till resurser.

| **Åtgärder** |  |
| --- | --- |
| * | Skapa och hantera resurser av alla typer av |

## <a name="contributor"></a>Deltagare
Låter dig hantera allt, med undantag för åtkomst till resurser.

| **Åtgärder** |  |
| --- | --- |
| * | Skapa och hantera resurser av alla typer av |

| **NotActions** |  |
| --- | --- |
| Microsoft.Authorization/*/Delete | Det går inte att ta bort roller och rolltilldelningar |
| Microsoft.Authorization/*/Write | Det går inte att skapa roller och rolltilldelningar |
| Microsoft.Authorization/elevateAccess/Action | Ger anroparen åtkomst till administratör för användaråtkomst i klientomfattningen |

## <a name="reader"></a>Läsare
Låter dig visa allting, men låter dig inte göra några ändringar.

| **Åtgärder** |  |
| --- | --- |
| * / läsa | Läsa resurser av alla typer utom hemligheter. |

## <a name="api-management-service-contributor"></a>API Management-tjänstdeltagare
Kan hantera tjänsten och samtliga API:er

| **Åtgärder** |  |
| --- | --- |
| Microsoft.ApiManagement/service/* | Skapa och hantera API Management-tjänsten |
| Microsoft.Authorization/*/read | Läsa tillstånd |
| Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="api-management-service-operator-role"></a>Operatörsroll för API Management
Kan hantera tjänsten men inte API:er

| **Åtgärder** |  |
| --- | --- |
| Microsoft.ApiManagement/service/*/read | Läs API Management-tjänsten-instanser |
| Microsoft.ApiManagement/service/backup/action | Säkerhetskopiering API-tjänsten till den angivna behållaren i en användare som storage-konto |
| Microsoft.ApiManagement/service/delete | Ta bort en instans för API Management-tjänsten |
| Microsoft.ApiManagement/service/managedeployments/action | Ändra SKU/enheter, Lägg till/ta bort regionala distributioner av API Management-tjänsten |
| Microsoft.ApiManagement/service/read | Läsa in metadata för en instans för API Management-tjänsten |
| Microsoft.ApiManagement/service/restore/action | Återställa API Management-tjänsten från den angivna behållaren i en användare har angett storage-konto |
| Microsoft.ApiManagement/service/updatecertificate/action | Ladda upp SSL-certifikat för en API Management-tjänsten |
| Microsoft.ApiManagement/service/updatehostname/action | Konfigurera, uppdatera eller ta bort anpassade domännamn för en API Management-tjänsten |
| Microsoft.ApiManagement/service/write | Skapa en ny instans av API Management-tjänsten |
| Microsoft.Authorization/*/read | Läsa tillstånd |
| Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

| **NotActions** |  |
| --- | --- |
| Microsoft.ApiManagement/service/users/keys/read | Hämta lista över användarnycklar |

## <a name="api-management-service-reader-role"></a>Läsarroll för API Management-tjänst
Skrivskyddad åtkomst till tjänst och API:er

| **Åtgärder** |  |
| --- | --- |
| Microsoft.ApiManagement/service/*/read | Läs API Management-tjänsten-instanser |
| Microsoft.ApiManagement/service/read | Läsa in metadata för en instans för API Management-tjänsten |
| Microsoft.Authorization/*/read | Läsa tillstånd |
| Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

| **NotActions** |  |
| --- | --- |
| Microsoft.ApiManagement/service/users/keys/read | Hämta lista över användarnycklar |

## <a name="application-insights-component-contributor"></a>Application Insights-komponentdeltagare
Kan hantera Application Insights-komponenter

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
| Microsoft.Insights/components/* | Skapa och hantera Insights-komponenter |
| Microsoft.Insights/webtests/* | Skapa och hantera webbtest |
| Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="application-insights-snapshot-debugger"></a>Felsökningsprogram för ögonblicksbilder i Application Insights
Ger användarbehörighet för att använda felsökningsfunktionerna för ögonblicksbilder i Application Insights

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.Insights/alertRules/* | Skapa och hantera insikter Varningsregler |
| Microsoft.Insights/components/*/read |  |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="automation-job-operator"></a>Automation-jobboperator
Skapa och hantera jobb med Automation Runbooks.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.Automation/automationAccounts/jobs/read | Hämtar en Azure Automation-jobb |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Återupptar ett Azure Automation-jobb |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Stoppar ett Azure Automation-jobb |
| Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Läser Hybrid Runbook Worker-resurser |
| Microsoft.Automation/automationAccounts/jobs/streams/read | Hämtar en Azure Automation-jobbström |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Pausar ett Azure Automation-jobb |
| Microsoft.Automation/automationAccounts/jobs/write | Skapar ett Azure Automation-jobb |
| Microsoft.Insights/alertRules/* | Skapa och hantera insikter Varningsregler |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="automation-operator"></a>Automation-operatör
Automation-operatörer kan starta, stoppa, göra uppehåll i och återuppta jobb

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.Automation/automationAccounts/jobs/read | Hämtar en Azure Automation-jobb |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Återupptar ett Azure Automation-jobb |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Stoppar ett Azure Automation-jobb |
| Microsoft.Automation/automationAccounts/jobs/streams/read | Hämtar en Azure Automation-jobbström |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Pausar ett Azure Automation-jobb |
| Microsoft.Automation/automationAccounts/jobs/write | Skapar ett Azure Automation-jobb |
| Microsoft.Automation/automationAccounts/jobSchedules/read | Hämtar ett Azure Automation-Jobbschema |
| Microsoft.Automation/automationAccounts/jobSchedules/write | Skapar ett Azure Automation-Jobbschema |
| Microsoft.Automation/automationAccounts/linkedWorkspace/read | Hämtar arbetsytan kopplad till automation-kontot |
| Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Läser Hybrid Runbook Worker-resurser |
| Microsoft.Automation/automationAccounts/read | Hämtar ett Azure Automation-konto |
| Microsoft.Automation/automationAccounts/runbooks/read | Hämtar en Azure Automation-runbook |
| Microsoft.Automation/automationAccounts/schedules/read | Hämtar en Azure Automation-schematillgång |
| Microsoft.Automation/automationAccounts/schedules/write | Skapar eller uppdaterar en Azure Automation-schematillgång |
| Microsoft.Insights/alertRules/* | Skapa och hantera insikter Varningsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="automation-runbook-operator"></a>Automation Runbook-operator
Läs runbook-egenskaperna för att kunna skapa jobb av den runbooken.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.Automation/automationAccounts/runbooks/read | Hämtar en Azure Automation-runbook |
| Microsoft.Insights/alertRules/* | Skapa och hantera insikter Varningsregler |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="azure-stack-registration-owner"></a>Ägare för Azure Stack-registrering
Låter dig hantera Azure Stack-registreringar.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.AzureStack/registrations/products/listDetails/action | Hämtar utökad information för en Azure-stacken Marketplace-produkten |
| Microsoft.AzureStack/registrations/products/read | Hämtar egenskaperna för en Azure-stacken Marketplace-produkt |
| Microsoft.AzureStack/registrations/read | Hämtar egenskaperna för en Azure-Stack-registrering |

## <a name="backup-contributor"></a>Säkerhetskopieringsmedarbetare
Låter dig hantera säkerhetskopieringstjänsten, men låter dig inte skapa valv eller ge åtkomst till andra

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.Network/virtualNetworks/read | Hämta definitionen av virtuellt nätverk |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Hantera resultatet av åtgärden för hantering av säkerhetskopiering |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Skapa och hantera säkerhetskopiering behållare i säkerhetskopiering finns hos Recovery Services-valvet |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | Skapa och hantera säkerhetskopieringsjobb |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportera jobben |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | Skapa och hantera meta-data som rör hantering av säkerhetskopiering |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Skapa och hantera resultaten av säkerhetskopiering hanteringsåtgärder |
| Microsoft.RecoveryServices/Vaults/backupPolicies/* | Skapa och hantera principer för säkerhetskopiering |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Skapa och hantera objekt som kan säkerhetskopieras |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Skapa och hantera säkerhetskopierade objekt |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Skapa och hantera behållare Säkerhetskopiera objekt |
| Microsoft.RecoveryServices/Vaults/certificates/* | Skapa och hantera certifikat som relaterar till säkerhetskopiering i Recovery Services-valvet |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | Skapa och hantera utökad information som rör valvet |
| Microsoft.RecoveryServices/Vaults/read | Åtgärden hämta valvet hämtar ett objekt som representerar Azure-resurs av typen 'valvet' |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | Hantera Identifieringsåtgärden för hämtning av nyskapad behållare |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Skapa och hantera registrerade identiteter |
| Microsoft.RecoveryServices/Vaults/usages/* | Skapa och hantera användningen av Recovery Services-valvet |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Returnerar sammanfattningar för skyddade objekt och skyddade servrar för en Recovery Services. |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Storage/storageAccounts/read | Returnerar listan med lagringskonton eller hämtar egenskaperna för det angivna lagringskontot. |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp är en intern åtgärd som används av tjänsten |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Hämtar aviseringar för Recovery services-valvet. |
| Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Returnerar resultatet av jobbet exportåtgärden. |
| Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="backup-operator"></a>Säkerhetskopieringsoperatör
Låter dig hantera säkerhetskopieringstjänster, med undantag för att ta bort säkerhetskopior, skapa valv eller ge åtkomst till andra

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.Network/virtualNetworks/read | Hämta definitionen av virtuellt nätverk |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Returnerar status för åtgärden |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ operationResults/läsning | Hämtar resultat från utförd åtgärd på skyddsbehållare. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/backup/åtgärd | Säkerhetskopierar ett skyddat objekt. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationResults/läsning | Hämtar resultat från utförd åtgärd på skyddade objekt. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationsStatus/läsning | Returnerar status för utförd åtgärd på skyddade objekt. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/läsning | Returnerar information om objekt för det skyddade objektet |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/läsning | Hämta återställningspunkter för skyddade objekt. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/återställning/åtgärd | Återskapa återställningspunkter för skyddade objekt. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/skrivning | Skapa en säkerhetskopiera skyddade objekt |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Returnerar alla registrerade behållare |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | Skapa och hantera säkerhetskopieringsjobb |
| Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Avbryts |
| Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Returnerar resultat från jobbåtgärd. |
| Microsoft.RecoveryServices/Vaults/backupJobs/read | Returnerar alla jobbobjekt |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportera jobben |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Returnerar metadata för hantering av säkerhetskopiering för Recovery Services-valvet. |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Skapa och hantera resultaten av säkerhetskopiering hanteringsåtgärder |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Hämtar resultat från principåtgärd. |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | Returnerar alla Protection-principer |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Skapa och hantera objekt som kan säkerhetskopieras |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Returnerar lista över alla objekt som ska skyddas. |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Returnerar listan över alla skyddade objekt. |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Returnerar alla behållare som hör till prenumerationen |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Returnerar sammanfattningar för skyddade objekt och skyddade servrar för en Recovery Services. |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv? |
| Microsoft.RecoveryServices/Vaults/extendedInformation/write | Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv? |
| Microsoft.RecoveryServices/Vaults/read | Åtgärden hämta valvet hämtar ett objekt som representerar Azure-resurs av typen 'valvet' |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | Hantera Identifieringsåtgärden för hämtning av nyskapad behållare |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Hämta åtgärden resulterar åtgärden kan användas för hämta Åtgärdsstatus och resultat för asynkront skickats igen |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Hämta behållarna åtgärden kan användas för hämta behållare som har registrerats för en resurs. |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Registrera tjänstbehållaren åtgärden kan användas för att registrera en behållare med återställningstjänsten. |
| Microsoft.RecoveryServices/Vaults/usages/read | Returnerar användningsinformation om Recovery Services-valvet. |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Storage/storageAccounts/read | Returnerar listan med lagringskonton eller hämtar egenskaperna för det angivna lagringskontot. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/provisionInstantItemRecovery/åtgärd | Etablera omedelbar återställning för skyddade objekt |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/revokeInstantItemRecovery/åtgärd | Återkalla omedelbar återställning för skyddade objekt |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp är en intern åtgärd som används av tjänsten |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Hämtar aviseringar för Recovery services-valvet. |
| Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Returnerar resultatet av jobbet exportåtgärden. |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationStatus/read |  |
| Microsoft.RecoveryServices/Vaults/certificates/write | Uppdatera resurs certifikat åtgärden uppdaterar Autentiseringscertifikatet resurs-valvet. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="backup-reader"></a>Säkerhetskopieringsläsare
Kan visa säkerhetskopieringstjänster, men inte göra några ändringar

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Returnerar status för åtgärden |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ operationResults/läsning | Hämtar resultat från utförd åtgärd på skyddsbehållare. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationResults/läsning | Hämtar resultat från utförd åtgärd på skyddade objekt. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationsStatus/läsning | Returnerar status för utförd åtgärd på skyddade objekt. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/läsning | Returnerar information om objekt för det skyddade objektet |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Returnerar alla registrerade behållare |
| Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Returnerar resultat från jobbåtgärd. |
| Microsoft.RecoveryServices/Vaults/backupJobs/read | Returnerar alla jobbobjekt |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportera jobben |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Returnerar metadata för hantering av säkerhetskopiering för Recovery Services-valvet. |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Returnerar resultat från säkerhetskopiering för Recovery Services-valvet. |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Hämtar resultat från principåtgärd. |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | Returnerar alla Protection-principer |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Returnerar listan över alla skyddade objekt. |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Returnerar alla behållare som hör till prenumerationen |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Returnerar sammanfattningar för skyddade objekt och skyddade servrar för en Recovery Services. |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv? |
| Microsoft.RecoveryServices/Vaults/read | Åtgärden hämta valvet hämtar ett objekt som representerar Azure-resurs av typen 'valvet' |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Hämta åtgärden resulterar åtgärden kan användas för hämta Åtgärdsstatus och resultat för asynkront skickats igen |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Hämta behållarna åtgärden kan användas för hämta behållare som har registrerats för en resurs. |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp är en intern åtgärd som används av tjänsten |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/läsning |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Hämtar aviseringar för Recovery services-valvet. |
| Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/read |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/läsning | Hämta återställningspunkter för skyddade objekt. |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Returnerar resultatet av jobbet exportåtgärden. |
| Microsoft.RecoveryServices/Vaults/usages/read | Returnerar användningsinformation om Recovery Services-valvet. |

## <a name="billing-reader"></a>Faktureringsläsare
Tillåter läsåtkomst till faktureringsdata

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.Billing/*/read | Läs faktureringsinformation |
| Microsoft.Consumption/*/read |  |
| Microsoft.Commerce/*/read |  |
| Microsoft.Management/managementGroups/read | Lista över hanteringsgrupper för den autentiserade användaren. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="biztalk-contributor"></a>BizTalk-deltagare
Låter dig hantera BizTalk-tjänster, men ger dig inte tillgång till dem.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.BizTalkServices/BizTalk/* | Skapa och hantera BizTalk-tjänst |
| Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="cdn-endpoint-contributor"></a>CDN-slutpunktsdeltagare
Kan hantera CDN-slutpunkter, men kan inte bevilja åtkomst till andra användare.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/endpoints/* |  |
| Microsoft.Insights/alertRules/* | Skapa och hantera insikter Varningsregler |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="cdn-endpoint-reader"></a>CDN-slutpunktsläsare
Kan visa CDN-slutpunkter, men kan inte göra ändringar.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/endpoints/*/read |  |
| Microsoft.Insights/alertRules/* | Skapa och hantera insikter Varningsregler |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="cdn-profile-contributor"></a>CDN-profildeltagare
Kan hantera CDN-profiler och deras slutpunkter, men kan inte bevilja åtkomst till andra användare.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/* |  |
| Microsoft.Insights/alertRules/* | Skapa och hantera insikter Varningsregler |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="cdn-profile-reader"></a>CDN-profilläsare
Kan visa CDN-profiler och deras slutpunkter, men kan inte göra ändringar.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/*/read |  |
| Microsoft.Insights/alertRules/* | Skapa och hantera insikter Varningsregler |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="classic-network-contributor"></a>Klassisk nätverksdeltagare
Låter dig hantera klassiska nätverk, men ger dig inte tillgång till dem.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läsa tillstånd |
| Microsoft.ClassicNetwork/* | Skapa och hantera klassiska nätverk |
| Microsoft.Insights/alertRules/* | Skapa och hantera insikter Varningsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="classic-storage-account-contributor"></a>Klassisk lagringskontodeltagare
Låter dig hantera klassiska lagringskonton, men ger dig inte åtkomst att hantera dem.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läsa tillstånd |
| Microsoft.ClassicStorage/storageAccounts/* | Skapa och hantera storage-konton |
| Microsoft.Insights/alertRules/* | Skapa och hantera insikter Varningsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="classic-storage-account-key-operator-service-role"></a>Tjänstroll som operatör av klassisk lagringskontonyckel
Operatörer av klassiska lagringskontonycklar får lista och återskapa nycklar till klassiska lagringskonton

| **Åtgärder** |  |
| --- | --- |
| Microsoft.ClassicStorage/storageAccounts/listkeys/action | Listar lagringskontots åtkomstnycklar. |
| Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Återskapar befintliga åtkomstnycklar för lagringskontot. |

## <a name="classic-virtual-machine-contributor"></a>Klassisk virtuell datordeltagare
Kan du hantera klassiska virtuella datorer, men inte åtkomst till dem, och inte virtuellt nätverk eller lagringskonto som de är anslutna till.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läsa tillstånd |
| Microsoft.ClassicCompute/domainNames/* | Skapa och hantera klassiska beräkning domännamn |
| Microsoft.ClassicCompute/virtualMachines/* | Skapa och hantera virtuella datorer |
| Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
| Microsoft.ClassicNetwork/reservedIps/link/action | Länka en reserverad IP |
| Microsoft.ClassicNetwork/reservedIps/read | Hämtar reserverade IP-adresser |
| Microsoft.ClassicNetwork/virtualNetworks/join/action | Anslut till det virtuella nätverket. |
| Microsoft.ClassicNetwork/virtualNetworks/read | Hämtar det virtuella nätverket. |
| Microsoft.ClassicStorage/storageAccounts/disks/read | Returnerar lagringskontodisken. |
| Microsoft.ClassicStorage/storageAccounts/images/read | Returnerar lagringskontoavbildningen. |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Listar lagringskontots åtkomstnycklar. |
| Microsoft.ClassicStorage/storageAccounts/read | Returnerar lagringskontot med det givna kontot. |
| Microsoft.Insights/alertRules/* | Skapa och hantera insikter Varningsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="cleardb-mysql-db-contributor"></a>ClearDB MySQL DB-deltagare
Låter dig hantera ClearDB MySQL-databaser, men ger dig inte tillgång till dem.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |
| successbricks.cleardb/Databases/* | Skapa och hantera ClearDB MySQL-databaser |

## <a name="cosmos-db-account-reader-role"></a>Läsarroll för Cosmos DB-konto
Kan läsa Azure Cosmos DB kontodata. Se [DocumentDB-konto deltagare](#documentdb-account-contributor) för att hantera Azure DB som Cosmos-konton.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar kan läsbehörighet till alla användare |
| Microsoft.DocumentDB/*/read | Läs en samling |
| Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Läser databasen readonly nycklar. |
| Microsoft.Insights/MetricDefinitions/read | Läs måttdefinitioner |
| Microsoft.Insights/Metrics/read | Läs mått |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="data-factory-contributor"></a>Data Factory-deltagare
Skapa och hantera datafabriker och deras underordnade resurser.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rollen tilldelningar |
| Microsoft.DataFactory/factory/* | Skapa och hantera datafabriker och underordnade resurser i dem. |
| Microsoft.DataFactory/dataFactories/* | Skapa och hantera datafabriker och underordnade resurser i dem. |
| Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="data-lake-analytics-developer"></a>Data Lake Analytics-utvecklare
Låter dig skicka in, övervaka och hantera dina egna jobb, men inte skapa eller ta bort Data Lake Analytics-konton.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.BigAnalytics/accounts/* |  |
| Microsoft.DataLakeAnalytics/accounts/* |  |
| Microsoft.Insights/alertRules/* | Skapa och hantera insikter Varningsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

| **NotActions** |  |
| --- | --- |
| Microsoft.BigAnalytics/accounts/Delete |  |
| Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
| Microsoft.BigAnalytics/accounts/Write |  |
| Microsoft.DataLakeAnalytics/accounts/Delete | Ta bort ett DataLakeAnalytics konto. |
| Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Bevilja behörighet att avbryta jobb som skickats av andra användare. |
| Microsoft.DataLakeAnalytics/accounts/Write | Skapa eller uppdatera ett DataLakeAnalytics konto. |
| Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Skapa eller uppdatera en länkad DataLakeStore konto för DataLakeAnalytics-konto. |
| Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Avlänka ett DataLakeStore konto från ett DataLakeAnalytics-konto. |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Skapa eller uppdatera ett länkat lagringskonto för DataLakeAnalytics-konto. |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | Avlänka ett lagringskonto från en DataLakeAnalytics-konto. |
| Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Skapa eller uppdatera en brandväggsregel. |
| Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Ta bort en brandväggsregel. |
| Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Skapa eller uppdatera en princip för beräkning. |
| Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Ta bort en princip för beräkning. |

## <a name="devtest-labs-user"></a>DevTest Labs-användare
Låter dig ansluta, starta, starta om och stänga av dina virtuella datorer i din Azure DevTest Labs.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rollen tilldelningar |
| Microsoft.Compute/availabilitySets/read | Hämta egenskaperna för en tillgänglighetsuppsättning |
| Microsoft.Compute/virtualMachines/*/read | Läsa egenskaperna för en virtuell dator (VM-storlekar, Körningsstatus, VM-tillägg, etc.) |
| Microsoft.Compute/virtualMachines/deallocate/action | Stänger av den virtuella datorn och frigör beräkningsresurser |
| Microsoft.Compute/virtualMachines/read | Hämta egenskaperna för en virtuell dator |
| Microsoft.Compute/virtualMachines/restart/action | Startar om den virtuella datorn |
| Microsoft.Compute/virtualMachines/start/action | Startar den virtuella datorn |
| Microsoft.DevTestLab/*/read | Läsa egenskaperna för ett labb |
| Microsoft.DevTestLab/labs/createEnvironment/action | Skapa virtuella datorer i ett labb. |
| Microsoft.DevTestLab/labs/claimAnyVm/action | Begär en slumpmässig claimable virtuell dator i labbet. |
| Microsoft.DevTestLab/labs/formulas/delete | Ta bort formler. |
| Microsoft.DevTestLab/labs/formulas/read | Läsa formler. |
| Microsoft.DevTestLab/labs/formulas/write | Lägg till eller ändra formler. |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Utvärderar lab princip. |
| Microsoft.DevTestLab/labs/virtualMachines/claim/action | Bli ägare till en befintlig virtuell dator |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Ansluter till en belastningsutjämnare adress serverdelspool |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Ansluter en inkommande nat-regel för belastningsutjämnare |
| Microsoft.Network/networkInterfaces/*/read | Läsa egenskaperna för ett nätverksgränssnitt (till exempel alla belastningsutjämnare som nätverksgränssnittet tillhör) |
| Microsoft.Network/networkInterfaces/join/action | Ansluter en virtuell dator till ett nätverksgränssnitt |
| Microsoft.Network/networkInterfaces/read | Hämtar en definition av nätverksgränssnitt.  |
| Microsoft.Network/networkInterfaces/write | Skapar ett nätverksgränssnitt eller uppdaterar en befintlig nätverksgränssnitt.  |
| Microsoft.Network/publicIPAddresses/*/read | Läsa egenskaperna för en offentlig IP-adress |
| Microsoft.Network/publicIPAddresses/join/action | Ansluter till en offentlig ip-adress |
| Microsoft.Network/publicIPAddresses/read | Hämtar en definition av offentlig ip-adress. |
| Microsoft.Network/virtualNetworks/subnets/join/action | Ansluter till ett virtuellt nätverk |
| Microsoft.Resources/deployments/operations/read | Hämtar eller listar distributionsåtgärder. |
| Microsoft.Resources/deployments/read | Hämtar eller listar distributioner. |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Storage/storageAccounts/listKeys/action | Returnerar åtkomstnycklarna för det angivna lagringskontot. |

| **NotActions** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/vmSizes/read | Visar en lista över tillgängliga storlekar som de virtuella datorerna kan uppdateras till |

## <a name="dns-zone-contributor"></a>DNS-zondeltagare
Låter dig hantera DNS-zoner och postuppsättningar i Azure DNS, men låter dig inte kontrollera vem som har åtkomst till dem.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
| Microsoft.Network/dnsZones/* | Skapa och hantera DNS-zoner och poster |
| Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="documentdb-account-contributor"></a>DocumentDB-kontodeltagare
Hantera Azure DB som Cosmos-konton. Azure Cosmos-DB är kallades DocumentDB.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rollen tilldelningar |
| Microsoft.DocumentDb/databaseAccounts/* | Skapa och hantera Azure DB som Cosmos-konton |
| Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="intelligent-systems-account-contributor"></a>Intelligent Systems-kontodeltagare
Låter dig hantera Intelligent Systems-konton, men ger dig inte tillgång till dem.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rollen tilldelningar |
| Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
| Microsoft.IntelligentSystems/accounts/* | Skapa och hantera intelligenta system konton |
| Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="key-vault-contributor"></a>Nyckelvalvsdeltagare
Låter dig hantera nyckelvalv, men inte ha åtkomst till dem.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.Insights/alertRules/* | Skapa och hantera insikter Varningsregler |
| Microsoft.KeyVault/* |  |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

| **NotActions** |  |
| --- | --- |
| Microsoft.KeyVault/locations/deletedVaults/purge/action | Rensa ett ej permanent borttaget nyckelvalv |
| Microsoft.KeyVault/hsmPools/* |  |

## <a name="lab-creator"></a>Lab Creator
Kan du skapa, hantera, ta bort dina hanterade labs under din Azure Lab konton.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.ManagedLab/labAccounts/createLab/action | Skapa ett labb i ett labb-konto. |
| Microsoft.ManagedLab/labAccounts/*/read |  |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="log-analytics-contributor"></a>Log Analytics Contributor
Log Analytics deltagare kan läsa alla övervakningsdata och redigera inställningarna för övervakning. Redigera inställningarna för övervakning innehåller lägger till VM-tillägget på virtuella datorer; läsa lagringskontonycklar för att kunna konfigurera samlingen loggar från Azure Storage; Skapa och konfigurera Automation-konton lägga till lösningar. och konfigurera Azure-diagnostik på alla Azure-resurser.

| **Åtgärder** |  |
| --- | --- |
| * / läsa | Läsa resurser av alla typer utom hemligheter. |
| Microsoft.Automation/automationAccounts/* |  |
| Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Listar lagringskontots åtkomstnycklar. |
| Microsoft.Compute/virtualMachines/extensions/* |  |
| Microsoft.Insights/alertRules/* | Skapa och hantera insikter Varningsregler |
| Microsoft.Insights/diagnosticSettings/* | Skapar, uppdaterar eller läser diagnostikinställningen för Analysis Server |
| Microsoft.OperationalInsights/* |  |
| Microsoft.OperationsManagement/* |  |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
| Microsoft.Storage/storageAccounts/listKeys/action | Returnerar åtkomstnycklarna för det angivna lagringskontot. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="log-analytics-reader"></a>Log Analytics Reader
Log Analytics Reader kan visa och söka i alla övervakningsdata och dessutom visa övervakningsinställningar, bl.a. konfigurationen av Azure Diagnostics på alla Azure-resurser.

| **Åtgärder** |  |
| --- | --- |
| * / läsa | Läsa resurser av alla typer utom hemligheter. |
| Microsoft.OperationalInsights/workspaces/analytics/query/action | Sökningen med nya motorn. |
| Microsoft.OperationalInsights/workspaces/search/action | Kör en sökfråga |
| Microsoft.Support/* | Skapa och hantera supportärenden |

| **NotActions** |  |
| --- | --- |
| Microsoft.OperationalInsights/workspaces/sharedKeys/read | Hämtar de delade nycklarna för arbetsytan. Dessa nycklar används för att ansluta Microsoft åtgärdsinformation agenter till arbetsytan. |

## <a name="logic-app-contributor"></a>Logic App-deltagare
Låter dig hantera logikappar, men du kan inte komma åt dem.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Listar lagringskontots åtkomstnycklar. |
| Microsoft.ClassicStorage/storageAccounts/read | Returnerar lagringskontot med det givna kontot. |
| Microsoft.Insights/alertRules/* | Skapa och hantera insikter Varningsregler |
| Microsoft.Insights/diagnosticSettings/* | Skapar, uppdaterar eller läser diagnostikinställningen för Analysis Server |
| Microsoft.Insights/logdefinitions/* | Den här behörigheten krävs för användare som behöver åtkomst till aktivitetsloggar via portalen. Lista loggen kategorier i aktivitetsloggen. |
| Microsoft.Insights/metricDefinitions/* | Läs måttdefinitionerna (lista över tillgängliga mått typer för en resurs). |
| Microsoft.Logic/* | Hanterar Logic Apps resurser. |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/operationresults/read | Hämtar prenumerationsåtgärdsresultaten. |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Storage/storageAccounts/listkeys/action | Returnerar åtkomstnycklarna för det angivna lagringskontot. |
| Microsoft.Storage/storageAccounts/read | Returnerar listan med lagringskonton eller hämtar egenskaperna för det angivna lagringskontot. |
| Microsoft.Support/* | Skapa och hantera supportärenden |
| Microsoft.Web/connectionGateways/* | Skapar och hanterar en Gateway med anslutning. |
| Microsoft.Web/connections/* | Skapar och hanterar en anslutning. |
| Microsoft.Web/customApis/* | Skapar och hanterar en anpassad API. |
| Microsoft.Web/serverFarms/join/action |  |
| Microsoft.Web/serverFarms/read | Visa egenskaperna för en App Service-Plan |
| Microsoft.Web/sites/functions/listSecrets/action | Lista hemligheter Web Apps funktioner. |

## <a name="logic-app-operator"></a>Logic App-operatör
Låter dig läsa, aktivera och inaktivera logikapp.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.Insights/alertRules/*/read | Läs insikter Varningsregler |
| Microsoft.Insights/diagnosticSettings/*/read | Hämtar diagnostikinställningar för Logic Apps |
| Microsoft.Insights/metricDefinitions/*/read | Hämtar tillgängliga mått för Logic Apps. |
| Microsoft.Logic/*/read | Läser Logic Apps resurser. |
| Microsoft.Logic/workflows/disable/action | Inaktiverar arbetsflödet. |
| Microsoft.Logic/workflows/enable/action | Aktiverar arbetsflödet. |
| Microsoft.Logic/workflows/validate/action | Verifierar arbetsflödet. |
| Microsoft.Resources/deployments/operations/read | Hämtar eller listar distributionsåtgärder. |
| Microsoft.Resources/subscriptions/operationresults/read | Hämtar prenumerationsåtgärdsresultaten. |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |
| Microsoft.Web/connectionGateways/*/read | Läsa anslutning Gateways. |
| Microsoft.Web/connections/*/read | Läsa anslutningar. |
| Microsoft.Web/customApis/*/read | Läs anpassade API. |
| Microsoft.Web/serverFarms/read | Visa egenskaperna för en App Service-Plan |

## <a name="managed-identity-contributor"></a>Hanterade identitet deltagare
Skapa, läsa, uppdatera och ta bort användaren som har tilldelats identitet

| **Åtgärder** |  |
| --- | --- |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/write |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/delete |  |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.Insights/alertRules/* | Skapa och hantera insikter Varningsregler |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="managed-identity-operator"></a>Hanterade identitet Operator
Läsa och tilldela användaren som har tilldelats identitet

| **Åtgärder** |  |
| --- | --- |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.Insights/alertRules/* | Skapa och hantera insikter Varningsregler |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="monitoring-contributor"></a>Övervaka deltagare
Kan läsa alla övervakningsdata och redigera inställningarna för övervakning. Se även [Kom igång med roller, behörigheter och säkerhet med Azure-Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles).

| **Åtgärder** |  |
| --- | --- |
| * / läsa | Läsa resurser av alla typer utom hemligheter. |
| Microsoft.AlertsManagement/alerts/* |  |
| Microsoft.AlertsManagement/alertsSummary/* |  |
| Microsoft.Insights/AlertRules/* | Läs/Skriv/ta bort Varningsregler. |
| Microsoft.Insights/components/* | Läs/Skriv/ta bort Application Insights-komponenter. |
| Microsoft.Insights/DiagnosticSettings/* | Diagnostikinställningar för Läs/Skriv/ta bort. |
| Microsoft.Insights/eventtypes/* | Lista över aktivitetsloggen händelser (management-händelser) i en prenumeration. Den här behörigheten gäller både programmässiga och portal åtkomst till aktivitetsloggen. |
| Microsoft.Insights/LogDefinitions/* | Den här behörigheten krävs för användare som behöver åtkomst till aktivitetsloggar via portalen. Lista loggen kategorier i aktivitetsloggen. |
| Microsoft.Insights/MetricDefinitions/* | Läs måttdefinitionerna (lista över tillgängliga mått typer för en resurs). |
| Microsoft.Insights/Metrics/* | Läsa måtten för en resurs. |
| Microsoft.Insights/Register/Action | Registrera Microsoft Insights-providern |
| Microsoft.Insights/webtests/* | Läs/Skriv/ta bort Application Insights web tester. |
| Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Läs/Skriv/ta bort logganalys-lösningen packs. |
| Microsoft.OperationalInsights/workspaces/savedSearches/* | Läs/Skriv/ta bort logganalys sparade sökningar. |
| Microsoft.OperationalInsights/workspaces/search/action | Kör en sökfråga |
| Microsoft.OperationalInsights/workspaces/sharedKeys/action | Hämtar de delade nycklarna för arbetsytan. Dessa nycklar används för att ansluta Microsoft åtgärdsinformation agenter till arbetsytan. |
| Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Läs/Skriv/ta bort logganalys insight lagringskonfigurationer. |
| Microsoft.Support/* | Skapa och hantera supportärenden |
| Microsoft.WorkloadMonitor/workloads/* |  |

## <a name="monitoring-reader"></a>Övervaka läsare
Kan läsa alla övervakningsdata (mått, loggar osv.). Se även [Kom igång med roller, behörigheter och säkerhet med Azure-Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles).

| **Åtgärder** |  |
| --- | --- |
| * / läsa | Läsa resurser av alla typer utom hemligheter. |
| Microsoft.OperationalInsights/workspaces/search/action | Kör en sökfråga |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="network-contributor"></a>Nätverksdeltagare
Låter dig hantera nätverk, men ger dig inte tillgång till dem.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rollen tilldelningar |
| Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
| Microsoft.Network/* | Skapa och hantera nätverk |
| Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="new-relic-apm-account-contributor"></a>New Relic APM-kontodeltagare
Låter dig hantera New Relic Application Performance Management-konton och program, men ger dig inte tillgång till dem.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.Insights/alertRules/* | Skapa och hantera insikter Varningsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |
| NewRelic.APM/accounts/* |  |

## <a name="redis-cache-contributor"></a>Redis Cache-deltagare
Låter dig hantera Redis-cacheminnen, men ger dig inte tillgång till dem.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rollen tilldelningar |
| Microsoft.Cache/redis/* | Skapa och hantera Redis-cache |
| Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="scheduler-job-collections-contributor"></a>Scheduler-jobbsamlingsdeltagare
Låter dig hantera Scheduler-jobbsystem, men ger dig inte tillgång till dem.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rollen tilldelningar |
| Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Scheduler/jobcollections/* | Skapa och hantera jobbsamlingar |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="search-service-contributor"></a>Söktjänstdeltagare
Låter dig hantera söktjänster, men ger dig inte tillgång till dem.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rollen tilldelningar |
| Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Search/searchServices/* | Skapa och hantera search-tjänster |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="security-admin"></a>Säkerhetsadministratör
I Security Center endast: Visa säkerhetsprinciper, visa säkerhetsstatus, redigera säkerhetsprinciper, Visa aviseringar och rekommendationerna, stänga aviseringar och rekommendationer

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.Authorization/policyAssignments/* | Skapa och hantera principtilldelningar |
| Microsoft.Authorization/policyDefinitions/* | Skapa och hantera principdefinitioner |
| Microsoft.Authorization/policySetDefinitions/* | Skapa och hantera principen anger |
| Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
| Microsoft.operationalInsights/workspaces/*/read | Visa logganalys data |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Security/*/read | Läs säkerhetskomponenter och principer |
| Microsoft.Security/locations/alerts/dismiss/action | Avvisa en säkerhetsavisering |
| Microsoft.Security/locations/tasks/dismiss/action | Stänga en säkerhetsrekommendation |
| Microsoft.Security/policies/write | Uppdaterar säkerhetsprincipen |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="security-manager"></a>Säkerhetshanteraren
Låter dig hantera säkerhetskomponenter, säkerhetsprinciper och virtuella datorer

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.ClassicCompute/*/read | Läsa konfigurationsinformation klassiska virtuella datorer |
| Microsoft.ClassicCompute/virtualMachines/*/write | Spara konfigurationen för klassiska virtuella datorer |
| Microsoft.ClassicNetwork/*/read | Läsa konfigurationsinformation om klassiska nätverket |
| Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Security/* | Skapa och hantera säkerhetskomponenter och principer |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="security-reader"></a>Säkerhetsläsare
I Security Center endast: Visa rekommendationer och aviseringar, visa säkerhetsprinciper, visa säkerhetsstatus, men det går inte att göra ändringar

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.operationalInsights/workspaces/*/read | Visa logganalys data |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.Support/* | Skapa och hantera supportärenden |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Security/*/read | Läs säkerhetskomponenter och principer |

## <a name="site-recovery-contributor"></a>Site Recovery-bidragsgivare
Låter dig hantera Site Recovery-tjänsten förutom att skapa valv och tilldela roller

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
| Microsoft.Network/virtualNetworks/read | Hämta definitionen av virtuellt nätverk |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp är en intern åtgärd som används av tjänsten |
| Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp är intern åtgärd som används av tjänsten |
| Microsoft.RecoveryServices/Vaults/certificates/write | Uppdatera resurs certifikat åtgärden uppdaterar Autentiseringscertifikatet resurs-valvet. |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | Skapa och hantera utökad information som rör valvet |
| Microsoft.RecoveryServices/Vaults/read | Åtgärden hämta valvet hämtar ett objekt som representerar Azure-resurs av typen 'valvet' |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Skapa och hantera registrerade identiteter |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Skapa eller uppdatera replikeringsinställningarna för avisering |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Läsa alla händelser |
| Microsoft.RecoveryServices/vaults/replicationFabrics/* | Skapa och hantera infrastrukturresurser för replikering |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | Skapa och hantera replikeringsjobb |
| Microsoft.RecoveryServices/vaults/replicationPolicies/* | Skapa och hantera replikeringsprinciper |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Skapa och hantera återställningsplaner |
| Microsoft.RecoveryServices/Vaults/storageConfig/* | Skapa och hantera konfigurationen av Recovery Services-valvet |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Returnerar tokeninformation för Recovery Services-valvet. |
| Microsoft.RecoveryServices/Vaults/usages/read | Returnerar användningsinformation om Recovery Services-valvet. |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | Token valvet igen kan användas för att hämta valv Token för valvet nivån backend-åtgärder. |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Läsa aviseringar för Recovery services-valvet |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/läsning |  |
| Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Storage/storageAccounts/read | Returnerar listan med lagringskonton eller hämtar egenskaperna för det angivna lagringskontot. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="site-recovery-operator"></a>Site Recovery-operatör
Låter dig växla vid fel och återställa men inte utföra andra Site Recovery-hanteringsåtgärder

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
| Microsoft.Network/virtualNetworks/read | Hämta definitionen av virtuellt nätverk |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp är en intern åtgärd som används av tjänsten |
| Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp är intern åtgärd som används av tjänsten |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv? |
| Microsoft.RecoveryServices/Vaults/read | Åtgärden hämta valvet hämtar ett objekt som representerar Azure-resurs av typen 'valvet' |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Hämta åtgärden resulterar åtgärden kan användas för hämta Åtgärdsstatus och resultat för asynkront skickats igen |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Hämta behållarna åtgärden kan användas för hämta behållare som har registrerats för en resurs. |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Läsa alla aviseringsinställningar |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Läsa alla händelser |
| Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Kontrollerar infrastrukturens enhetlighet |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read | Läsa alla Infrastrukturresurser |
| Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Skapa en ny koppling Gateway |
| Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Förnya certifikat för infrastruktur |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Läsa alla nätverk |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/läsning | Läsa alla nätverksmappningar |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/läsning | Läsa skydd behållare |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/läsning | Läsa alla objekt som kan skyddas |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/åtgärd | Tillämpa återställningspunkt |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/failoverCommit/åtgärd | Redundansåtgärd |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/plannedFailover/åtgärd | Planerad redundans |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/läsning | Läsa alla skyddade objekt |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/läsning | Läsa alla återställningspunkter för replikeringen |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/repairReplication/åtgärd | Reparera replikering |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/skyddar/åtgärd | Skapa nytt skyddat objekt |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/testFailover/åtgärd | Testa redundans |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/åtgärd | Redundanstestningen |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/unplannedFailover/åtgärd | Redundans |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/updateMobilityService/åtgärd | Uppdatera Mobilitetstjänsten |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/läsning | Läsa alla mappningar för behållaren skydd |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/läsning | Läsa alla Recovery Services-Providers |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/refreshProvider/åtgärd | Uppdatera providern |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/läsning | Läsa alla Lagringsklassificeringar |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/läsning | Läsa alla mappningar för klassificering av lagring |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Läsa alla jobb |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | Skapa och hantera replikeringsjobb |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read | Läsa alla principer |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Redundansåtgärd återställningsplan |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Planerad redundans återställningsplan |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Läsa alla Återställningsplaner |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Skapa nytt återställningsplan |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Testa redundans återställningsplan |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Testa redundans Rensa återställningsplan |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plan för växling vid fel |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Läsa aviseringar för Recovery services-valvet |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/läsning |  |
| Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Returnerar tokeninformation för Recovery Services-valvet. |
| Microsoft.RecoveryServices/Vaults/usages/read | Returnerar användningsinformation om Recovery Services-valvet. |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | Token valvet igen kan användas för att hämta valv Token för valvet nivån backend-åtgärder. |
| Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Storage/storageAccounts/read | Returnerar listan med lagringskonton eller hämtar egenskaperna för det angivna lagringskontot. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="site-recovery-reader"></a>Site Recovery-läsare
Låter dig se Site Recovery-status men inte utföra andra hanteringsåtgärder

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp är en intern åtgärd som används av tjänsten |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv? |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Hämtar aviseringar för Recovery services-valvet. |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/läsning |  |
| Microsoft.RecoveryServices/Vaults/read | Åtgärden hämta valvet hämtar ett objekt som representerar Azure-resurs av typen 'valvet' |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Hämta åtgärden resulterar åtgärden kan användas för hämta Åtgärdsstatus och resultat för asynkront skickats igen |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Hämta behållarna åtgärden kan användas för hämta behållare som har registrerats för en resurs. |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Läsa alla aviseringsinställningar |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Läsa alla händelser |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read | Läsa alla Infrastrukturresurser |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Läsa alla nätverk |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/läsning | Läsa alla nätverksmappningar |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/läsning | Läsa skydd behållare |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/läsning | Läsa alla objekt som kan skyddas |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/läsning | Läsa alla skyddade objekt |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/läsning | Läsa alla återställningspunkter för replikeringen |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/läsning | Läsa alla mappningar för behållaren skydd |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/läsning | Läsa alla Recovery Services-Providers |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/läsning | Läsa alla Lagringsklassificeringar |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/läsning | Läsa alla mappningar för klassificering av lagring |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Läsa alla jobb |
| Microsoft.RecoveryServices/vaults/replicationJobs/read | Läsa alla jobb |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read | Läsa alla principer |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Läsa alla Återställningsplaner |
| Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Returnerar tokeninformation för Recovery Services-valvet. |
| Microsoft.RecoveryServices/Vaults/usages/read | Returnerar användningsinformation om Recovery Services-valvet. |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | Token valvet igen kan användas för att hämta valv Token för valvet nivån backend-åtgärder. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="sql-db-contributor"></a>SQL DB-deltagare
Kan du hantera SQL-databaser, men inte åtkomst till dem. Du kan också hantera deras säkerhetsrelaterade principer eller sina överordnade SQL-servrar.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rollen tilldelningar |
| Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Sql/locations/*/read |  |
| Microsoft.Sql/servers/databases/* | Skapa och hantera SQL-databaser |
| Microsoft.Sql/servers/read | Returnera listan över servrar eller hämtar egenskaperna för den angivna servern. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Det går inte att redigera granskningsprinciper |
| Microsoft.Sql/servers/databases/auditingSettings/* | Det går inte att redigera granskningsinställningar |
| Microsoft.Sql/servers/databases/auditRecords/read | Hämta databasen blob granskningsposter |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Det går inte att redigera principer |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Det går inte att redigera datamaskning principer |
| Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Det går inte att redigera avisering säkerhetsprinciper |
| Microsoft.Sql/servers/databases/securityMetrics/* | Det går inte att redigera säkerhet mått |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |

## <a name="sql-security-manager"></a>SQL-säkerhetshanteraren
Tillåter dig att hantera säkerhetsrelaterade principer för SQL-servrar och databaser, men inte åtkomst till dem.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs Microsoft auktorisering |
| Microsoft.Insights/alertRules/* | Skapa och hantera insikter Varningsregler |
| Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Ansluter till resursen, till exempel lagringskonto eller SQL-databas till ett undernät. |
| Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Sql/servers/auditingPolicies/* | Skapa och hantera granskningsprinciper för SQL server |
| Microsoft.Sql/servers/auditingSettings/* | Skapa och hantera granskning inställning för SQL server |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Skapa och hantera granskningsprinciper för SQL server-databas |
| Microsoft.Sql/servers/databases/auditingSettings/* | Skapa och hantera granskning inställningar för SQL server-databas |
| Microsoft.Sql/servers/databases/auditRecords/read | Läs granskningsposter |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Skapa och hantera principer för SQL server-databas |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Skapa och hantera SQL server-databasen datamaskning principer |
| Microsoft.Sql/servers/databases/read | Returnera listan över databaser eller hämtar egenskaperna för den angivna databasen. |
| Microsoft.Sql/servers/databases/schemas/read | Hämta listan över scheman för en databas |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | Hämta listan över kolumner i en tabell |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/read | Hämta listan över tabeller i en databas |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Skapa och hantera SQL server-databasen avisering säkerhetsprinciper |
| Microsoft.Sql/servers/databases/securityMetrics/* | Skapa och hantera mått för SQL server-databasen säkerhet |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
| Microsoft.Sql/servers/firewallRules/* |  |
| Microsoft.Sql/servers/read | Returnera listan över servrar eller hämtar egenskaperna för den angivna servern. |
| Microsoft.Sql/servers/securityAlertPolicies/* | Skapa och hantera aviseringar principer för SQL server-säkerhet |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="sql-server-contributor"></a>SQL Server-deltagare
Tillåter dig att hantera SQL-servrar och databaser, men inte åtkomst till dem eller deras säkerhetsrelaterade principer.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.Insights/alertRules/* | Skapa och hantera insikter Varningsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Sql/locations/*/read |  |
| Microsoft.Sql/servers/* | Skapa och hantera SQL-servrar |
| Microsoft.Support/* | Skapa och hantera supportärenden |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/auditingPolicies/* | Det går inte att redigera SQL server-granskningsprinciper |
| Microsoft.Sql/servers/auditingSettings/* | Det går inte att redigera SQL server-granskningsinställningar |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Det går inte att redigera granskningsprinciper för SQL server-databas |
| Microsoft.Sql/servers/databases/auditingSettings/* | Det går inte att redigera granskningsinställningar för SQL server-databas |
| Microsoft.Sql/servers/databases/auditRecords/read | Det går inte att läsa granskningsposter |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Det går inte att redigera principer för SQL server-databas |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Det går inte att redigera SQL server-databasen datamaskning principer |
| Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Det går inte att redigera SQL server-databasen avisering säkerhetsprinciper |
| Microsoft.Sql/servers/databases/securityMetrics/* | Det går inte att redigera SQL server-databasen säkerhet mått |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
| Microsoft.Sql/servers/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/securityAlertPolicies/* | Det går inte att redigera avisering principer för SQL server-säkerhet |

## <a name="storage-account-contributor"></a>Lagringskontodeltagare
Låter dig hantera lagringskonton, men ger dig inte åtkomst till dem.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läsa alla auktorisering |
| Microsoft.Insights/alertRules/* | Skapa och hantera insikter Varningsregler |
| Microsoft.Insights/diagnosticSettings/* | Hantera diagnostikinställningar |
| Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Ansluter till resursen, till exempel lagringskonto eller SQL-databas till ett undernät. |
| Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Storage/storageAccounts/* | Skapa och hantera storage-konton |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="storage-account-key-operator-service-role"></a>Tjänstroll som operatör av lagringskontonyckel
Operatörer av lagringskontonycklar får lista och återskapa nycklar till lagringskonton

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Storage/storageAccounts/listkeys/action | Returnerar åtkomstnycklarna för det angivna lagringskontot. |
| Microsoft.Storage/storageAccounts/regeneratekey/action | Återskapar åtkomstnycklarna för det angivna lagringskontot. |

## <a name="support-request-contributor"></a>Supportförfrågningsdeltagare
Låter dig skapa och hantera supportförfrågningar

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läsa tillstånd |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="traffic-manager-contributor"></a>Traffic Manager-deltagare
Låter dig hantera Traffic Manager-profiler, men låter dig inte kontrollera vem som har åtkomst till dem.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
| Microsoft.Insights/alertRules/* | Skapa och hantera insikter Varningsregler |
| Microsoft.Network/trafficManagerProfiles/* |  |
| Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="user-access-administrator"></a>Administratör för användaråtkomst
Låter dig hantera användaråtkomst till Azure-resurser.

| **Åtgärder** |  |
| --- | --- |
| * / läsa | Läsa resurser av alla typer utom hemligheter. |
| Microsoft.Authorization/* | Hantera auktorisering |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="virtual-machine-administrator-login"></a>Administratören för virtuella datorer inloggning
-   Användare med den här rollen har möjlighet att logga in på en virtuell dator med Windows-administratör eller användarbehörigheter för Linux-rot.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/loginAsAdmin/action |  |
| Microsoft.Compute/virtualMachines/login/action |  |
| Microsoft.Compute/virtualMachine/loginAsAdmin/action |  |
| Microsoft.Compute/virtualMachine/logon/action |  |

## <a name="virtual-machine-contributor"></a>Virtuell datordeltagare
Kan du hantera virtuella datorer, men inte åtkomst till dem, och inte virtuellt nätverk eller lagringskonto som de är anslutna till.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läsa tillstånd |
| Microsoft.Compute/availabilitySets/* | Skapa och hantera beräknings-tillgänglighetsuppsättningar |
| Microsoft.Compute/locations/* | Skapa och hantera beräknings-platser |
| Microsoft.Compute/virtualMachines/* | Skapa och hantera virtuella datorer |
| Microsoft.Compute/virtualMachineScaleSets/* | Skapa och hantera virtuella datorer |
| Microsoft.DevTestLab/schedules/* |  |
| Microsoft.Insights/alertRules/* | Skapa och hantera insikter Varningsregler |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | Ansluter en serverdelsadresspool för programmet gateway |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Ansluter till en belastningsutjämnare adress serverdelspool |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action | Ansluter till en belastningsutjämnare inkommande nat-pool |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Ansluter en inkommande nat-regel för belastningsutjämnare |
| Microsoft.Network/loadBalancers/probes/join/action | Kan använda avsökningar av belastningsutjämning. Med den här behörigheten healthProbe egenskapen VM scale kan set referera exempelvis avsökningen. |
| Microsoft.Network/loadBalancers/read | Hämtar en belastningsutjämnardefinition |
| Microsoft.Network/locations/* | Skapa och hantera nätverksplatser |
| Microsoft.Network/networkInterfaces/* | Skapa och hantera nätverksgränssnitt |
| Microsoft.Network/networkSecurityGroups/join/action | Ansluter en nätverkssäkerhetsgrupp |
| Microsoft.Network/networkSecurityGroups/read | Hämtar en definition av nätverkssäkerhetsgrupp |
| Microsoft.Network/publicIPAddresses/join/action | Ansluter till en offentlig ip-adress |
| Microsoft.Network/publicIPAddresses/read | Hämtar en definition av offentlig ip-adress. |
| Microsoft.Network/virtualNetworks/read | Hämta definitionen av virtuellt nätverk |
| Microsoft.Network/virtualNetworks/subnets/join/action | Ansluter till ett virtuellt nätverk |
| Microsoft.RecoveryServices/locations/* |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems / * / läsa |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/läsning | Returnerar information om objekt för det skyddade objektet |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/skrivning | Skapa en säkerhetskopiera skyddade objekt |
| Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Skapa en säkerhetskopiering skydd avsett |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | Returnerar alla Protection-principer |
| Microsoft.RecoveryServices/Vaults/backupPolicies/write | Skapar Protection-principen |
| Microsoft.RecoveryServices/Vaults/read | Åtgärden hämta valvet hämtar ett objekt som representerar Azure-resurs av typen 'valvet' |
| Microsoft.RecoveryServices/Vaults/usages/read | Returnerar användningsinformation om Recovery Services-valvet. |
| Microsoft.RecoveryServices/Vaults/write | Med skapa valv så skapas en Azure-resurs av typen valv |
| Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Storage/storageAccounts/listKeys/action | Returnerar åtkomstnycklarna för det angivna lagringskontot. |
| Microsoft.Storage/storageAccounts/read | Returnerar listan med lagringskonton eller hämtar egenskaperna för det angivna lagringskontot. |
| Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="virtual-machine-user-login"></a>Användarinloggning för virtuell dator
Användare med den här rollen har möjlighet att logga in på en virtuell dator som en vanlig användare.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/login/action |  |
| Microsoft.Compute/virtualMachine/logon/action |  |

## <a name="web-plan-contributor"></a>Webbplan-deltagare
Låter dig hantera webbplaner för webbplatser, men ger dig inte tillgång till dem.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läsa tillstånd |
| Microsoft.Insights/alertRules/* | Skapa och hantera insikter Varningsregler |
| Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |
| Microsoft.Web/serverFarms/* | Skapa och hantera servergrupper |

## <a name="website-contributor"></a>Webbplatsdeltagare
Låter dig hantera webbplatser (men inte webbplaner), men ger dig inte tillgång till dem.

| **Åtgärder** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Läsa tillstånd |
| Microsoft.Insights/alertRules/* | Skapa och hantera insikter Varningsregler |
| Microsoft.Insights/components/* | Skapa och hantera Insights-komponenter |
| Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
| Microsoft.Resources/deployments/* | Skapa och hantera distributionen av resursgrupper |
| Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
| Microsoft.Support/* | Skapa och hantera supportärenden |
| Microsoft.Web/certificates/* | Skapa och hantera webbplatscertifikat |
| Microsoft.Web/listSitesAssignedToHostName/read | Hämta namnen på de platser som tilldelats värdnamn. |
| Microsoft.Web/serverFarms/join/action |  |
| Microsoft.Web/serverFarms/read | Visa egenskaperna för en App Service-Plan |
| Microsoft.Web/sites/* | Skapa och hantera webbplatser (för att skapa även kräver behörighet att skriva till den associerade Apptjänstplan) |

## <a name="see-also"></a>Se också
* [Rollbaserad åtkomstkontroll](role-assignments-portal.md): komma igång med RBAC på Azure-portalen.
* [Anpassade roller i Azure RBAC](custom-roles.md): Lär dig att skapa anpassade roller som passar dina åtkomstbehov.
* [Skapa en profil över åtkomständringshistorik](change-history-report.md): hålla reda på hur du ändrar rolltilldelningar i RBAC.
* [Rollbaserad åtkomstkontroll felsökning](troubleshooting.md): få förslag om hur du löser vanliga problem.
* [Behörigheter i Azure Security Center](../security-center/security-center-permissions.md)
