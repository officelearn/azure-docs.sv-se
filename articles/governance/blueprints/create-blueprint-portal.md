---
title: 'Quickstart: Create a blueprint in the portal'
description: In this quickstart, you use Azure Blueprints to create, define, and deploy artifacts through the Azure portal.
ms.date: 03/11/2019
ms.topic: quickstart
ms.openlocfilehash: 47c225f3a3e51dc965580e48b8f838f557676d2e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74210479"
---
# <a name="quickstart-define-and-assign-a-blueprint-in-the-portal"></a>Quickstart: Define and assign a blueprint in the portal

When you learn how to create and assign blueprints, you can define common patterns to develop reusable and rapidly deployable configurations based on Azure Resource Manager templates, policy, security, and more. In this tutorial, you learn to use Azure Blueprints to do some of the common tasks related to creating, publishing, and assigning a blueprint within your organization. These tasks include:

> [!div class="checklist"]
> - Skapa en ny skiss och lägga till olika artefakter som stöds
> - Göra ändringar i en befintlig skiss som fortfarande har **utkaststatus**
> - Markera en skiss som redo att tilldelas med **Publicerad**
> - Tilldela en skiss till en befintlig prenumeration
> - Kontrollera statusen och förloppet för en tilldelad skiss
> - Ta bort en skiss som har tilldelats till en prenumeration

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

## <a name="create-a-blueprint"></a>Skapa en skiss

Det första steget när du definierar ett standardmönster för efterlevnad är att skapa en skiss från de tillgängliga resurserna. In this example, create a new blueprint named **MyBlueprint** to configure role and policy assignments for the subscription. Then add a new resource group, and create a Resource Manager template and role assignment on the new resource group.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Select **Blueprint definitions** from the page on the left and select the **+ Create blueprint** button at the top of the page.

   Or, select **Create** from the **Getting started** page to go straight to creating a blueprint.

   ![Create a blueprint from the Blueprint definitions page](./media/create-blueprint-portal/create-blueprint-button.png)

1. Provide a **Blueprint name** such as **MyBlueprint**. (Use up to 48 letters and numbers, but no spaces or special characters). Leave **Blueprint description** blank for now.

1. In the **Definition location** box, select the ellipsis on the right, select the [management group](../management-groups/overview.md) or subscription where you want to save the blueprint, and choose **Select**.

1. Verify that the information is correct. The **Blueprint name** and **Definition location** fields can't be changed later. Then select **Next : Artifacts** at the bottom of the page or the **Artifacts** tab at the top of the page.

1. Add a role assignment at the subscription level:

   1. Select the **+ Add artifact** row under **Subscription**. The **Add artifact** window opens on the right side of the browser.

   1. Select **Role assignment** for **Artifact type**.

   1. Under **Role**, select **Contributor**. Leave the **Add user, app or group** box with the check box that indicates a dynamic parameter.

   1. Select **Add** to add this artifact to the blueprint.

   ![Role assignment for a blueprint artifact](./media/create-blueprint-portal/add-role-assignment.png)

   > [!NOTE]
   > Most artifacts support parameters. A parameter that's assigned a value during blueprint creation is a *static parameter*. If the parameter is assigned during blueprint assignment, it's a *dynamic parameter*. Mer information finns [Skissparametrar](./concepts/parameters.md).

1. Add a policy assignment at the subscription level:

   1. Select the **+ Add artifact** row under the role assignment artifact.

   1. Select **Policy assignment** for **Artifact type**.

   1. Change **Type** to **Built-in**. In **Search**, enter **tag**.

   1. Klicka på **Sök** om du vill genomföra filtrering. Select **Append tag and its default value to resource groups**.

   1. Select **Add** to add this artifact to the blueprint.

1. Select the row of the policy assignment **Append tag and its default value to resource groups**.

1. The window to provide parameters to the artifact as part of the blueprint definition opens and allows setting the parameters for all assignments (static parameters) based on this blueprint instead of during assignment (dynamic parameters). This example uses dynamic parameters during blueprint assignment, so leave the defaults and select **Cancel**.

1. Add a resource group at the subscription level:

   1. Select the **+ Add artifact** row under **Subscription**.

   1. Select **Resource group** for **Artifact type**.

   1. Leave the **Artifact display name**, **Resource Group Name**, and **Location** boxes blank, but make sure that the check box is checked for each parameter property to make them dynamic parameters.

   1. Select **Add** to add this artifact to the blueprint.

