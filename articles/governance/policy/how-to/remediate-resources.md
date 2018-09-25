---
title: Åtgärda icke-kompatibla resurser med Azure Policy
description: Den här anvisningen vägleder dig genom reparation av resurser som är icke-kompatibla principer i Azure Policy.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 747650bc47644cdca07f705f42d063c995ebe9bf
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46980261"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Åtgärda icke-kompatibla resurser med Azure Policy

Resurser som är icke-kompatibla till en **deployIfNotExists** princip kan placeras i ett kompatibelt tillstånd via **reparation**. Reparation åstadkoms genom att uppmana körs den **deployIfNotExists** tilldelade policyns effekt på dina befintliga resurser. Den här anvisningen beskriver de steg som krävs för att göra detta.

## <a name="how-remediation-security-works"></a>Hur fungerar säkerheten för reparation

När principen körs mallen i den **deployIfNotExists** principdefinition som den gör det med hjälp av en [hanterad identitet](../../../active-directory/managed-identities-azure-resources/overview.md).
Principen skapar en hanterad identitet för varje uppgift för dig, men du måste tillhandahålla information om vilka roller att bevilja den hanterade identitet. Om den hanterade identitet saknas roller, visas detta under tilldelningen av principen eller ett initiativ som innehåller principen. När du använder portalen beviljas principen automatiskt hanterad identitet listade rollerna när tilldelningen har initierats.

![Hanterad identitet - saknas roll](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> Om en resurs ändras av **deployIfNotExists** är utanför omfattningen av principtilldelning tilldelningens hanterad identitet måste vara programmässigt beviljas åtkomst eller reparation distributionen kommer att misslyckas.

## <a name="configure-policy-definition"></a>Konfigurera principdefinition

Det första steget är att definiera rollerna som **deployIfNotExists** behöver distribuera innehållet i den inkluderade mallen i principdefinitionen. Under den **information** egenskapen, lägga till en **roleDefinitionIds** egenskapen. Det här är en matris med strängar som matchar roller i din miljö.
En fullständig exempel finns i den [deployIfNotExists exempel](../concepts/effects.md#deployifnotexists-example).

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
Get-AzureRmRoleDefinition -Name 'Contributor'
```

## <a name="create-a-remediation-task"></a>Skapa en uppgift för reparation

Under utvärderingen, principtilldelning med **deployIfNotExists** effekt avgör om det finns inkompatibla resurser. När icke-kompatibla resurser finns informationen tillhandahålls på den **reparation** sidan. Tillsammans med i listan över principer som har icke-kompatibla resurser är alternativet för att utlösa en **reparation uppgift**. Det här är vad skapar en distribution från den **deployIfNotExists** mall.

Skapa en **reparation uppgiften**, Följ dessa steg:

1. Starta Azure Policy-tjänsten i Azure Portal genom att klicka på **Alla tjänster** och sedan söka efter och välja **Princip**.

   ![Sök efter princip](../media/remediate-resources/search-policy.png)

1. Välj **reparation** till vänster på sidan för Azure Policy.

   ![Välj reparation](../media/remediate-resources/select-remediation.png)

1. Alla **deployIfNotExists** principtilldelningar med icke-kompatibla resurser ingår i den **principer för att åtgärda** fliken och en datatabell. Klicka på en princip med resurser som är icke-kompatibla. Den **ny reparation uppgift** öppnas.

   > [!NOTE]
   > Ett annat sätt att öppna den **reparation uppgift** är att hitta och klicka på principen från den **efterlevnad** sidan och klicka sedan på den **skapa reparation uppgift** knappen.

1. På den **ny reparation uppgift** sidan, filtrera resurser för att åtgärda problemet med hjälp av den **omfång** ellipserna att välja underordnade resurser från där principen har tilldelats (inklusive till enskild resurs objekt). Dessutom kan använda den **platser** listrutan filtreras ytterligare resurser. Endast de resurser som anges i tabellen ska åtgärdas.

   ![Åtgärda – Välj resurser](../media/remediate-resources/select-resources.png)

1. Starta reparation aktiviteten när resurserna som har filtrerats genom att klicka på **reparera**. Öppnar sidan principer för efterlevnad för den **reparation uppgifter** flik för att visa tillståndet för uppgifter förloppet.

   ![Åtgärda – uppgiften pågår](../media/remediate-resources/task-progress.png)

1. Klicka på den **reparation uppgift** från sidan princip för efterlevnad för att få information om förloppet. Filtrering som används för aktiviteten visas tillsammans med en lista över de resurser som åtgärdas.

1. Från den **remedation uppgift** högerklickar du på en resurs för att visa antingen reparation aktivitetens distribution eller resursen. I slutet av raden klickar du på **relaterade händelser** du vill ha information, till exempel ett felmeddelande.

   ![Åtgärda - resurs snabbmenyn för uppgift](../media/remediate-resources/resource-task-context-menu.png)

Resurser som distribueras via en **reparation uppgift** kommer att läggas till i **distribuerade resurser** fliken på policysidan för efterlevnad efter en kort fördröjning.

## <a name="next-steps"></a>Nästa steg

- Se exempel på [Azure Policy-exempel](../samples/index.md)
- Granska den [Policy-definitionsstruktur](../concepts/definition-structure.md)
- Granska [förstå effekterna av princip](../concepts/effects.md)
- Förstå hur du [skapa principer programmässigt](programmatically-create.md)
- Lär dig hur du [hämta data för kompatibilitetsinställningar](getting-compliance-data.md)
- Se över vad en hanteringsgrupp är med sidan om att [organisera dina resurser med Azure-hanteringsgrupper](../../management-groups/overview.md)