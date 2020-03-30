---
title: Åtgärda icke-kompatibla resurser
description: Den här guiden går igenom reparation av resurser som inte är kompatibla med principer i Azure Policy.
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 71af5c81e0dce4d5c0a0461534f634db36bd66a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471395"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Åtgärda icke-kompatibla resurser med Azure Policy

Resurser som inte är kompatibla med en **distributionOmnotNotExister** eller **ändra** princip kan placeras i ett kompatibelt tillstånd via **Reparation**. Reparationen utförs genom att instruera Azure-principen att köra **effekten deployIfNotExists** eller **taggåtgärderna** för den tilldelade principen på dina befintliga resurser, oavsett om tilldelningen är till en hanteringsgrupp, en prenumeration, en resursgrupp eller en enskild resurs. Den här artikeln visar de steg som behövs för att förstå och utföra reparation med Azure Policy.

## <a name="how-remediation-security-works"></a>Så här fungerar reparationssäkerhet

När Azure Policy kör mallen i **principdefinitionen distribueraOmnotExister,** gör den det med hjälp av en [hanterad identitet](../../../active-directory/managed-identities-azure-resources/overview.md).
Azure Policy skapar en hanterad identitet för varje tilldelning, men måste ha information om vilka roller som ska beviljas den hanterade identiteten. Om den hanterade identiteten saknar roller visas det här felet under tilldelningen av principen eller ett initiativ. När du använder portalen beviljar Azure Policy automatiskt den hanterade identiteten de listade rollerna när tilldelningen startar. Platsen _för_ den hanterade identiteten påverkar inte dess åtgärd med Azure Policy.