1. Add a template under the resource group:

   1. Select the **+ Add artifact** row under the **ResourceGroup** entry.

   1. Select **Azure Resource Manager template** for **Artifact type**, set **Artifact display name** to **StorageAccount**, and leave **Description** blank.

   1. Klistra in följande Resource Manager-mall i redigeringsrutan på fliken **Mall**.
      After you paste the template, select the **Parameters** tab and note that the template parameters **storageAccountType** and **location** were detected. Each parameter was automatically detected and populated, but configured as a dynamic parameter.

      > [!IMPORTANT]
      > If you're importing the template, ensure that the file is only JSON and doesn't include HTML. When you're pointing to a URL on GitHub, ensure that you have selected **RAW** to get the pure JSON file and not the one wrapped with HTML for display on GitHub. Ett fel inträffar om den importerade mallen inte är ren JSON.

      ```json
      {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
              "storageAccountType": {
                  "type": "string",
                  "defaultValue": "Standard_LRS",
                  "allowedValues": [
                      "Standard_LRS",
                      "Standard_GRS",
                      "Standard_ZRS",
                      "Premium_LRS"
                  ],
                  "metadata": {
                      "description": "Storage Account type"
                  }
              },
              "location": {
                  "type": "string",
                  "defaultValue": "[resourceGroup().location]",
                  "metadata": {
                      "description": "Location for all resources."
                  }
              }
          },
          "variables": {
              "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
          },
          "resources": [{
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[variables('storageAccountName')]",
              "location": "[parameters('location')]",
              "apiVersion": "2018-07-01",
              "sku": {
                  "name": "[parameters('storageAccountType')]"
              },
              "kind": "StorageV2",
              "properties": {}
          }],
          "outputs": {
              "storageAccountName": {
                  "type": "string",
                  "value": "[variables('storageAccountName')]"
              }
          }
      }
      ```

   1. Clear the **storageAccountType** check box and note that the drop-down list contains only values included in the Resource Manager template under **allowedValues**. Select the box to set it back to a dynamic parameter.

   1. Select **Add** to add this artifact to the blueprint.

   ![Resource Manager template for the blueprint artifact](./media/create-blueprint-portal/add-resource-manager-template.png)

1. Din färdiga skiss bör se ut som i det följande. Notice that each artifact has **_x_ out of _y_ parameters populated** in the **Parameters** column. The dynamic parameters are set during each assignment of the blueprint.

   ![Completed blueprint definition](./media/create-blueprint-portal/completed-blueprint.png)

1. Now that all planned artifacts have been added, select **Save Draft** at the bottom of the page.

## <a name="edit-a-blueprint"></a>Redigera en skiss

