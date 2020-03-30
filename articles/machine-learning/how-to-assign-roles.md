---
title: Hantera roller på arbetsytan
titleSuffix: Azure Machine Learning
description: Lär dig hur du kommer åt en Azure Machine Learning-arbetsyta med hjälp av rollbaserad åtkomstkontroll (RBAC).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 03/06/2020
ms.custom: seodec18
ms.openlocfilehash: 127a0a2b7f7573db91df9347169e90de3e14c4c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270100"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Hantera åtkomst till en Azure Machine Learning-arbetsyta
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du hanterar åtkomst till en Azure Machine Learning-arbetsyta. [Rollbaserad åtkomstkontroll (RBAC)](/azure/role-based-access-control/overview) används för att hantera åtkomst till Azure-resurser. Användare i din Azure Active Directory tilldelas specifika roller som ger åtkomst till resurser. Azure ger både inbyggda roller och möjligheten att skapa anpassade roller.

## <a name="default-roles"></a>Standardroller

En Azure Machine Learning-arbetsyta är en Azure-resurs. Precis som andra Azure-resurser, när en ny Azure Machine Learning-arbetsyta skapas, levereras den med tre standardroller. Du kan lägga till användare på arbetsytan och tilldela dem till en av dessa inbyggda roller.

| Roll | Åtkomstnivå |
| --- | --- |
| **Läsare** | Skrivskyddade åtgärder på arbetsytan. Läsare kan lista och visa tillgångar på en arbetsyta, men de kan inte skapa eller uppdatera dessa resurser. |
| **Deltagare** | Visa, skapa, redigera eller ta bort (i förekommande fall) tillgångar på en arbetsyta. Deltagare kan till exempel skapa ett experiment, skapa eller bifoga ett beräkningskluster, skicka en körning och distribuera en webbtjänst. |
| **Ägare** | Fullständig åtkomst till arbetsytan, inklusive möjligheten att visa, skapa, redigera eller ta bort (i förekommande fall) tillgångar på en arbetsyta. Dessutom kan du ändra rolltilldelningar. |

