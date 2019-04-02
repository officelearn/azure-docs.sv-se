---
title: Åtgärda icke-kompatibla resurser
description: Den här anvisningen vägleder dig genom reparation av resurser som är icke-kompatibla principer i Azure Policy.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: e86553e789fc9879ea4a6326b08d893fe24c8ac8
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58802566"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Åtgärda icke-kompatibla resurser med Azure Policy

Resurser som är icke-kompatibla till en **deployIfNotExists** princip kan placeras i ett kompatibelt tillstånd via **reparation**. Reparation åstadkoms genom att uppmana körs den **deployIfNotExists** tilldelade policyns effekt på dina befintliga resurser. Den här artikeln visar de steg som krävs för att förstå och utföra åtgärder med principen.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="how-remediation-security-works"></a>Hur fungerar säkerheten för reparation

När principen körs mallen i den **deployIfNotExists** principdefinition som den gör det med hjälp av en [hanterad identitet](../../../active-directory/managed-identities-azure-resources/overview.md).
Principen skapas en hanterad identitet för varje uppgift, men måste ha information om vilka roller att bevilja den hanterade identitet. Om den hanterade identitet saknas roller, visas det här felet under tilldelningen av principen eller ett initiativ. När du använder portalen beviljas principen automatiskt hanterad identitet listade rollerna när tilldelningen har startats.

