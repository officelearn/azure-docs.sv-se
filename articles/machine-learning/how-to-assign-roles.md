---
title: Hantera roller i din arbets yta
titleSuffix: Azure Machine Learning
description: Lär dig hur du kommer åt en Azure Machine Learning-arbetsyta med rollbaserad åtkomst kontroll (RBAC).
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79270100"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Hantera åtkomst till en Azure Machine Learning-arbetsyta
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du hanterar åtkomst till en Azure Machine Learning-arbetsyta. [Rollbaserad åtkomst kontroll (RBAC)](/azure/role-based-access-control/overview) används för att hantera åtkomst till Azure-resurser. Användare i Azure Active Directory tilldelas vissa roller som ger åtkomst till resurser. Azure tillhandahåller både inbyggda roller och möjligheten att skapa anpassade roller.

## <a name="default-roles"></a>Standard roller

En Azure Machine Learning-arbetsyta är en Azure-resurs. Precis som andra Azure-resurser, och när en ny Azure Machine Learning arbets yta skapas, levereras den med tre standard roller. Du kan lägga till användare i arbets ytan och tilldela dem till någon av dessa inbyggda roller.

| Roll | Åtkomstnivå |
| --- | --- |
| **Läsare** | Skrivskyddade åtgärder på arbets ytan. Läsarna kan visa och Visa till gångar i en arbets yta, men kan inte skapa eller uppdatera dessa till gångar. |
| **Deltagare** | Visa, skapa, redigera eller ta bort (om det är tillämpligt) till gångar i en arbets yta. Deltagare kan till exempel skapa ett experiment, skapa eller ansluta ett beräknings kluster, skicka in en körning och distribuera en webb tjänst. |
| **Ägare** | Fullständig åtkomst till arbets ytan, inklusive möjligheten att visa, skapa, redigera eller ta bort (om tillämpligt) till gångar i en arbets yta. Dessutom kan du ändra roll tilldelningar. |

