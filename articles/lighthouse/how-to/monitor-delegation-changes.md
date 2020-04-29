---
title: Övervaka Delegerings ändringar i hanterings klienten
description: Lär dig hur du övervakar Delegerings aktivitet från kund klienter till din hanterings klient.
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: a4593b34311eca34e4fb68926a3820899ab3f324
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81458819"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>Övervaka Delegerings ändringar i hanterings klienten

Som en tjänst leverantör kanske du vill vara medveten om kund prenumerationer eller resurs grupper delegeras till din klient organisation via [Azure delegerad resurs hantering](../concepts/azure-delegated-resource-management.md), eller när tidigare delegerade resurser tas bort.

I hanterings klienten spårar [Azure aktivitets loggen](../../azure-monitor/platform/platform-logs-overview.md) Delegerings aktivitet på klient nivå. Den här loggade aktiviteten innehåller alla tillagda eller borttagna delegeringar från alla kund klienter.

I det här avsnittet beskrivs de behörigheter som krävs för att övervaka Delegerings aktivitet till din klient (i alla kunder) och bästa praxis för att göra detta. Det innehåller också ett exempel skript som visar en metod för att fråga och rapportera om dessa data.

> [!IMPORTANT]
> Alla de här stegen måste utföras i hanterings klienten i stället för i alla kund klienter.

## <a name="enable-access-to-tenant-level-data"></a>Ge åtkomst till data på klient nivå

