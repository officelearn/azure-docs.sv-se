---
title: 'Quickstart: Create a blueprint with PowerShell'
description: In this quickstart, you use Azure Blueprints to create, define, and deploy artifacts using the PowerShell.
ms.date: 11/21/2019
ms.topic: quickstart
ms.openlocfilehash: ed337fa040804159dae3194de1befc43ff2b698f
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327470"
---
# <a name="quickstart-define-and-assign-an-azure-blueprint-with-powershell"></a>Quickstart: Define and Assign an Azure Blueprint with PowerShell

När du skapar och tilldelar skisser kan definitionen av vanliga mönster utveckla återanvändbara och snabbt distribuerbara konfigurationer baserade på Resource Manager-mallar, principer, säkerhet med mera. I den här självstudien får du lära dig att använda Azure Blueprint för att utföra några av de vanliga uppgifter som rör generering, publicering och tilldelning av en skiss i din organisation. Du lär dig till exempel att:

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

If it isn't already installed, follow the instructions in [Add the Az.Blueprint module](./how-to/manage-assignments-ps.md#add-the-azblueprint-module) to install and validate the **Az.Blueprint** module from the PowerShell Gallery.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-blueprint"></a>Skapa en skiss

Det första steget när du definierar ett standardmönster för efterlevnad är att skapa en skiss från de tillgängliga resurserna. Vi skapar en skiss med namnet ”MyBlueprint” för att konfigurera roll- och principtilldelningar för prenumerationen. Sedan lägger vi till en resursgrupp, en Resource Manager-mall och en rolltilldelning för resursgruppen.

> [!NOTE]
> When using PowerShell, the _blueprint_ object is created first. För varje _artefakt_ som ska läggas till som har parametrar, måste parametrarna definieras i förväg i den första _skissen_.

1. Skapa det första _skissobjektet_. The **BlueprintFile** parameter takes a JSON file which includes properties about the blueprint, any resource groups to create, and all of the blueprint level parameters. Parametrarna anges vid tilldelning och används av artefakterna som lagts till i senare steg.

   - JSON file - blueprint.json

     ```json
     {
         "properties": {
             "description": "This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.",
             "targetScope": "subscription",
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
                         "displayName": "storage account type.",
                         "description": null
                     }
                 },
                 "tagName": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The name of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "tagValue": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The value of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "contributors": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Contributor role at the subscription",
                         "strongType": "PrincipalId"
                     }
                 },
                 "owners": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Owner role at the resource group",
                         "strongType": "PrincipalId"
                     }
                 }
             },
             "resourceGroups": {
                 "storageRG": {
                     "description": "Contains the resource template deployment and a role assignment."
                 }
             }
         }
     }
     ```

   - PowerShell command

     ```azurepowershell-interactive
     # Login first with Connect-AzAccount if not using Cloud Shell

     # Get a reference to the new blueprint object, we'll use it in subsequent steps
     $blueprint = New-AzBlueprint -Name 'MyBlueprint' -BlueprintFile .\blueprint.json
     ```

     > [!NOTE]
     > Use the filename _blueprint.json_ when creating your blueprint definitions programmatically.
     > This file name is used when calling [Import-AzBlueprintWithArtifact](/powershell/module/az.blueprint/import-azblueprintwithartifact).

     The blueprint object is created in the default subscription by default. To specify the management group, use parameter **ManagementGroupId**. To specify the subscription, use parameter **SubscriptionId**.

1. Lägg till rolltilldelningen för prenumerationen. The **ArtifactFile** defines the _kind_ of artifact, the properties align to the role definition identifier, and the principal identities are passed as an array of values. I exemplet nedan konfigureras huvudidentiteterna som beviljas den angivna rollen till en parameter som anges under skisstilldelningen. I det här exemplet används den inbyggda rollen _Deltagare_ med ett GUID på `b24988ac-6180-42a0-ab88-20f7382dd24c`.

   - JSON file - \artifacts\roleContributor.json

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
             "principalIds": "[parameters('contributors')]"
         }
     }
     ```

   - PowerShell command

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'roleContributor' -ArtifactFile .\artifacts\roleContributor.json
     ```

