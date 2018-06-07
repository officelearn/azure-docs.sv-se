---
title: Hämta Data för kompatibilitetsinställningar i Azure princip
description: Azure princip utvärderingar och effekter bedöma efterlevnad. Lär dig mer om att hämta information om kompatibilitet.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 05/24/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: d36ecb18811901fb781e151c06badc0697c2d769
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655398"
---
# <a name="getting-compliance-data"></a>Hämta Data för kompatibilitetsinställningar

En av de största fördelarna med Azure-principen är insikt och kontroller över resurser i en prenumeration eller [hanteringsgruppen](../azure-resource-manager/management-groups-overview.md) av prenumerationer. Den här kontrollen kan utföras på många olika sätt, till exempel förhindra resurser som skapas på fel plats framtvinga användning av gemensam och enhetlig taggen eller granska befintliga resurser för behov konfigurationer och inställningar. I samtliga fall data som genereras av en princip för att du ska förstå kompatibilitetsstatusen för din miljö.

Det finns flera sätt att komma åt kompatibilitetsinformationen som genererats av din princip och initiativ tilldelningar:

- Med den [Azure-portalen](#portal)
- Via [kommandoraden](#command_line) skript

Innan du tittar på metoder för att rapportera om kompatibiliteten ska vi titta på när kompatibilitetsinformation uppdateras och frekvens och händelser som utlöser ett utvärderingscykel.

## <a name="evaluation-triggers"></a>Utvärdering av utlösare

Resultatet av en slutförd utvärderingscykel återspeglas i den `Microsoft.PolicyInsights` Resursprovidern via `PolicyStates` och `PolicyEvents` åtgärder. Mer information om alternativen och funktionerna i princip insikter REST-API finns [princip insikter](/rest/api/policy-insights/).

Utvärderingen av principer som är tilldelade och initiativ sker till följd av olika händelser:

- En princip eller initiativ tilldelas nyligen ett scope. När detta inträffar tar cirka 30 minuter för tilldelningen ska tillämpas på det angivna omfånget. När den används av utvärderingscykel börjar för resurser i omfattningen mot nytilldelade principen eller initiativ och beroende på de effekter som används av principen eller initiativ, resurser är markerade som kompatibla eller icke-kompatibla. En stor eller initiativ utvärderas mot en stor omfattning resurser kan ta tid, så det finns ingen fördefinierad förväntningen på när utvärderingscykeln slutförs. Uppdaterade kompatibilitetsresultat är tillgängliga i portalen och SDK: er när den är klar.
- En princip eller initiativ som redan har tilldelats ett scope har uppdaterats. Utvärderingscykel och val av tidpunkt för det här scenariot är desamma som för en ny tilldelning till ett omfång.
- En resurs har distribuerats i ett omfång med en tilldelning via Hanteraren för filserverresurser REST, Azure CLI eller Azure PowerShell. I det här scenariot händelsen gälla (lägga till, granska, neka, distribuera) och kompatibel statusinformation blir tillgänglig i portalen och SDK cirka 15 minuter senare.
- Utvärderingscykel för standard kompatibilitet. En gång per dygn är tilldelningar automatiskt ny utvärdering. En stor eller initiativ utvärderas mot en stor omfattning resurser kan ta tid, så det finns ingen fördefinierad förväntningen på när utvärderingscykeln slutförs. Uppdaterade kompatibilitetsresultat är tillgängliga i portalen och SDK: er när den är klar.

## <a name="how-compliance-works"></a>Så här fungerar kompatibilitet

En resurs är icke-kompatibla om du inte följer principen eller initiativ regler för en tilldelning. Följande tabell visar hur olika principer effekter som fungerar med villkoret utvärderingsversionen för resulterande kompatibilitetsstatus:

| Resource tillstånd | Verkan | Utvärderingen | Kompatibilitetsstatus |
| --- | --- | --- | --- |
| Finns | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Icke-kompatibel |
| Finns | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Kompatibel |
| Ny | Audit, AuditIfNotExist\* | True | Icke-kompatibel |
| Ny | Audit, AuditIfNotExist\* | False | Kompatibel |

\* För åtgärderna Append, DeployIfNotExist och AuditIfNotExist måste IF-instruktionen är TRUE.
Åtgärderna kräver också att villkoret Finns är FALSE för att vara icke-kompatibla. När det är TRUE utlöser IF-villkoret utvärdering av villkoret Finns för de relaterade resurserna.

För att bättre förstå hur resurser har flaggats som icke-kompatibla, ska vi använda princip tilldelningen exemplet skapade ovan.

Anta att du har en resursgrupp – ContsoRG, med vissa storage-konton (markerat i rött) som exponeras för offentliga nätverk.

![Storage-konton som exponeras för offentliga nätverk](media/policy-insights/resource-group01.png)

I det här exemplet behöver du vara försiktig med säkerhetsrisker. Nu när du har skapat en principtilldelning, utvärderas den för alla lagringskonton i resursgruppen ContosoRG. Den granskningar tre icke-kompatibla lagringskonton, därför ändra deras tillstånd att **icke-kompatibla.**

![Granskas icke-kompatibla storage-konton](media/policy-insights/resource-group03.png)

## <a name="portal"></a>Portalen

Azure-portalen innehåller en grafisk upplevelse av att visualisera och förstå status för efterlevnad i din miljö. På den **princip** sidan den **översikt** alternativet innehåller information om tillgängliga scope på kompatibiliteten för både principer och initiativ. Förutom kompatibilitetsstatus och antal per tilldelning innehåller ett diagram över kompatibiliteten under de senaste sju dagarna. Den **kompatibilitet** sidan innehåller en stor del av samma information (utom diagram), men ger ytterligare filtrera och sortera alternativ.

![Sidan för princip-kompatibilitet](media/policy-compliance/compliance-page.png)

Som en princip eller initiativ kan tilldelas till olika omfång, Observera du omfattningen för varje tilldelning och vilken sorts definition som har tilldelats detta scope i tabellen. Det finns också antalet principer för icke-kompatibla och icke-kompatibla resurser för varje tilldelning. Klicka på en princip eller initiativ i tabellen innehåller en djupare inblick i kompatibiliteten för den specifika tilldelningen.

![Information om kompatibilitet](media/policy-compliance/compliance-details.png)

När listan över resurser på den **icke-kompatibel resurser** fliken återspeglar status för utvärdering av befintliga resurser för den aktuella tilldelningen händelser (lägga till, granska, neka, distribuera) utlöses av en begäran att skapa en resurs är visas under den **händelser** fliken.

![Kompatibilitet Principhändelser](media/policy-compliance/compliance-events.png)

Högerklicka på raden i den händelse som du vill samla in mer information om och välj **visa aktivitetsloggar**. Aktivitetsloggsidan öppnas och är före filtrerade i sökningen visar information om tilldelning och händelser. Aktivitetsloggen ger ytterligare kontext och information om dessa händelser.

![Princip för efterlevnad aktivitetsloggen](media/policy-compliance/compliance-activitylog.png)

## <a name="command-line"></a>Kommandorad

Samma information som är tillgänglig i portalen kan hämtas med hjälp av REST-API direkt (inklusive med [ARMClient](https://github.com/projectkudu/ARMClient)) eller Azure PowerShell med REST API. Fullständig information om REST-API finns i [princip insikter](/rest/api/policy-insights/) referens. Sidorna REST API-referens har en grön ' försök-knappen på varje åtgärd där du kan prova direkt i webbläsaren.

Skapa ett autentiseringstoken med den här exempelkoden för att använda följande exempel i Azure PowerShell. Ersätt sedan $restUri med önskad strängen i exemplen att hämta en JSON-objekt som sedan kan parsas.

```azurepowershell-interactive
# Login first with Connect-AzureRmAccount if not using Cloud Shell

$azContext = Get-AzureRmContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Define the REST API to communicate with
# Use double quotes for $restUri as some endpoints take strings passed in single quotes
$restUri = "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04"

# Invoke the REST API
$response = Invoke-RestMethod -Uri $restUri -Method POST -Headers $authHeader

# View the response object (as JSON)
$response
```

### <a name="summarize-results"></a>Sammanfatta resultat

Sammanfattningen innehåller REST-API, kan utföras av hanteringsgruppen prenumeration, resursgrupp, resurs, initiativ, princip, tilldelning av prenumerationen eller resurstilldelning av grupp. Här är ett exempel på sammanfattning på prenumerationsnivån med princip Insight [sammanfatta för prenumerationen](/rest/api/policy-insights/policystates/summarizeforsubscription):

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

Utdata sammanfattas prenumerationen. I de exempel på utdata nedan sammanfattade kompatibiliteten är under **value.results.nonCompliantResources** och **value.results.nonCompliantPolicies**. Denna begäran ger ytterligare information, inklusive varje tilldelning som består av icke-kompatibla siffror och definitionsinformation för varje tilldelning. Varje princip för objekt i hierarkin innehåller en **queryResultsUri** som kan användas för att få ytterligare information på den nivån.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
        "results": {
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>Frågan för resurser

I exemplet ovan, **value.policyAssignments.policyDefinitions.results.queryResultsUri** försett oss med ett exempel på URI: N för att hämta alla icke-kompatibla resurser för en specifik princip-definition. Titta på den **$filter** IsCompliant är lika med (eq) till false, PolicyAssignmentId har angetts för principdefinitionen och PolicyDefinitionId sig själv.
Orsaken till inklusive PolicyAssignmentId i filtret är att PolicyDefinitionId kan finnas i princip- eller initiativ tilldelningar med olika omfång. Genom att ange både PolicyAssignmentId och PolicyDefinitionId kan vara vi explicit i resultaten vi letar efter. Tidigare använde vi **senaste** för PolicyStates (endast tillåtet värde för **policyStatesSummaryResource** på operatorn sammanfatta för prenumeration), som anger automatiskt en  **från** och **till** tidsperioden för de senaste 24 timmarna.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Svaret exemplet nedan har tagits bort visar bara en enskild icke-kompatibla resurs planeringsaspekter (Observera att @odata.count är faktiskt 15 och matchar antal icke-kompatibla resurser från exemplet ovan). Detaljerad svaret innehåller flera delar av data om resursen, principen (eller initiativ), och tilldelningen. Observera att du kan också se vilka tilldelning parametrar skickades till principdefinitionen.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 15,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "timestamp": "2018-05-19T04:41:09Z",
        "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Compute/virtualMachines/linux",
        "policyAssignmentId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Authorization/policyAssignments/37ce239ae4304622914f0c77",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "effectiveParameters": "",
        "isCompliant": false,
        "subscriptionId": "{subscriptionId}",
        "resourceType": "/Microsoft.Compute/virtualMachines",
        "resourceLocation": "westus2",
        "resourceGroup": "RG-Tags",
        "resourceTags": "tbd",
        "policyAssignmentName": "37ce239ae4304622914f0c77",
        "policyAssignmentOwner": "tbd",
        "policyAssignmentParameters": "{\"tagName\":{\"value\":\"costCenter\"},\"tagValue\":{\"value\":\"Contoso-Test\"}}",
        "policyAssignmentScope": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags",
        "policyDefinitionName": "1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "policyDefinitionAction": "deny",
        "policyDefinitionCategory": "tbd",
        "policySetDefinitionId": "",
        "policySetDefinitionName": "",
        "policySetDefinitionOwner": "",
        "policySetDefinitionCategory": "",
        "policySetDefinitionParameters": "",
        "managementGroupIds": "",
        "policyDefinitionReferenceId": ""
    }]
}
```

### <a name="view-events"></a>Visa händelser

När en resurs skapas eller uppdateras, skapas en utvärderingsresultat av principen. Resultaten kallas _Principhändelser_. Använd följande URI: N om du vill visa den senaste Principhändelser som är associerade med prenumerationen.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2018-04-04
```

Ditt resultat liknar följande exempel:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 16
    }]
}
```

Mer information om frågor Principhändelser finns i [Principhändelser](/rest/api/policy-insights/policyevents) referensartikeln.

### <a name="azure-powershell-preview"></a>Azure PowerShell (förhandsgranskning)

Azure PowerShell-modulen för principen inte är ännu slutlig, men är tillgängliga på PowerShell-galleriet som en [Förhandsgranska versionen](https://www.powershellgallery.com/packages/AzureRM.PolicyInsights).
Om PowerShellGet är minst version 1.6.0 (krävs för att stödja förhands-objekt), kan du hämta preview version använder `Install-Module` (Kontrollera att du har senast [Azure PowerShell](/powershell/azure/install-azurerm-ps) installerad):

```powershell
# Download preview from PowerShell Gallery via PowerShellGet
Install-Module -Name AzureRM.PolicyInsights -AllowPrerelease

