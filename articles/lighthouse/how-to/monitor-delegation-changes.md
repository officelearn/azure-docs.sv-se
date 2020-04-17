---
title: Övervaka delegeringsändringar i hanteringsklienten
description: Lär dig hur du övervakar delegeringsaktivitet från kundklienter till din hantering av klienten.
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: a4593b34311eca34e4fb68926a3820899ab3f324
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458819"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>Övervaka delegeringsändringar i hanteringsklienten

Som tjänsteleverantör kanske du vill vara medveten om när kundprenumerationer eller resursgrupper delegeras till din klient via [Azure-delegerad resurshantering](../concepts/azure-delegated-resource-management.md)eller när tidigare delegerade resurser tas bort.

I den hanterande klienten spårar [Azure-aktivitetsloggen](../../azure-monitor/platform/platform-logs-overview.md) delegeringsaktivitet på klientnivå. Den här loggade aktiviteten innehåller alla tillagda eller borttagna delegeringar från alla kundklienter.

I det här avsnittet beskrivs de behörigheter som krävs för att övervaka delegeringsaktiviteten till din klientorganisation (över alla dina kunder) och de bästa metoderna för detta. Den innehåller också ett exempelskript som visar en metod för att fråga och rapportera om dessa data.

> [!IMPORTANT]
> Alla dessa steg måste utföras i din hantering av klienten, snarare än i alla kundklienter.

## <a name="enable-access-to-tenant-level-data"></a>Aktivera åtkomst till data på klientnivå

För att komma åt aktivitetsloggdata på klientnivå måste ett konto tilldelas den inbyggda rollen [Övervakningsläsare](../../role-based-access-control/built-in-roles.md#monitoring-reader) vid rotomfattning (/). Den här tilldelningen måste utföras av en användare som har rollen Global administratör med ytterligare förhöjd åtkomst.

### <a name="elevate-access-for-a-global-administrator-account"></a>Öka åtkomsten för ett globalt administratörskonto

Om du vill tilldela en roll i rotomfattningen (/) måste du ha rollen Global administratör med förhöjd åtkomst. Den här förhöjda åtkomsten ska bara läggas till när du behöver göra rolltilldelningen och sedan tas bort när du är klar.

Detaljerade instruktioner om hur du lägger till och tar bort höjd finns i [Öka åtkomsten för att hantera alla Azure-prenumerationer och hanteringsgrupper](../../role-based-access-control/elevate-access-global-admin.md).

När du har höjt din åtkomst har ditt konto rollen Administratör för användaråtkomst i Azure i rotomfånget. Med den här rolltilldelningen kan du visa alla resurser och tilldela åtkomst i en prenumeration eller hanteringsgrupp i katalogen, samt att göra rolltilldelningar i rotomfattning. 

### <a name="create-a-new-service-principal-account-to-access-tenant-level-data"></a>Skapa ett nytt huvudkonto för tjänsten för att komma åt data på klientnivå

När du har höjt din åtkomst kan du tilldela rätt behörighet till ett konto så att det kan fråga aktivitetsloggdata på klientnivå. Det här kontot måste ha den inbyggda rollen [Övervakningsläsare](../../role-based-access-control/built-in-roles.md#monitoring-reader) tilldelad i rotomfånget för din hanteringsklient.

> [!IMPORTANT]
> Att bevilja en rolltilldelning i rotomfånget innebär att samma behörigheter gäller för varje resurs i klienten.

Eftersom detta är en bred åtkomstnivå rekommenderar vi att du tilldelar den här rollen till ett huvudkonto för tjänsten i stället för till en enskild användare eller till en grupp. Dessutom rekommenderar vi följande metodtips:

- [Skapa ett nytt huvudkonto](../../active-directory/develop/howto-create-service-principal-portal.md) för tjänsten som endast ska användas för den här funktionen i stället för att tilldela den här rollen till ett befintligt tjänsthuvudnamn som används för annan automatisering.
- Kontrollera att tjänstens huvudnamn inte har åtkomst till några delegerade kundresurser.
- [Använd ett certifikat för att autentisera](../../active-directory/develop/howto-create-service-principal-portal.md#certificates-and-secrets) och [lagra det säkert i Azure Key Vault](../../key-vault/general/best-practices.md).
- Begränsa de användare som har åtkomst att agera på uppdrag av tjänstens huvudnamn.

Använd någon av följande metoder för att göra rotomfattningstilldelningarna.

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

### <a name="remove-elevated-access-for-the-global-administrator-account"></a>Ta bort förhöjd åtkomst för kontot för global administratör

När du har skapat tjänstens huvudkonto och tilldelat rollen Övervakningsläsare i rotomfånget måste du ta bort den förhöjda åtkomsten för det globala [administratörskontot,](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access) eftersom den här åtkomstnivån inte längre behövs.

## <a name="query-the-activity-log"></a>Fråga aktivitetsloggen

När du har skapat ett nytt huvudkonto för tjänsten med övervakningsläsare åtkomst till rotomfånget för din hanterande klient kan du använda det för att fråga och rapportera om delegeringsaktivitet i din klientorganisation. 

[Det här Azure PowerShell-skriptet](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/monitor-delegation-changes) kan användas för att fråga de senaste 1 aktivitetsdagarna och rapporter om alla tillagda eller borttagna delegeringar (eller försök som inte lyckades). Den frågar [efter klientaktivitetsloggdata](https://docs.microsoft.com/rest/api/monitor/TenantActivityLogs/List) och konstruerar sedan följande värden för att rapportera om delegeringar som läggs till eller tas bort:

- **DelegatedResourceId**: ID för den delegerade prenumerationen eller resursgruppen
- **CustomerTenantId**: Kundens klient-ID
- **CustomerSubscriptionId**: Prenumerations-ID:t som delegerats eller som innehåller resursgruppen som delegerades
- **CustomerDelegationStatus**: Statusändringen för den delegerade resursen (lyckades eller misslyckades)
- **EventTimeStamp**: Datum och tid då delegeringsändringen loggades

Tänk på att fråga dessa data:

- Om flera resursgrupper delegeras i en enda distribution returneras separata poster för varje resursgrupp.
- Ändringar som gjorts i en tidigare delegering (t.ex. uppdatering av behörighetsstrukturen) loggas som en extra delegering.
- Som nämnts ovan måste ett konto ha den inbyggda rollen Övervakningsläsare vid rotomfattning (/) för att komma åt dessa data på klientnivå.
- Du kan använda dessa data i dina egna arbetsflöden och rapportering. Du kan till exempel använda [HTTP Data Collector API (offentlig förhandsversion)](../../azure-monitor/platform/data-collector-api.md) för att logga data till Azure Monitor från en REST API-klient och sedan använda [åtgärdsgrupper](../../azure-monitor/platform/action-groups.md) för att skapa meddelanden eller aviseringar.

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

- Lär dig hur du 200880-000-kunder till [Azure-delegerad resurshantering](../concepts/azure-delegated-resource-management.md).
- Lär dig mer om [Azure Monitor](../../azure-monitor/index.yml) och [Azure-aktivitetsloggen](../../azure-monitor/platform/platform-logs-overview.md).
