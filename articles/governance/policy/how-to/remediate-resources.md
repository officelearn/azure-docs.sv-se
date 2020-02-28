---
title: Åtgärda icke-kompatibla resurser
description: Den här guiden vägleder dig genom reparationen av resurser som inte är kompatibla med principer i Azure Policy.
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 5cf26f5235fbc35cdc9bfc8527967c3cc5ca91b8
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670805"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Åtgärda icke-kompatibla resurser med Azure Policy

Resurser som inte är kompatibla med en **deployIfNotExists** eller en **ändrings** princip kan försättas i ett kompatibelt tillstånd genom **reparation**. Reparationen utförs genom att instruera Azure Policy att köra **deployIfNotExists** -eller tag- **åtgärderna** för den tilldelade principen på dina befintliga resurser, om tilldelningen är till en hanterings grupp, en prenumeration, en resurs grupp eller en enskild resurs. Den här artikeln visar de steg som krävs för att förstå och utföra reparation med Azure Policy.

## <a name="how-remediation-security-works"></a>Hur fungerar säkerheten för reparation

När Azure Policy kör mallen i **deployIfNotExists** -princip definitionen används en [hanterad identitet](../../../active-directory/managed-identities-azure-resources/overview.md).
Azure Policy skapar en hanterad identitet för varje tilldelning, men du måste ha information om vilka roller som ska bevilja den hanterade identiteten. Om den hanterade identitet saknas roller, visas det här felet under tilldelningen av principen eller ett initiativ. När du använder portalen beviljas Azure Policy automatiskt den hanterade identitet som listade roller när tilldelningen startas.

