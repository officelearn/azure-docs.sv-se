---
title: Självstudie – bevilja en användare åtkomst till Azure-resurser med RBAC och Resource Manager-mall | Microsoft Docs
description: Lär dig att ge en användaråtkomst till Azure-resurser med hjälp av rollbaserad åtkomstkontroll (RBAC) med hjälp av Azure Resource Manager-mall.
services: role-based-access-control,azure-resource-manager
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control,azure-resource-manager
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/15/2019
ms.author: rolyon
ms.openlocfilehash: e99a9d2cfa38c9b2ea74f9075b18f81006b34881
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65761431"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-resource-manager-template"></a>Självstudier: Ge en användaråtkomst till Azure-resurser med RBAC och Resource Manager-mall

[Rollbaserad åtkomstkontroll (RBAC)](overview.md) är metoden som du använder när du hanterar åtkomst till Azure-resurser. I den här självstudien får du skapa en resursgrupp och ge en användarbehörighet att skapa och hantera virtuella datorer i resursgruppen. Den här självstudien fokuserar på processen för att distribuera en Resource Manager-mall för att bevilja åtkomst. Mer information om hur du utvecklar Resource Manager-mallar finns i [Resource Manager-dokumentation](/azure/azure-resource-manager/) och [mallreferensen](/azure/templates/microsoft.authorization/allversions
).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Bevilja åtkomst för en användare i ett resursgruppomfång
> * Verifiera distributionen
> * Rensa

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill lägga till och ta bort rolltilldelningar, måste du ha:

* `Microsoft.Authorization/roleAssignments/write` och `Microsoft.Authorization/roleAssignments/delete` behörigheter, till exempel [administratör för användaråtkomst](built-in-roles.md#user-access-administrator) eller [ägare](built-in-roles.md#owner)

## <a name="grant-access"></a>Bevilja åtkomst

Den mall som användes i den här snabbstarten är från [Azures snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-rbac-builtinrole-resourcegroup/). Fler Azure auktorisering relaterade mallar hittar [här](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization).

Om du vill distribuera mallen, Välj **prova** att öppna Azure Cloud shell och klistra in följande PowerShell-skript i fönstret shell. Om du vill klistra in koden, högerklicka på shell-fönstret och välj sedan **klistra in**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$emailAddress = Read-Host -Prompt "Enter your email address that is associated with your Azure subscription (used to find the principal ID)"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

$resourceGroupName = "${projectName}rg"
$roleAssignmentName = New-Guid
$principalId = (Get-AzAdUser -Mail $emailAddress).id
$roleDefinitionId = (Get-AzRoleDefinition -name "Virtual Machine Contributor").id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-rbac-builtinrole-resourcegroup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -roleAssignmentName $roleAssignmentName -roleDefinitionID $roleDefinitionId -principalId $principalId
```

## <a name="validate-the-deployment"></a>Verifiera distributionen

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Öppna resursgruppen som skapades i föregående procedur. Standardnamnet är projektnamnet på med **rg** sist.
1. Välj **Åtkomstkontroll (IAM)** från den vänstra menyn.
1. Välj **rolltilldelningar**. 
1. I **namn**, ange den e-postadress som du angav i föregående procedur. Du bör se användare med e-postadressen har den **virtuell Datordeltagare** roll.

## <a name="clean-up"></a>Rensa

Om du vill ta bort resursgruppen som skapades i föregående procedur, Välj **prova** att öppna Azure Cloud shell och klistra in följande PowerShell-skript i fönstret shell.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Ge en användare åtkomst till Azure-resurser med RBAC och Azure PowerShell](tutorial-role-assignments-user-powershell.md)