![Hanterad identitet - saknas roll](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> Om en resurs ändras av **deployIfNotExists** ligger utanför omfånget av tilldelning av principer eller mallen kommer åt egenskaper för resurser utanför omfånget för principtilldelningen, tilldelningens hanterad identitet måste vara [manuellt beviljas åtkomst](#manually-configure-the-managed-identity) eller reparation distributionen kommer att misslyckas.

## <a name="configure-policy-definition"></a>Konfigurera principdefinition

Det första steget är att definiera rollerna som **deployIfNotExists** behöver distribuera innehållet i den inkluderade mallen i principdefinitionen. Under den **information** egenskapen, lägga till en **roleDefinitionIds** egenskapen. Den här egenskapen är en matris med strängar som matchar roller i din miljö. En fullständig exempel finns i den [deployIfNotExists exempel](../concepts/effects.md#deployifnotexists-example).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscription/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

**roleDefinitionIds** använder fullständiga resurs-ID och tar inte kortsiktiga **roleName** i rollen. Använd följande kod för att hämta ID för rollen ”Bidragsgivar” i din miljö:

```azurecli-interactive
az role definition list --name 'Contributor'
```

```azurepowershell-interactive
Get-AzRoleDefinition -Name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>Konfigurera manuellt hanterad identitet

När du skapar en uppgift med hjälp av portalen princip både genererar den hanterade identitet och beviljar de roller som definierats i **roleDefinitionIds**. Steg för att skapa den hanterade identitet och tilldela den behörigheter måste göras manuellt under följande förhållanden:

- När du använder SDK: N (till exempel Azure PowerShell)
- När en resurs utanför tilldelningsomfånget ändras av mallen
- När en resurs utanför tilldelningsomfånget läses av mallen

> [!NOTE]
> Azure PowerShell och .NET är de enda SDK: er som för närvarande stöd för den här funktionen.

### <a name="create-managed-identity-with-powershell"></a>Skapa hanterad identitet med PowerShell

Skapa en hanterad identitet vid tilldelningen av principen, **plats** måste definieras och **AssignIdentity** används. I följande exempel hämtas definitionen av den inbyggda principen **distribuera SQL DB transparent datakryptering**anger målresursgruppen och skapar sedan tilldelningen.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

Den `$assignment` variabeln innehåller nu ägar-ID för den hanterade identitet tillsammans med de standardvärden som returneras när du skapar en principtilldelning. Den kan nås via `$assignment.Identity.PrincipalId`.

### <a name="grant-defined-roles-with-powershell"></a>Bevilja definierat roller med PowerShell

Den nya hantera identiteten måste slutföra replikering via Azure Active Directory innan den kan beviljas de nödvändiga rollerna. När replikeringen är klar, i följande exempel itererar principdefinitionen i `$policyDef` för den **roleDefinitionIds** och använder [New AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) att ge den nya hantera identiteten den roller.

```azurepowershell-interactive
# Use the $policyDef to get to the roleDefinitionIds array
$roleDefinitionIds = $policyDef.Properties.policyRule.then.details.roleDefinitionIds

if ($roleDefinitionIds.Count -gt 0)
{
    $roleDefinitionIds | ForEach-Object {
        $roleDefId = $_.Split("/") | Select-Object -Last 1
        New-AzRoleAssignment -Scope $resourceGroup.ResourceId -ObjectId $assignment.Identity.PrincipalId -RoleDefinitionId $roleDefId
    }
}
```

### <a name="grant-defined-roles-through-portal"></a>Bevilja definierat roller via portalen

Det finns två sätt att ge en tilldelning hanterad identitet definierade roller med hjälp av portalen med hjälp av **åtkomstkontroll (IAM)** eller genom att redigera tilldelningen princip eller ett initiativ och klicka på **spara**.

Följ dessa steg om du vill lägga till en roll i tilldelningens hanterad identitet:

1. Starta Azure Policy-tjänsten i Azure Portal genom att klicka på **Alla tjänster** och sedan söka efter och välja **Princip**.

1. Välj **Tilldelningar** till vänster på sidan Azure Policy.

1. Leta upp den tilldelningen som har en hanterad identitet och klicka på namnet.

1. Hitta den **tilldelnings-ID** egenskap på sidan Redigera. Tilldelnings-ID blir något som liknar:

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   Namnet på den hanterade identitet är den sista delen av tilldelning av resurs-ID, vilket är `2802056bfc094dfb95d4d7a5` i det här exemplet. Kopiera den här delen av tilldelning av resurs-ID.

1. Gå till resursen eller resurser överordnade behållaren (resursgrupp, prenumeration, hanteringsgrupp) som behöver rolldefinitionen la till manuellt.

1. Klicka på den **åtkomstkontroll (IAM)** länka på resurssidan och klicka på **+ Lägg till rolltilldelning** överst på sidan för kontroll av åtkomst.

1. Välj rätt roll som matchar en **roleDefinitionIds** från principdefinitionen. Lämna **tilldela åtkomst till** inställt på standardvärdet ”Azure AD användaren, gruppen eller programmet”. I den **Välj** rutan, klistra in eller Skriv delen av tilldelning av resurs-ID finns tidigare. När sökningen är klar klickar du på objektet med samma namn och välj ID och klicka på **spara**.

## <a name="create-a-remediation-task"></a>Skapa en uppgift för reparation

### <a name="create-a-remediation-task-through-portal"></a>Skapa en reparation uppgift via portalen

Under utvärderingen, principtilldelning med **deployIfNotExists** effekt avgör om det finns inkompatibla resurser. När icke-kompatibla resurser finns informationen tillhandahålls på den **reparation** sidan. Tillsammans med i listan över principer som har icke-kompatibla resurser är alternativet för att utlösa en **reparation uppgift**. Det här alternativet är det skapar en distribution från den **deployIfNotExists** mall.

Skapa en **reparation uppgiften**, Följ dessa steg:

1. Starta Azure Policy-tjänsten i Azure Portal genom att klicka på **Alla tjänster** och sedan söka efter och välja **Princip**.

   ![Sök efter princip i alla tjänster](../media/remediate-resources/search-policy.png)

1. Välj **reparation** till vänster på sidan för Azure Policy.

   ![Välj reparation på sidan för principer](../media/remediate-resources/select-remediation.png)

1. Alla **deployIfNotExists** principtilldelningar med icke-kompatibla resurser ingår i den **principer för att åtgärda** fliken och en datatabell. Klicka på en princip med resurser som är icke-kompatibla. Den **ny reparation uppgift** öppnas.

   > [!NOTE]
   > Ett annat sätt att öppna den **reparation uppgift** är att hitta och klicka på principen från den **efterlevnad** sidan och klicka sedan på den **skapa reparation uppgift** knappen.

1. På den **ny reparation uppgift** sidan, filtrera resurser för att åtgärda problemet med hjälp av den **omfång** ellipserna att välja underordnade resurser från där principen tilldelas (inklusive till enskild resurs objekt). Dessutom kan använda den **platser** listrutan filtreras ytterligare resurser. Endast de resurser som anges i tabellen ska åtgärdas.

   ![Åtgärda – Välj vilka resurser behöver uppdateras](../media/remediate-resources/select-resources.png)

1. Starta reparation aktiviteten när resurserna som har filtrerats genom att klicka på **reparera**. Öppnar sidan principer för efterlevnad för den **reparation uppgifter** flik för att visa tillståndet för uppgifter förloppet.

   ![Åtgärda - status för reparation aktiviteter](../media/remediate-resources/task-progress.png)

1. Klicka på den **reparation uppgift** från sidan princip för efterlevnad för att få information om förloppet. Filtrering som används för aktiviteten visas tillsammans med en lista över de resurser som åtgärdas.

1. Från den **reparation uppgift** högerklickar du på en resurs för att visa antingen reparation aktivitetens distribution eller resursen. I slutet av raden klickar du på **relaterade händelser** du vill ha information, till exempel ett felmeddelande.

   ![Åtgärda - resurs snabbmenyn för uppgift](../media/remediate-resources/resource-task-context-menu.png)

Resurser som distribueras via en **reparation uppgift** läggs till i **distribuerade resurser** fliken på policysidan för efterlevnad.

### <a name="create-a-remediation-task-through-azure-cli"></a>Skapa en reparation uppgift via Azure CLI

Skapa en **reparation uppgift** med Azure CLI, använder de `az policy remediation` kommandon. Ersätt `{subscriptionId}` med ditt prenumerations-ID och `{myAssignmentId}` med din **deployIfNotExists** princip tilldelning-ID.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Andra åtgärder kommandon och exempel finns i den [az princip reparation](/cli/azure/policy/remediation) kommandon.

### <a name="create-a-remediation-task-through-azure-powershell"></a>Skapa en reparation uppgift via Azure PowerShell

Skapa en **reparation uppgift** med Azure PowerShell kan använda den `Start-AzPolicyRemediation` kommandon. Ersätt `{subscriptionId}` med ditt prenumerations-ID och `{myAssignmentId}` med din **deployIfNotExists** princip tilldelning-ID.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Andra åtgärder cmdletar och exempel finns i den [Az.PolicyInsights](/powershell/module/az.policyinsights/#policy_insights) modulen.

## <a name="next-steps"></a>Nästa steg

- Se exempel på [Azure Policy-exempel](../samples/index.md)
- Granska den [Policy-definitionsstruktur](../concepts/definition-structure.md)
- Granska [förstå effekterna av princip](../concepts/effects.md)
- Förstå hur du [skapa principer programmässigt](programmatically-create.md)
- Lär dig hur du [hämta data för kompatibilitetsinställningar](getting-compliance-data.md)
- Se över vad en hanteringsgrupp är med sidan om att [organisera dina resurser med Azure-hanteringsgrupper](../../management-groups/overview.md)