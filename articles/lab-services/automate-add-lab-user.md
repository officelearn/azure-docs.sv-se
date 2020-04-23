---
title: Automatisera lägger till en labbanvändare i Azure DevTest Labs | Microsoft-dokument
description: Den här artikeln visar hur du automatiserar lägger till en användare i ett labb i Azure DevTest Labs med Azure Resource Manager-mallar, PowerShell och CLI.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 81a8c5030f716246caf3dcd8b540bb47fcaf6520
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "82023630"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>Automatisera lägger till en labbanvändare i ett labb i Azure DevTest Labs
Med Azure DevTest Labs kan du snabbt skapa självbetjäningsmiljöer för utvecklingstest med hjälp av Azure-portalen. Men om du har flera team och flera DevTest Labs-instanser kan det spara tid genom att automatisera skapandeprocessen. [Med Azure Resource Manager-mallar](https://github.com/Azure/azure-devtestlab/tree/master/Environments) kan du skapa labb, virtuella labb-datorer, anpassade avbildningar, formler och lägga till användare på ett automatiserat sätt. Den här artikeln fokuserar specifikt på att lägga till användare i en DevTest Labs-instans.

Om du vill lägga till en användare i ett labb lägger du till användaren i användarrollen **DevTest Labs** för labbet. I den här artikeln beskrivs hur du automatiserar att lägga till en användare i ett labb på något av följande sätt:

- Azure Resource Manager-mallar
- Azure PowerShell-cmdletar 
- Azure CLI.

## <a name="use-azure-resource-manager-templates"></a>Använda Azure Resource Manager-mallar
Följande exempelmall för Resource Manager anger en användare som ska läggas till i användarrollen **DevTest Labs i** ett labb. 

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

Om du tilldelar rollen i samma mall som skapar labbet, kom ihåg att lägga till ett samband mellan rolltilldelningsresursen och labbet. Mer information finns i Artikeln Definiera beroenden i Azure [Resource Manager-mallar.](../azure-resource-manager/templates/define-resource-dependency.md)

### <a name="role-assignment-resource-information"></a>Resursinformation om rolltilldelning
Rolltilldelningsresursen måste ange typ och namn.

Det första du bör tänka på är att `Microsoft.Authorization/roleAssignments` resurstypen inte är som den skulle vara för en resursgrupp.  I stället följer resurstypen `{provider-namespace}/{resource-type}/providers/roleAssignments`mönstret . I det här fallet blir `Microsoft.DevTestLab/labs/providers/roleAssignments`resurstypen .

Själva rolltilldelningsnamnet måste vara globalt unikt.  Namnet på tilldelningen använder `{labName}/Microsoft.Authorization/{newGuid}`mönstret . Det `newGuid` är ett parametervärde för mallen. Det säkerställer att rolltilldelningsnamnet är unikt. Eftersom det inte finns några mallfunktioner för att skapa GUIDs, måste du generera en GUID själv med hjälp av någon GUID generator verktyg.  

I mallen definieras namnet på rolltilldelningen av variabeln. `fullDevTestLabUserRoleName` Den exakta raden från mallen är:

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>Resursegenskaper för rolltilldelning
En rolltilldelning definierar tre egenskaper. Den behöver `roleDefinitionId` `principalId`, `scope`och .

### <a name="role-definition"></a>Rolldefinition
Rolldefinitions-ID är strängidentifieraren för den befintliga rolldefinitionen. Roll-ID:et `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}`finns i formuläret . 

Prenumerations-ID erhålls `subscription().subscriptionId` med hjälp av mallfunktionen.  

Du måste få rolldefinitionen `DevTest Labs User` för den inbyggda rollen. Om du vill hämta GUID för [användarrollen DevTest Labs](../role-based-access-control/built-in-roles.md#devtest-labs-user) kan du använda [REST API:et för rolltilldelningar](/rest/api/authorization/roleassignments) eller cmdleten [Get-AzRoleDefinition.](/powershell/module/az.resources/get-azroledefinition?view=azps-1.8.0)

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

Roll-ID definieras i variabelavsnittet `devTestLabUserRoleId`med namnet . I mallen är roll-ID:et: 11111111-0000-0000-1111111111111111111111111. 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>Huvud-ID
Huvud-ID är objekt-ID för Active Directory-användaren, gruppen eller tjänsthuvudhuvudet som du vill lägga till som labbanvändare i labbet. Mallen använder `ObjectId` parametern som parameter.

Du kan hämta ObjectId med hjälp av [Get-AzureRMADUser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0), [Get-AzureRMADGroup eller [Get-AzureRMADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) PowerShell-cmdlets. Dessa cmdlets returnerar en eller flera listor med Active Directory-objekt som har en ID-egenskap, vilket är det objekt-ID som du behöver. I följande exempel visas hur du hämtar objekt-ID för en enskild användare på ett företag.

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

Du kan också använda Azure Active Directory PowerShell-cmdlets som innehåller [Get-MsolUser,](/powershell/module/msonline/get-msoluser?view=azureadps-1.0) [Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0)och [Get-MsolServicePrincipal](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0).

### <a name="scope"></a>Omfång
Scope anger den resurs eller resursgrupp som rolltilldelningen ska gälla för. För resurser finns omfånget `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}`i formuläret : . `subscription().subscriptionId` Mallen använder funktionen för `subscription-id` att fylla `resourceGroup().name` i delen och `resource-group-name` mallfunktionen för att fylla i delen. Med hjälp av dessa funktioner innebär att labbet som du tilldelar en roll till måste finnas i den aktuella prenumerationen och samma resursgrupp som malldistributionen görs till. Den sista `resource-name`delen är namnet på labbet. Det här värdet tas emot via mallparametern i det här exemplet. 

Rollomfånget i mallen: 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>Distribuera mallen
Skapa först en parameterfil (till exempel azuredeploy.parameters.json) som skickar värden för parametrar i resource manager-mallen. 

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

Använd sedan [PowerShell-cmdleten New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0) för att distribuera Resource Manager-mallen. Följande exempelkommando tilldelar en person, grupp eller ett tjänsthuvudnamn till rollen DevTest Labs-användare för ett labb.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

Det är viktigt att notera att gruppdistributionsnamnet och rolltilldelnings-GUID måste vara unika. Om du försöker distribuera en resurstilldelning med ett icke-unikt `RoleAssignmentUpdateNotPermitted` GUID får du ett felmeddelande.

Om du planerar att använda mallen flera gånger för att lägga till flera Active Directory-objekt i användarrollen DevTest Labs för labbet kan du överväga att använda dynamiska objekt i PowerShell-kommandot. I följande exempel används cmdleten [New-Guid](/powershell/module/Microsoft.PowerShell.Utility/New-Guid?view=powershell-5.0) för att ange resursgruppsdistributionsnamnet och rolltilldelningen GUID dynamiskt.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>Använda Azure PowerShell
Som beskrivs i inledningen skapar du en ny Azure-rolltilldelning för att lägga till en användare i användarrollen **DevTest Labs** för labbet. I PowerShell gör du det med hjälp av cmdleten [New-AzureRMRoleAssignment.](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0) Denna cmdlet har många valfria parametrar för att möjliggöra flexibilitet. Den `ObjectId` `SigninName`, `ServicePrincipalName` , eller kan anges som objektet som beviljas behörigheter.  

Här är ett exempel på Azure PowerShell-kommando som lägger till en användare i användarrollen DevTest Labs i det angivna labbet.

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

Om du vill ange vilken resurs som behörigheter beviljas `ResourceName`till `ResourceType` `ResourceGroup` kan anges `scope` av en kombination av , eller av parametern. Oavsett vilken kombination av parametrar som används, ge tillräckligt med information till cmdlet för att unikt identifiera Active Directory-objektet (användaren, gruppen eller tjänstens huvudnamn), omfattning (resursgrupp eller resurs) och rolldefinition.

## <a name="use-azure-command-line-interface-cli"></a>Använda AZURE Command Line Interface (CLI)
I Azure CLI görs det med kommandot att lägga `az role assignment create` till en labbanvändare i ett labb. Mer information om Azure CLI-cmdlets finns i [Hantera åtkomst till Azure-resurser med RBAC och Azure CLI](../role-based-access-control/role-assignments-cli.md).

Objektet som beviljas åtkomst kan anges av `objectId` `signInName`parametrarna , , `spn` . Labbet som objektet beviljas åtkomst till kan `scope` identifieras med url:en `resource-type`eller `resource-group` en kombination av parametrarna `resource-name`, och parametrarna.

Följande Azure CLI-exempel visar hur du lägger till en person i användarrollen DevTest Labs för det angivna labbet.  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa och hantera virtuella datorer med DevTest Labs med Hjälp av Azure CLI](devtest-lab-vmcli.md)
- [Skapa en virtuell dator med DevTest Labs med Azure PowerShell](devtest-lab-vm-powershell.md)
- [Använda kommandoradsverktyg för att starta och stoppa virtuella Azure DevTest Labs-datorer](use-command-line-start-stop-virtual-machines.md)