> [!IMPORTANT]
> Rollåtkomst kan begränsas till flera nivåer i Azure. Till exempel kan det hända att någon med ägaråtkomst till en arbetsyta inte har ägarbehörighet till resursgruppen som innehåller arbetsytan. Mer information finns i [Så här fungerar RBAC](/azure/role-based-access-control/overview#how-rbac-works).

Mer information om specifika inbyggda roller finns [i Inbyggda roller för Azure](/azure/role-based-access-control/built-in-roles).

## <a name="manage-workspace-access"></a>Hantera åtkomst till arbetsyta

Om du äger en arbetsyta kan du lägga till och ta bort roller för arbetsytan. Du kan också tilldela roller till användare. Använd följande länkar för att identifiera hur du hanterar åtkomst:
- [Azure portal användargränssnitt](/azure/role-based-access-control/role-assignments-portal)
- [Powershell](/azure/role-based-access-control/role-assignments-powershell)
- [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [REST API](/azure/role-based-access-control/role-assignments-rest)
- [Azure Resource Manager-mallar](/azure/role-based-access-control/role-assignments-template)

Om du har installerat [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md)kan du också använda ett CLI-kommando för att tilldela roller till användare.

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

Fältet `user` är e-postadressen till en befintlig användare i instansen av Azure Active Directory där den överordnade prenumerationen på arbetsytan finns. Här är ett exempel på hur du använder det här kommandot:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Skapa anpassad roll

Om de inbyggda rollerna är otillräckliga kan du skapa anpassade roller. Anpassade roller kan ha läs-, skriv-, borttagnings- och beräkningsresursbehörigheter på arbetsytan. Du kan göra rollen tillgänglig på en viss arbetsytasnivå, en viss resursgruppsnivå eller en viss prenumerationsnivå.

> [!NOTE]
> Du måste vara ägare till resursen på den nivån för att kunna skapa anpassade roller inom resursen.

Om du vill skapa en anpassad roll konstruerar du först en JSON-rolldefinition som anger behörighet och omfång för rollen. I följande exempel definieras en anpassad roll med namnet "Data Scientist" på en viss arbetsytasnivå:

`data_scientist_role.json` :
```json
{
    "Name": "Data Scientist",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

Du kan `AssignableScopes` ändra fältet för att ange omfattningen för den här anpassade rollen på prenumerationsnivå, resursgruppsnivå eller en viss arbetsytasnivå.

Den här anpassade rollen kan göra allt på arbetsytan förutom följande åtgärder:

- Det kan inte skapa eller uppdatera en beräkningsresurs.
- Det går inte att ta bort en beräkningsresurs.
- Det går inte att lägga till, ta bort eller ändra rolltilldelningar.
- Det går inte att ta bort arbetsytan.

Om du vill distribuera den här anpassade rollen använder du följande Azure CLI-kommando:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Efter distributionen blir den här rollen tillgänglig på den angivna arbetsytan. Nu kan du lägga till och tilldela den här rollen i Azure-portalen. Du kan också tilldela den här rollen `az ml workspace share` till en användare med kommandot CLI:

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

Mer information om anpassade roller finns i [Anpassade roller för Azure-resurser](/azure/role-based-access-control/custom-roles).

Mer information om de åtgärder (åtgärder) som kan hanteras med anpassade roller finns i [Resursprovideråtgärder](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices).


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar


### <a name="q-what-are-the-permissions-needed-to-perform-various-actions-in-the-azure-machine-learning-service"></a>F. Vilka behörigheter behövs för att utföra olika åtgärder i Azure Machine Learning-tjänsten?

Följande tabell är en sammanfattning av Azure Machine Learning-aktiviteter och de behörigheter som krävs för att utföra dem minst omfång. Som ett exempel om en aktivitet kan utföras med ett arbetsyteomfattning (kolumn 4), fungerar även allt högre omfång med den behörigheten automatiskt. Alla banor i den här `Microsoft.MachineLearningServices/`tabellen är **relativa banor** till .

| Aktivitet | Omfattning på prenumerationsnivå | Omfång på resursgruppsnivå | Omfattning på arbetsytasnivå |
|---|---|---|---|
| Skapa ny arbetsyta | Krävs inte | Ägare eller bidragsgivare | Saknas (blir ägare eller ärver rollen med högre omfattning när den har skapats) |
| Skapa nytt beräkningskluster | Krävs inte | Krävs inte | Ägare, deltagare eller anpassad roll som tillåter:`workspaces/computes/write` |
| Skapa ny virtuell dator för bärbara datorer | Krävs inte | Ägare eller bidragsgivare | Inte möjligt |
| Skapa ny beräkningsinstans | Krävs inte | Krävs inte | Ägare, deltagare eller anpassad roll som tillåter:`workspaces/computes/write` |
| Dataplanaktivitet som att skicka körning, komma åt data, distribuera modell eller publicera pipeline | Krävs inte | Krävs inte | Ägare, deltagare eller anpassad roll som tillåter:`workspaces/*/write` <br/> Observera att du också behöver ett datalager som är registrerat på arbetsytan så att MSI kan komma åt data i ditt lagringskonto. |


### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>F. Hur listar jag alla anpassade roller i min prenumeration?

I Azure CLI kör du följande kommando.

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>F. Hur hittar jag rolldefinitionen för en roll i min prenumeration?

I Azure CLI kör du följande kommando. Observera `<role-name>` att bör vara i samma format som returneras av kommandot ovan.

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>F. Hur uppdaterar jag en rolldefinition?

I Azure CLI kör du följande kommando.

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

Observera att du måste ha behörigheter för hela omfattningen av den nya rolldefinitionen. Om den nya rollen till exempel har ett scope mellan tre prenumerationer måste du ha behörighet för alla tre prenumerationer. 

> [!NOTE]
> Rolluppdateringar kan ta 15 minuter till en timme att tillämpa över alla rolltilldelningar i det omfånget.
### <a name="q-can-i-define-a-role-that-prevents-updating-the-workspace-edition"></a>F. Kan jag definiera en roll som förhindrar att uppdatera workspace Edition? 

Ja, du kan definiera en roll som förhindrar att uppdatera workspace Edition. Eftersom uppdateringen av arbetsytan är ett PATCH-anrop på arbetsytan fungerar `"NotActions"` det genom att placera följande åtgärd i matrisen i JSON-definitionen: 

`"Microsoft.MachineLearningServices/workspaces/write"`

### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>F. Vilka behörigheter krävs för att utföra kvotåtgärder på en arbetsyta? 

Du behöver behörigheter på prenumerationsnivå för att utföra alla kvotrelaterade åtgärder på arbetsytan. Det innebär att det bara kan hända att du anger kvoter på prenumerationsnivå eller arbetsyta för dina hanterade beräkningsresurser om du har skrivbehörighet i prenumerationsomfånget. 


## <a name="next-steps"></a>Nästa steg

- [Översikt över Enterprise Security](concept-enterprise-security.md)
- [Kör experiment och slutsatser/poäng på ett säkert sätt i ett virtuellt nätverk](how-to-enable-virtual-network.md)
- [Handledning: Träna modeller](tutorial-train-models-with-aml.md)
- [Åtgärder för resursprovider](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)
