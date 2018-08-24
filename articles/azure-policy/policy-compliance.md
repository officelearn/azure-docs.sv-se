---
title: Hämta Efterlevnadsdata i Azure Policy
description: Azure Policy-utvärderingar och effekterna avgör efterlevnad. Lär dig hur du hämtar information om kompatibilitet.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/29/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 6b310daec67f41ba589ce279e4a2dad427adb734
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818900"
---
# <a name="getting-compliance-data"></a>Hämta Data för kompatibilitetsinställningar

En av de största fördelarna med Azure Policy är insikter och kontroller över resurser i en prenumeration eller [hanteringsgruppen](../azure-resource-manager/management-groups-overview.md) av prenumerationer. Den här kontrollen kan utföras på många olika sätt, till exempel förhindrar resurser som skapas på fel plats, tillämpa gemensam och enhetlig taggen användning, eller granskning befintliga resurser för lämpliga konfigurationer och inställningar. I samtliga fall genereras data av en princip så att du kan förstå kompatibilitetsstatusen för din miljö.

Det finns flera sätt att komma åt kompatibilitetsinformationen som genereras av din princip och initiativtilldelningar:

- Med hjälp av den [Azure-portalen](#portal)
- Via [kommandoraden](#command-line) skript

Innan du tittar på metoder för att rapportera om efterlevnad, låt oss titta på när kompatibilitetsinformation uppdateras och frekvens och händelser som utlöser en utvärderingscykel för datorprincip.

> [!WARNING]
> Om kompatibilitetsstatusen rapporteras som **”ej tillämpligt”**, kontrollerar du att den **Microsoft.PolicyInsights** Resource Provider är registrerad och att användaren har den lämpliga rollbaserad åtkomstkontrollen (RBAC) behörighet enligt [här](azure-policy-introduction.md#rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Utvärderingen utlösare

Resultatet av en slutförd utvärderingscykel för datorprincip avspeglas i den `Microsoft.PolicyInsights` Resource Provider via `PolicyStates` och `PolicyEvents` åtgärder. Mer information om alternativen och funktionerna för princip för Insights REST API finns i [Policy Insights](/rest/api/policy-insights/).

Utvärderingar av tilldelade principer och initiativ inträffa till följd av olika händelser:

- En princip eller ett initiativ har nyligen tilldelats ett omfång. När detta inträffar kan tar det cirka 30 minuter innan tilldelningen börjar tillämpas på den definierade omfattningen. När den används omvärderingscykeln på börjar för resurser inom detta omfång mot den nyligen tilldelade princip eller ett initiativ och, beroende på de effekter som används av principen eller initiativ, resurser markeras som kompatibla eller inkompatibla. En stor princip eller ett initiativ utvärderas mot en stor omfattning av resurser kan ta tid, så det är fördefinierade förväntar när utvärderingen migreringscykel slutförs. När testet är klart är uppdaterade kompatibilitetsresultat tillgängliga i portalen och SDK: er.
- En princip eller ett initiativ som redan har tilldelats till ett scope har uppdaterats. Utvärderingscykel för datorprincip och val av tidpunkt för det här scenariot är desamma som för en ny tilldelning till ett omfång.
- En resurs har distribuerats till ett omfång med en tilldelning via Resource Manager, REST, Azure CLI eller Azure PowerShell. I det här scenariot händelsen effekt (lägga till, granska, neka, distribuera) och kompatibel statusinformation för enskilda resursen blir tillgänglig i portalen och SDK: er ungefär 15 minuter senare. Den här händelsen orsakar inte en utvärdering av andra resurser.
- Utvärderingscykel för standard efterlevnad. En gång per dygn är tilldelningar automatiskt ny utvärdering. En stor princip eller ett initiativ utvärderas mot en stor omfattning av resurser kan ta tid, så det är fördefinierade förväntar när utvärderingen migreringscykel slutförs. När testet är klart är uppdaterade kompatibilitetsresultat tillgängliga i portalen och SDK: er.

## <a name="how-compliance-works"></a>Så här fungerar efterlevnad

I en tilldelning är en resurs icke-kompatibel om den inte följa principen eller initiativ regler. I följande tabell visar hur olika effekter som fungerar med villkorsutvärderingen för resultatet av kompatibilitetstillståndet-princip:

| Resurstillstånd | Effekt | Principutvärdering | Kompatibilitetstillstånd |
| --- | --- | --- | --- |
| Finns | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | Sant | Inkompatibel |
| Finns | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | Falskt | Kompatibel |
| Ny | Audit, AuditIfNotExist\* | Sant | Inkompatibel |
| Ny | Audit, AuditIfNotExist\* | Falskt | Kompatibel |

\* För åtgärderna Append, DeployIfNotExist och AuditIfNotExist måste IF-instruktionen är TRUE.
Åtgärderna kräver också att villkoret Finns är FALSE för att vara icke-kompatibla. När det är TRUE utlöser IF-villkoret utvärdering av villkoret Finns för de relaterade resurserna.

Anta att du har en resursgrupp – ContsoRG, med vissa lagringskonton (markerat i rött) som exponeras för offentliga nätverk.

![Storage-konton som är exponerade för offentliga nätverk](media/policy-insights/resource-group01.png)

I det här exemplet behöver du vara försiktig med säkerhetsrisker. Nu när du har skapat en principtilldelning utvärderas för alla lagringskonton i resursgruppen ContosoRG. Den granskar de tre icke-kompatibla storage-konton kan därmed ändra deras tillstånd att **icke-kompatibla.**

![Granskas icke-kompatibla storage-konton](media/policy-insights/resource-group03.png)

## <a name="portal"></a>Portal

Azure-portalen visar en grafisk upplevelse av att visualisera och förstå tillståndet för efterlevnad i din miljö. På den **princip** kan den **översikt** alternativ innehåller information om tillgängliga scope på kompatibiliteten för både principer och initiativ. Förutom kompatibilitetsstatus och antal per tilldelning innehåller den ett diagram som visar efterlevnad under de senaste sju dagarna. Den **efterlevnad** sidan innehåller en stor del av samma information (utom diagram), men ger ytterligare filtrera och sortera alternativ.

![Sidan för principen för efterlevnad](media/policy-compliance/compliance-page.png)

När en princip eller ett initiativ kan tilldelas till olika omfång, Observera att omfattningen för varje uppgift och vilken sorts definition som har tilldelats detta omfång i tabellen. Det finns också antalet icke-kompatibla principer och icke-kompatibla resurser för varje tilldelning. När du klickar på en princip eller ett initiativ i tabellen innehåller en närmare titt på kompatibilitet för den specifika tilldelningen.

![Information om efterlevnad](media/policy-compliance/compliance-details.png)

När listan över resurser på den **icke-kompatibla resurser** fliken återspeglar utvärderingsstatus för befintliga resurser för den aktuella tilldelningen för händelser (lägga till, granska, neka, distribuera) utlöses av begäran om att skapa en resurs är visas under den **händelser** fliken.

![Efterlevnad Principhändelser](media/policy-compliance/compliance-events.png)

Högerklicka på raden i den händelse som du vill samla in mer information om och välj **visa aktivitetsloggar**. Aktivitetsloggsidan öppnas och är redan filtrerat till search som visar information för tilldelningen och händelser. Aktivitetsloggen innehåller ytterligare kontext och information om dessa händelser.

![Princip för efterlevnad aktivitetsloggen](media/policy-compliance/compliance-activitylog.png)

## <a name="command-line"></a>Kommandorad

Samma information som är tillgänglig i portalen kan hämtas med hjälp av REST API direkt (inklusive med [ARMClient](https://github.com/projectkudu/ARMClient)) eller Azure PowerShell med REST API. Fullständig information om REST API finns i den [Policy Insights](/rest/api/policy-insights/) referens. Referenssidor för REST API har en grön ”prova”-knapp på varje åtgärd som gör att du kan prova direkt i webbläsaren.

Om du vill använda i följande exempel i Azure PowerShell skapar du en autentiseringstoken med den här koden för exemplet. Ersätt sedan $restUri med önskad strängen i exemplen för att hämta en JSON-objekt som sedan kan parsas.

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

Med hjälp av REST-API, kan sammanfattning utföras av hanteringsgruppen, prenumeration, resursgrupp, resurs, initiativ, princip, tilldelning av prenumeration eller resource group tilldelning. Här är ett exempel på sammanfattning på prenumerationsnivå med principen Insight [sammanfatta för prenumeration](/rest/api/policy-insights/policystates/summarizeforsubscription):

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

Utdata innehåller en sammanfattning av prenumerationen. I de exempel på utdata nedan sammanfattande kompatibiliteten är **value.results.nonCompliantResources** och **value.results.nonCompliantPolicies**. Den här förfrågan innehåller ytterligare information, inklusive varje tilldelning som består av icke-kompatibla siffrorna och definitionsinformation för varje tilldelning. Varje princip för objekt i hierarkin innehåller en **queryResultsUri** som kan användas för att hämta ytterligare information på den nivån.

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

### <a name="query-for-resources"></a>Fråga för resurser

I exemplet ovan, **value.policyAssignments.policyDefinitions.results.queryResultsUri** gett oss ett exempel på URI: N för att hämta alla icke-kompatibla resurser för en specifik principdefinition. Titta på den **$filter** värdet, IsCompliant är lika med (eq) till false, PolicyAssignmentId har angetts för principdefinitionen, varefter PolicyDefinitionId själva.
Orsaken för att inkludera PolicyAssignmentId i filtret är PolicyDefinitionId kan finnas i flera principen eller initiativtilldelningar med en rad olika omfång. Genom att ange både PolicyAssignmentId och PolicyDefinitionId kan vara vi explicit i resultaten som vi letar efter. Tidigare var vi använde **senaste** för PolicyStates (den enda tillåtna värdet för **policyStatesSummaryResource** på operatorn sammanfatta för prenumeration), som anger automatiskt en  **från** och **till** tidsfönstret för de senaste 24 timmarna.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Svaret exemplet nedan har varit tas bort bara visar en enskild icke-kompatibel resurs kortfattat (Observera att @odata.count är faktiskt 15 och matchar antalet icke-kompatibla resurser i exemplet ovan). Detaljerad svaret innehåller flera olika typer av data om resursen, principen (eller initiativ), och tilldelningen. Observera att du kan också se vilka tilldelning parametrar skickades till principdefinitionen.

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

När en resurs skapas eller uppdateras, genereras en utvärderingsresultat av principen. Resultaten kallas _Principhändelser_. Använd följande URI: N om du vill visa den senaste Principhändelser som är associerade med prenumerationen.

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

Mer information om hur du frågar Principhändelser finns i den [Principhändelser](/rest/api/policy-insights/policyevents) referensartikeln.

### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell-modulen för principen är tillgänglig på PowerShell-galleriet som [AzureRM.PolicyInsights](https://www.powershellgallery.com/packages/AzureRM.PolicyInsights). Med PowerShellGet kan du installera modulen med hjälp av `Install-Module -Name AzureRM.PolicyInsights` (Kontrollera att du har senast [Azure PowerShell](/powershell/azure/install-azurerm-ps) installerat):

```powershell
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name AzureRM.PolicyInsights

# Import the downloaded module
Import-Module AzureRM.PolicyInsights

# Login with Connect-AzureRmAccount if not using Cloud Shell
Connect-AzureRmAccount
```

Modulen har tre cmdletar:

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

Exempel: Hämta posten tillståndet för de nyligen utvärderat resurs (standard är efter tidsstämpel i fallande ordning).

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

Exempel: Hämtning av information om alla inkompatibla virtuella nätverksresurser.

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

Exempel: Hämta händelser relaterade till icke-kompatibla virtuella nätverksresurser som inträffade efter ett visst datum.

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

Den **PrincipalOid** fältet kan användas för att få en viss användare med Azure PowerShell-cmdlet `Get-AzureRmADUser`. Ersätt **{principalOid}** med de svar du får från föregående exempel.

```powershell
PS > (Get-AzureRmADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="log-analytics"></a>Log Analytics

Om du har en [Log Analytics](../log-analytics/log-analytics-overview.md) arbetsyta med den `AzureActivity` lösning som är kopplad till din prenumeration och du kan också visa inkompatibilitet resultaten från utvärderingscykel med hjälp av enkla Kusto-frågor och `AzureActivity` tabell. Med inkompatibilitet information i Log Analytics innebär det också att aviseringar kan konfigureras för att titta på för icke-kompatibilitet på en specifik resurs, resursgrupp eller även ett tröskelvärde för icke-kompatibla artiklar, t.ex fler än 10 under de senaste 24 timmarna.

![Principefterlevnad använda Log Analytics](media/policy-compliance/compliance-loganalytics.png)

## <a name="next-steps"></a>Nästa steg

- Granska [Policy-definitionsstrukturen](policy-definition.md).
- Granska [Förstå policy-effekter](policy-effects.md).
- Se över vad en hanteringsgrupp är med sidan om att [organisera dina resurser med Azure-hanteringsgrupper](../azure-resource-manager/management-groups-overview.md)
