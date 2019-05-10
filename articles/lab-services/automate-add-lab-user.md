---
title: Automatisera tillägg av användare labb i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du automatiserar om du lägger till ett labb-användare till ett labb i Azure DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2019
ms.author: spelluru
ms.openlocfilehash: 0eed874d405fcf99241a702292f8ceadae6c5a07
ms.sourcegitcommit: 1d257ad14ab837dd13145a6908bc0ed7af7f50a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65502035"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>Automatisera tillägg av lab användare i ett labb i Azure DevTest Labs
Azure DevTest Labs kan du snabbt skapa självbetjäning utvecklings-och testmiljöer med hjälp av Azure portal. Men om du har flera team och flera DevTest Labs-instanser kan kan automatisera processen spara tid. [Azure Resource Manager-mallar](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) kan du skapa labb, labb virtuella datorer, anpassade avbildningar, formler och lägga till användare i obevakat läge. Den här artikeln fokuserar särskilt på att lägga till användare i en labb-instans.

Om du vill lägga till en användare till ett labb, du lägger till användare till den **DevTest Labs-användare** rollen för övningen. Den här artikeln visar hur du automatiserar att lägga till en användare i ett labb med hjälp av något av följande sätt:

- Azure Resource Manager-mallar
- Azure PowerShell-cmdletar 
- Azure CLI.

## <a name="use-azure-resource-manager-templates"></a>Använda Azure Resource Manager-mallar
Följande exempel Resource Manager-mallen anger en användare som ska läggas till i **DevTest Labs-användare** rollen för ett labb. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The objectId of the user, group, or service principal for the role."
      }
    },
    "labName": {
      "type": "string",
      "metadata": {
        "description": "The name of the lab instance to be created."
      }
    },
    "roleAssignmentGuid": {
      "type": "string",
      "metadata": {
        "description": "Guid to use as the name for the role assignment."
      }
    }
  },
  "variables": {
    "devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
    "fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]",
    "roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
  },
  "resources": [
    {
      "apiVersion": "2016-05-15",
      "type": "Microsoft.DevTestLab/labs",
      "name": "[parameters('labName')]",
      "location": "[resourceGroup().location]"
    },
    {
      "apiVersion": "2016-07-01",
      "type": "Microsoft.DevTestLab/labs/providers/roleAssignments",
      "name": "[variables('fullDevTestLabUserRoleName')]",
      "properties": {
        "roleDefinitionId": "[variables('devTestLabUserRoleId')]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('roleScope')]"
      },
      "dependsOn": [
        "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
      ]
    }
  ]
}

```

Om du tilldelar rollen i samma mall som skapat labbet, kan du komma ihåg att lägga till ett beroende mellan datorrollresurs för tilldelning och labbet. Mer information finns i [definiera beroenden i Azure Resource Manager-mallar](/azure-resource-manager/resource-group-define-dependencies.md) artikeln.

### <a name="role-assignment-resource-information"></a>Rollen tilldelning resursinformation
Tilldelningen datorrollresurs måste ange typ och namn.

Det första att notera är att typen för resursen inte är `Microsoft.Authorization/roleAssignments` eftersom det skulle vara för en resursgrupp.  I stället resurstypen följer mönstret `{provider-namespace}/{resource-type}/providers/roleAssignments`. I det här fallet resurstypen blir `Microsoft.DevTestLab/labs/providers/roleAssignments`.

Rolltilldelningsnamnet själva måste vara globalt unikt.  Namnet på tilldelningen använder mönstret `{labName}/Microsoft.Authorization/{newGuid}`. Den `newGuid` är ett parametervärde för mallen. Det innebär att rolltilldelningsnamnet är unikt. Eftersom det finns inga funktioner för mall för att skapa GUID, måste du generera ett GUID själv med hjälp av GUID-generatorn.  

I mallen definieras namnet för rolltilldelningen av den `fullDevTestLabUserRoleName` variabeln. Den exakta raden från mallen är:

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>Rolltilldelningens resursegenskaper
En rolltilldelning själva definierar tre egenskaper. Den måste den `roleDefinitionId`, `principalId`, och `scope`.

### <a name="role-definition"></a>Rolldefinition
Rolldefinitions-ID är den sträng-identifieraren för den befintliga rolldefinitionen för. Rollen med ID: T är i formatet `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}`. 

Prenumerationen ID hämtas med hjälp av `subscription().subscriptionId` mallfunktionen.  

Du måste hämta rolldefinitionen för den `DevTest Labs User` inbyggd roll. Hämta GUID för den [DevTest Labs-användare](../role-based-access-control/built-in-roles.md#devtest-labs-user) roll, som du kan använda den [rollen tilldelningar REST API](/rest/api/authorization/roleassignments) eller [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition?view=azps-1.8.0) cmdlet.

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

Roll-ID definieras i variables-avsnittet och med namnet `devTestLabUserRoleId`. Roll-ID är inställd i mallen: 111111111-0000-0000-11111111111111111. 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>Huvudkonto-ID
Ägar-ID är objekt-ID för den Active Directory-användare, grupp eller tjänstens huvudnamn som du vill lägga till som en lab-användare till labbet. Mallen använder den `ObjectId` som en parameter.

Du kan hämta ObjectId med hjälp av den [Get-AzureRMADUser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0), [Get-AzureRMADGroup eller [Get-AzureRMADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) PowerShell-cmdletar. Dessa cmdletar returnerar en enda eller en lista över Active Directory-objekt som har en ID-egenskap som är objekt-ID som du behöver. I följande exempel visar hur du hämtar objekt-ID för en enskild användare på ett företag.

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

Du kan också använda Azure Active Directory PowerShell-cmdlets som innehåller [Get-MsolUser](/powershell/module/msonline/get-msoluser?view=azureadps-1.0), [Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0), och [Get-MsolServicePrincipal](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0).

### <a name="scope"></a>Scope
Omfång anger den resurs eller resursgrupp som rolltilldelningen ska användas. För resurser, omfånget är i formatet: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}`. Mallen använder den `subscription().subscriptionId` funktionen att fylla i den `subscription-id` del och `resourceGroup().name` mallfunktionen att fylla i den `resource-group-name` del. Med hjälp av dessa funktioner innebär att labbet som du tilldelar en roll måste finnas i den aktuella prenumerationen och samma resursgrupp som malldistributionen görs. Den sista delen `resource-name`, är namnet på labbet. Det här värdet tas emot via mallparameter i det här exemplet. 

