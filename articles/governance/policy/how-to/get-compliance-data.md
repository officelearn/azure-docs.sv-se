---
title: Hämta policyefterlevnadsdata
description: Utvärderingar och effekter i Azure-princip avgör efterlevnaden. Läs om hur du får efterlevnadsinformation för dina Azure-resurser.
ms.date: 02/01/2019
ms.topic: how-to
ms.openlocfilehash: 891c9c72d8e83dc8f9adb930e8ebd11b70f6aad8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280643"
---
# <a name="get-compliance-data-of-azure-resources"></a>Hämta efterlevnadsdata för Azure-resurser

En av de största fördelarna med Azure Policy är den insikt och kontroller som den tillhandahåller över resurser i en prenumeration eller [hanteringsgrupp](../../management-groups/overview.md) av prenumerationer. Den här kontrollen kan utövas på många olika sätt, till exempel förhindra att resurser skapas på fel plats, genomdriva gemensam och konsekvent tagganvändning eller granska befintliga resurser för lämpliga konfigurationer och inställningar. I samtliga fall genereras data av Azure Policy så att du kan förstå efterlevnadstillståndet för din miljö.

Det finns flera sätt att komma åt den efterlevnadsinformation som genereras av dina policy- och initiativtilldelningar:

- Använda [Azure-portalen](#portal)
- Genom [kommandoradsskript](#command-line)

Innan du tittar på metoderna för att rapportera om efterlevnad, låt oss titta på när efterlevnadsinformation uppdateras och frekvensen och händelser som utlöser en utvärderingscykel.

> [!WARNING]
> Om efterlevnadstillståndet rapporteras som **Inte registrerat**kontrollerar du att **Microsoft.PolicyInsights** Resource Provider är registrerad och att användaren har lämpliga rollbaserade åtkomstkontroll (RBAC) behörigheter som beskrivs i [RBAC i Azure Policy](../overview.md#rbac-permissions-in-azure-policy).

## <a name="evaluation-triggers"></a>Utvärdering utlöser

Resultaten av en slutförd utvärderingscykel är tillgängliga i `Microsoft.PolicyInsights` resursprovidern och `PolicyStates` `PolicyEvents` åtgärder. Mer information om hur du använder REST-API:et för Azure Policy Insights finns i [Azure Policy Insights](/rest/api/policy-insights/).

Utvärderingar av tilldelade strategier och initiativ sker till följd av olika händelser:

- En princip eller ett initiativ har nyligen tilldelats ett scope. Det tar cirka 30 minuter innan tilldelningen tillämpas på det definierade omfånget. När utvärderingscykeln har tillämpats börjar den för resurser inom det omfånget mot den nyligen tilldelade principen eller initiativet och beroende på vilka effekter som används av principen eller initiativet markeras resurser som kompatibla eller icke-kompatibla. En stor politik eller ett stort initiativ som utvärderas mot en stor mängd resurser kan ta tid. Som sådan, Det finns ingen fördefinierad förväntan om när utvärderingscykeln kommer att slutföras. När den är klar är uppdaterade efterlevnadsresultat tillgängliga i portalen och SDK:er.

- En princip eller ett initiativ som redan har tilldelats ett scope uppdateras. Utvärderingscykeln och tidpunkten för det här scenariot är samma som för en ny tilldelning till ett scope.

- En resurs distribueras till ett scope med en tilldelning via Resource Manager, REST, Azure CLI eller Azure PowerShell. I det här fallet blir effekthändelsen (tillägg, granskning, neka, distribuera) och kompatibel statusinformation för den enskilda resursen tillgänglig i portalen och SDK:er cirka 15 minuter senare. Den här händelsen orsakar inte en utvärdering av andra resurser.

- Utvärderingscykel för standardefterlevnad. En gång var 24:e timme omvärderas tilldelningarna automatiskt. En stor politik eller ett stort initiativ med många resurser kan ta tid, så det finns ingen förutbestämd förväntan om när utvärderingscykeln kommer att slutföras. När den är klar är uppdaterade efterlevnadsresultat tillgängliga i portalen och SDK:er.

- Resursleverantören [gästkonfiguration](../concepts/guest-configuration.md) uppdateras med efterlevnadsinformation av en hanterad resurs.

- Genomsökning på begäran

### <a name="on-demand-evaluation-scan"></a>Utvärderingsgenomsökning på begäran

En utvärderingssökning för en prenumeration eller en resursgrupp kan startas med ett anrop till REST API. Den här genomsökningen är en asynkron process. Därför väntar inte REST-slutpunkten för att starta genomsökningen tills genomsökningen är klar för att svara. I stället ger den en URI för att fråga status för den begärda utvärderingen.

I varje REST API-URI finns det variabler som används och som du måste ersätta med egna värden:

- `{YourRG}`- Ersätt med namnet på din resursgrupp
- `{subscriptionId}` – Ersätt med ditt prenumerations-ID

Genomsökningen stöder utvärdering av resurser i en prenumeration eller i en resursgrupp. Starta en genomsökning efter scope med kommandot REST API **POST** med följande URI-strukturer:

- Prenumeration

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

- Resursgrupp

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

Anropet returnerar en **status för 202 Accepterad.** I svarshuvudet ingår egenskapen **Plats** med följande format:

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2018-07-01-preview
```

`{ResourceContainerGUID}`genereras statiskt för det begärda scopet. Om ett scope redan kör en genomsökning på begäran startas ingen ny genomsökning. I stället tillhandahålls den nya `{ResourceContainerGUID}` begäran samma **plats-URI** för status. Ett REST API **GET-kommando** till plats-URI:n returnerar ett **Location** **202-godkänt** medan utvärderingen pågår. När utvärderingsgenomsökningen är klar returnerar den statusen **200 OK.** Brödtexten i en slutförd genomsökning är ett JSON-svar med status:

```json
{
    "status": "Succeeded"
}
```

## <a name="how-compliance-works"></a>Så här fungerar efterlevnad

I en tilldelning är en resurs **icke-kompatibel** om den inte följer princip- eller initiativregler.
I följande tabell visas hur olika principeffekter fungerar med villkorsutvärderingen för det resulterande efterlevnadstillståndet:

| Resurstillstånd | Verkan | Utvärdering av politiken | Kompatibilitetstillstånd |
| --- | --- | --- | --- |
| Finns | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | Icke-kompatibel |
| Finns | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | Kompatibel |
| Ny | Audit, AuditIfNotExist\* | True | Icke-kompatibel |
| Ny | Audit, AuditIfNotExist\* | False | Kompatibel |

\* För åtgärderna Append, DeployIfNotExist och AuditIfNotExist måste IF-instruktionen är TRUE.
Åtgärderna kräver också att villkoret Finns är FALSE för att vara icke-kompatibla. När det är TRUE utlöser IF-villkoret utvärdering av villkoret Finns för de relaterade resurserna.

Anta till exempel att du har en resursgrupp – ContsoRG, med vissa lagringskonton (markerade i rött) som exponeras för offentliga nätverk.

![Lagringskonton som exponeras för offentliga nätverk](../media/getting-compliance-data/resource-group01.png)

I det här exemplet måste du vara försiktig med säkerhetsrisker. Nu när du har skapat en principtilldelning utvärderas den för alla lagringskonton i resursgruppen ContosoRG. Den granskar de tre lagringskontona som inte uppfyller kraven och ändrar därför sina tillstånd till **icke-överensstämmande.**

![Granskade lagringskonton som inte uppfyller kraven](../media/getting-compliance-data/resource-group03.png)

Förutom **kompatibel** och **icke-kompatibel**har principer och resurser tre andra tillstånd:

- **Motstridiga**: Det finns två eller flera principer med motstridiga regler. Till exempel två principer som rapporterar om samma tagg med olika värden.
- **Startar inte**: Utvärderingscykeln har inte startats för principen eller resursen.
- **Inte registrerad**: Azure Policy Resource Provider har inte registrerats eller kontot som är inloggad har inte behörighet att läsa efterlevnadsdata.

Azure Policy använder **typ-** och **namnfälten** i definitionen för att avgöra om en resurs är en matchning. När resursen matchar anses den vara tillämplig och har statusen **Kompatibla** eller **icke-kompatibla**. Om antingen **typ** eller **namn** är den enda egenskapen i definitionen, anses alla resurser vara tillämpliga och utvärderas.

Efterlevnadsprocenten bestäms genom att **kompatibla** resurser divideras med _totala resurser_.
_Summa resurser_ definieras som summan av resurserna **Kompatibel,** **Icke-kompatibel**och **Motstridiga** resurser. De övergripande efterlevnadsnumren är summan av distinkta resurser som är **kompatibla** dividerat med summan av alla distinkta resurser. I bilden nedan finns det 20 olika resurser som är tillämpliga och endast en är **icke-kompatibel**. Den totala resursefterlevnaden är 95 % (19 av 20).

![Exempel på policyefterlevnad från sidan Efterlevnad](../media/getting-compliance-data/simple-compliance.png)

## <a name="portal"></a>Portalen

Azure-portalen visar en grafisk upplevelse av att visualisera och förstå efterlevnadens tillstånd i din miljö. På sidan **Princip** **Overview** finns information om tillgängliga omfattningar om efterlevnaden av både principer och initiativ. Tillsammans med efterlevnadstillståndet och antalet per tilldelning innehåller det ett diagram som visar efterlevnad under de senaste sju dagarna. Sidan **Efterlevnad** innehåller mycket av samma information (förutom diagrammet), men innehåller ytterligare filtrerings- och sorteringsalternativ.

![Exempel på sidan Efterlevnad av Azure-princip](../media/getting-compliance-data/compliance-page.png)

Eftersom en princip eller ett initiativ kan tilldelas olika scope, innehåller tabellen omfånget för varje tilldelning och vilken typ av definition som har tilldelats. Antalet icke-kompatibla resurser och principer som inte är kompatibla för varje tilldelning tillhandahålls också. Om du klickar på en princip eller ett initiativ i tabellen ges en djupare titt på efterlevnaden för just den tilldelningen.

![Exempel på sidan Information om efterlevnad av Azure-princip](../media/getting-compliance-data/compliance-details.png)

Listan över resurser på fliken **Resursefterlevnad** visar utvärderingsstatusen för befintliga resurser för den aktuella tilldelningen. Fliken är som standard **icke-kompatibel,** men kan filtreras.
Händelser (tillägg, granskning, neka, distribuera) som utlöses av begäran om att skapa en resurs visas under fliken **Händelser.**

> [!NOTE]
> För en AKS-motorprincip är resursen som visas resursgruppen.

![Exempel på Azure Policy Compliance-händelser](../media/getting-compliance-data/compliance-events.png)

För [resursproviderlägesresurser](../concepts/definition-structure.md#resource-provider-modes) **öppnar** komponentefterlevnadsinformationen på fliken **Resursefterlevnad.** Den här sidan innehåller också flikar för att se de principer som har tilldelats den här resursen, händelser, komponenthändelser och ändringshistorik.

![Exempel på efterlevnadsinformation för Azure Policy Component](../media/getting-compliance-data/compliance-components.png)

Tillbaka på sidan resursefterlevnad högerklickar du på raden för den händelse som du vill samla in mer information om och väljer **Visa aktivitetsloggar**. Aktivitetsloggsidan öppnas och filtreras i förinslag till sökningen som visar information om tilldelningen och händelserna. Aktivitetsloggen innehåller ytterligare kontext och information om dessa händelser.

![Exempel på aktivitetslogg för Azure Policy Compliance](../media/getting-compliance-data/compliance-activitylog.png)

### <a name="understand-non-compliance"></a>Förstå bristande efterlevnad

<a name="change-history-preview"></a>

När en resurs bedöms vara **icke-kompatibel**finns det många möjliga orsaker. Information om orsaken till att en resurs inte är **kompatibel** eller hitta den ansvariga ändringen finns [i Fastställa bristande efterlevnad](./determine-non-compliance.md).

## <a name="command-line"></a>Kommandorad

Samma information som är tillgänglig i portalen kan hämtas med REST API (inklusive med [ARMClient),](https://github.com/projectkudu/ARMClient)Azure PowerShell och Azure CLI (förhandsversion).
Fullständig information om REST API finns i Azure [Policy Insights-referensen.](/rest/api/policy-insights/) REST API-referenssidorna har en grön "Prova"-knappen på varje åtgärd som gör att du kan prova rätt i webbläsaren.

Använd ARMClient eller ett liknande verktyg för att hantera autentisering till Azure för REST API-exemplen.

### <a name="summarize-results"></a>Sammanfatta resultat

Med REST API kan summering utföras av behållare, definition eller tilldelning. Här är ett exempel på summering på prenumerationsnivå med hjälp av Azure Policy Insight's [Summarize For Subscription:](/rest/api/policy-insights/policystates/summarizeforsubscription)

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

Utdata sammanfattar prenumerationen. I exempelutdatan nedan finns den sammanfattade överensstämmelsen under **value.results.nonCompliantResources** och **value.results.nonCompliantPolicies**. Den här begäran innehåller ytterligare information, inklusive varje tilldelning som utgjorde de icke-kompatibla numren och definitionsinformationen för varje tilldelning. Varje principobjekt i hierarkin innehåller en **frågaResultsUri** som kan användas för att få ytterligare information på den nivån.

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

### <a name="query-for-resources"></a>Fråga efter resurser

I exemplet ovan tillhandahåller **value.policyAssignments.policyDefinitions.results.queryResultsUri** ett exempel på Uri för alla icke-kompatibla resurser för en viss principdefinition. Om man tittar på **värdet $filter** är IsCompliant lika (eq) till false, PolicyAssignmentId anges för principdefinitionen och sedan själva PolicyDefinitionId. Anledningen till att inkludera PolicyAssignmentId i filtret är att PolicyDefinitionId kan finnas i flera princip- eller initiativtilldelningar med olika omfattningar. Genom att ange både PolicyAssignmentId och PolicyDefinitionId kan vi vara tydliga i de resultat vi letar efter. Tidigare, för PolicyStates vi använde **senaste**, som automatiskt ställer in en **från** och **till** tidsfönster för de senaste 24 timmarna.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

Exempelsvaret nedan har trimmats till en enda resurs som inte är kompatibel för korthet. Det detaljerade svaret innehåller flera data om resursen, principen eller initiativet och tilldelningen. Observera att du också kan se vilka tilldelningsparametrar som skickades till principdefinitionen.

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

När en resurs skapas eller uppdateras genereras ett principutvärderingsresultat. Resultat kallas _principhändelser_. Använd följande Uri för att visa de senaste principhändelser som är associerade med prenumerationen.

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

Mer information om hur du frågar efter principhändelser finns i referensartikeln för [Azure Policy Events.](/rest/api/policy-insights/policyevents)

### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell-modulen för Azure-principen är tillgänglig i PowerShell-galleriet som [Az.PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights).
Med PowerShellGet kan du installera `Install-Module -Name Az.PolicyInsights` modulen med (se till att du har den senaste [Azure PowerShell](/powershell/azure/install-az-ps) installerad):

```azurepowershell-interactive
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name Az.PolicyInsights

# Import the downloaded module
Import-Module Az.PolicyInsights

# Login with Connect-AzAccount if not using Cloud Shell
Connect-AzAccount
```

Modulen har följande cmdlets:

- `Get-AzPolicyStateSummary`
- `Get-AzPolicyState`
- `Get-AzPolicyEvent`
- `Get-AzPolicyRemediation`
- `Remove-AzPolicyRemediation`
- `Start-AzPolicyRemediation`
- `Stop-AzPolicyRemediation`

Exempel: Hämta tillståndssammanfattningen för den högst tilldelade principen med det högsta antalet icke-kompatibla resurser.

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

Exempel: Hämta tillståndsposten för den senast utvärderade resursen (standard är av tidsstämpel i fallande ordning).

```azurepowershell-interactive
PS> Get-AzPolicyState -Top 1

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

```azurepowershell-interactive
PS> Get-AzPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

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

```azurepowershell-interactive
PS> Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2018-05-19'

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

Fältet **PrincipalOid** kan användas för att hämta en viss användare `Get-AzADUser`med Azure PowerShell-cmdlet . Ersätt **{principalOid}** med det svar du får från föregående exempel.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Azure Monitor-loggar

Om du har en Log `AzureActivity` [Analytics-arbetsyta](../../../log-analytics/log-analytics-overview.md) med från lösningen Activity Log [Analytics](../../../azure-monitor/platform/activity-log-collect.md) som är kopplad till din prenumeration kan `AzureActivity` du även visa resultat som inte följer reglerna från utvärderingscykeln med enkla Kusto-frågor och tabellen. Med information i Azure Monitor-loggar kan aviseringar konfigureras för att titta på bristande efterlevnad.


![Azure Policy Compliance med Hjälp av Azure Monitor-loggar](../media/getting-compliance-data/compliance-loganalytics.png)

## <a name="next-steps"></a>Nästa steg

- Granska exempel på [Azure Policy-exempel](../samples/index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).
- Förstå hur du [programmässigt skapar principer](programmatically-create.md).
- Lär dig hur du [åtgärdar icke-kompatibla resurser](remediate-resources.md).
- Granska vad en hanteringsgrupp är med [Organisera dina resurser med Azure-hanteringsgrupper](../../management-groups/overview.md).