För att få åtkomst till aktivitets logg data på klient nivå måste ett konto tilldelas den inbyggda rollen för [övervaknings läsaren](../../role-based-access-control/built-in-roles.md#monitoring-reader) i rot omfånget (/). Den här tilldelningen måste utföras av en användare som har rollen global administratör med ytterligare utökad åtkomst.

### <a name="elevate-access-for-a-global-administrator-account"></a>Öka åtkomsten för ett globalt administratörs konto

Om du vill tilldela en roll i rot omfånget (/) måste du ha rollen global administratör med utökad åtkomst. Den här utökade åtkomsten bör endast läggas till när du behöver göra roll tilldelningen och sedan ta bort den när du är färdig.

Detaljerade anvisningar om hur du lägger till och tar bort höjning finns i [öka åtkomsten för att hantera alla Azure-prenumerationer och hanterings grupper](../../role-based-access-control/elevate-access-global-admin.md).

När du har tilldelat åtkomsten har ditt konto rollen administratör för användar åtkomst i Azure i rot omfånget. Med den här roll tilldelningen kan du Visa alla resurser och tilldela åtkomst i alla prenumerationer eller hanterings grupper i katalogen, samt för att skapa roll tilldelningar i rot omfånget. 

### <a name="create-a-new-service-principal-account-to-access-tenant-level-data"></a>Skapa ett nytt tjänst huvud konto för att få åtkomst till data på klient nivå

När du har förhöjd åtkomst kan du tilldela rätt behörigheter till ett konto så att det kan fråga efter aktivitets logg data på klient nivå. Det här kontot måste ha den inbyggda rollen [övervaknings läsare](../../role-based-access-control/built-in-roles.md#monitoring-reader) tilldelad till rot omfånget för hanterings klienten.

> [!IMPORTANT]
> Att bevilja en roll tilldelning i rot omfånget innebär att samma behörigheter gäller för alla resurser i klienten.

Eftersom detta är en bred åtkomst nivå rekommenderar vi att du tilldelar rollen till ett huvud konto för tjänsten i stället för till en enskild användare eller till en grupp. Dessutom rekommenderar vi följande bästa praxis:

- [Skapa ett nytt tjänst huvud konto](../../active-directory/develop/howto-create-service-principal-portal.md) som endast ska användas för den här funktionen, i stället för att tilldela den här rollen till ett befintligt huvud namn för tjänsten som används för annan automatisering.
- Se till att det här tjänstens huvud namn inte har åtkomst till några delegerade kund resurser.
- [Använd ett certifikat för att autentisera](../../active-directory/develop/howto-create-service-principal-portal.md#certificates-and-secrets) och [lagra det på ett säkert sätt i Azure Key Vault](../../key-vault/general/best-practices.md).
- Begränsa de användare som har åtkomst till Act för tjänstens huvud namn.

Använd någon av följande metoder för att göra rot omfångs tilldelningar.

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

New-AzRoleAssignment -SignInName <yourLoginName> -Scope "/" -RoleDefinitionName "Monitoring Reader"  -ApplicationId $servicePrincipal.ApplicationId 
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az role assignment create --assignee 00000000-0000-0000-0000-000000000000 --role "Monitoring Reader" --scope "/"
```

### <a name="remove-elevated-access-for-the-global-administrator-account"></a>Ta bort utökad åtkomst för det globala administratörs kontot

När du har skapat ditt tjänst huvud konto och tilldelat övervaknings läsaren rollen i rot omfånget, måste du [ta bort den utökade åtkomsten](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access) för det globala administratörs kontot eftersom den här åtkomst nivån inte längre behövs.

## <a name="query-the-activity-log"></a>Fråga aktivitets loggen

När du har skapat ett nytt tjänst huvud konto med övervaknings läsaren åtkomst till rot omfånget för din hanterings klient kan du använda det för att fråga efter och rapportera om Delegerings aktivitet i din klient. 

[Det här Azure PowerShell skriptet](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) kan användas för att skicka frågor till den senaste 1 dagen av aktivitet och rapporter om eventuella tillagda eller borttagna delegeringar (eller försök som inte lyckades). Den frågar [klient aktivitets logg](https://docs.microsoft.com/rest/api/monitor/TenantActivityLogs/List) data och skapar sedan följande värden för att rapportera om delegeringar som läggs till eller tas bort:

- **DelegatedResourceId**: ID för den delegerade prenumerationen eller resurs gruppen
- **CustomerTenantId**: kundens klient-ID
- **CustomerSubscriptionId**: det PRENUMERATIONS-ID som har delegerats eller som innehåller den resurs grupp som har delegerats
- **CustomerDelegationStatus**: status ändring för den delegerade resursen (lyckades eller misslyckades)
- **EventTimeStamp**: datum och tid då Delegerings ändringen loggades

Tänk på följande när du frågar efter dessa data:

- Om flera resurs grupper delegeras i en enda distribution returneras separata poster för varje resurs grupp.
- Ändringar som görs i en tidigare delegering (till exempel uppdatering av behörighets strukturen) kommer att loggas som en tillagd delegering.
- Som anges ovan måste ett konto ha den inbyggda rollen övervaknings läsare i rot omfånget (/) för att få åtkomst till dessa data på klient nivå.
- Du kan använda dessa data i dina egna arbets flöden och rapporter. Du kan till exempel använda [http data Collector-API: t (offentlig för hands version)](../../azure-monitor/platform/data-collector-api.md) för att logga Data till Azure Monitor från en REST API-klient och sedan använda [Åtgärds grupper](../../azure-monitor/platform/action-groups.md) för att skapa meddelanden eller aviseringar.

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Azure Lighthouse: Query Tenant Activity Log for registered/unregistered delegations for the past 1 day

$GetDate = (Get-Date).AddDays((-1))

$dateFormatForQuery = $GetDate.ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ssZ")

# Getting Azure context for the API call
$currentContext = Get-AzContext

# Fetching new token
$azureRmProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = [Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient]::new($azureRmProfile)
$token = $profileClient.AcquireAccessToken($currentContext.Tenant.Id)

$listOperations = @{
    Uri = "https://management.azure.com/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&`$filter=eventTimestamp ge '$($dateFormatForQuery)'"
    Headers = @{
        Authorization = "Bearer $($token.AccessToken)"
        'Content-Type' = 'application/json'
    }
    Method = 'GET'
}
$list = Invoke-RestMethod @listOperations
$showOperations = $list.value

if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/register/action")
{
    $registerOutputs  = $showOperations | Where-Object -FilterScript {$_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/register/action"}
    foreach ($registerOutput in $registerOutputs)
    {
    $registerOutputdata = [pscustomobject]@{
        Event = "An Azure customer has delegated resources to your tenant";
        DelegatedResourceId = $registerOutput.description |%{$_.split('"')[11]};
        CustomerTenantId = $registerOutput.description |%{$_.split('"')[7]};
        CustomerSubscriptionId = $registerOutput.subscriptionId;
        CustomerDelegationStatus = $registerOutput.status.value;
        EventTimeStamp = $registerOutput.eventTimestamp;
        }
        $registerOutputdata | Format-List
    }
}
if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/unregister/action") 
{
    $unregisterOutputs  = $showOperations | Where-Object -FilterScript {$_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/unregister/action"}
    foreach ($unregisterOutput in $unregisterOutputs)
    {
    $unregisterOutputdata = [pscustomobject]@{
        Event = "An Azure customer has removed delegated resources from your tenant";
        DelegatedResourceId = $unregisterOutput.description |%{$_.split('"')[11]};
        CustomerTenantId = $unregisterOutput.description |%{$_.split('"')[7]};
        CustomerSubscriptionId = $unregisterOutput.subscriptionId;
        CustomerDelegationStatus = $unregisterOutput.status.value;
        EventTimeStamp = $unregisterOutput.eventTimestamp;
        }
        $unregisterOutputdata | Format-List
    }
}
else 
{
    Write-Output "No new delegation changes."
}   


```

## <a name="next-steps"></a>Nästa steg

- Lär dig att publicera kunder till [Azure-delegerad resurs hantering](../concepts/azure-delegated-resource-management.md).
- Läs mer om [Azure Monitor](../../azure-monitor/index.yml) och [Azure aktivitets logg](../../azure-monitor/platform/platform-logs-overview.md).