> [!IMPORTANT]
> Roll åtkomsten kan begränsas till flera nivåer i Azure. Till exempel kanske någon med ägar åtkomst till en arbets yta saknar ägar åtkomst till den resurs grupp som innehåller arbets ytan. Mer information finns i [hur RBAC fungerar](/azure/role-based-access-control/overview#how-rbac-works).

Mer information om de inbyggda rollerna finns i [inbyggda roller för Azure](/azure/role-based-access-control/built-in-roles).

## <a name="manage-workspace-access"></a>Hantera åtkomst till arbets ytan

Om du är ägare till en arbets yta kan du lägga till och ta bort roller för arbets ytan. Du kan också tilldela roller till användare. Använd följande länkar för att identifiera hur du hanterar åtkomst:
- [Azure Portal användar gränssnitt](/azure/role-based-access-control/role-assignments-portal)
- [PowerShell](/azure/role-based-access-control/role-assignments-powershell)
- [Azure CLI](/azure/role-based-access-control/role-assignments-cli)
- [REST API](/azure/role-based-access-control/role-assignments-rest)
- [Azure Resource Manager-mallar](/azure/role-based-access-control/role-assignments-template)

Om du har installerat [Azure Machine Learning CLI](reference-azure-machine-learning-cli.md)kan du också använda ett CLI-kommando för att tilldela roller till användare.

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

`user` Fältet är e-postadressen till en befintlig användare i instansen av Azure Active Directory där arbets ytans överordnade prenumeration finns. Här är ett exempel på hur du använder det här kommandot:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

## <a name="create-custom-role"></a>Skapa anpassad roll

Om de inbyggda rollerna är otillräckliga kan du skapa anpassade roller. Anpassade roller kan ha behörigheterna läsa, skriva, ta bort och beräkna resurs på arbets ytan. Du kan göra rollen tillgänglig på en speciell arbets yta, en bestämd resurs grupps nivå eller en speciell prenumerations nivå.

> [!NOTE]
> Du måste vara ägare till resursen på den nivån för att skapa anpassade roller i resursen.

Skapa en anpassad roll genom att skapa en JSON-fil för roll definition som anger behörigheten och omfånget för rollen. I följande exempel definieras en anpassad roll med namnet "data expert" på en speciell arbets ytans nivå:

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

Du kan ändra `AssignableScopes` fältet för att ange omfånget för den här anpassade rollen på prenumerations nivå, resurs grupp nivå eller en speciell arbets ytans nivå.

Den här anpassade rollen kan göra allt på arbets ytan, förutom följande åtgärder:

- Det går inte att skapa eller uppdatera en beräknings resurs.
- Det går inte att ta bort en beräknings resurs.
- Det går inte att lägga till, ta bort eller ändra roll tilldelningar.
- Det går inte att ta bort arbets ytan.

Använd följande Azure CLI-kommando för att distribuera den här anpassade rollen:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Efter distributionen blir den här rollen tillgänglig på den angivna arbets ytan. Nu kan du lägga till och tilldela den här rollen i Azure Portal. Du kan också tilldela rollen till en användare med hjälp av `az ml workspace share` CLI-kommandot:

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

Mer information om anpassade roller finns i [anpassade roller för Azure-resurser](/azure/role-based-access-control/custom-roles).

Mer information om de åtgärder (åtgärder) som kan användas med anpassade roller finns i [Resource Provider-åtgärder](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices).


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar


### <a name="q-what-are-the-permissions-needed-to-perform-various-actions-in-the-azure-machine-learning-service"></a>F. Vilka är de behörigheter som krävs för att utföra olika åtgärder i Azure Machine Learnings tjänsten?

Följande tabell är en sammanfattning av Azure Machine Learning aktiviteter och de behörigheter som krävs för att utföra dem med minsta möjliga omfattning. Exempel: om en aktivitet kan utföras med ett område för arbets ytor (kolumn 4), kommer alla högre omfång med den behörigheten också att fungera automatiskt. Alla sökvägar i den här tabellen är **relativa sökvägar** till `Microsoft.MachineLearningServices/`.

| Aktivitet | Omfång på prenumerations nivå | Omfång på resurs grupps nivå | Omfång på arbets ytans nivå |
|---|---|---|---|
| Skapa ny arbets yta | Krävs inte | Ägare eller deltagare | Ej tillämpligt (blir ägare eller ärver högre omfattnings roll efter att det har skapats) |
| Skapa nytt beräknings kluster | Krävs inte | Krävs inte | Ägare, deltagare eller anpassad roll som tillåter:`workspaces/computes/write` |
| Skapa ny virtuell dator för Notebook | Krävs inte | Ägare eller deltagare | Inte möjligt |
| Skapa en ny beräknings instans | Krävs inte | Krävs inte | Ägare, deltagare eller anpassad roll som tillåter:`workspaces/computes/write` |
| Data Plans aktivitet som att skicka körning, komma åt data, distribuera modell eller publicera pipeline | Krävs inte | Krävs inte | Ägare, deltagare eller anpassad roll som tillåter:`workspaces/*/write` <br/> Observera att du också behöver ett data lager som är registrerat på arbets ytan för att tillåta MSI att komma åt data i ditt lagrings konto. |


### <a name="q-how-do-i-list-all-the-custom-roles-in-my-subscription"></a>F. Hur gör jag för att lista alla anpassade roller i min prenumeration?

Kör följande kommando i Azure CLI.

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

### <a name="q-how-do-i-find-the-role-definition-for-a-role-in-my-subscription"></a>F. Hur gör jag för att hittar du roll definitionen för en roll i min prenumeration?

Kör följande kommando i Azure CLI. Observera att `<role-name>` ska ha samma format som returneras av kommandot ovan.

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

### <a name="q-how-do-i-update-a-role-definition"></a>F. Hur gör jag för att uppdatera en roll definition?

Kör följande kommando i Azure CLI.

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

Observera att du måste ha behörighet för hela omfattningen av din nya roll definition. Om den nya rollen till exempel har ett omfång över tre prenumerationer måste du ha behörighet för alla tre prenumerationerna. 

> [!NOTE]
> Roll uppdateringar kan ta 15 minuter till en timme att tillämpa på alla roll tilldelningar i det omfånget.
### <a name="q-can-i-define-a-role-that-prevents-updating-the-workspace-edition"></a>F. Kan jag definiera en roll som förhindrar uppdatering av arbets ytans version? 

Ja, du kan definiera en roll som förhindrar uppdatering av arbets ytans version. Eftersom uppdatering av arbets ytan är ett KORRIGERINGs anrop för objektet arbets yta gör du detta genom att lägga till följande åtgärd `"NotActions"` i matrisen i din JSON-definition: 

`"Microsoft.MachineLearningServices/workspaces/write"`

### <a name="q-what-permissions-are-needed-to-perform-quota-operations-in-a-workspace"></a>F. Vilka behörigheter krävs för att utföra kvot åtgärder på en arbets yta? 

Du måste ha behörighet som prenumerations nivå för att utföra en kvot relaterad åtgärd i arbets ytan. Det innebär att det bara går att ange kvoten på prenumerations nivå eller på arbets ytans kvot för dina hanterade beräknings resurser om du har Skriv behörighet i prenumerations omfånget. 


## <a name="next-steps"></a>Nästa steg

- [Översikt över Enterprise Security](concept-enterprise-security.md)
- [Köra experiment och härledning/Poäng i ett virtuellt nätverk på ett säkert sätt](how-to-enable-virtual-network.md)
- [Självstudie: träna modeller](tutorial-train-models-with-aml.md)
- [Åtgärder för resursprovider](/azure/role-based-access-control/resource-provider-operations#microsoftmachinelearningservices)