1. Lägg till principtilldelning för prenumerationen. The **ArtifactFile** defines the _kind_ of artifact, the properties that align to a policy or initiative definition, and configures the policy assignment to use the defined blueprint parameters to configure during blueprint assignment. I det här exemplet används den inbyggda principen _Lägg till tagg och standardvärdet i resursgrupper_ med ett GUID på `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - JSON file - \artifacts\policyTags.json

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "displayName": "Apply tag and its default value to resource groups",
             "description": "Apply tag and its default value to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "[parameters('tagName')]"
                 },
                 "tagValue": {
                     "value": "[parameters('tagValue')]"
                 }
             }
         }
     }
     ```

   - PowerShell command

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'policyTags' -ArtifactFile .\artifacts\policyTags.json
     ```

1. Lägg till en till principtilldelning för lagringstaggen (genom att återanvända parametern _storageAccountType_) för prenumerationen. Den här ytterligare principtilldelningsartefakten visar att en parameter som definierats för skissen kan användas av mer än en artefakt. I exemplet används **storageAccountType** för att ange en tagg på resursgruppen. Det här värdet anger information om lagringskontot som skapas i nästa steg. I det här exemplet används den inbyggda principen _Lägg till tagg och standardvärdet i resursgrupper_ med ett GUID på `49c88fc8-6fd1-46fd-a676-f12d1d3a4c71`.

   - JSON file - \artifacts\policyStorageTags.json

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "displayName": "Apply storage tag to resource group",
             "description": "Apply storage tag and the parameter also used by the template to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "StorageType"
                 },
                 "tagValue": {
                     "value": "[parameters('storageAccountType')]"
                 }
             }
         }
     }
     ```

   - PowerShell command

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'policyStorageTags' -ArtifactFile .\artifacts\policyStorageTags.json
     ```

1. Lägg till en mall under resursgruppen. The **TemplateFile** for a Resource Manager template includes the normal JSON component of the template. Mallen återanvänder även skissparametrarna **storageAccountType**, **tagName** och **tagValue** genom att dem till mallen. The blueprint parameters are available to the template by using parameter **TemplateParameterFile** and inside the template JSON that key-value pair is used to inject the value. The blueprint and template parameter names could be the same.

   - JSON Azure Resource Manager template file - \artifacts\templateStorage.json

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "type": "string",
                 "metadata": {
                     "description": "Storage Account type"
                 }
             },
             "tagNameFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag name from blueprint"
                 }
             },
             "tagValueFromBP": {
                 "type": "string",
                 "defaultValue": "NotSet",
                 "metadata": {
                     "description": "Tag value from blueprint"
                 }
             }
         },
         "variables": {
             "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
         },
         "resources": [{
             "type": "Microsoft.Storage/storageAccounts",
             "name": "[variables('storageAccountName')]",
             "apiVersion": "2016-01-01",
             "tags": {
                 "[parameters('tagNameFromBP')]": "[parameters('tagValueFromBP')]"
             },
             "location": "[resourceGroup().location]",
             "sku": {
                 "name": "[parameters('storageAccountTypeFromBP')]"
             },
             "kind": "Storage",
             "properties": {}
         }],
         "outputs": {
             "storageAccountSku": {
                 "type": "string",
                 "value": "[variables('storageAccountName')]"
             }
         }
     }
     ```

   - JSON Azure Resource Manager template parameter file - \artifacts\templateStorageParams.json

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "storageAccountTypeFromBP": {
                 "value": "[parameters('storageAccountType')]"
             },
             "tagNameFromBP": {
                 "value": "[parameters('tagName')]"
             },
             "tagValueFromBP": {
                 "value": "[parameters('tagValue')]"
             }
         }
     }
     ```

   - PowerShell command

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Type TemplateArtifact -Name 'templateStorage' -TemplateFile .\artifacts\templateStorage.json -TemplateParameterFile .\artifacts\templateStorageParams.json -ResourceGroupName storageRG
     ```

1. Lägg till rolltilldelning under resursgruppen. På liknande sätt som i föregående rolltilldelningspost använder exemplet nedan definitionsidentifieraren för rollen **Ägare** och tilldelar den en annan parameter från skissen. I det här exemplet används den inbyggda rollen _Ägare_ med ett GUID på `8e3af657-a8ff-443c-a75c-2fe8c4bcb635`.

   - JSON file - \artifacts\roleOwner.json

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "resourceGroup": "storageRG",
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
             "principalIds": "[parameters('owners')]"
         }
     }
     ```

   - PowerShell command

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintArtifact -Blueprint $blueprint -Name 'roleOwner' -ArtifactFile .\artifacts\roleOwner.json
     ```

## <a name="publish-a-blueprint"></a>Publicera en skiss