# Import the downloaded module
Import-Module AzureRM.PolicyInsights

# Login with Connect-AzureRmAccount if not using Cloud Shell
Connect-AzureRmAccount
```

Modulen preview har tre cmdletar:

- `Get-AzureRmPolicyStateSummary`
- `Get-AzureRmPolicyState`
- `Get-AzureRmPolicyEvent`

Exempel: Hämta status sammanfattning för den översta tilldelade principen med det högsta antalet icke-kompatibla resurser.

```powershell
PS > Get-AzureRmPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Exempel: Hämta posten tillstånd för de nyligen utvärderas resurs (standard är av tidsstämpel i fallande ordning).

```powershell
PS > Get-AzureRmPolicyState -Top 1

Timestamp                  : 5/22/2018 3:47:34 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/networkInterfaces/linux316
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/networkInterfaces
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Exempel: Hämta information för alla icke-kompatibla virtuella nätverksresurser.

```powershell
PS > Get-AzureRmPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

Timestamp                  : 5/22/2018 4:02:20 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

Exempel: Hämtningen av händelser relaterade till icke-kompatibla virtuella nätverksresurser som inträffade efter ett visst datum.

```powershell
PS > Get-AzureRmPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2018-05-19'

Timestamp                  : 5/19/2018 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : eastus
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
TenantId                   : {tenantId}
PrincipalOid               : {principalOid}
```