![Hanterad identitet - saknad roll](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> Om en resurs som ändrats av **deployIfNotExists** eller **ändra** inte omfattas av principtilldelningen eller mallen har åtkomst till egenskaper för resurser utanför principtilldelningens omfattning, måste tilldelningens hanterade identitet [beviljas manuellt åtkomst](#manually-configure-the-managed-identity) eller reparationsdistributionen misslyckas.

## <a name="configure-policy-definition"></a>Konfigurera principdefinition

Det första steget är att definiera de roller som **distribuerarOmNotExister och** **ändra** behov i principdefinitionen för att distribuera innehållet i den medföljande mallen. Lägg till en **rollDefinitionIds-egenskap under** egenskapen **details.** Den här egenskapen är en matris med strängar som matchar roller i din miljö. Ett fullständigt exempel finns i [exemplet distribueraOmNotExister](../concepts/effects.md#deployifnotexists-example) eller [exemplen ändra](../concepts/effects.md#modify-examples).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

Egenskapen **roleDefinitionIds** använder den fullständiga resursidentifieraren och tar inte rollens korta **rollNamn.** Om du vill hämta ID:t för rollen Deltagare i din miljö använder du följande kod:

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>Konfigurera den hanterade identiteten manuellt

När du skapar en tilldelning med hjälp av portalen genererar Azure Policy både den hanterade identiteten och ger den de roller som definierats i **roleDefinitionIds**. I följande villkor måste steg för att skapa den hanterade identiteten och tilldela den behörigheter göras manuellt:

- När du använder SDK (till exempel Azure PowerShell)
- När en resurs utanför tilldelningsomfånget ändras av mallen
- När en resurs utanför tilldelningsomfånget läss av mallen

> [!NOTE]
> Azure PowerShell och .NET är de enda SDK:er som för närvarande stöder den här funktionen.

### <a name="create-managed-identity-with-powershell"></a>Skapa hanterad identitet med PowerShell

Om du vill skapa en hanterad identitet under tilldelningen av principen måste **lagerställe** definieras och **AssignIdentity** användas. I följande exempel får definitionen av den inbyggda principen **Distribuera SQL DB transparent datakryptering,** anger målgruppen och skapar sedan tilldelningen.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

Variabeln `$assignment` innehåller nu huvud-ID för den hanterade identiteten tillsammans med standardvärdena som returneras när en principtilldelning skapas. Den kan nås `$assignment.Identity.PrincipalId`via .

### <a name="grant-defined-roles-with-powershell"></a>Bevilja definierade roller med PowerShell

Den nya hanterade identiteten måste slutföra replikeringen via Azure Active Directory innan den kan beviljas de nödvändiga rollerna. När replikeringen är klar itererar `$policyDef` följande exempel principdefinitionen för **rollDefinitionIds** och använder [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) för att ge den nya hanterade identiteten rollerna.

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

### <a name="grant-defined-roles-through-portal"></a>Bevilja definierade roller via portalen

Det finns två sätt att ge en tilldelnings hanterade identitet de definierade rollerna med hjälp av portalen, genom att använda **Åtkomstkontroll (IAM)** eller genom att redigera princip- eller initiativtilldelningen och klicka på **Spara**.

Så här lägger du till en roll i tilldelningens hanterade identitet:

1. Starta Azure Policy-tjänsten i Azure Portal genom att klicka på **Alla tjänster** och sedan söka efter och välja **Princip**.

1. Välj **Tilldelningar** till vänster på sidan Azure Policy.

1. Leta reda på tilldelningen som har en hanterad identitet och klicka på namnet.

1. Leta reda på egenskapen **Tilldelnings-ID** på redigeringssidan. Tilldelnings-ID:et kommer att vara något i stil med:

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   Namnet på den hanterade identiteten är den sista delen `2802056bfc094dfb95d4d7a5` av tilldelningsresurs-ID: t, vilket finns i det här exemplet. Kopiera den här delen av tilldelningsresurs-ID: et.

1. Navigera till resursen eller den överordnade resursbehållaren (resursgrupp, prenumeration, hanteringsgrupp) som behöver rolldefinitionen manuellt tillagd.

1. Klicka på länken **IAM (Access control)** på resurssidan och klicka på **+ Lägg till rolltilldelning** högst upp på åtkomstkontrollsidan.

1. Välj lämplig roll som matchar en **rollDefinitionIds** från principdefinitionen.
   Lämna **tilldela åtkomst för att** ange standardvärdet för Azure AD-användare, grupp eller program. Klistra in eller skriv den del av tilldelningsresurs-ID:t som fanns tidigare i rutan **Markera.** När sökningen är klar klickar du på objektet med samma namn för att välja ID och klicka på **Spara**.

## <a name="create-a-remediation-task"></a>Skapa en reparationsuppgift

### <a name="create-a-remediation-task-through-portal"></a>Skapa en reparationsuppgift via portalen

Under utvärderingen avgör principtilldelningen med **deployIfNotExists** eller **ändra** effekter om det finns icke-kompatibla resurser. När resurser som inte är kompatibla hittas finns informationen på sidan **Reparation.** Tillsammans med listan över principer som har icke-kompatibla resurser är alternativet att utlösa en **reparationsaktivitet**. Det här alternativet är vad som skapar en distribution från mallen **deployIfNotExists** eller **ändringsåtgärderna.**

Så här skapar du en **reparationsuppgift:**

1. Starta Azure Policy-tjänsten i Azure Portal genom att klicka på **Alla tjänster** och sedan söka efter och välja **Princip**.

   ![Sök efter policy i alla tjänster](../media/remediate-resources/search-policy.png)

1. Välj **Reparation** till vänster på sidan Azure-princip.

   ![Välj Reparation på sidan Princip](../media/remediate-resources/select-remediation.png)

1. Alla **deployIfNotExists** och **ändra** principtilldelningar med icke-kompatibla resurser finns på **principerna för att åtgärda** flik- och datatabellen. Klicka på en princip med resurser som inte är kompatibla. Uppgiftssidan **För ny reparation** öppnas.

   > [!NOTE]
   > Ett alternativt sätt att öppna **åtgärdsaktivitetssidan** är att söka efter och klicka på principen från sidan **Efterlevnad** och sedan klicka på knappen **Skapa reparationsaktivitet.**

1. På sidan **Ny reparationsaktivitet** filtrerar du de resurser som ska åtgärdas med hjälp av scope-ellipserna för att välja underordnade resurser från vilka principen tilldelas (inklusive ner till de enskilda resursobjekten). **Scope** Dessutom kan du använda listrutan **Platser** för att ytterligare filtrera resurserna. Endast resurser som anges i tabellen kommer att åtgärdas.

   ![Åtgärda - välj vilka resurser som ska åtgärdas](../media/remediate-resources/select-resources.png)

1. Påbörja reparationsaktiviteten när resurserna har filtrerats genom att klicka på **Åtgärda**. Sidan principefterlevnad öppnas på fliken **Reparationsuppgifter** för att visa status för aktiviteterna. Distributioner som skapats av reparationsuppgiften börjar direkt.

   ![Åtgärda - framsteg för saneringsuppgifter](../media/remediate-resources/task-progress.png)

1. Klicka på **reparationsuppgiften** från sidan för regelefterlevnad för att få information om förloppet. Filtreringen som används för aktiviteten visas tillsammans med en lista över de resurser som åtgärdas.

1. Från **åtgärdsaktivitetssidan** högerklickar du på en resurs för att visa antingen reparationsaktivitetens distribution eller resursen. I slutet av raden klickar du på **Relaterade händelser** för att se information som ett felmeddelande.

   ![Åtgärda - snabbmeny för resursaktivitet](../media/remediate-resources/resource-task-context-menu.png)

Resurser som distribueras via en **reparationsaktivitet** läggs till på fliken **Distribuerade resurser** på sidan principefterlevnad.

### <a name="create-a-remediation-task-through-azure-cli"></a>Skapa en reparationsuppgift via Azure CLI

Om du vill skapa en **reparationsuppgift** med Azure CLI använder du kommandona. `az policy remediation` Ersätt `{subscriptionId}` med ditt prenumerations-ID och `{myAssignmentId}` med din **deployIfNotExists** eller **ändra** principtilldelnings-ID.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Andra reparationskommandon och exempel finns i kommandona [för az-princips reparation.](/cli/azure/policy/remediation)

### <a name="create-a-remediation-task-through-azure-powershell"></a>Skapa en reparationsuppgift via Azure PowerShell

Om du vill skapa en **reparationsuppgift** med Azure PowerShell använder du kommandona. `Start-AzPolicyRemediation` Ersätt `{subscriptionId}` med ditt prenumerations-ID och `{myAssignmentId}` med din **deployIfNotExists** eller **ändra** principtilldelnings-ID.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

För andra reparations-cmdlets och exempel finns i modulen [Az.PolicyInsights.](/powershell/module/az.policyinsights/#policy_insights)

## <a name="next-steps"></a>Nästa steg

- Granska exempel på [Azure Policy-exempel](../samples/index.md).
- Granska [Azure Policy-definitionsstrukturen](../concepts/definition-structure.md).
- Granska [Förstå policy-effekter](../concepts/effects.md).
- Förstå hur du [programmässigt skapar principer](programmatically-create.md).
- Läs om hur du [hämtar efterlevnadsdata](get-compliance-data.md).
- Granska vad en hanteringsgrupp är med [Organisera dina resurser med Azure-hanteringsgrupper](../../management-groups/overview.md).