Nu när artefakter har lagts till i skissen är det dags att publicera den. När den har publicerats kan den tilldelas till en prenumeration.

```azurepowershell-interactive
# Use the reference to the new blueprint object from the previous steps
Publish-AzBlueprint -Blueprint $blueprint -Version '{BlueprintVersion}'
```

Värdet för `{BlueprintVersion}` är en sträng med bokstäver, siffror och bindestreck (inga blanksteg eller andra specialtecken) med en högsta längd på 20 tecken. Använd något unikt och beskrivande som **v20180622-135541**.

## <a name="assign-a-blueprint"></a>Tilldela en skiss

Once a blueprint is published using PowerShell, it's assignable to a subscription. Tilldela skissen som du skapade till någon av prenumerationerna i din hierarki med hanteringsgrupper. Om skissen sparas till en prenumeration kan den endast tilldelas till den prenumerationen. The **Blueprint** parameter specifies the blueprint to assign. To provide name, location, identity, lock, and blueprint parameters, use the matching PowerShell parameters on the `New-AzBlueprintAssignment` cmdlet or provide them in the **AssignmentFile** parameter JSON file.

1. Kör skissdistributionen genom att tilldela den till en prenumeration. As the **contributors** and **owners** parameters require an array of objectIds of the principals to be granted the role assignment, use [Azure Active Directory Graph API](../../active-directory/develop/active-directory-graph-api.md) for gathering the objectIds for use in the **AssignmentFile** for your own users, groups, or service principals.

   - JSON file - blueprintAssignment.json

     ```json
     {
         "properties": {
             "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint",
             "resourceGroups": {
                 "storageRG": {
                     "name": "StorageAccount",
                     "location": "eastus2"
                 }
             },
             "parameters": {
                 "storageAccountType": {
                     "value": "Standard_GRS"
                 },
                 "tagName": {
                     "value": "CostCenter"
                 },
                 "tagValue": {
                     "value": "ContosoIT"
                 },
                 "contributors": {
                     "value": [
                         "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                         "38833b56-194d-420b-90ce-cff578296714"
                     ]
                 },
                 "owners": {
                     "value": [
                         "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                         "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                     ]
                 }
             }
         },
         "identity": {
             "type": "systemAssigned"
         },
         "location": "westus"
     }
     ```

   - PowerShell command

     ```azurepowershell-interactive
     # Use the reference to the new blueprint object from the previous steps
     New-AzBlueprintAssignment -Blueprint $blueprint -Name 'assignMyBlueprint' -AssignmentFile .\blueprintAssignment.json
     ```

   - Användartilldelad hanterad identitet

     En skisstilldelning kan även använda en [användartilldelad hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md).
     In this case, the **identity** portion of the JSON assignment file changes as follows. Replace `{tenantId}`, `{subscriptionId}`, `{yourRG}`, and `{userIdentity}` with your tenantId, subscriptionId, resource group name, and the name of your user-assigned managed identity, respectively.

     ```json
     "identity": {
         "type": "userAssigned",
         "tenantId": "{tenantId}",
         "userAssignedIdentities": {
             "/subscriptions/{subscriptionId}/resourceGroups/{yourRG}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{userIdentity}": {}
         }
     },
     ```

     Den **användartilldelade hanterade identiteten** kan finnas i någon av de prenumerationer och resursgrupper som användaren som tilldelade skissen har behörighet till.

     > [!IMPORTANT]
     > Skisser hanterar inte den användartilldelade hanterade identiteten. Användarna är ansvariga för att tilldela tillräckligt med roller och behörigheter, för att inte skisstilldelningen ska misslyckas.

## <a name="unassign-a-blueprint"></a>Ta bort en skisstilldelning

Du kan ta bort en skiss från en prenumeration. Borttagningen görs ofta när artefaktresurserna inte längre behövs. När en skiss tas bort blir artefakterna som tilldelats som en del av skissen kvar. To remove a blueprint assignment, use the `Remove-AzBlueprintAssignment` cmdlet:

assignMyBlueprint

```azurepowershell-interactive
Remove-AzBlueprintAssignment -Name 'assignMyBlueprint'
```

## <a name="next-steps"></a>Nästa steg

In this quickstart, you've created, assigned, and removed a blueprint with PowerShell. To learn more about Azure Blueprints, continue to the blueprint lifecycle article.

> [!div class="nextstepaction"]
> [Learn about the blueprint lifecycle](./concepts/lifecycle.md)