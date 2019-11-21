---
title: 'Quickstart: New policy assignment with templates'
description: In this quickstart, you use a Resource Manager template to create a policy assignment to identify non-compliant resources.
ms.date: 03/13/2019
ms.topic: quickstart
ms.openlocfilehash: d85d02408b05ccaef608a2bb951a48e5a8526da6
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74216807"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>Quickstart: Create a policy assignment to identify non-compliant resources by using a Resource Manager template

Det första steget mot att förstå kompatibilitet i Azure är att identifiera dina resursers status.
Denna snabbstart vägleder dig genom processen för att skapa en principtilldelning som identifierar virtuella datorer som inte använder hanterade diskar.

Efter den här genomgången kommer du att kunna identifiera virtuella datorer som inte använder hanterade diskar. De är *inkompatibla* med principtilldelningen.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="create-a-policy-assignment"></a>Skapa en principtilldelning

In this quickstart, you create a policy assignment and assign a built-in policy definition called *Audit VMs that do not use managed disks*. For a partial list of available built-in policies, see [Azure Policy samples](./samples/index.md).

There are several methods for creating policy assignments. In this quickstart, you use a [quickstart template](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/).
Here is a copy of the template:

[!code-json[policy-assignment](~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json)]

> [!NOTE]
> Azure Policy service is free.  For more information, see [Overview of Azure Policy](./overview.md).

1. Select the following image to sign in to the Azure portal and open the template:

   [![Deploy the Policy template to Azure](./media/assign-policy-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json)

1. Select or enter the following values:

   | Namn | Värde |
   |------|-------|
   | Prenumeration | Välj din Azure-prenumeration. |
   | Resursgrupp | Select **Create new**, specify a name, and then select **OK**. In the screenshot, the resource group name is *mypolicyquickstart\<Date in MMDD>rg*. |
   | Plats | Välj en region. Välj till exempel **USA, centrala**. |
   | Policy Assignment Name | Specify a policy assignment name. You can use the policy definition display if you want. For example, **Audit VMs that do not use managed disks**. |
   | Rg Name | Specify a resource group name where you want to assign the policy to. In this quickstart, use the default value **[resourceGroup().name]** . **[resourceGroup()](../../azure-resource-manager/resource-group-template-functions-resource.md#resourcegroup)** is a template function that retrieves the resource group. |
   | Policy Definition ID | Specify **/providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4c19-b460-a2d36003525a**. |
   | I agree to the terms and conditions stated above | (Select) |

1. Välj **Köp**.

Some additional resources:

- To find more samples templates, see [Azure Quickstart template](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular).
- To see the template reference, go to [Azure template reference](/azure/templates/microsoft.authorization/allversions).
- To learn how to develop Resource Manager templates, see [Azure Resource Manager documentation](/azure/azure-resource-manager/).
- To learn subscription-level deployment, see [Create resource groups and resources at the subscription level](../../azure-resource-manager/deploy-to-subscription.md).

## <a name="identify-non-compliant-resources"></a>Identifiera icke-kompatibla resurser

Välj **Efterlevnad** till vänster på sidan. Leta sedan upp principtilldelningen **Granska virtuella datorer som inte använder hanterade diskar** som du skapade.

![Policy compliance overview page](./media/assign-policy-template/policy-compliance.png)

Om det finns befintliga resurser som inte är kompatibla med denna nya tilldelning visas de under **Icke-kompatibla resurser**.

For more information, see [How compliance works](./how-to/get-compliance-data.md#how-compliance-works).

## <a name="clean-up-resources"></a>Rensa resurser

Följ dessa steg för att ta bort tilldelningen som skapades:

1. Välj **Efterlevnad** (eller **Tilldelningar**) till vänster på sidan Azure Policy och leta upp principtilldelningen **Granska virtuella datorer som inte använder hanterade diskar** som du skapade.

1. Right-click the **Audit VMs that do not use managed disks** policy assignment and select **Delete assignment**.

   ![Delete an assignment from the compliance overview page](./media/assign-policy-template/delete-assignment.png)

## <a name="next-steps"></a>Nästa steg

In this quickstart, you assigned a built-in policy definition to a scope and evaluated its compliance report. Principdefinitionen kontrollerar att alla resurser i omfånget är kompatibla och identifierar vilka som inte är det.

Om du vill ha mer information om tilldelning av principer för att validera att de nya resurserna är kompatibla fortsätter du till självstudien för att:

> [!div class="nextstepaction"]
> [Skapa och hantera principer](./tutorials/create-and-manage.md)