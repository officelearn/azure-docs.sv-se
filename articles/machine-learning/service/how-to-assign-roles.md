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
ms.openlocfilehash: f6c74da2e9189c5dddb88cb80f04422f49cfaee2
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2019
ms.locfileid: "56594261"
---
# <a name="manage-users-and-roles-in-an-azure-machine-learning-workspace"></a>Hantera användare och roller i en Azure Machine Learning-arbetsyta

I den här artikeln får du lära dig att lägga till användare till en Azure Machine Learning-arbetsyta och tilldela dem i olika roller. Du också lära dig hur du skapar anpassade roller.

## <a name="built-in-roles"></a>Inbyggda roller
Azure Machine Learning-arbetsyta är en Azure-resurs. Och som alla Azure-resurser när en ny Azure Machine Learning-arbetsytan har skapats medföljer tre standardroller. Du kan lägga till användare till arbetsytan och tilldela dem till någon av dessa inbyggda roller.

- **Läsare**
    
    Den här rollen kan skrivskyddade åtgärder på arbetsytan. Med den här rollen kan du visa en lista med och visa tillgångar i en arbetsyta, till exempel experiment, körningar, beräkning, modeller, webbtjänster, publiceras pipelines och så vidare. Men du kan inte skapa eller uppdatera dessa tillgångar.

- **Deltagare**
    
    Den här rollen kan du visa, skapa, redigera eller ta bort (om tillämpligt) tillgångar i en arbetsyta. Till exempel med den här rollen kan du skapa ett experiment, skapa eller koppla ett beräkningskluster och skicka en körning, registrera en modell och distribuera en webbtjänst.

- **Ägare**
    
    Den här rollen ger dig fullständig åtkomst till arbetsytan, så att du kan visa, skapa, redigera eller ta bort (om tillämpligt) tillgångar i en arbetsyta. Dessutom kan kan du också lägga till eller ta bort användare och ändra deras rolltilldelningar.

## <a name="add-or-remove-users"></a>Lägg till eller ta bort användare
Om du är ägare till en arbetsyta kan du lägga till eller ta bort användare från arbetsytan genom att välja någon av följande metoder för att utföra åtgärden:
- [Azure-portalens användargränssnitt](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [REST-API](/azure/role-based-access-control/role-assignments-rest)
- [Azure Resource Manager-mallar](/azure/role-based-access-control/role-assignments-template).

Du kan också om du har installerat [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md), du kan också använda en lämplig CLI-kommando för att lägga till användare till arbetsytan.

```azurecli-interactive 
az ml workspace share -n <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

Tänk på att den `user` fältet är den e-postadressen för en befintlig användare i samma Azure Active Directory var överordnat Azure-prenumeration i arbetsytan finns. Nedan visas ett exempel på hur du använder det här kommandot:

```azurecli-interactive 
az ml workspace share -n my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Skapa anpassad roll
Om de inbyggda rollerna är tillräckligt för dina behov kan skapa du också anpassade roller. Anpassade roller kan ha läsa, skriva eller ta bort behörigheter för arbetsytan och beräkningsresursen på arbetsytan. Och du kan tillgängliggöra rollen på en viss arbetsyta-nivå, en specifik resursgruppsnivå eller en specifik prenumerationsnivå. 

> [!NOTE]
> Du måste vara en ägare till resursen på den nivån för att skapa anpassade roller i den här resursen.

Om du vill skapa en anpassad roll, skapa först en roll definition JSON-fil som du anger behörighet och omfång som du vill använda för rollen. Nedan visas exempelvis en roll-definitionsfil för en anpassad roll med namnet ”dataexpert” omfång på en viss arbetsytenivå.

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

Den här rollen kan du göra allt i arbetsytan förutom följande åtgärder:
- Det går inte att skapa eller uppdatera en beräkningsresurs.
- Det går inte att ta bort en beräkningsresurs.
- Det går inte att lägga till, ta bort användare eller ändra en användares rolltilldelningar.
- Det går inte att ta bort arbetsytan.

Om du vill distribuera den här anpassade rollen, använder du följande Azure CLI-kommando:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

När distribuerats blir den här rollen tillgänglig i den angivna arbetsytan. Nu kan du lägga till användare och tilldela den här rollen till dem i Azure-portalen. Du kan också lägga till en användare med den här rollen med hjälp av den `az ml workspace share` CLI-kommando:

```azurecli-interactive
az ml workspace share -n my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

Du kan också ändra den `AssignableScopes` fält för att ange omfånget för den här anpassade rollen på prenumerationsnivå resursgruppsnivå, eller på en viss arbetsyta-nivå (enligt).

Mer information i hur anpassade roller fungerar i Azure, referera till [det här dokumentet](/azure/role-based-access-control/custom-roles).

## <a name="next-steps"></a>Nästa steg

Följ den kompletta självstudiekursen lär du dig hur du använder en arbetsyta för att skapa, träna och distribuera modeller med Azure Machine Learning-tjänsten.

> [!div class="nextstepaction"]
> [Självstudier: Skapa modeller](tutorial-train-models-with-aml.md)
