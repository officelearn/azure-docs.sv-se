---
title: Hantera roller i en Azure Machine Learning-arbetsyta
titleSuffix: Azure Machine Learning service
description: Lär dig mer om att få åtkomst till en arbetsyta för Azure Machine Learning-tjänsten som använder rollbaserad åtkomstkontroll (RBAC).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: Blackmist
ms.date: 2/20/2019
ms.custom: seodec18
ms.openlocfilehash: b40edf705ba61713f4b695dd55a6a20028936c82
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993824"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Hantera åtkomst till en Azure Machine Learning-arbetsyta

I den här artikeln får du lära dig hur du hanterar åtkomst till en Azure Machine Learning-arbetsyta. [Rollbaserad åtkomstkontroll (RBAC)](/azure/role-based-access-control/overview) används för att hantera åtkomst till Azure-resurser. Användare i Azure Active Directory tilldelas för specifika roller som beviljar åtkomst till resurser. Azure tillhandahåller både inbyggda roller och möjligheten att skapa anpassade roller.

## <a name="default-roles"></a>Standardroller

En Azure Machine Learning-arbetsyta är en Azure-resurs. När en ny Azure Machine Learning-arbetsytan har skapats som andra Azure-resurser kommer med tre standardroller. Du kan lägga till användare till arbetsytan och tilldela dem till någon av dessa inbyggda roller.

| Roll | Åtkomstnivå |
| --- | --- |
| **Läsare** | Skrivskyddade åtgärder på arbetsytan. Läsare kan listan och visa tillgångar i en arbetsyta, men det går inte att skapa eller uppdatera dessa tillgångar. |
| **Deltagare** | Visa, skapa, redigera eller ta bort (om tillämpligt) tillgångar i en arbetsyta. Till exempel kan deltagare skapa ett experiment, skapa eller koppla ett beräkningskluster, skicka en körning och distribuera en webbtjänst. |
| **Ägare** | Fullständig åtkomst till arbetsytan, inklusive möjligheten att visa, skapa, redigera eller ta bort (om tillämpligt) tillgångar i en arbetsyta. Du kan dessutom ändra rolltilldelningar. |

> [!IMPORTANT]
> Rollåtkomst kan begränsas till flera nivåer i Azure. Till exempel kanske någon med ägaråtkomst till en arbetsgrupp inte ägaråtkomst till resursgruppen som innehåller arbetsgruppen. Mer information finns i [hur RBAC fungerar](/azure/role-based-access-control/overview#how-rbac-works).

Mer information om specifika inbyggda roller finns i [inbyggda roller för Azure](/azure/role-based-access-control/built-in-roles).

## <a name="manage-workspace-access"></a>Hantera åtkomst till arbetsytan

Om du är ägare till en arbetsyta kan du lägga till och ta bort roller för arbetsytan. Du kan också tilldela roller till användare. Använd följande länkar för att se hur du hanterar åtkomst:
- [Azure-portalens användargränssnitt](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [REST-API](/azure/role-based-access-control/role-assignments-rest)
- [Azure Resource Manager-mallar](/azure/role-based-access-control/role-assignments-template)

Om du har installerat den [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md), du kan också använda en CLI-kommando för att tilldela roller till användare.

```azurecli-interactive 
az ml workspace share -n <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

Den `user` fältet är den e-postadressen för en befintlig användare instans av Azure Active Directory var den överordnade prenumerationen för arbetsytan finns. Här är ett exempel på hur du använder det här kommandot:

```azurecli-interactive 
az ml workspace share -n my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Skapa anpassad roll

Om det finns inte tillräckligt med inbyggda roller, kan du skapa anpassade roller. Anpassade roller kan ha läsa, skriva, ta bort och compute resursbehörighet på arbetsytan. Du kan göra rollen på en viss arbetsyta-nivå, en specifik resursgruppsnivå eller en specifik prenumerationsnivå.

> [!NOTE]
> Du måste vara ägare av resursen på den nivån för att skapa anpassade roller i den här resursen.

Om du vill skapa en anpassad roll, skapa först en roll definition JSON-fil som anger behörighet och omfång för rollen. I följande exempel definierar en anpassad roll med namnet ”dataexpert” omfång på en viss arbetsyta-nivå:

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

Du kan ändra den `AssignableScopes` fält för att ange omfånget för den här anpassade rollen på prenumerationsnivån, resursgruppsnivå eller en viss arbetsyta.

Den här anpassade rollen kan göra allt i arbetsytan förutom följande åtgärder:

- Det går inte att skapa eller uppdatera en beräkningsresurs.
- Det går inte att ta bort en beräkningsresurs.
- Det går inte att lägga till, ta bort eller ändra rolltilldelningar.
- Det går inte att ta bort arbetsytan.

Om du vill distribuera den här anpassade rollen, använder du följande Azure CLI-kommando:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Efter distributionen kan blir den här rollen tillgänglig i den angivna arbetsytan. Du kan nu lägga till och tilldela den här rollen i Azure-portalen. Eller så kan du tilldela den här rollen till en användare med hjälp av den `az ml workspace share` CLI-kommando:

```azurecli-interactive
az ml workspace share -n my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```


Mer information finns i [anpassade roller för Azure-resurser](/azure/role-based-access-control/custom-roles).

## <a name="next-steps"></a>Nästa steg

- [Översikt över Enterprise-säkerhet](concept-enterprise-security.md)
- [Kör säkert experiment och inferensjobb i ett virtuellt nätverk](how-to-enable-virtual-network.md)
- [Självstudier: Skapa modeller](tutorial-train-models-with-aml.md)
