---
title: Skapa en anpassad Azure-roll med hjälp av en Azure Resource Manager mall – Azure RBAC
description: Lär dig hur du skapar en anpassad Azure-roll med hjälp av Azure Resource Manager mallar och rollbaserad åtkomst kontroll i Azure (Azure RBAC).
services: role-based-access-control,azure-resource-manager
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.custom: subject-armqs
ms.workload: identity
ms.date: 06/25/2020
ms.author: rolyon
ms.openlocfilehash: bcf1966ffc326291448cb611d99390fe0d652151
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/26/2020
ms.locfileid: "85392980"
---
# <a name="create-an-azure-custom-role-using-an-azure-resource-manager-template"></a>Skapa en anpassad Azure-roll med hjälp av en Azure Resource Manager mall

Om de [inbyggda Azure-rollerna](built-in-roles.md) inte uppfyller organisationens specifika behov kan du skapa dina egna [anpassade roller](custom-roles.md). Den här artikeln beskriver hur du skapar en anpassad roll med hjälp av en Azure Resource Manager-mall.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Krav

Om du vill skapa en anpassad roll måste du ha:

- Behörigheter att skapa anpassade roller som [Owner](built-in-roles.md#owner) (Ägare) eller [User Access Administrator](built-in-roles.md#user-access-administrator) (Administratör för användaråtkomst)

## <a name="create-a-custom-role"></a>Skapa en anpassad roll

Om du vill skapa en anpassad roll anger du ett roll namn, behörigheter och var rollen kan användas. I den här artikeln skapar du en roll med namnet "anpassad roll-RG läsare" med resurs behörigheter som kan tilldelas till ett prenumerations omfång eller lägre.

### <a name="review-the-template"></a>Granska mallen

Mallen som används i den här artikeln är från [Azure snabb starts mallar](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments/create-role-def). Mallen har fyra parametrar och avsnittet Resources. De fyra parametrarna är:

- Matris med åtgärder med standardvärdet ["Microsoft. Resources/Subscriptions/resourceGroups/Read"]
- Matris med notActions med ett tomt standardvärde
- Rollnamn med standardvärdet "Custom Role-RG Reader"
- Roll beskrivning med standardvärdet "distribution av prenumerations nivå för en roll definition"

Den resurs som definierats i mallen är:

- [Microsoft. Authorization/roleDefinitions](/azure/templates/Microsoft.Authorization/roleDefinitions)

Omfånget där den här anpassade rollen kan tilldelas har angetts till den aktuella prenumerationen.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-role-def/azuredeploy.json":::

### <a name="deploy-the-template"></a>Distribuera mallen

Följ de här stegen för att distribuera den tidigare mallen.

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Öppna Azure Cloud Shell för PowerShell.

1. Kopiera och klistra in följande skript i Cloud Shell.

    ```azurepowershell
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"
    [string[]]$actions = Read-Host -Prompt "Enter actions as a comma-separated list (i.e. action1,action2)"
    $actions = $actions.Split(',')

    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json"

    New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    ```

1. Ange en plats för distributionen, t. ex. *centralt*.

1. Ange en lista med åtgärder för den anpassade rollen som en kommaavgränsad lista, t. ex *. Microsoft. Resources/resurser/Read, Microsoft. Resources/Subscriptions/resourceGroups/Read*.

1. Om det behövs trycker du på RETUR för att köra kommandot New-AzDeployment.

    Kommandot [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) distribuerar mallen för att skapa den anpassade rollen.

    Du bör se utdata som liknar följande:

    ```azurepowershell
    PS> New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    
    Id                      : /subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/azuredeploy
    DeploymentName          : azuredeploy
    Location                : centralus
    ProvisioningState       : Succeeded
    Timestamp               : 6/25/2020 8:08:32 PM
    Mode                    : Incremental
    TemplateLink            :
                              Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json
                              ContentVersion : 1.0.0.0
    
    Parameters              :
                              Name               Type                       Value
                              =================  =========================  ==========
                              actions            Array                      [
                                "Microsoft.Resources/resources/read",
                                "Microsoft.Resources/subscriptions/resourceGroups/read"
                              ]
                              notActions         Array                      []
                              roleName           String                     Custom Role - RG Reader
                              roleDescription    String                     Subscription Level Deployment of a Role Definition
    
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Följ de här stegen för att kontrol lera att den anpassade rollen har skapats.

1. Kör kommandot [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) för att visa en lista med den anpassade rollen.

    ```azurepowershell
    Get-AzRoleDefinition "Custom Role - RG Reader" | ConvertTo-Json
    ```

    Du bör se utdata som liknar följande:

    ```azurepowershell
    {
      "Name": "Custom Role - RG Reader",
      "Id": "11111111-1111-1111-1111-111111111111",
      "IsCustom": true,
      "Description": "Subscription Level Deployment of a Role Definition",
      "Actions": [
        "Microsoft.Resources/resources/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/{subscriptionId}"
      ]
    }
    ```

1. Öppna din prenumeration i Azure Portal.

1. Klicka på **åtkomst kontroll (IAM)** på den vänstra menyn.

1. Klicka på fliken **roller** .

1. Ange listan **typ** till **CustomRole**.

1. Verifiera att rollen för den **anpassade rollen RG läsare** visas i listan.

   ![Ny anpassad roll i Azure Portal](./media/custom-roles-template/custom-role-template-portal.png)

## <a name="clean-up-resources"></a>Rensa resurser

Följ dessa steg om du vill ta bort den anpassade rollen.

1. Kör följande kommando för att ta bort den anpassade rollen.

    ```azurepowershell
    Get-AzRoleDefinition -Name "Custom Role - RG Reader" | Remove-AzRoleDefinition
    ```

1. Bekräfta att du vill ta bort den anpassade rollen genom att ange **Y** .

## <a name="next-steps"></a>Nästa steg

- [Förstå roll definitioner för Azure](role-definitions.md)
- [Snabb start: lägga till en Azure-roll tilldelning med en Azure Resource Manager-mall](quickstart-role-assignments-template.md)
- [Dokumentation om ARM-mallar](../azure-resource-manager/templates/index.yml)