In [Create a blueprint](#create-a-blueprint), you didn't provide a description or add the role assignment to the new resource group. You can fix both by following these steps:

1. Välj **Skissdefinitioner** till vänster på sidan.

1. In the list of blueprints, right-click the one that you previously created and select **Edit blueprint**.

1. I **Skissbeskrivning** anger du information om skissen och de artefakter som ingår i den. In this case, enter something like: **This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.**

1. Select **Next : Artifacts** at the bottom of the page or the **Artifacts** tab at the top of the page.

1. Add a role assignment under the resource group:

   1. Select the **+ Add artifact** row directly under the **ResourceGroup** entry.

   1. Select **Role assignment** for **Artifact type**.

   1. Under **Role**, select **Owner**, and clear the check box under the **Add user, app or group** box.

   1. Search for and select a user, app, or group to add. This artifact uses a static parameter set the same in every assignment of this blueprint.

   e. Select **Add** to add this artifact to the blueprint.

   ![Second role assignment for the blueprint artifact](./media/create-blueprint-portal/add-role-assignment-2.png)

1. Din färdiga skiss bör se ut som i det följande. Notice that the newly added role assignment shows **1 out of 1 parameters populated**. That means it's a static parameter.

   ![Second definition for the completed blueprint](./media/create-blueprint-portal/completed-blueprint-2.png)

1. Select **Save Draft** now that it has been updated.

## <a name="publish-a-blueprint"></a>Publicera en skiss

Nu när alla de planerade artefakterna har lagts till i skissen är det dags att publicera den.
Publishing makes the blueprint available to be assigned to a subscription.

1. Välj **Skissdefinitioner** till vänster på sidan.

1. In the list of blueprints, right-click the one you previously created and select **Publish blueprint**.

1. In the pane that opens, provide a **Version** (letters, numbers, and hyphens with a maximum length of 20 characters), such as **v1**. Optionally, enter text in **Change notes**, such as **First publish**.

1. Select **Publish** at the bottom of the page.

## <a name="assign-a-blueprint"></a>Tilldela en skiss

After a blueprint has been published, it can be assigned to a subscription. Assign the blueprint that you created to one of the subscriptions under your management group hierarchy. Om skissen sparas till en prenumeration kan den endast tilldelas till den prenumerationen.

1. Välj **Skissdefinitioner** till vänster på sidan.

1. In the list of blueprints, right-click the one that you previously created (or select the ellipsis) and select **Assign blueprint**.

1. On the **Assign blueprint** page, in the **Subscription** drop-down list, select the subscriptions that you want to deploy this blueprint to.

   Om det finns Enterprise-erbjudanden som stöds tillgängliga från [Azure-fakturering](../../billing/index.md) aktiveras länken **Skapa nytt** under rutan **Prenumeration**. Följ de här stegen:

   1. Välj länken **Skapa nytt** för att skapa en ny prenumeration i stället för att välja befintliga.

   1. Ange ett **Visningsnamn** för den nya prenumerationen.

   1. Select the available **Offer** from the drop-down list.

   1. Use the ellipsis to select the [management group](../management-groups/overview.md) that the subscription will be a child of.

   1. Välj **Skapa** längst ned på sidan.

   ![Create a subscription for a blueprint assignment subscription](./media/create-blueprint-portal/assignment-create-subscription.png)

   > [!IMPORTANT]
   > The new subscription is created immediately after you select **Create**.

   > [!NOTE]
   > An assignment is created for each subscription that you select. You can make changes to a single subscription assignment at a later time without forcing changes on the remainder of the selected subscriptions.

1. For **Assignment name**, provide a unique name for this assignment.

1. In **Location**, select a region for the managed identity and subscription deployment object to be created in. Azure Blueprint använder den här hanterade identiteten för att distribuera alla artefakter i den tilldelade skissen. Mer information finns i [Hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

1. Leave the **Blueprint definition version** drop-down selection of **Published** versions on the **v1** entry. (The default is the most recently published version.)

1. Låt standardvärdet **Lås inte** vara för **Lås tilldelning**. For more information, see [Blueprints resource locking](./concepts/resource-locking.md).

   ![Locking and managed identities for the assignment](./media/create-blueprint-portal/assignment-locking-mi.png)

1. Under **Managed Identity**, leave the default of **System assigned**.

1. För prenumerationsnivåns rolltilldelning **[Användargrupp eller programnamn]: Deltagare**, så sök efter och välj en användare, en app eller grupp.

1. For the subscription level policy assignment, set **Tag Name** to **CostCenter** and the **Tag Value** to **ContosoIT**.

1. For **ResourceGroup**, provide a **Name** of **StorageAccount** and a **Location** of **East US 2** from the drop-down list.

   > [!NOTE]
   > For each artifact that you added under the resource group during blueprint definition, that artifact is indented to align with the resource group or object that you'll deploy it with.
   > Artifacts that either don't take parameters or have no parameters to be defined at assignment are listed only for contextual information.

1. On the Azure Resource Manager template **StorageAccount**, select **Standard_GRS** for the **storageAccountType** parameter.

1. Read the information box at the bottom of the page, and then select **Assign**.

## <a name="track-deployment-of-a-blueprint"></a>Spåra distributionen av en skiss

När en skiss har tilldelats till en eller flera prenumerationer händer två saker:

- The blueprint is added to the **Assigned blueprints** page for each subscription.
- The process of deploying all the artifacts defined by the blueprint begins.

Now that the blueprint has been assigned to a subscription, verify the progress of the deployment:

1. Välj **Tilldelade skisser** på sidan till vänster.

1. In the list of blueprints, right-click the one that you previously assigned and select **View assignment details**.

   ![View assignment details from the Assigned blueprints page](./media/create-blueprint-portal/view-assignment-details.png)

1. On the **Blueprint assignment** page, validate that all artifacts were successfully deployed and that there were no errors during the deployment. If errors occurred, see [Troubleshooting blueprints](./troubleshoot/general.md) for steps to determine what went wrong.

## <a name="unassign-a-blueprint"></a>Ta bort en skisstilldelning

If you no longer need a blueprint assignment, remove it from a subscription. The blueprint might have been replaced by a newer blueprint with updated patterns, policies, and designs. När en skiss tas bort blir artefakterna som tilldelats som en del av skissen kvar. Följ dessa steg om du vill ta bort en skisstilldelning:

1. Välj **Tilldelade skisser** på sidan till vänster.

1. In the list of blueprints, select the blueprint that you want to unassign. Then select the **Unassign blueprint** button at the top of the page.

1. Read the confirmation message and then select **OK**.

## <a name="delete-a-blueprint"></a>Ta bort en skiss

1. Välj **Skissdefinitioner** till vänster på sidan.

1. Right-click the blueprint that you want to delete, and select **Delete blueprint**. Then select **Yes** in the confirmation dialog box.

> [!NOTE]
> Deleting a blueprint in this method also deletes all published versions of the selected blueprint.
> To delete a single version, open the blueprint, select the **Published versions** tab, select the version that you want to delete, and then select **Delete This Version**. Also, you can't delete a blueprint until you've deleted all blueprint assignment of that blueprint definition.

## <a name="next-steps"></a>Nästa steg

- Mer information om [livscykeln för en skiss](./concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](./concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](./concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](./concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](./how-to/update-existing-assignments.md).
- Lös problem som kan uppstå vid tilldelningen av en skiss med [allmän felsökning](./troubleshoot/general.md).