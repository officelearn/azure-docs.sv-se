---
title: Hantera användare och roller i en Azure Machine Learning-arbetsyta
titleSuffix: Azure Machine Learning service
description: Lär dig mer om att hantera användare och roller i en arbetsyta för Azure Machine Learning-tjänsten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: hning86
ms.author: haining
ms.date: 2/20/2019
ms.custom: seodec18
ms.openlocfilehash: 623aaff3cba86e8e523a86e4adcb0a359c339c45
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57336472"
---
# <a name="manage-users-and-roles-in-an-azure-machine-learning-workspace"></a>Hantera användare och roller i en Azure Machine Learning-arbetsyta

I den här artikeln får du lära dig hur du lägger till användare till en Azure Machine Learning-arbetsyta. Du också lära dig hur du tilldela användare till olika roller och skapa anpassade roller.

## <a name="built-in-roles"></a>Inbyggda roller
En Azure Machine Learning-arbetsyta är en Azure-resurs. När en ny Azure Machine Learning-arbetsytan har skapats som andra Azure-resurser kommer med tre standardroller. Du kan lägga till användare till arbetsytan och tilldela dem till någon av dessa inbyggda roller.

- **Läsare**
    
    Den här rollen kan skrivskyddade åtgärder på arbetsytan. Läsare kan listan och visa tillgångar i en arbetsyta, men det går inte att skapa eller uppdatera dessa tillgångar.

- **Deltagare**
    
    Den här rollen kan du visa, skapa, redigera eller ta bort (om tillämpligt) tillgångar i en arbetsyta. Till exempel kan deltagare skapa ett experiment, skapa eller koppla ett beräkningskluster, skicka en körning och distribuera en webbtjänst.

- **Ägare**
    
    Den här rollen ger användare fullständig åtkomst till arbetsytan, inklusive möjligheten att visa, skapa, redigera eller ta bort (om tillämpligt) tillgångar i en arbetsyta. Dessutom kan du lägga till eller ta bort användare och ändrar rolltilldelningar.

## <a name="add-or-remove-users"></a>Lägg till eller ta bort användare
Om du är ägare till en arbetsyta kan du lägga till och ta bort användare från arbetsytan genom att välja något av följande metoder:
- [Azure-portalens användargränssnitt](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [REST-API](/azure/role-based-access-control/role-assignments-rest)
- [Azure Resource Manager-mallar](/azure/role-based-access-control/role-assignments-template)

Om du har installerat den [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md), du kan också använda en CLI-kommando för att lägga till användare till arbetsytan.

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

Om du vill skapa en anpassad roll, skapa först en roll definition JSON-fil som anger behörighet och omfång som du vill använda för rollen. Här är till exempel en roll-definitionsfil för en anpassad roll med namnet ”dataexpert” omfång på en viss arbetsyta-nivå:

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

Efter distributionen kan blir den här rollen tillgänglig i den angivna arbetsytan. Du kan nu lägga till och tilldela den här rollen i Azure-portalen. Du kan också lägga till en användare med den här rollen med hjälp av den `az ml workspace share` CLI-kommando:

```azurecli-interactive
az ml workspace share -n my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```


Mer information om anpassade roller i Azure finns i [det här dokumentet](/azure/role-based-access-control/custom-roles).

## <a name="next-steps"></a>Nästa steg

Följ den kompletta självstudiekursen lär du dig hur du använder en arbetsyta för att skapa, träna och distribuera modeller med Azure Machine Learning-tjänsten.

> [!div class="nextstepaction"]
> [Självstudier: Skapa modeller](tutorial-train-models-with-aml.md)