![Hanterad identitet - saknas roll](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> Om en resurs som har ändrats av **deployIfNotExists** eller **ändra** ligger utanför omfånget för princip tilldelningen eller om mallen har åtkomst till egenskaper för resurser utanför omfånget för princip tilldelningen, måste tilldelningens hanterade identitet [manuellt beviljas åtkomst](#manually-configure-the-managed-identity) eller så Miss fungerar reparations distributionen.

## <a name="configure-policy-definition"></a>Konfigurera principdefinition

Det första steget är att definiera de roller som **deployIfNotExists** och **ändra** behöver i princip definitionen för att kunna distribuera innehållet i den inkluderade mallen. Lägg till en **roleDefinitionIds** -egenskap under egenskapen **information** . Den här egenskapen är en matris med strängar som matchar roller i din miljö. Ett fullständigt exempel finns i DeployIfNotExists- [exemplet](../concepts/effects.md#deployifnotexists-example) eller i [ändra exempel](../concepts/effects.md#modify-examples).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

Egenskapen **roleDefinitionIds** använder det fullständiga resurs-ID: n och tar inte med den korta **roleName** för rollen. Använd följande kod för att hämta ID för rollen ”Bidragsgivar” i din miljö:

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>Konfigurera manuellt hanterad identitet

När du skapar en tilldelning med hjälp av portalen genererar Azure Policy båda den hanterade identiteten och ger den roller som definierats i **roleDefinitionIds**. Steg för att skapa den hanterade identitet och tilldela den behörigheter måste göras manuellt under följande förhållanden:

- När du använder SDK: N (till exempel Azure PowerShell)
- När en resurs utanför tilldelningsomfånget ändras av mallen
- När en resurs utanför tilldelningsomfånget läses av mallen

> [!NOTE]
> Azure PowerShell och .NET är de enda SDK: er som för närvarande stöd för den här funktionen.

### <a name="create-managed-identity-with-powershell"></a>Skapa hanterad identitet med PowerShell

Om du vill skapa en hanterad identitet under tilldelningen av principen måste **plats** definieras och **AssignIdentity** användas. I följande exempel hämtas definitionen av den inbyggda principen **distribuera transparent data kryptering i SQL DB**, ställer in mål resurs gruppen och skapar sedan tilldelningen.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

Variabeln `$assignment` innehåller nu ägar-ID: t för den hanterade identiteten tillsammans med de standardvärden som returneras när du skapar en princip tilldelning. Den kan nås via `$assignment.Identity.PrincipalId`.

### <a name="grant-defined-roles-with-powershell"></a>Bevilja definierat roller med PowerShell

Den nya hantera identiteten måste slutföra replikering via Azure Active Directory innan den kan beviljas de nödvändiga rollerna. När replikeringen är klar itererar följande exempel princip definitionen i `$policyDef` för **roleDefinitionIds** och använder [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) för att ge rollerna den nya hanterade identiteten.

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

Det finns två sätt att ge en tilldelnings hanterade identitet de definierade rollerna med hjälp av portalen, med hjälp av **åtkomst kontroll (IAM)** eller genom att redigera principen eller initiativ tilldelningen och klicka på **Spara**.

Följ dessa steg om du vill lägga till en roll i tilldelningens hanterad identitet:

1. Starta Azure Policy-tjänsten i Azure Portal genom att klicka på **Alla tjänster** och sedan söka efter och välja **Princip**.

1. Välj **Tilldelningar** till vänster på sidan Azure Policy.

1. Leta upp den tilldelningen som har en hanterad identitet och klicka på namnet.

1. Hitta egenskapen **tilldelnings-ID** på sidan Redigera. Tilldelnings-ID blir något som liknar:

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   Namnet på den hanterade identiteten är den sista delen av tilldelnings resurs-ID: t, som är `2802056bfc094dfb95d4d7a5` i det här exemplet. Kopiera den här delen av tilldelning av resurs-ID.

1. Gå till resursen eller resurser överordnade behållaren (resursgrupp, prenumeration, hanteringsgrupp) som behöver rolldefinitionen la till manuellt.

1. Klicka på länken **åtkomst kontroll (IAM)** på sidan resurser och klicka på **+ Lägg till roll tilldelning** överst på sidan åtkomst kontroll.

1. Välj lämplig roll som matchar en **roleDefinitionIds** från princip definitionen.
   Lämna **behörigheten tilldela** till standardvärdet för Azure AD-användare, grupp eller program. I rutan **Välj** klistrar du in eller skriver den del av tilldelningens resurs-ID som finns tidigare. När sökningen är klar klickar du på objektet med samma namn för att välja ID och klickar på **Spara**.

## <a name="create-a-remediation-task"></a>Skapa en uppgift för reparation

### <a name="create-a-remediation-task-through-portal"></a>Skapa en reparations uppgift via portalen

Under utvärderingen bestämmer princip tilldelningen med **deployIfNotExists** eller **ändra** effekter om det finns icke-kompatibla resurser. När icke-kompatibla resurser hittas finns informationen på **reparations** sidan. Tillsammans med listan över principer som har icke-kompatibla resurser är alternativet att utlösa en **reparations aktivitet**. Det här alternativet är vad som skapar en distribution från **deployIfNotExists** -mallen eller **ändra** -åtgärder.

Följ dessa steg om du vill skapa en **reparations uppgift**:

1. Starta Azure Policy-tjänsten i Azure Portal genom att klicka på **Alla tjänster** och sedan söka efter och välja **Princip**.

   ![Sök efter princip i alla tjänster](../media/remediate-resources/search-policy.png)

1. Välj **reparation** till vänster på sidan Azure policy.

   ![Välj reparation på princip Sidan](../media/remediate-resources/select-remediation.png)

1. Alla **deployIfNotExists** och **ändra** princip tilldelningar med icke-kompatibla resurser ingår i **principerna för att åtgärda** fliken och data tabellen. Klicka på en princip med resurser som är icke-kompatibla. Sidan **ny reparations aktivitet** öppnas.

   > [!NOTE]
   > Ett annat sätt att öppna sidan **reparations uppgift** är att söka efter och klicka på principen på sidan **efterlevnad** . Klicka sedan på knappen **skapa reparations aktivitet** .

1. På sidan **ny reparations aktivitet** filtrerar du de resurser som ska åtgärdas genom att använda **omfångs** punkter för att välja underordnade resurser från där principen tilldelas (inklusive enskilda resurs objekt). Använd dessutom List rutan **platser** för att ytterligare filtrera resurserna. Endast de resurser som anges i tabellen ska åtgärdas.

   ![Åtgärda – Välj vilka resurser som ska åtgärdas](../media/remediate-resources/select-resources.png)

1. Påbörja reparations åtgärden när resurserna har filtrerats genom att klicka på **åtgärda**. Sidan policy efterlevnad öppnas på fliken **reparations aktiviteter** för att visa status för aktiviteternas förlopp. Distributioner som skapats av reparations aktiviteten börjar direkt.

   ![Åtgärds förlopp för reparations åtgärder](../media/remediate-resources/task-progress.png)

1. Klicka på **reparations uppgiften** på sidan efterlevnadsprincip om du vill ha mer information om förloppet. Filtrering som används för aktiviteten visas tillsammans med en lista över de resurser som åtgärdas.

1. På sidan **reparations aktivitet** högerklickar du på en resurs för att Visa reparations aktivitetens distribution eller resursen. Klicka på **relaterade händelser** i slutet av raden för att se information, till exempel ett fel meddelande.

   ![Åtgärda - resurs snabbmenyn för uppgift](../media/remediate-resources/resource-task-context-menu.png)

Resurser som distribueras via en **reparations uppgift** läggs till på fliken **distribuerade resurser** på sidan efterlevnad av principer.

### <a name="create-a-remediation-task-through-azure-cli"></a>Skapa en reparations uppgift via Azure CLI

Använd `az policy remediation`-kommandon för att skapa en **reparations uppgift** med Azure CLI. Ersätt `{subscriptionId}` med ditt prenumerations-ID och `{myAssignmentId}` med ditt **deployIfNotExists** eller **ändra** princip tilldelnings-ID.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Andra reparations kommandon och exempel finns i kommandona för att [Reparera AZ-principer](/cli/azure/policy/remediation) .

### <a name="create-a-remediation-task-through-azure-powershell"></a>Skapa en reparations uppgift via Azure PowerShell

Om du vill skapa en **reparations aktivitet** med Azure PowerShell använder du `Start-AzPolicyRemediation`-kommandon. Ersätt `{subscriptionId}` med ditt prenumerations-ID och `{myAssignmentId}` med ditt **deployIfNotExists** eller **ändra** princip tilldelnings-ID.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Andra reparations-cmdletar och exempel finns i modulen [AZ. PolicyInsights](/powershell/module/az.policyinsights/#policy_insights) .

## <a name="next-steps"></a>Nästa steg

- Granska exempel i [Azure policy exempel](../samples/index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).
- Lär dig att [program mässigt skapa principer](programmatically-create.md).
- Lär dig hur du [hämtar efterlevnadsprinciper](get-compliance-data.md).
- Granska en hanterings grupp med [organisera dina resurser med Azures hanterings grupper](../../management-groups/overview.md).
