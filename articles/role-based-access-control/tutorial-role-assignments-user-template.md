---
title: 'Självstudie: ge en användare åtkomst till Azure-resurser med hjälp av en Azure Resource Manager-mall – Azure RBAC'
description: Lär dig hur du beviljar en användare åtkomst till Azure-resurser med hjälp av en Azure Resource Manager-mall och rollbaserad åtkomst kontroll i Azure (Azure RBAC) i den här självstudien.
services: role-based-access-control,azure-resource-manager
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 05/15/2019
ms.author: rolyon
ms.openlocfilehash: f98b1e9c57cf9f624c5af3e59c8afd56d073dd28
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735478"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-an-azure-resource-manager-template"></a>Självstudie: ge en användare åtkomst till Azure-resurser med hjälp av en Azure Resource Manager mall

[Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](overview.md) är hur du hanterar åtkomst till Azure-resurser. I den här självstudien skapar du en resurs grupp och ger användaren åtkomst till att skapa och hantera virtuella datorer i resurs gruppen. Den här självstudien fokuserar på processen att distribuera en Resource Manager-mall för att ge åtkomst. Mer information om hur du utvecklar Resource Manager-mallar finns i [Resource Manager-dokumentation](/azure/azure-resource-manager/) och [mallen referens](/azure/templates/microsoft.authorization/allversions
).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Bevilja åtkomst för en användare i ett resursgruppomfång
> * Verifiera distributionen
> * Rensa

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill lägga till och ta bort roll tilldelningar måste du ha:

* `Microsoft.Authorization/roleAssignments/write`och `Microsoft.Authorization/roleAssignments/delete` behörigheter, till exempel [administratör för användar åtkomst](built-in-roles.md#user-access-administrator) eller [ägare](built-in-roles.md#owner)

## <a name="grant-access"></a>Bevilja åtkomst

Mallen som används i den här snabb starten är från [Azure snabb starts-mallar](https://azure.microsoft.com/resources/templates/101-rbac-builtinrole-resourcegroup/). Fler Azure-auktoriserings-relaterade mallar hittar du [här](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization).

Om du vill distribuera mallen väljer du **prova** att öppna Azure Cloud Shell och klistrar sedan in följande PowerShell-skript i Shell-fönstret. Om du vill klistra in koden högerklickar du på Shell-fönstret och väljer **Klistra in**.

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
1. Öppna resurs gruppen som skapades i den senaste proceduren. Standard namnet är projekt namnet med **RG** tillagt.
1. Välj **Åtkomstkontroll (IAM)** från den vänstra menyn.
1. Välj **Rolltilldelningar**. 
1. I **namn**anger du den e-postadress du angav i den senaste proceduren. Du får se att användaren med e-postadressen har rollen **virtuell dator deltagare** .

## <a name="clean-up"></a>Rensa

Om du vill ta bort resurs gruppen som skapades i den senaste proceduren väljer du **prova** att öppna Azure Cloud Shell och klistrar sedan in följande PowerShell-skript i Shell-fönstret.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: ge en användare åtkomst till Azure-resurser med hjälp av Azure PowerShell](tutorial-role-assignments-user-powershell.md)