Rollomfång i mallen: 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>Distribuera mallen
Börja med att skapa en parameterfil (till exempel: azuredeploy.parameters.json) som skickar värden för parametrar i Resource Manager-mallen. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "value": "11111111-1111-1111-1111-111111111111"
    },
    "labName": {
      "value": "MyLab"
    },
    "roleAssignmentGuid": {
      "value": "22222222-2222-2222-2222-222222222222"
    }
  }
}
```

Använd sedan den [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0) PowerShell-cmdlet för att distribuera Resource Manager-mallen. Följande exempelkommando tilldelar en person, grupp eller ett huvudnamn för tjänsten till rollen DevTest Labs-användare för ett labb.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

Det är viktigt att Observera att distributionen namn och rollen grupptilldelningen GUID måste vara unikt. Om du försöker distribuera en resurstilldelning med ett icke-unikt GUID så får du en `RoleAssignmentUpdateNotPermitted` fel.

Överväg att använda dynamiska objekt i ett PowerShell-kommando om du planerar att använda mallen flera gånger för att lägga till flera Active Directory-objekt i rollen DevTest Labs-användare för ditt labb. I följande exempel används den [New-Guid](/powershell/module/Microsoft.PowerShell.Utility/New-Guid?view=powershell-5.0) cmdlet för att ange grupp distribution namn och rollen resurstilldelningen GUID dynamiskt.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>Använda Azure PowerShell
Som beskrivs i inledningen kan du skapa en ny Azure rolltilldelning för att lägga till en användare till den **DevTest Labs-användare** rollen för övningen. I PowerShell kan du göra det med hjälp av den [New-AzureRMRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0) cmdlet. Denna cmdlet har många valfria parametrar för att tillåta flexibilitet. Den `ObjectId`, `SigninName`, eller `ServicePrincipalName` kan anges som beviljas behörigheter-objektet.  

Här är en Azure PowerShell-kommando som lägger tillför en användare i rollen DevTest Labs-användare i den angivna testmiljön.

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

Ange resursen som behörigheter som ska beviljas kan anges med en kombination av `ResourceName`, `ResourceType`, `ResourceGroup` eller den `scope` parametern. Valfri kombination av parametrar används kan innehålla all information till cmdleten för att unikt identifiera Active Directory-objekt (användare, grupp eller tjänstens huvudnamn), omfång (resursgruppen eller resursen) och rolldefinition.

## <a name="use-azure-command-line-interface-cli"></a>Använd Azure kommandoradsgränssnitt (CLI)
I Azure CLI att lägga till en labs-användare i ett labb görs med hjälp av den `az role assignment create` kommando. Läs mer om Azure CLI-cmdletar, [hantera åtkomst till Azure-resurser med RBAC och Azure CLI](../role-based-access-control/role-assignments-cli.md).

Det objekt som är de ges tillgång kan anges med den `objectId`, `signInName`, `spn` parametrar. Labbet som objektet är som har behörighet kan identifieras av den `scope` url eller en kombination av de `resource-name`, `resource-type`, och `resource-group` parametrar.

I följande Azure CLI-exempel visar hur du lägger till en person till rollen DevTest Labs-användare för den angivna testmiljön.  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa och hantera virtuella datorer med DevTest Labs med Azure CLI](devtest-lab-vmcli.md)
- [Skapa en virtuell dator med DevTest Labs med Azure PowerShell](devtest-lab-vm-powershell.md)
- [Använd kommandoradsverktygen för att starta och stoppa virtuella datorer i Azure DevTest Labs](use-command-line-start-stop-virtual-machines.md)