Den **PrincipalOid** fältet kan användas för att få en viss användare med Azure PowerShell-cmdleten `Get-AzureRmADUser`. Ersätt **{principalOid}** med svar du får från föregående exempel.

```powershell
PS > (Get-AzureRmADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="log-analytics"></a>Log Analytics

Om du har en [logganalys](../log-analytics/log-analytics-overview.md) med den `AzureActivity` lösning som är knutna till din prenumeration och du kan också visa överträdelser resultat från utvärderingscykel med hjälp av enkla Kusto frågor och `AzureActivity` tabell. Med information om icke-kompatibilitet i logganalys innebär det också att aviseringar kan konfigureras för att bevaka bristande kompatibilitet på en specifik resurs, resursgrupp eller även ett tröskelvärde för icke-kompatibla objekt, till exempel fler än 10 under de senaste 24 timmarna.

![Princip för kompatibilitet med logganalys](media/policy-compliance/compliance-loganalytics.png)

## <a name="next-steps"></a>Nästa steg

- Granska de [definition principstruktur](policy-definition.md).
- Granska [Förstå princip effekter](policy-effects.md).
- Granska vad en hanteringsgrupp är med [ordna dina resurser med Azure-Hanteringsgrupper](../azure-resource-manager/management-groups-overview.md)