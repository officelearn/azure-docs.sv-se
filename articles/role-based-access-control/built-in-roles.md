---
title: Inbyggda roller för Azure-resurser | Microsoft Docs
description: Beskriver de inbyggda rollerna för rollbaserad åtkomstkontroll (RBAC) och Azure-resurser. Visar en lista över åtgärder, NotActions, DataActions och NotDataActions.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 10/19/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: b0dc41959902bda4e61e8ce6a25f94163a562b12
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466860"
---
# <a name="built-in-roles-for-azure-resources"></a>Inbyggda roller för Azure-resurser
[Rollbaserad åtkomstkontroll (RBAC)](overview.md) har flera inbyggda rolldefinitioner som du kan tilldela till användare, grupper och tjänstens huvudnamn. Rolltilldelningar är det sätt som du styr åtkomst till resurser i Azure. Om de inbyggda rollerna inte uppfyller organisationens specifika krav kan du skapa egna, [anpassade roller](custom-roles.md).

De inbyggda rollerna är alltid under utveckling. Hämta de senaste rolldefinitionerna för [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) eller [az role definition list](/cli/azure/role/definition#az-role-definition-list).

## <a name="built-in-role-descriptions"></a>Inbyggd rollbeskrivningar
I följande tabell innehåller korta beskrivningar av de inbyggda rollerna. Klicka på namnet på rollen för att se en lista över `Actions`, `NotActions`, `DataActions`, och `NotDataActions` för varje roll.


| Inbyggd roll | Beskrivning |
| --- | --- |
| [Ägare](#owner) | Låter dig hantera allt, inklusive åtkomst till resurser. |
| [Deltagare](#contributor) | Låter dig hantera allt, med undantag för åtkomst till resurser. |
| [Läsare](#reader) | Låter dig visa allting, men låter dig inte göra några ändringar. |
| [AcrImageSigner](#acrimagesigner) | acr-bildsignerare |
| [AcrQuarantineReader](#acrquarantinereader) | acr-karantändataläsare |
| [AcrQuarantineWriter](#acrquarantinewriter) | acr-karantändataskrivare |
| [API Management-Tjänstdeltagare](#api-management-service-contributor) | Låter dig hantera API Management-tjänster, men ger dig inte tillgång till dem. |
| [Operatörsroll för API Management](#api-management-service-operator-role) | Kan hantera tjänsten men inte API:er |
| [Läsarroll för API Management-tjänsten](#api-management-service-reader-role) | Skrivskyddad åtkomst till tjänst och API:er |
| [Application Insights-Komponentdeltagare](#application-insights-component-contributor) | Kan hantera Application Insights-komponenter |
| [Application Insights Snapshot Debugger](#application-insights-snapshot-debugger) | Ger användarbehörighet att visa och hämta ögonblicksbilder för felsökning som samlas in med Application Insights Snapshot Debugger. Observera att dessa behörigheter inte ingår i den [ägare](#owner) eller [deltagare](#contributor) roller. |
| [Automation-Jobboperator](#automation-job-operator) | Skapa och hantera jobb med Automation Runbooks. |
| [Automation-operatör](#automation-operator) | Automation-operatörer kan starta, stoppa, göra uppehåll i och återuppta jobb |
| [Automation Runbook-Operator](#automation-runbook-operator) | Läs runbook-egenskaperna för att kunna skapa jobb av den runbooken. |
| [Administratörsroll för Azure Kubernetes Service-kluster](#azure-kubernetes-service-cluster-admin-role) | Lista över kluster administratörsåtgärd autentiseringsuppgifter. |
| [Användarrollen för Azure Kubernetes Service-kluster](#azure-kubernetes-service-cluster-user-role) | Lista över autentiseringsuppgifter för kluster användaråtgärd. |
| [Azure Stack-registrering ägare](#azure-stack-registration-owner) | Låter dig hantera Azure Stack-registreringar. |
| [Säkerhetskopieringsmedarbetare](#backup-contributor) | Låter dig hantera säkerhetskopieringstjänsten, men låter dig inte skapa valv eller ge åtkomst till andra |
| [Ansvarig för säkerhetskopiering](#backup-operator) | Låter dig hantera säkerhetskopieringstjänster, med undantag för att ta bort säkerhetskopior, skapa valv eller ge åtkomst till andra |
| [Säkerhetskopieringsläsare](#backup-reader) | Kan visa säkerhetskopieringstjänster, men inte göra några ändringar |
| [Faktureringsläsare](#billing-reader) | Låter dig läsa faktureringsdata |
| [BizTalk-deltagare](#biztalk-contributor) | Låter dig hantera BizTalk-tjänster, men ger dig inte tillgång till dem. |
| [CDN-Slutpunktsdeltagare](#cdn-endpoint-contributor) | Kan hantera CDN-slutpunkter, men kan inte bevilja åtkomst till andra användare. |
| [CDN-Slutpunktsläsare](#cdn-endpoint-reader) | Kan visa CDN-slutpunkter, men kan inte göra ändringar. |
| [CDN-Profildeltagare](#cdn-profile-contributor) | Kan hantera CDN-profiler och deras slutpunkter, men kan inte bevilja åtkomst till andra användare. |
| [CDN-Profilläsare](#cdn-profile-reader) | Kan visa CDN-profiler och deras slutpunkter, men kan inte göra ändringar. |
| [Klassisk nätverksdeltagare](#classic-network-contributor) | Låter dig hantera klassiska nätverk, men ger dig inte tillgång till dem. |
| [Klassisk Lagringskontodeltagare](#classic-storage-account-contributor) | Låter dig hantera klassiska lagringskonton, men ger dig inte åtkomst att hantera dem. |
| [Tjänstroll som operatör av Lagringskontonyckel konto klassisk lagring](#classic-storage-account-key-operator-service-role) | Operatörer av klassiska lagringskontonycklar får lista och återskapa nycklar till klassiska lagringskonton |
| [Klassisk virtuell Datordeltagare](#classic-virtual-machine-contributor) | Låter dig hantera klassiska virtuella datorer, men ger dig inte tillgång till dem eller till det virtuella nätverk eller lagringskonto som de är anslutna till. |
| [Cognitive Services-deltagare](#cognitive-services-contributor) | Kan du skapa, läsa, uppdatera, ta bort och hantera nycklar för Cognitive Services. |
| [Cognitive Services-användare](#cognitive-services-user) | Kan du läsa och lista nycklar för Cognitive Services. |
| [Läsarroll för cosmos DB-konto](#cosmos-db-account-reader-role) | Kan läsa data i Azure Cosmos DB-konto. Se [DocumentDB-Kontodeltagare](#documentdb-account-contributor) för att hantera Azure Cosmos DB-konton. |
| [Kostnadshantering deltagare](#cost-management-contributor) | Kan visa kostnaderna och hantera kostnaden konfiguration (t.ex. budgetar, export) |
| [Kostnadshantering läsare](#cost-management-reader) | Visa kostnadsdata och konfiguration (t.ex. budgetar, export) |
| [Data Box-deltagare](#data-box-contributor) | Låter dig hantera allt under Data Box-tjänsten förutom att ge åtkomst till andra. |
| [Data Box-läsare](#data-box-reader) | Låter dig hantera Data Box-tjänsten förutom att skapas eller redigera orderinformationen och ge åtkomst till andra. |
| [Data Factory-deltagare](#data-factory-contributor) | Låter dig hantera datafabriker, men ger dig inte tillgång till dem. |
| [Data Lake Analytics-utvecklare](#data-lake-analytics-developer) | Låter dig skicka in, övervaka och hantera dina egna jobb, men inte skapa eller ta bort Data Lake Analytics-konton. |
| [Data Purger](#data-purger) | Det går att rensa analysdata |
| [DevTest Labs-användare](#devtest-labs-user) | Låter dig ansluta, starta, starta om och stänga av virtuella datorer i din Azure DevTest Labs. |
| [DNS-Zondeltagare](#dns-zone-contributor) | Låter dig hantera DNS-zoner och postuppsättningar i Azure DNS, men låter dig inte kontrollera vem som har åtkomst till dem. |
| [DocumentDB-Kontodeltagare](#documentdb-account-contributor) | Hantera Azure Cosmos DB-konton. Azure Cosmos DB är kallades DocumentDB. |
| [HDInsight domäntjänster deltagare](#hdinsight-domain-services-contributor) | Kan läsa, skapa, ändra och ta bort domäntjänster relaterade åtgärder som behövs för Enterprise-säkerhetspaketet för HDInsight |
| [Intelligent Systems-Kontodeltagare](#intelligent-systems-account-contributor) | Låter dig hantera Intelligent Systems-konton, men ger dig inte tillgång till dem. |
| [Nyckelvalvsdeltagare](#key-vault-contributor) | Låter dig hantera nyckelvalv, men inte ha åtkomst till dem. |
| [Labbskaparen](#lab-creator) | Låter dig skapa, hantera och ta bort dina hanterade labbar under dina Azure Lab-konton. |
| [Log Analytics Contributor](#log-analytics-contributor) | Log Analytics Contributor kan läsa alla övervakningsdata och redigera övervakningsinställningarna. Redigera övervakningsinställningarna omfattar att lägga till VM-tillägget för virtuella datorer; läsa lagringskontonycklar för att kunna konfigurera loggsamlingar från Azure-lagring. Skapa och konfigurera automationskonton; lägga till lösningar och konfigurera Azure diagnostics på alla Azure-resurser. |
| [Log Analytics Reader](#log-analytics-reader) | Log Analytics Reader kan visa och söka i alla övervakningsdata och dessutom visa övervakningsinställningar, bl.a. konfigurationen av Azure Diagnostics på alla Azure-resurser. |
| [Logic App-deltagare](#logic-app-contributor) | Låter dig hantera logikappar, men du kan inte komma åt dem. |
| [Logic App-operatör](#logic-app-operator) | Låter dig läsa, aktivera och inaktivera logikapp. |
| [Operatörsroll för hanterat program](#managed-application-operator-role) | Du kan läsa och utföra åtgärder på hanterade resurser |
| [Läsare för hanterade program](#managed-applications-reader) | Kan du läsa resurser i en hanterad app och begära JIT-åtkomst. |
| [Hanterad Identitetsdeltagare](#managed-identity-contributor) | Skapa, läs, uppdatera och ta bort användartilldelad identitet |
| [Hanterade Identitetsoperatör](#managed-identity-operator) | Läs och tilldela användartilldelad identitet |
| [Hanteringsgrupp-deltagare](#management-group-contributor) | Rollen hanteringsgrupp-deltagare |
| [Hanteringsgruppen läsare](#management-group-reader) | Rollen hanteringsgrupp-läsare |
| [Övervaka deltagare](#monitoring-contributor) | Kan läsa alla övervakningsdata och redigera övervakningsinställningarna. Se även [Kom igång med roller, behörigheter och säkerhet med Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
| [Övervaka mått utgivare](#monitoring-metrics-publisher) | Aktiverar publicering av måtten mot Azure-resurser |
| [Övervaka läsare](#monitoring-reader) | Kan läsa alla övervakningsdata (mått, loggar osv.). Se även [Kom igång med roller, behörigheter och säkerhet med Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
| [Nätverksdeltagare](#network-contributor) | Låter dig hantera nätverk, men ger dig inte tillgång till dem. |
| [Nya Relic APM-Kontodeltagare](#new-relic-apm-account-contributor) | Låter dig hantera New Relic Application Performance Management-konton och program, men ger dig inte tillgång till dem. |
| [Testa PowerApps-administratör](#powerapps-administrator-test) | Låter dig hantera DNS-zoner och postuppsättningar i Azure DNS, men låter dig inte kontrollera vem som har åtkomst till dem. |
| [Läsare och dataåtkomst](#reader-and-data-access) | Kan du visa allt, men inte kan du ta bort eller skapa ett lagringskonto eller en resurs. Det gör också att läs-/ skrivåtkomst till alla data i ett lagringskonto via åtkomst till lagringskontonycklarna. |
| [Redis Cache-deltagare](#redis-cache-contributor) | Låter dig hantera Redis-cacheminnen, men ger dig inte tillgång till dem. |
| [Resursprincip (förhandsversion)](#resource-policy-contributor-preview) | (Förhandsversion) Användare från EA med behörighet att skapa/ändra resursprinciper, skapa supportbegäranden och läsa resurser/hierarkier. |
| [Scheduler-Jobbsamlingsdeltagare](#scheduler-job-collections-contributor) | Låter dig hantera Scheduler-jobbsystem, men ger dig inte tillgång till dem. |
| [Söktjänstdeltagare](#search-service-contributor) | Låter dig hantera söktjänster, men ger dig inte tillgång till dem. |
| [Säkerhetsadministratör](#security-admin) | I Security Center endast: kan visa säkerhetsprinciper, security tillstånd, redigera säkerhetsprinciper, Visa aviseringar och rekommendationer, avvisa aviseringar och rekommendationer |
| [Säkerhetshanteraren](#security-manager) | Låter dig hantera säkerhetskomponenter, säkerhetsprinciper och virtuella datorer |
| [Säkerhetsläsare](#security-reader) | I Security Center endast: Visa rekommendationer och aviseringar, visa IPSec-principer security tillstånd, men det går inte att göra ändringar |
| [Site Recovery-bidragsgivare](#site-recovery-contributor) | Låter dig hantera Site Recovery-tjänsten förutom att skapa valv och tilldela roller |
| [Site Recovery-operatör](#site-recovery-operator) | Låter dig växla vid fel och återställa men inte utföra andra Site Recovery-hanteringsåtgärder |
| [Site Recovery-läsare](#site-recovery-reader) | Låter dig se Site Recovery-status men inte utföra andra hanteringsåtgärder |
| [SQL DB-deltagare](#sql-db-contributor) | Låter dig hantera SQL-databaser, men inte tillgång till dem. Dessutom kan inte hantera deras säkerhetsrelaterade principer eller deras överordnade SQL-servrar. |
| [SQL-Säkerhetshanteraren](#sql-security-manager) | Tillåter dig att hantera säkerhetsrelaterade principer för SQL-servrar och databaser, men inte åtkomst till dem. |
| [SQL Server-deltagare](#sql-server-contributor) | Tillåter dig att hantera SQL-servrar och databaser, men inte åtkomst till dem eller deras säkerhetsrelaterade principer. |
| [Lagringskontodeltagare](#storage-account-contributor) | Låter dig hantera lagringskonton, men ger dig inte åtkomst till dem. |
| [Tjänstroll som Storage-konto operatör av Lagringskontonyckel](#storage-account-key-operator-service-role) | Operatörer av lagringskontonycklar får lista och återskapa nycklar till lagringskonton |
| [Storage Blob Data-deltagare (förhandsgranskning)](#storage-blob-data-contributor-preview) | Tillåter läs-, skriv- och borttagningsåtkomst till Azure Storage Blob-containers och data |
| [Storage Blob Data-läsare (förhandsgranskning)](#storage-blob-data-reader-preview) | Tillåter läsåtkomst till Azure Storage Blob-containers och data |
| [Lagringsködata-deltagare (förhandsgranskning)](#storage-queue-data-contributor-preview) | Tillåter läs-, skriv- och borttagningssåtkomst till Azure Storage-köer och kömeddelanden |
| [Lagringsködata-läsare (förhandsgranskning)](#storage-queue-data-reader-preview) | Tillåter läsåtkomst till Azure Storage-köer och kömeddelanden |
| [Supportförfrågningsdeltagare](#support-request-contributor) | Låter dig skapa och hantera supportförfrågningar |
| [Traffic Manager-deltagare](#traffic-manager-contributor) | Låter dig hantera Traffic Manager-profiler, men låter dig inte kontrollera vem som har åtkomst till dem. |
| [Administratör för användaråtkomst](#user-access-administrator) | Låter dig hantera användaråtkomst till Azure-resurser. |
| [Administratörsinloggning för virtuell dator](#virtual-machine-administrator-login) | Visa virtuella datorer i portalen och logga in som administratör |
| [Virtuell datordeltagare](#virtual-machine-contributor) | Låter dig hantera virtuella datorer, men ger dig inte åtkomst till dem eller till det virtuella nätverk eller lagringskonto som de är anslutna till. |
| [Användarinloggning för virtuell dator](#virtual-machine-user-login) | Visa virtuella datorer i portalen och logga in som en vanlig användare. |
| [Webbplan-deltagare](#web-plan-contributor) | Låter dig hantera webbplaner för webbplatser, men ger dig inte tillgång till dem. |
| [Webbplatsdeltagare](#website-contributor) | Låter dig hantera webbplatser (men inte webbplaner), men ger dig inte tillgång till dem. |


## <a name="owner"></a>Ägare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera allt, inklusive åtkomst till resurser. |
> | **Id** | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | **Åtgärder** |  |
> | * | Skapa och hantera resurser för alla typer av |

## <a name="contributor"></a>Deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera allt, med undantag för åtkomst till resurser. |
> | **Id** | b24988ac-6180-42A0-ab88-20f7382dd24c |
> | **Åtgärder** |  |
> | * | Skapa och hantera resurser för alla typer av |
> | **NotActions** |  |
> | Microsoft.Authorization/*/Delete | Det går inte att ta bort roller och rolltilldelningar |
> | Microsoft.Authorization/*/Write | Det går inte att skapa roller och rolltilldelningar |
> | Microsoft.Authorization/elevateAccess/Action | Ger anroparen åtkomst till administratör för användaråtkomst i klientomfattningen |
> | Microsoft.Blueprint/blueprintAssignments/write | Skapa eller uppdatera eventuella skissartefakter |
> | Microsoft.Blueprint/blueprintAssignments/delete | Ta bort eventuella skissartefakter |

## <a name="reader"></a>Läsare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig visa allting, men låter dig inte göra några ändringar. |
> | **Id** | acdd72a7-3385-48EF-bd42-f606fba81ae7 |
> | **Åtgärder** |  |
> | * / läsa | Läsa resurser av alla typer utom hemligheter. |

## <a name="acrimagesigner"></a>AcrImageSigner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | acr-bildsignerare |
> | **Id** | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | **Åtgärder** |  |
> | Microsoft.ContainerRegistry/registries/*/read |  |
> | Microsoft.ContainerRegistry/registries/*/write |  |

## <a name="acrquarantinereader"></a>AcrQuarantineReader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | acr-karantändataläsare |
> | **Id** | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | **Åtgärder** |  |
> | Microsoft.ContainerRegistry/registries/*/read |  |

## <a name="acrquarantinewriter"></a>AcrQuarantineWriter
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | acr-karantändataskrivare |
> | **Id** | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | **Åtgärder** |  |
> | Microsoft.ContainerRegistry/registries/*/write |  |
> | Microsoft.ContainerRegistry/registries/*/read |  |

## <a name="api-management-service-contributor"></a>API Management-tjänstdeltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera API Management-tjänster, men ger dig inte tillgång till dem. |
> | **Id** | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | **Åtgärder** |  |
> | Microsoft.ApiManagement/service/* | Skapa och hantera API Management-tjänsten |
> | Microsoft.Authorization/*/read | Läsa auktorisering |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="api-management-service-operator-role"></a>Operatörsroll för API Management
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan hantera tjänsten men inte API:er |
> | **Id** | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | **Åtgärder** |  |
> | Microsoft.ApiManagement/service/*/read | Läs API Management-tjänstinstanser |
> | Microsoft.ApiManagement/service/backup/action | Säkerhetskopiering API Management-tjänsten till den angivna behållaren i en användare tillhandahålls storage-konto |
> | Microsoft.ApiManagement/service/delete | Ta bort instansen av tjänsten API Management |
> | Microsoft.ApiManagement/service/managedeployments/action | Ändra SKU/enheter, Lägg till/ta bort de regionala distributionerna av API Management-tjänsten |
> | Microsoft.ApiManagement/service/read | Läsa metadata för en API Management-tjänstinstans |
> | Microsoft.ApiManagement/service/restore/action | Återställa API Management-tjänsten från den angivna behållaren i en användare som tillhandahålls av storage-konto |
> | Microsoft.ApiManagement/service/updatecertificate/action | Ladda upp SSL-certifikat för en API Management-tjänsten |
> | Microsoft.ApiManagement/service/updatehostname/action | Konfigurera, uppdatera eller ta bort anpassade domännamn för en API Management-tjänsten |
> | Microsoft.ApiManagement/service/write | Skapa en ny instans av tjänsten API Management |
> | Microsoft.Authorization/*/read | Läsa auktorisering |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Hämta lista över användarnycklar |

## <a name="api-management-service-reader-role"></a>Läsarroll för API Management-tjänst
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Skrivskyddad åtkomst till tjänst och API:er |
> | **Id** | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | **Åtgärder** |  |
> | Microsoft.ApiManagement/service/*/read | Läs API Management-tjänstinstanser |
> | Microsoft.ApiManagement/service/read | Läsa metadata för en API Management-tjänstinstans |
> | Microsoft.Authorization/*/read | Läsa auktorisering |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Hämta lista över användarnycklar |

## <a name="application-insights-component-contributor"></a>Application Insights-komponentdeltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan hantera Application Insights-komponenter |
> | **Id** | ae349356-3a1b-4a5e-921d-050484c6347e |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
> | Microsoft.Insights/components/* | Skapa och hantera Insights-komponenter |
> | Microsoft.Insights/webtests/* | Skapa och hantera webbtester |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="application-insights-snapshot-debugger"></a>Application Insights Snapshot Debugger
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Ger användarbehörighet att visa och hämta ögonblicksbilder för felsökning som samlas in med Application Insights Snapshot Debugger. Observera att dessa behörigheter inte ingår i den [ägare](#owner) eller [deltagare](#contributor) roller. |
> | **Id** | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera Insights Varningsregler |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="automation-job-operator"></a>Automation-jobboperator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Skapa och hantera jobb med Automation Runbooks. |
> | **Id** | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Läser Hybrid Runbook Worker-resurser |
> | Microsoft.Automation/automationAccounts/jobs/read | Hämtar en Azure Automation-jobb |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Återupptar ett Azure Automation-jobb |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Stoppar ett Azure Automation-jobb |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Hämtar en Azure Automation-jobbström |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Pausar ett Azure Automation-jobb |
> | Microsoft.Automation/automationAccounts/jobs/write | Skapar ett Azure Automation-jobb |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Hämtar utdata för ett jobb |
> | Microsoft.Insights/alertRules/* | Skapa och hantera Insights Varningsregler |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="automation-operator"></a>Automation-operatör
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Automation-operatörer kan starta, stoppa, göra uppehåll i och återuppta jobb |
> | **Id** | d3881f73-407a-4167-8283-e981cbba0404 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Läser Hybrid Runbook Worker-resurser |
> | Microsoft.Automation/automationAccounts/jobs/read | Hämtar en Azure Automation-jobb |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Återupptar ett Azure Automation-jobb |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Stoppar ett Azure Automation-jobb |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Hämtar en Azure Automation-jobbström |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Pausar ett Azure Automation-jobb |
> | Microsoft.Automation/automationAccounts/jobs/write | Skapar ett Azure Automation-jobb |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | Hämtar ett Azure Automation-Jobbschema |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | Skapar ett Azure Automation-Jobbschema |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Hämtar den arbetsyta som är länkad till automation-konto |
> | Microsoft.Automation/automationAccounts/read | Hämtar ett Azure Automation-konto |
> | Microsoft.Automation/automationAccounts/runbooks/read | Hämtar en Azure Automation-runbook |
> | Microsoft.Automation/automationAccounts/schedules/read | Hämtar en Azure Automation-schematillgång |
> | Microsoft.Automation/automationAccounts/schedules/write | Skapar eller uppdaterar en Azure Automation-schematillgång |
> | Microsoft.Insights/alertRules/* | Skapa och hantera Insights Varningsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Hämtar utdata för ett jobb |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="automation-runbook-operator"></a>Automation Runbook-operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Läs runbook-egenskaperna för att kunna skapa jobb av den runbooken. |
> | **Id** | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.Automation/automationAccounts/runbooks/read | Hämtar en Azure Automation-runbook |
> | Microsoft.Insights/alertRules/* | Skapa och hantera Insights Varningsregler |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="azure-kubernetes-service-cluster-admin-role"></a>Administratörsroll för Azure Kubernetes Service-kluster
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Lista över kluster administratörsåtgärd autentiseringsuppgifter. |
> | **Id** | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | **Åtgärder** |  |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Lista över clusterAdmin autentiseringsuppgifterna för ett hanterat kluster |

## <a name="azure-kubernetes-service-cluster-user-role"></a>Användarrollen för Azure Kubernetes Service-kluster
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Lista över autentiseringsuppgifter för kluster användaråtgärd. |
> | **Id** | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | **Åtgärder** |  |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Lista över clusterUser autentiseringsuppgifterna för ett hanterat kluster |

## <a name="azure-stack-registration-owner"></a>Ägare för Azure Stack-registrering
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera Azure Stack-registreringar. |
> | **Id** | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | **Åtgärder** |  |
> | Microsoft.AzureStack/registrations/products/listDetails/action | Hämtar utökade informationen för en produkt i Azure Stack Marketplace |
> | Microsoft.AzureStack/registrations/products/read | Hämtar egenskaperna för en produkt i Azure Stack Marketplace |
> | Microsoft.AzureStack/registrations/read | Hämtar egenskaperna för en Azure Stack-registrering |

## <a name="backup-contributor"></a>Säkerhetskopieringsmedarbetare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera säkerhetskopieringstjänsten, men låter dig inte skapa valv eller ge åtkomst till andra |
> | **Id** | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.Network/virtualNetworks/read | Hämta definitionen av virtuella nätverket |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Hantera resultatet av åtgärden för hantering av säkerhetskopiering |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Skapa och hantera säkerhetskopiering behållare inom säkerhetskopiering infrastrukturer för Recovery Services-valv |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Uppdaterar behållarlistan |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Skapa och hantera säkerhetskopieringsjobb |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Export-jobb |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Returnerar resultatet från Jobbexportåtgärd. |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | Skapa och hantera metadata som rör hantering av säkerhetskopiering |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Skapa och hantera resultat av åtgärder för hantering av säkerhetskopiering |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/* | Skapa och hantera principer för säkerhetskopiering |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Skapa och hantera objekt som kan säkerhetskopieras |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Skapa och hantera säkerhetskopierade objekt |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Skapa och hantera behållare som innehåller säkerhetskopieringsobjekt |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Returnerar sammanfattning av skyddade objekt och skyddade servrar för Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/certificates/* | Skapa och hantera certifikat som relaterar till säkerhetskopiering i Recovery Services-valv |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Skapa och hantera utökad information som rör valv |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Hämtar aviseringarna för Recovery services-valvet. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | Hämta valv-åtgärden hämtar ett objekt som representerar Azure-resursen av typen ”vault' |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Skapa och hantera registrerade identiteter |
> | Microsoft.RecoveryServices/Vaults/usages/* | Skapa och hantera användningen av Recovery Services-valv |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Storage/storageAccounts/read | Returnerar listan med lagringskonton eller hämtar egenskaperna för det angivna lagringskontot. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Validera åtgärden på skyddat objekt |
> | Microsoft.RecoveryServices/Vaults/write | Med skapa valv så skapas en Azure-resurs av typen valv |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Returnerar Säkerhetskopieringsstatus för Recovery Services-valv. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Returnerar alla säkerhetskopieringshanteringsservrar registrerade i valvet. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Hämta alla behållare som kan skyddas |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Kontrollera Säkerhetskopieringsstatus för Recovery Services-valv |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Validera funktioner |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Löser aviseringen. |
> | Microsoft.RecoveryServices/operations/read | Åtgärden returnerar listan över åtgärder för en Resursprovider |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Hämtar Åtgärdsstatus för en viss åtgärd |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Lista över alla backup Protection avsikter |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="backup-operator"></a>Säkerhetskopieringsoperatör
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera säkerhetskopieringstjänster, med undantag för att ta bort säkerhetskopior, skapa valv eller ge åtkomst till andra |
> | **Id** | 00c29273-979b-4161-815c-10b084fb9324 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.Network/virtualNetworks/read | Hämta definitionen av virtuella nätverket |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Returnerar status för åtgärden |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Hämtar resultat från utförd åtgärd på skyddsbehållare. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Säkerhetskopierar ett skyddat objekt. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Hämtar resultat från utförd åtgärd på skyddade objekt. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Returnerar status för utförd åtgärd på skyddade objekt. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Returnerar information om det skyddade objektet |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Tillhandahåll direkt Objektåterställning för skyddat objekt |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Hämta återställningspunkter för skyddade objekt. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Återskapa återställningspunkter för skyddade objekt. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Återkalla direkt Objektåterställning för skyddat objekt |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Skapa ett säkerhetskopierat skyddat objekt |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Returnerar alla registrerade behållare |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Uppdaterar behållarlistan |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Skapa och hantera säkerhetskopieringsjobb |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Export-jobb |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Returnerar resultatet från Jobbexportåtgärd. |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Returnerar metadata för hantering av säkerhetskopiering för Recovery Services-valvet. |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Skapa och hantera resultat av åtgärder för hantering av säkerhetskopiering |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Hämtar resultat från principåtgärd. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Returnerar alla skyddsprinciper |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Skapa och hantera objekt som kan säkerhetskopieras |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Returnerar listan över alla skyddade objekt. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Returnerar alla behållare som hör till prenumerationen |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Returnerar sammanfattning av skyddade objekt och skyddade servrar för Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Åtgärden Uppdatera Resurscertifikat uppdateras certifikatet för resurs/valv-autentiseringsuppgifter. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv? |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Hämtar aviseringarna för Recovery services-valvet. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | Hämta valv-åtgärden hämtar ett objekt som representerar Azure-resursen av typen ”vault' |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Åtgärden hämta Åtgärdsresultat åtgärd kan du hämta åtgärdsstatusen och resultatet för åtgärden som skickats asynkront |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Hämta behållare kan du använda åtgärden hämta behållare som är registrerade för en resurs. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Åtgärden registrera tjänstbehållare kan användas för att registrera en behållare med återställningstjänsten. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Returnerar användningsinformation om Recovery Services-valvet. |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Storage/storageAccounts/read | Returnerar listan med lagringskonton eller hämtar egenskaperna för det angivna lagringskontot. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Validera åtgärden på skyddat objekt |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Returnerar Säkerhetskopieringsstatus för Recovery Services-valv. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Hämta Status för principåtgärd. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Skapar en registrerad behållare |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Gör förfrågan om arbetsbelastningar inom en behållare |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Returnerar alla säkerhetskopieringshanteringsservrar registrerade i valvet. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Skapa en säkerhetskopia för Avsiktsskydd |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Hämta en säkerhetskopia för Avsiktsskydd |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Hämta alla behållare som kan skyddas |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Hämta alla objekt i en behållare |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Kontrollera Säkerhetskopieringsstatus för Recovery Services-valv |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Validera funktioner |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Löser aviseringen. |
> | Microsoft.RecoveryServices/operations/read | Åtgärden returnerar listan över åtgärder för en Resursprovider |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Hämtar Åtgärdsstatus för en viss åtgärd |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Lista över alla backup Protection avsikter |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="backup-reader"></a>Säkerhetskopieringsläsare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan visa säkerhetskopieringstjänster, men inte göra några ändringar |
> | **Id** | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp är en intern åtgärd som används av tjänsten |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Returnerar status för åtgärden |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Hämtar resultat från utförd åtgärd på skyddsbehållare. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Hämtar resultat från utförd åtgärd på skyddade objekt. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Returnerar status för utförd åtgärd på skyddade objekt. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Returnerar information om det skyddade objektet |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Hämta återställningspunkter för skyddade objekt. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Returnerar alla registrerade behållare |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Returnerar resultat från jobbåtgärd. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Returnerar alla jobbobjekt |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Export-jobb |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Returnerar resultatet från Jobbexportåtgärd. |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Returnerar metadata för hantering av säkerhetskopiering för Recovery Services-valvet. |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Returnerar resultat från säkerhetskopiering för Recovery Services-valvet. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Hämtar resultat från principåtgärd. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Returnerar alla skyddsprinciper |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Returnerar listan över alla skyddade objekt. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Returnerar alla behållare som hör till prenumerationen |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Returnerar sammanfattning av skyddade objekt och skyddade servrar för Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Hämtar aviseringarna för Recovery services-valvet. |
> | Microsoft.RecoveryServices/Vaults/read | Hämta valv-åtgärden hämtar ett objekt som representerar Azure-resursen av typen ”vault' |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Åtgärden hämta Åtgärdsresultat åtgärd kan du hämta åtgärdsstatusen och resultatet för åtgärden som skickats asynkront |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Hämta behållare kan du använda åtgärden hämta behållare som är registrerade för en resurs. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Returnerar lagringskonfiguration för Recovery Services-valv. |
> | Microsoft.RecoveryServices/Vaults/backupconfig/read | Returnerar konfiguration för Recovery Services-valv. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Returnerar Säkerhetskopieringsstatus för Recovery Services-valv. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Hämta Status för principåtgärd. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Returnerar alla säkerhetskopieringshanteringsservrar registrerade i valvet. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Hämta en säkerhetskopia för Avsiktsskydd |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Hämta alla objekt i en behållare |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Kontrollera Säkerhetskopieringsstatus för Recovery Services-valv |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Löser aviseringen. |
> | Microsoft.RecoveryServices/operations/read | Åtgärden returnerar listan över åtgärder för en Resursprovider |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Hämtar Åtgärdsstatus för en viss åtgärd |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Lista över alla backup Protection avsikter |
> | Microsoft.RecoveryServices/Vaults/usages/read | Returnerar användningsinformation om Recovery Services-valvet. |

## <a name="billing-reader"></a>Faktureringsläsare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig läsa faktureringsdata |
> | **Id** | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.Billing/*/read | Läs faktureringsinformation |
> | Microsoft.Commerce/*/read |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.Management/managementGroups/read | Lista över hanteringsgrupper för autentiserade användare. |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="biztalk-contributor"></a>BizTalk-deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera BizTalk-tjänster, men ger dig inte tillgång till dem. |
> | **Id** | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.BizTalkServices/BizTalk/* | Skapa och hantera BizTalk services |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="cdn-endpoint-contributor"></a>CDN-slutpunktsdeltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan hantera CDN-slutpunkter, men kan inte bevilja åtkomst till andra användare. |
> | **Id** | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/* |  |
> | Microsoft.Insights/alertRules/* | Skapa och hantera Insights Varningsregler |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="cdn-endpoint-reader"></a>CDN-slutpunktsläsare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan visa CDN-slutpunkter, men kan inte göra ändringar. |
> | **Id** | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/*/read |  |
> | Microsoft.Insights/alertRules/* | Skapa och hantera Insights Varningsregler |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="cdn-profile-contributor"></a>CDN-profildeltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan hantera CDN-profiler och deras slutpunkter, men kan inte bevilja åtkomst till andra användare. |
> | **Id** | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/* |  |
> | Microsoft.Insights/alertRules/* | Skapa och hantera Insights Varningsregler |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="cdn-profile-reader"></a>CDN-profilläsare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan visa CDN-profiler och deras slutpunkter, men kan inte göra ändringar. |
> | **Id** | 8f96442b-4075-438f-813d-ad51ab4019af |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/*/read |  |
> | Microsoft.Insights/alertRules/* | Skapa och hantera Insights Varningsregler |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="classic-network-contributor"></a>Klassisk nätverksdeltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera klassiska nätverk, men ger dig inte tillgång till dem. |
> | **Id** | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läsa auktorisering |
> | Microsoft.ClassicNetwork/* | Skapa och hantera klassiska nätverk |
> | Microsoft.Insights/alertRules/* | Skapa och hantera Insights Varningsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="classic-storage-account-contributor"></a>Klassisk lagringskontodeltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera klassiska lagringskonton, men ger dig inte åtkomst att hantera dem. |
> | **Id** | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läsa auktorisering |
> | Microsoft.ClassicStorage/storageAccounts/* | Skapa och hantera lagringskonton |
> | Microsoft.Insights/alertRules/* | Skapa och hantera Insights Varningsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="classic-storage-account-key-operator-service-role"></a>Tjänstroll som operatör av klassisk lagringskontonyckel
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Operatörer av klassiska lagringskontonycklar får lista och återskapa nycklar till klassiska lagringskonton |
> | **Id** | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | **Åtgärder** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | Listar lagringskontots åtkomstnycklar. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Återskapar befintliga åtkomstnycklar för lagringskontot. |

## <a name="classic-virtual-machine-contributor"></a>Klassisk virtuell datordeltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera klassiska virtuella datorer, men ger dig inte tillgång till dem eller till det virtuella nätverk eller lagringskonto som de är anslutna till. |
> | **Id** | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läsa auktorisering |
> | Microsoft.ClassicCompute/domainNames/* | Skapa och hantera domännamn för klassisk beräkning |
> | Microsoft.ClassicCompute/virtualMachines/* | Skapa och hantera virtuella datorer |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | Länka en reserverad IP |
> | Microsoft.ClassicNetwork/reservedIps/read | Hämtar reserverade IP-adresser |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | Anslut till det virtuella nätverket. |
> | Microsoft.ClassicNetwork/virtualNetworks/read | Hämtar det virtuella nätverket. |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | Returnerar lagringskontodisken. |
> | Microsoft.ClassicStorage/storageAccounts/images/read | Returnerar lagringskontoavbildning. (Inaktuell. Använd ”Microsoft.ClassicStorage/storageAccounts/vmImages”) |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Listar lagringskontots åtkomstnycklar. |
> | Microsoft.ClassicStorage/storageAccounts/read | Returnerar lagringskontot med det givna kontot. |
> | Microsoft.Insights/alertRules/* | Skapa och hantera Insights Varningsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="cognitive-services-contributor"></a>Cognitive Services-deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan du skapa, läsa, uppdatera, ta bort och hantera nycklar för Cognitive Services. |
> | **Id** | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.CognitiveServices/* |  |
> | Microsoft.Features/features/read | Hämtar en prenumerations funktioner. |
> | Microsoft.Features/providers/features/read | Hämtar en prenumerations funktion hos en given resursprovider. |
> | Microsoft.Insights/alertRules/* | Skapa och hantera Insights Varningsregler |
> | Microsoft.Insights/diagnosticSettings/* | Skapar, uppdaterar eller läser diagnostikinställningen för Analysis Server |
> | Microsoft.Insights/logDefinitions/read | Läs loggdefinitioner |
> | Microsoft.Insights/metricdefinitions/read | Läs måttdefinitioner |
> | Microsoft.Insights/metrics/read | Läs mått |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/deployments/operations/read | Hämtar eller listar distributionsåtgärder. |
> | Microsoft.Resources/subscriptions/operationresults/read | Hämtar prenumerationsåtgärdsresultaten. |
> | Microsoft.Resources/subscriptions/read | Hämtar listan över prenumerationer. |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="cognitive-services-user"></a>Cognitive Services-användare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan du läsa och lista nycklar för Cognitive Services. |
> | **Id** | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Åtgärder** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | Microsoft.CognitiveServices/accounts/listkeys/action | Visa nyckellista |
> | Microsoft.Insights/metricdefinitions/read | Läs måttdefinitioner |
> | Microsoft.Insights/metrics/read | Läs mått |
> | Microsoft.Insights/alertRules/read | Läsa en klassisk måttavisering |
> | Microsoft.Insights/diagnosticSettings/read | Läsa en resursdiagnostikinställning |
> | Microsoft.Insights/logDefinitions/read | Läs loggdefinitioner |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/operations/read | Hämtar eller listar distributionsåtgärder. |
> | Microsoft.Resources/subscriptions/operationresults/read | Hämtar prenumerationsåtgärdsresultaten. |
> | Microsoft.Resources/subscriptions/read | Hämtar listan över prenumerationer. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="cosmos-db-account-reader-role"></a>Läsarroll för Cosmos DB-konto
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan läsa data i Azure Cosmos DB-konto. Se [DocumentDB-Kontodeltagare](#documentdb-account-contributor) för att hantera Azure Cosmos DB-konton. |
> | **Id** | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar kan läsa behörigheter för varje användare |
> | Microsoft.DocumentDB/*/read | Läs valfri samling |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Läser databasen readonly-kontonycklar. |
> | Microsoft.Insights/MetricDefinitions/read | Läs måttdefinitioner |
> | Microsoft.Insights/Metrics/read | Läs mått |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="cost-management-contributor"></a>Kostnadshantering deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan visa kostnaderna och hantera kostnaden konfiguration (t.ex. budgetar, export) |
> | **Id** | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | **Åtgärder** |  |
> | Microsoft.Consumption/* |  |
> | Microsoft.CostManagement/* |  |
> | Microsoft.Billing/billingPeriods/read | Visar en lista över tillgängliga faktureringsperioder |
> | Microsoft.Resources/subscriptions/read | Hämtar listan över prenumerationer. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="cost-management-reader"></a>Kostnadshantering läsare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Visa kostnadsdata och konfiguration (t.ex. budgetar, export) |
> | **Id** | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | **Åtgärder** |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Billing/billingPeriods/read | Visar en lista över tillgängliga faktureringsperioder |
> | Microsoft.Resources/subscriptions/read | Hämtar listan över prenumerationer. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="data-box-contributor"></a>Data Box-deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera allt under Data Box-tjänsten förutom att ge åtkomst till andra. |
> | **Id** | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |
> | Microsoft.Databox/* |  |

## <a name="data-box-reader"></a>Data Box-läsare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera Data Box-tjänsten förutom att skapas eller redigera orderinformationen och ge åtkomst till andra. |
> | **Id** | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.Databox/*/read |  |
> | Microsoft.Databox/jobs/listsecrets/action |  |
> | Microsoft.Databox/jobs/listcredentials/action | Visar en lista över okrypterade autentiseringsuppgifter beställningen. |
> | Microsoft.Databox/locations/availableSkus/action | Den här metoden returnerar listan över tillgängliga SKU:er. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="data-factory-contributor"></a>Data Factory-deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera datafabriker, men ger dig inte tillgång till dem. |
> | **Id** | 673868aa-7521-48A0-acc6-0f60742d39f5 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rollen tilldelningar |
> | Microsoft.DataFactory/dataFactories/* | Skapa och hantera datafabriker och underordnade resurser. |
> | Microsoft.DataFactory/factories/* | Skapa och hantera datafabriker och underordnade resurser. |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="data-lake-analytics-developer"></a>Data Lake Analytics-utvecklare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig skicka in, övervaka och hantera dina egna jobb, men inte skapa eller ta bort Data Lake Analytics-konton. |
> | **Id** | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.BigAnalytics/accounts/* |  |
> | Microsoft.DataLakeAnalytics/accounts/* |  |
> | Microsoft.Insights/alertRules/* | Skapa och hantera Insights Varningsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | Microsoft.DataLakeAnalytics/accounts/Delete | Ta bort ett DataLakeAnalytics-konto. |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Beviljar behörigheter att avbryta jobb som skickats av andra användare. |
> | Microsoft.DataLakeAnalytics/accounts/Write | Skapa eller uppdatera ett DataLakeAnalytics-konto. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Skapa eller uppdatera ett länkat DataLakeStore-konto för ett DataLakeAnalytics-konto. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Ta bort länk till ett DataLakeStore-konto från ett DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Skapa eller uppdatera ett länkat lagringskonto för ett DataLakeAnalytics-konto. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | Ta bort länk till ett lagringskonto från ett DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Skapa eller uppdatera en brandväggsregel. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Ta bort en brandväggsregel. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Skapa eller uppdatera en princip för beräkning. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Ta bort en princip för beräkning. |

## <a name="data-purger"></a>Datarensaren
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Det går att rensa analysdata |
> | **Id** | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | **Åtgärder** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Insights/components/purge/action | Rensar data från Application Insights |
> | Microsoft.OperationalInsights/workspaces/*/read |  |
> | Microsoft.OperationalInsights/workspaces/purge/action | Ta bort angivna data från arbetsytan |

## <a name="devtest-labs-user"></a>DevTest Labs-användare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig ansluta, starta, starta om och stänga av virtuella datorer i din Azure DevTest Labs. |
> | **Id** | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rollen tilldelningar |
> | Microsoft.Compute/availabilitySets/read | Hämta egenskaperna för en tillgänglighetsuppsättning |
> | Microsoft.Compute/virtualMachines/*/read | Läsa egenskaperna för en virtuell dator (VM-storlekar, Körningsstatus, VM-tillägg, osv.) |
> | Microsoft.Compute/virtualMachines/deallocate/action | Stänger av den virtuella datorn och frigör beräkningsresurser |
> | Microsoft.Compute/virtualMachines/read | Hämta egenskaperna för en virtuell dator |
> | Microsoft.Compute/virtualMachines/restart/action | Startar om den virtuella datorn |
> | Microsoft.Compute/virtualMachines/start/action | Startar den virtuella datorn |
> | Microsoft.DevTestLab/*/read | Läsa egenskaperna för ett labb |
> | Microsoft.DevTestLab/labs/createEnvironment/action | Skapa virtuella datorer i ett labb. |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | Gör anspråk på en slumpmässig tillgängliga virtuell dator i labbet. |
> | Microsoft.DevTestLab/labs/formulas/delete | Ta bort formler. |
> | Microsoft.DevTestLab/labs/formulas/read | Läsa formler. |
> | Microsoft.DevTestLab/labs/formulas/write | Lägg till eller ändra formler. |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Utvärderar lab principen. |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | Bli ägare till en befintlig virtuell dator |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Ansluter till en load balancer-serverdelsadresspool |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Ansluter till en belastningsutjämningsregel för ingående nat |
> | Microsoft.Network/networkInterfaces/*/read | Läsa egenskaperna för ett nätverksgränssnitt (till exempel alla belastningsutjämnare som nätverksgränssnittet är en del av) |
> | Microsoft.Network/networkInterfaces/join/action | Ansluter till en virtuell dator till ett nätverksgränssnitt |
> | Microsoft.Network/networkInterfaces/read | Hämtar en definition för nätverk-gränssnittet.  |
> | Microsoft.Network/networkInterfaces/write | Skapar ett nätverksgränssnitt eller uppdaterar en befintlig nätverksgränssnitt.  |
> | Microsoft.Network/publicIPAddresses/*/read | Läsa egenskaperna för en offentlig IP-adress |
> | Microsoft.Network/publicIPAddresses/join/action | Ansluter till en offentlig ip-adress |
> | Microsoft.Network/publicIPAddresses/read | Hämtar en definition av offentlig ip-adress. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Ansluter till ett virtuellt nätverk |
> | Microsoft.Resources/deployments/operations/read | Hämtar eller listar distributionsåtgärder. |
> | Microsoft.Resources/deployments/read | Hämtar eller listar distributioner. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Storage/storageAccounts/listKeys/action | Returnerar åtkomstnycklarna för det angivna lagringskontot. |
> | **NotActions** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read | Visar en lista över tillgängliga storlekar som de virtuella datorerna kan uppdateras till |

## <a name="dns-zone-contributor"></a>DNS-zondeltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera DNS-zoner och postuppsättningar i Azure DNS, men låter dig inte kontrollera vem som har åtkomst till dem. |
> | **Id** | befefa01-2a29-4197-83a8-272ff33ce314 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
> | Microsoft.Network/dnsZones/* | Skapa och hantera DNS-zoner och poster |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="documentdb-account-contributor"></a>DocumentDB-kontodeltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Hantera Azure Cosmos DB-konton. Azure Cosmos DB är kallades DocumentDB. |
> | **Id** | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rollen tilldelningar |
> | Microsoft.DocumentDb/databaseAccounts/* | Skapa och hantera Azure Cosmos DB-konton |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="hdinsight-domain-services-contributor"></a>HDInsight domäntjänster deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan läsa, skapa, ändra och ta bort domäntjänster relaterade åtgärder som behövs för Enterprise-säkerhetspaketet för HDInsight |
> | **Id** | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | **Åtgärder** |  |
> | Microsoft.AAD/*/read |  |
> | Microsoft.AAD/domainServices/*/read |  |
> | Microsoft.AAD/domainServices/oucontainer/* |  |

## <a name="intelligent-systems-account-contributor"></a>Intelligent Systems-kontodeltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera Intelligent Systems-konton, men ger dig inte tillgång till dem. |
> | **Id** | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rollen tilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
> | Microsoft.IntelligentSystems/accounts/* | Skapa och hantera intelligent systems-konton |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="key-vault-contributor"></a>Nyckelvalvsdeltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera nyckelvalv, men inte ha åtkomst till dem. |
> | **Id** | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera Insights Varningsregler |
> | Microsoft.KeyVault/* |  |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |
> | **NotActions** |  |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | Rensa ett ej permanent borttaget nyckelvalv |
> | Microsoft.KeyVault/hsmPools/* |  |

## <a name="lab-creator"></a>Labbskaparen
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig skapa, hantera och ta bort dina hanterade labbar under dina Azure Lab-konton. |
> | **Id** | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | Skapa ett labb i ett labbkonto. |
> | Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action |  |
> | Microsoft.LabServices/labAccounts/getRegionalAvailability/action | Hämta information om regional tillgänglighet för varje kategori för storlek som konfigurerats under ett labbkonto |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="log-analytics-contributor"></a>Log Analytics Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Log Analytics Contributor kan läsa alla övervakningsdata och redigera övervakningsinställningarna. Redigera övervakningsinställningarna omfattar att lägga till VM-tillägget för virtuella datorer; läsa lagringskontonycklar för att kunna konfigurera loggsamlingar från Azure-lagring. Skapa och konfigurera automationskonton; lägga till lösningar och konfigurera Azure diagnostics på alla Azure-resurser. |
> | **Id** | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | **Åtgärder** |  |
> | * / läsa | Läsa resurser av alla typer utom hemligheter. |
> | Microsoft.Automation/automationAccounts/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Listar lagringskontots åtkomstnycklar. |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft.Insights/alertRules/* | Skapa och hantera Insights Varningsregler |
> | Microsoft.Insights/diagnosticSettings/* | Skapar, uppdaterar eller läser diagnostikinställningen för Analysis Server |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Returnerar åtkomstnycklarna för det angivna lagringskontot. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="log-analytics-reader"></a>Log Analytics Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Log Analytics Reader kan visa och söka i alla övervakningsdata och dessutom visa övervakningsinställningar, bl.a. konfigurationen av Azure Diagnostics på alla Azure-resurser. |
> | **Id** | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Åtgärder** |  |
> | * / läsa | Läsa resurser av alla typer utom hemligheter. |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Sök med den nya motorn. |
> | Microsoft.OperationalInsights/workspaces/search/action | Kör en sökfråga |
> | Microsoft.Support/* | Skapa och hantera supportärenden |
> | **NotActions** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Hämtar de delade nycklarna för arbetsytan. De här nycklarna används för att ansluta Microsoft Operational Insights-agenter till arbetsytan. |

## <a name="logic-app-contributor"></a>Logic App-deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera logikappar, men du kan inte komma åt dem. |
> | **Id** | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Listar lagringskontots åtkomstnycklar. |
> | Microsoft.ClassicStorage/storageAccounts/read | Returnerar lagringskontot med det givna kontot. |
> | Microsoft.Insights/alertRules/* | Skapa och hantera Insights Varningsregler |
> | Microsoft.Insights/diagnosticSettings/* | Skapar, uppdaterar eller läser diagnostikinställningen för Analysis Server |
> | Microsoft.Insights/logdefinitions/* | Den här behörigheten krävs för användare som behöver åtkomst till aktivitetsloggar via portalen. Lista loggkategorier i aktivitetsloggen. |
> | Microsoft.Insights/metricDefinitions/* | Läs måttdefinitionerna (lista över tillgängliga typer av mått för en resurs). |
> | Microsoft.Logic/* | Hanterar Logic Apps-resurser. |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/operationresults/read | Hämtar prenumerationsåtgärdsresultaten. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Storage/storageAccounts/listkeys/action | Returnerar åtkomstnycklarna för det angivna lagringskontot. |
> | Microsoft.Storage/storageAccounts/read | Returnerar listan med lagringskonton eller hämtar egenskaperna för det angivna lagringskontot. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |
> | Microsoft.Web/connectionGateways/* | Skapar och hanterar en Gateway-anslutning. |
> | Microsoft.Web/connections/* | Skapar och hanterar en anslutning. |
> | Microsoft.Web/customApis/* | Skapar och hanterar ett anpassat API. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Visa egenskaperna för en App Service Plan |
> | Microsoft.Web/sites/functions/listSecrets/action | Lista hemligheter Web Apps-funktioner. |

## <a name="logic-app-operator"></a>Logic App-operatör
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig läsa, aktivera och inaktivera logikapp. |
> | **Id** | 515c2055-d9d4-4321-B1B9-bd0c9a0f79fe |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/*/read | Läs insikter Varningsregler |
> | Microsoft.Insights/diagnosticSettings/*/read | Hämtar diagnostikinställningarna för Logic Apps |
> | Microsoft.Insights/metricDefinitions/*/read | Hämtar tillgängliga mått för Logic Apps. |
> | Microsoft.Logic/*/read | Läser Logic Apps-resurser. |
> | Microsoft.Logic/workflows/disable/action | Inaktiverar arbetsflödet. |
> | Microsoft.Logic/workflows/enable/action | Aktiverar arbetsflödet. |
> | Microsoft.Logic/workflows/validate/action | Verifierar arbetsflödet. |
> | Microsoft.Resources/deployments/operations/read | Hämtar eller listar distributionsåtgärder. |
> | Microsoft.Resources/subscriptions/operationresults/read | Hämtar prenumerationsåtgärdsresultaten. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |
> | Microsoft.Web/connectionGateways/*/read | Läsa anslutning gatewayer. |
> | Microsoft.Web/connections/*/read | Läsa anslutningar. |
> | Microsoft.Web/customApis/*/read | Läsa anpassat API. |
> | Microsoft.Web/serverFarms/read | Visa egenskaperna för en App Service Plan |

## <a name="managed-application-operator-role"></a>Operatörsroll för hanterat program
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Du kan läsa och utföra åtgärder på hanterade resurser |
> | **Id** | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | **Åtgärder** |  |
> | Microsoft.Solutions/applications/read | Hämtar en lista över program. |

## <a name="managed-applications-reader"></a>Läsare för hanterade program
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan du läsa resurser i en hanterad app och begära JIT-åtkomst. |
> | **Id** | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | **Åtgärder** |  |
> | * / läsa | Läsa resurser av alla typer utom hemligheter. |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Solutions/jitRequests/* |  |

## <a name="managed-identity-contributor"></a>Hanterad identitetsdeltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Skapa, läs, uppdatera och ta bort användartilldelad identitet |
> | **Id** | e40ec5ca-96e0-45A2-b4ff-59039f2c2b59 |
> | **Åtgärder** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/write |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/delete |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera Insights Varningsregler |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="managed-identity-operator"></a>Operatör för hanterad identitet
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Läs och tilldela användartilldelad identitet |
> | **Id** | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Åtgärder** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera Insights Varningsregler |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="management-group-contributor"></a>Hanteringsgrupp-deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Rollen hanteringsgrupp-deltagare |
> | **Id** | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | **Åtgärder** |  |
> | Microsoft.Management/managementGroups/delete | Ta bort hanteringsgruppen. |
> | Microsoft.Management/managementGroups/read | Lista över hanteringsgrupper för autentiserade användare. |
> | Microsoft.Management/managementGroups/subscriptions/delete | Ta bort associerar prenumeration från hanteringsgruppen. |
> | Microsoft.Management/managementGroups/subscriptions/write | Associates befintliga prenumeration med hanteringsgruppen. |
> | Microsoft.Management/managementGroups/write | Skapa eller uppdatera en hanteringsgrupp. |

## <a name="management-group-reader"></a>Hanteringsgrupp-läsare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Rollen hanteringsgrupp-läsare |
> | **Id** | ac63b705-f282-497d-ac71-919bf39d939d |
> | **Åtgärder** |  |
> | Microsoft.Management/managementGroups/read | Lista över hanteringsgrupper för autentiserade användare. |

## <a name="monitoring-contributor"></a>Övervaka deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan läsa alla övervakningsdata och redigera övervakningsinställningarna. Se även [Kom igång med roller, behörigheter och säkerhet med Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
> | **Id** | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | **Åtgärder** |  |
> | * / läsa | Läsa resurser av alla typer utom hemligheter. |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft.Insights/actiongroups/* |  |
> | Microsoft.Insights/AlertRules/* | Läs/Skriv/ta bort aviseringsregler. |
> | Microsoft.Insights/components/* | Läs/Skriv/ta bort Application Insights-komponenter. |
> | Microsoft.Insights/DiagnosticSettings/* | Läs/Skriv/ta bort diagnostikinställningar. |
> | Microsoft.Insights/eventtypes/* | Lista över aktivitetslogghändelser (av hanteringshändelser) i en prenumeration. Den här behörigheten gäller för både program- och portalen åtkomst till aktivitetsloggen. |
> | Microsoft.Insights/LogDefinitions/* | Den här behörigheten krävs för användare som behöver åtkomst till aktivitetsloggar via portalen. Lista loggkategorier i aktivitetsloggen. |
> | Microsoft.Insights/metricalerts/* |  |
> | Microsoft.Insights/MetricDefinitions/* | Läs måttdefinitionerna (lista över tillgängliga typer av mått för en resurs). |
> | Microsoft.Insights/Metrics/* | Läsa måtten för en resurs. |
> | Microsoft.Insights/Register/Action | Registrera Microsoft Insights-providern |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.Insights/webtests/* | Läs/Skriv/ta bort Application Insights webbtester. |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Läs/Skriv/ta bort Log Analytics-lösningspaket. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | Läs/Skriv/ta bort Log Analytics sparade sökningar. |
> | Microsoft.OperationalInsights/workspaces/search/action | Kör en sökfråga |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Hämtar de delade nycklarna för arbetsytan. De här nycklarna används för att ansluta Microsoft Operational Insights-agenter till arbetsytan. |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Läs/Skriv/ta bort Log Analytics insight lagringskonfigurationer. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |
> | Microsoft.WorkloadMonitor/monitors/* |  |
> | Microsoft.WorkloadMonitor/notificationSettings/* |  |
> | Microsoft.WorkloadMonitor/workloadInsights/* |  |

## <a name="monitoring-metrics-publisher"></a>Övervaka mått utgivare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Aktiverar publicering av måtten mot Azure-resurser |
> | **Id** | 3913510d-42f4-4e42-8a64-420c390055eb |
> | **Åtgärder** |  |
> | Microsoft.Insights/Register/Action | Registrera Microsoft Insights-providern |
> | Microsoft.Support/* | Skapa och hantera supportärenden |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | **DataActions** |  |
> | Microsoft.Insights/Metrics/Write | Skrivning av mått |

## <a name="monitoring-reader"></a>Övervaka läsare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan läsa alla övervakningsdata (mått, loggar osv.). Se även [Kom igång med roller, behörigheter och säkerhet med Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
> | **Id** | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | **Åtgärder** |  |
> | * / läsa | Läsa resurser av alla typer utom hemligheter. |
> | Microsoft.OperationalInsights/workspaces/search/action | Kör en sökfråga |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="network-contributor"></a>Nätverksdeltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera nätverk, men ger dig inte tillgång till dem. |
> | **Id** | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rollen tilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
> | Microsoft.Network/* | Skapa och hantera nätverk |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="new-relic-apm-account-contributor"></a>New Relic APM-kontodeltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera New Relic Application Performance Management-konton och program, men ger dig inte tillgång till dem. |
> | **Id** | 5d28c62d-5b37-4476-8438-e587778df237 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera Insights Varningsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |
> | NewRelic.APM/accounts/* |  |

## <a name="powerapps-administrator-test"></a>Testa PowerApps-administratör
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera DNS-zoner och postuppsättningar i Azure DNS, men låter dig inte kontrollera vem som har åtkomst till dem. |
> | **Id** | befefa01-2a29-4897-83a8-272ff33ce314 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera Insights Varningsregler |
> | Microsoft.Network/dnsZones/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="reader-and-data-access"></a>Läs- och dataåtkomst
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan du visa allt, men inte kan du ta bort eller skapa ett lagringskonto eller en resurs. Det gör också att läs-/ skrivåtkomst till alla data i ett lagringskonto via åtkomst till lagringskontonycklarna. |
> | **Id** | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | **Åtgärder** |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Returnerar åtkomstnycklarna för det angivna lagringskontot. |
> | Microsoft.Storage/storageAccounts/read | Returnerar listan med lagringskonton eller hämtar egenskaperna för det angivna lagringskontot. |

## <a name="redis-cache-contributor"></a>Redis Cache-deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera Redis-cacheminnen, men ger dig inte tillgång till dem. |
> | **Id** | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rollen tilldelningar |
> | Microsoft.Cache/redis/* | Skapa och hantera Redis-cacheminnen |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="resource-policy-contributor-preview"></a>Deltagare för resursprincip (förhandsversion)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | (Förhandsversion) Användare från EA med behörighet att skapa/ändra resursprinciper, skapa supportbegäranden och läsa resurser/hierarkier. |
> | **Id** | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | **Åtgärder** |  |
> | * / läsa | Läsa resurser av alla typer utom hemligheter. |
> | Microsoft.Authorization/policyassignments/* | Skapa och hantera principtilldelningar |
> | Microsoft.Authorization/policydefinitions/* | Skapa och hantera principdefinitioner |
> | Microsoft.Authorization/policysetdefinitions/* | Skapa och hantera principuppsättningar |
> | Microsoft.PolicyInsights/* |  |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="scheduler-job-collections-contributor"></a>Scheduler-jobbsamlingsdeltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera Scheduler-jobbsystem, men ger dig inte tillgång till dem. |
> | **Id** | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rollen tilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Scheduler/jobcollections/* | Skapa och hantera jobbsamlingar |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="search-service-contributor"></a>Söktjänstdeltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera söktjänster, men ger dig inte tillgång till dem. |
> | **Id** | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rollen tilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Search/searchServices/* | Skapa och hantera söktjänster |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="security-admin"></a>Säkerhetsadministratör
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | I Security Center endast: kan visa säkerhetsprinciper, security tillstånd, redigera säkerhetsprinciper, Visa aviseringar och rekommendationer, avvisa aviseringar och rekommendationer |
> | **Id** | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.Authorization/policyAssignments/* | Skapa och hantera principtilldelningar |
> | Microsoft.Authorization/policyDefinitions/* | Skapa och hantera principdefinitioner |
> | Microsoft.Authorization/policySetDefinitions/* | Skapa och hantera principuppsättningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
> | Microsoft.Management/managementGroups/read | Lista över hanteringsgrupper för autentiserade användare. |
> | Microsoft.operationalInsights/workspaces/*/read | Visa Log Analytics-data |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Security/*/read | Läs säkerhetskomponenter och principer |
> | Microsoft.Security/locations/alerts/activate/action | Aktivera en säkerhetsvarning |
> | Microsoft.Security/locations/alerts/dismiss/action | Avvisa en säkerhetsavisering |
> | Microsoft.Security/locations/tasks/activate/action | Aktivera en säkerhetsrekommendation |
> | Microsoft.Security/locations/tasks/dismiss/action | Stänga en säkerhetsrekommendation |
> | Microsoft.Security/policies/write | Uppdaterar säkerhetsprincipen |
> | Microsoft.Security/securityContacts/write | Uppdaterar säkerhetskontakt |
> | Microsoft.Security/securityContacts/delete | Tar bort säkerhetskontakt |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="security-manager"></a>Säkerhetshanteraren
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera säkerhetskomponenter, säkerhetsprinciper och virtuella datorer |
> | **Id** | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.ClassicCompute/*/read | Läsa konfigurationsinformation klassiska virtuella datorer |
> | Microsoft.ClassicCompute/virtualMachines/*/write | Spara konfigurationen för klassiska virtuella datorer |
> | Microsoft.ClassicNetwork/*/read | Läsa konfigurationsinformation om klassiskt nätverk |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Security/* | Skapa och hantera säkerhetskomponenter och principer |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="security-reader"></a>Säkerhetsläsare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | I Security Center endast: Visa rekommendationer och aviseringar, visa IPSec-principer security tillstånd, men det går inte att göra ändringar |
> | **Id** | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
> | Microsoft.operationalInsights/workspaces/*/read | Visa Log Analytics-data |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Security/*/read | Läs säkerhetskomponenter och principer |
> | Microsoft.Support/* | Skapa och hantera supportärenden |
> | Microsoft.Management/managementGroups/read | Lista över hanteringsgrupper för autentiserade användare. |

## <a name="site-recovery-contributor"></a>Site Recovery-bidragsgivare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera Site Recovery-tjänsten förutom att skapa valv och tilldela roller |
> | **Id** | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
> | Microsoft.Network/virtualNetworks/read | Hämta definitionen av virtuella nätverket |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp är en intern åtgärd som används av tjänsten |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | Allocatedstamp är en intern åtgärd som används av tjänsten |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Åtgärden Uppdatera Resurscertifikat uppdateras certifikatet för resurs/valv-autentiseringsuppgifter. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Skapa och hantera utökad information som rör valv |
> | Microsoft.RecoveryServices/Vaults/read | Hämta valv-åtgärden hämtar ett objekt som representerar Azure-resursen av typen ”vault' |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Skapa och hantera registrerade identiteter |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Skapa eller uppdatera aviseringsinställningar för replikering |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Läsa alla händelser |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/* | Skapa och hantera replikeringsinfrastrukturer |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Skapa och hantera replikeringsjobb |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/* | Skapa och hantera replikeringsprinciper |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Skapa och hantera återställningsplaner |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* | Skapa och hantera lagringskonfiguration för Recovery Services-valv |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Returnerar tokeninformation för Recovery Services-valv. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Returnerar användningsinformation om Recovery Services-valvet. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Valvtokenåtgärden kan användas för att hämta Valvtoken för serverdelsåtgärder på valvnivå. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Läsa aviseringar för Recovery services-valvet |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Storage/storageAccounts/read | Returnerar listan med lagringskonton eller hämtar egenskaperna för det angivna lagringskontot. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="site-recovery-operator"></a>Site Recovery-operatör
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig växla vid fel och återställa men inte utföra andra Site Recovery-hanteringsåtgärder |
> | **Id** | 494ae006-db33-4328-BF46-533a6560a3ca |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
> | Microsoft.Network/virtualNetworks/read | Hämta definitionen av virtuella nätverket |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp är en intern åtgärd som används av tjänsten |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | Allocatedstamp är en intern åtgärd som används av tjänsten |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv? |
> | Microsoft.RecoveryServices/Vaults/read | Hämta valv-åtgärden hämtar ett objekt som representerar Azure-resursen av typen ”vault' |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Åtgärden hämta Åtgärdsresultat åtgärd kan du hämta åtgärdsstatusen och resultatet för åtgärden som skickats asynkront |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Hämta behållare kan du använda åtgärden hämta behållare som är registrerade för en resurs. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Läsa alla aviseringsinställningar |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Läsa alla händelser |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Kontrollerar infrastrukturens enhetlighet |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Läsa alla infrastrukturer |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Skapa en ny koppling Gateway |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Förnya certifikat för infrastruktur |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Läsa alla nätverk |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Läsa alla nätverksmappningar |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Läsa alla skyddsbehållare |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Läsa alla objekt som ska skyddas |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Använda återställningspunkt |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Redundansåtgärd |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Planerad redundans |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Läsa alla skyddade objekt |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Läsa alla återställningspunkter för replikering |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Reparera replikering |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Återaktivera skydd för skyddat objekt |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Testa redundans |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Redundanstest |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Redundans |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Uppdateringen av Mobilitetstjänsten |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Läsa alla mappningar av skyddsbehållare |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Läsa alla Recovery Services-leverantörer |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Uppdatera Provider |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Läsa alla Lagringsklassificeringar |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Läsa alla mappningar av Lagringsklassificering |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Läsa alla vCenters |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Skapa och hantera replikeringsjobb |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Läsa alla principer |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Återställningsplan för redundansåtgärd |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Återställningsplan för planerad redundans |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Läsa alla Återställningsplaner |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Återskydd |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Redundansåterställning testplan |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Återställningsplan för test av Redundanstest |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Återställningsplan för redundans |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Läsa aviseringar för Recovery services-valvet |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Returnerar tokeninformation för Recovery Services-valv. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Returnerar användningsinformation om Recovery Services-valvet. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Valvtokenåtgärden kan användas för att hämta Valvtoken för serverdelsåtgärder på valvnivå. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Storage/storageAccounts/read | Returnerar listan med lagringskonton eller hämtar egenskaperna för det angivna lagringskontot. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="site-recovery-reader"></a>Site Recovery-läsare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig se Site Recovery-status men inte utföra andra hanteringsåtgärder |
> | **Id** | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp är en intern åtgärd som används av tjänsten |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Hämtar aviseringarna för Recovery services-valvet. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/read | Hämta valv-åtgärden hämtar ett objekt som representerar Azure-resursen av typen ”vault' |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Åtgärden hämta Åtgärdsresultat åtgärd kan du hämta åtgärdsstatusen och resultatet för åtgärden som skickats asynkront |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Hämta behållare kan du använda åtgärden hämta behållare som är registrerade för en resurs. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Läsa alla aviseringsinställningar |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Läsa alla händelser |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Läsa alla infrastrukturer |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Läsa alla nätverk |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Läsa alla nätverksmappningar |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Läsa alla skyddsbehållare |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Läsa alla objekt som ska skyddas |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Läsa alla skyddade objekt |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Läsa alla återställningspunkter för replikering |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Läsa alla mappningar av skyddsbehållare |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Läsa alla Recovery Services-leverantörer |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Läsa alla Lagringsklassificeringar |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Läsa alla mappningar av Lagringsklassificering |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Läsa alla vCenters |
> | Microsoft.RecoveryServices/vaults/replicationJobs/read | Läsa alla jobb |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Läsa alla principer |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Läsa alla Återställningsplaner |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Returnerar tokeninformation för Recovery Services-valv. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Returnerar användningsinformation om Recovery Services-valvet. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Valvtokenåtgärden kan användas för att hämta Valvtoken för serverdelsåtgärder på valvnivå. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="sql-db-contributor"></a>SQL DB-deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera SQL-databaser, men inte tillgång till dem. Dessutom kan inte hantera deras säkerhetsrelaterade principer eller deras överordnade SQL-servrar. |
> | **Id** | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rollen tilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviseringsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/databases/* | Skapa och hantera SQL-databaser |
> | Microsoft.Sql/servers/read | Returnera listan över servrar eller hämtar egenskaperna för den angivna servern. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |
> | Microsoft.Insights/metrics/read | Läs mått |
> | Microsoft.Insights/metricDefinitions/read | Läs måttdefinitioner |
> | **NotActions** |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Det går inte att redigera granskningsprinciper |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Det går inte att redigera granskningsinställningar |
> | Microsoft.Sql/servers/databases/auditRecords/read | Hämta granskningsposter för databas-blob |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Det går inte att redigera principer |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Det går inte att redigera principer för datamaskning |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Det går inte att redigera säkerhetsprinciper för avisering |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Det går inte att redigera säkerhetsmått |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |

## <a name="sql-security-manager"></a>SQL-säkerhetshanteraren
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Tillåter dig att hantera säkerhetsrelaterade principer för SQL-servrar och databaser, men inte åtkomst till dem. |
> | **Id** | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs Microsoft-auktorisering |
> | Microsoft.Insights/alertRules/* | Skapa och hantera Insights Varningsregler |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Ansluter till resursen, till exempel storage-konto eller SQL-databas till ett undernät. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Skapa och hantera granskningsprinciper för SQL server |
> | Microsoft.Sql/servers/auditingSettings/* | Skapa och hantera granskning inställningen för SQL server |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Skapa och hantera granskningsprinciper för SQL server-databas |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Skapa och hantera granskningsinställningarna för SQL server-databas |
> | Microsoft.Sql/servers/databases/auditRecords/read | Läs granskningsposter |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Skapa och hantera principer för SQL server-databas |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Skapa och hantera SQL server-databas datamaskning principer |
> | Microsoft.Sql/servers/databases/read | Returnera listan över databaser eller hämtar egenskaperna för den angivna databasen. |
> | Microsoft.Sql/servers/databases/schemas/read | Hämta lista över scheman för en databas |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Hämta listan över kolumner i en tabell |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/read | Hämta listan över tabeller i en databas |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Skapa och hantera SQL server-databas avisering säkerhetsprinciper |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Skapa och hantera säkerhetsmått för SQL server-databas |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRules/* |  |
> | Microsoft.Sql/servers/read | Returnera listan över servrar eller hämtar egenskaperna för den angivna servern. |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Skapa och hantera aviseringar principer för SQL server-säkerhet |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="sql-server-contributor"></a>SQL Server-deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Tillåter dig att hantera SQL-servrar och databaser, men inte åtkomst till dem eller deras säkerhetsrelaterade principer. |
> | **Id** | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera Insights Varningsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/* | Skapa och hantera SQL-servrar |
> | Microsoft.Support/* | Skapa och hantera supportärenden |
> | Microsoft.Insights/metrics/read | Läs mått |
> | Microsoft.Insights/metricDefinitions/read | Läs måttdefinitioner |
> | **NotActions** |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Det går inte att redigera granskningsprinciper för SQL server |
> | Microsoft.Sql/servers/auditingSettings/* | Det går inte att redigera granskningsinställningarna för SQL server |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Det går inte att redigera granskningsprinciper för SQL server-databas |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Det går inte att redigera granskningsinställningarna för SQL server-databas |
> | Microsoft.Sql/servers/databases/auditRecords/read | Det går inte att läsa granskningsposter |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Det går inte att redigera principer för SQL server-databas |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Det går inte att redigera SQL server-databas datamaskning principer |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Det går inte att redigera SQL server-databas avisering säkerhetsprinciper |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Det går inte att redigera säkerhetsmått för SQL server-databas |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Det går inte att redigera avisering principer för SQL server-säkerhet |

## <a name="storage-account-contributor"></a>Lagringskontodeltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera lagringskonton, men ger dig inte åtkomst till dem. |
> | **Id** | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läsa alla auktorisering |
> | Microsoft.Insights/alertRules/* | Skapa och hantera Insights Varningsregler |
> | Microsoft.Insights/diagnosticSettings/* | Hantera diagnostikinställningar |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Ansluter till resursen, till exempel storage-konto eller SQL-databas till ett undernät. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Storage/storageAccounts/* | Skapa och hantera lagringskonton |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="storage-account-key-operator-service-role"></a>Tjänstroll som operatör av lagringskontonyckel
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Operatörer av lagringskontonycklar får lista och återskapa nycklar till lagringskonton |
> | **Id** | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | **Åtgärder** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | Returnerar åtkomstnycklarna för det angivna lagringskontot. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | Återskapar åtkomstnycklarna för det angivna lagringskontot. |

## <a name="storage-blob-data-contributor-preview"></a>Storage Blob Data-deltagare (förhandsgranskning)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Tillåter läs-, skriv- och borttagningsåtkomst till Azure Storage Blob-containers och data |
> | **Id** | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | **Åtgärder** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Returnerar resultatet av att ta bort en container |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Returnerar en container eller containerlista |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Returnerar resultatet av att placera eller låna blobcontainer |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Returnerar resultatet av att ta bort en blob |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Returnerar en blob eller bloblista |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Returnerar resultatet av att skriva en blob |

## <a name="storage-blob-data-reader-preview"></a>Storage Blob Data-läsare (förhandsgranskning)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Tillåter läsåtkomst till Azure Storage Blob-containers och data |
> | **Id** | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | **Åtgärder** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Returnerar en container eller containerlista |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Returnerar en blob eller bloblista |

## <a name="storage-queue-data-contributor-preview"></a>Lagringsködata-deltagare (förhandsgranskning)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Tillåter läs-, skriv- och borttagningssåtkomst till Azure Storage-köer och kömeddelanden |
> | **Id** | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | **Åtgärder** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Returnerar resultatet av att ta bort en kö |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Returnerar en kö eller kölista. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | Returnerar resultatet av att skriva en kö |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Returnerar resultatet av att ta bort ett meddelande |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Returnerar ett meddelande |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Returnerar resultatet av att skriva ett meddelande |

## <a name="storage-queue-data-reader-preview"></a>Lagringsködata-läsare (förhandsgranskning)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Tillåter läsåtkomst till Azure Storage-köer och kömeddelanden |
> | **Id** | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Åtgärder** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Returnerar en kö eller kölista. |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Returnerar ett meddelande |

## <a name="support-request-contributor"></a>Supportförfrågningsdeltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig skapa och hantera supportförfrågningar |
> | **Id** | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läsa auktorisering |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="traffic-manager-contributor"></a>Traffic Manager-deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera Traffic Manager-profiler, men låter dig inte kontrollera vem som har åtkomst till dem. |
> | **Id** | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera Insights Varningsregler |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="user-access-administrator"></a>Administratör för användaråtkomst
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera användaråtkomst till Azure-resurser. |
> | **Id** | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Åtgärder** |  |
> | * / läsa | Läsa resurser av alla typer utom hemligheter. |
> | Microsoft.Authorization/* | Hantera auktorisering |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="virtual-machine-administrator-login"></a>Administratörsinloggning för virtuell dator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Visa virtuella datorer i portalen och logga in som administratör |
> | **Id** | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | **Åtgärder** |  |
> | Microsoft.Network/publicIPAddresses/read | Hämtar en definition av offentlig ip-adress. |
> | Microsoft.Network/virtualNetworks/read | Hämta definitionen av virtuella nätverket |
> | Microsoft.Network/loadBalancers/read | Hämtar en definition för load balancer |
> | Microsoft.Network/networkInterfaces/read | Hämtar en definition för nätverk-gränssnittet.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Logga in på en virtuell dator som vanlig användare |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Logga in på en virtuell dator med behörighet som Windows-administratör eller Linux-rotanvändare |

## <a name="virtual-machine-contributor"></a>Virtuell datordeltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera virtuella datorer, men ger dig inte åtkomst till dem eller till det virtuella nätverk eller lagringskonto som de är anslutna till. |
> | **Id** | 9980e02c-c2be-4D73-94e8-173b1dc7cf3c |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läsa auktorisering |
> | Microsoft.Compute/availabilitySets/* | Skapa och hantera tillgänglighetsuppsättningar för beräkning |
> | Microsoft.Compute/locations/* | Skapa och hantera beräknings-platser |
> | Microsoft.Compute/virtualMachines/* | Skapa och hantera virtuella datorer |
> | Microsoft.Compute/virtualMachineScaleSets/* | Skapa och hantera VM-skalningsuppsättningar |
> | Microsoft.DevTestLab/schedules/* |  |
> | Microsoft.Insights/alertRules/* | Skapa och hantera Insights Varningsregler |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Ansluter till en application gateway backend-adresspool |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Ansluter till en load balancer-serverdelsadresspool |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Ansluter till en belastningsutjämnare inkommande nat-pool |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Ansluter till en belastningsutjämningsregel för ingående nat |
> | Microsoft.Network/loadBalancers/probes/join/action | Kan använda avsökningar av en belastningsutjämnare. Med den här behörigheten healthProbe-egenskapen för VM scale referera set till exempel till avsökningen. |
> | Microsoft.Network/loadBalancers/read | Hämtar en definition för load balancer |
> | Microsoft.Network/locations/* | Skapa och hantera nätverksplatser |
> | Microsoft.Network/networkInterfaces/* | Skapa och hantera nätverksgränssnitt |
> | Microsoft.Network/networkSecurityGroups/join/action | Ansluter till en nätverkssäkerhetsgrupp |
> | Microsoft.Network/networkSecurityGroups/read | Hämtar en Gruppdefinition för network security |
> | Microsoft.Network/publicIPAddresses/join/action | Ansluter till en offentlig ip-adress |
> | Microsoft.Network/publicIPAddresses/read | Hämtar en definition av offentlig ip-adress. |
> | Microsoft.Network/virtualNetworks/read | Hämta definitionen av virtuella nätverket |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Ansluter till ett virtuellt nätverk |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Skapa en säkerhetskopia för Avsiktsskydd |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Returnerar information om det skyddade objektet |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Skapa ett säkerhetskopierat skyddat objekt |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Returnerar alla skyddsprinciper |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Skapar en Skyddsprincip |
> | Microsoft.RecoveryServices/Vaults/read | Hämta valv-åtgärden hämtar ett objekt som representerar Azure-resursen av typen ”vault' |
> | Microsoft.RecoveryServices/Vaults/usages/read | Returnerar användningsinformation om Recovery Services-valvet. |
> | Microsoft.RecoveryServices/Vaults/write | Med skapa valv så skapas en Azure-resurs av typen valv |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.SqlVirtualMachine/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Returnerar åtkomstnycklarna för det angivna lagringskontot. |
> | Microsoft.Storage/storageAccounts/read | Returnerar listan med lagringskonton eller hämtar egenskaperna för det angivna lagringskontot. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |

## <a name="virtual-machine-user-login"></a>Användarinloggning för virtuell dator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Visa virtuella datorer i portalen och logga in som en vanlig användare. |
> | **Id** | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Åtgärder** |  |
> | Microsoft.Network/publicIPAddresses/read | Hämtar en definition av offentlig ip-adress. |
> | Microsoft.Network/virtualNetworks/read | Hämta definitionen av virtuella nätverket |
> | Microsoft.Network/loadBalancers/read | Hämtar en definition för load balancer |
> | Microsoft.Network/networkInterfaces/read | Hämtar en definition för nätverk-gränssnittet.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Logga in på en virtuell dator som vanlig användare |

## <a name="web-plan-contributor"></a>Webbplan-deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera webbplaner för webbplatser, men ger dig inte tillgång till dem. |
> | **Id** | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läsa auktorisering |
> | Microsoft.Insights/alertRules/* | Skapa och hantera Insights Varningsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |
> | Microsoft.Web/serverFarms/* | Skapa och hantera servergrupper |

## <a name="website-contributor"></a>Webbplatsdeltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera webbplatser (men inte webbplaner), men ger dig inte tillgång till dem. |
> | **Id** | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läsa auktorisering |
> | Microsoft.Insights/alertRules/* | Skapa och hantera Insights Varningsregler |
> | Microsoft.Insights/components/* | Skapa och hantera Insights-komponenter |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Microsoft.Resources/deployments/* | Skapa och hantera distribution av resursgrupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportärenden |
> | Microsoft.Web/certificates/* | Skapa och hantera webbplatscertifikat |
> | Microsoft.Web/listSitesAssignedToHostName/read | Hämta namnen på webbplatser tilldelade till värdnamn. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Visa egenskaperna för en App Service Plan |
> | Microsoft.Web/sites/* | Skapa och hantera webbplatser (för att skapa webbplatser även kräver skrivbehörighet till den associerade Apptjänstplan) |

## <a name="next-steps"></a>Nästa steg

- [Anpassade roller](custom-roles.md)
- [Hantera rolltilldelningar i Azure Portal](role-assignments-portal.md)
- [Behörigheter i Azure Security Center](../security-center/security-center-permissions.md)
