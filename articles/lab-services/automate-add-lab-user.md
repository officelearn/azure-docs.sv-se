---
title: Automatisera tillägg av en labb användare i Azure DevTest Labs | Microsoft Docs
description: Den här artikeln visar hur du automatiserar hur du lägger till en användare i ett labb i Azure DevTest Labs att använda Azure Resource Manager mallar, PowerShell och CLI.
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
ms.openlocfilehash: 70a6359923734c83590d4677bb2c93966c925d14
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718147"
---
# <a name="automate-adding-a-lab-user-to-a-lab-in-azure-devtest-labs"></a>Automatisera tillägg av en labb användare i ett labb i Azure DevTest Labs
Azure DevTest Labs gör att du snabbt kan skapa självbetjänings utvecklings-och test miljöer med hjälp av Azure Portal. Men om du har flera team och flera DevTest Labs-instanser kan du spara tid genom att automatisera skapandet av processen. Med [Azure Resource Manager mallar](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates) kan du skapa labb, virtuella labb datorer, anpassade bilder, formler och lägga till användare på ett automatiserat sätt. Den här artikeln fokuserar särskilt på att lägga till användare till en DevTest Labs-instans.

Om du vill lägga till en användare i ett labb lägger du till användaren i **användar rollen DevTest Labs** för labbet. Den här artikeln visar hur du automatiserar hur du lägger till en användare i ett labb på något av följande sätt:

- Azure Resource Manager-mallar
- Azure PowerShell-cmdletar 
- Azure CLI.

## <a name="use-azure-resource-manager-templates"></a>Använda Azure Resource Manager-mallar
Följande exempel på en Resource Manager-mall anger en användare som ska läggas till i **användar rollen DevTest Labs** i ett labb. 

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

Kom ihåg att lägga till ett beroende mellan roll tilldelnings resursen och labbet om du tilldelar rollen i samma mall som skapar labbet. Mer information finns i [definiera beroenden i Azure Resource Manager Retemplates](../azure-resource-manager/templates/define-resource-dependency.md) -artikeln.

### <a name="role-assignment-resource-information"></a>Resurs information om roll tilldelning
Roll tilldelnings resursen måste ange typ och namn.

Det första du ska notera är att typen för resursen inte `Microsoft.Authorization/roleAssignments` som den skulle vara för en resurs grupp.  Resurs typen följer i stället mönstret `{provider-namespace}/{resource-type}/providers/roleAssignments`. I det här fallet kommer resurs typen att `Microsoft.DevTestLab/labs/providers/roleAssignments`.

Roll tilldelnings namnet måste vara globalt unikt.  Namnet på tilldelningen använder mönster `{labName}/Microsoft.Authorization/{newGuid}`. `newGuid` är ett parameter värde för mallen. Det säkerställer att roll tilldelningens namn är unikt. Eftersom det inte finns några mallar för att skapa GUID måste du skapa ett GUID själv genom att använda valfritt verktyg för GUID-Generator.  

I mallen definieras namnet på roll tilldelningen av variabeln `fullDevTestLabUserRoleName`. Den exakta raden från mallen är:

```json
"fullDevTestLabUserRoleName": "[concat(parameters('labName'), '/Microsoft.Authorization/', parameters('roleAssignmentGuid'))]"
```


### <a name="role-assignment-resource-properties"></a>Resurs egenskaper för roll tilldelning
En roll tilldelning definierar tre egenskaper. Den behöver `roleDefinitionId`, `principalId`och `scope`.

### <a name="role-definition"></a>Roll definition
Roll Definitions-ID: t är sträng identifieraren för den befintliga roll definitionen. Roll-ID: t har formatet `/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{role-definition-id}`. 

Prenumerations-ID: t erhålls med hjälp av `subscription().subscriptionId` Template-funktionen.  

Du måste hämta roll definitionen för den `DevTest Labs User` inbyggda rollen. Om du vill hämta GUID för [användar rollen DevTest Labs](../role-based-access-control/built-in-roles.md#devtest-labs-user) kan du använda [roll tilldelningarna REST API](/rest/api/authorization/roleassignments) eller cmdleten [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition?view=azps-1.8.0) .

```powershell
$dtlUserRoleDefId = (Get-AzRoleDefinition -Name "DevTest Labs User").Id
```

Roll-ID: t definieras i avsnittet Variables och heter `devTestLabUserRoleId`. I mallen är roll-ID: t inställt på: 111111111-0000-0000-11111111111111111. 

```json
"devTestLabUserRoleId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/111111111-0000-0000-11111111111111111')]",
```

### <a name="principal-id"></a>Ägar-ID
Huvud-ID är objekt-ID: t för den Active Directory användare, grupp eller tjänstens huvud namn som du vill lägga till som labb användare i labbet. Mallen använder `ObjectId` som en parameter.

Du kan hämta ObjectId med hjälp av cmdletarna [Get-AzureRMADUser](/powershell/module/azurerm.resources/get-azurermaduser?view=azurermps-6.13.0), [Get-AzureRMADGroup eller [Get-AzureRMADServicePrincipal](/powershell/module/azurerm.resources/get-azurermadserviceprincipal?view=azurermps-6.13.0) PowerShell. Dessa cmdletar returnerar en eller flera listor med Active Directory objekt som har en ID-egenskap, vilket är det objekt-ID som du behöver. I följande exempel visas hur du hämtar objekt-ID för en enskild användare i ett företag.

```powershell
$userObjectId = (Get-AzureRmADUser -UserPrincipalName ‘email@company.com').Id
```

Du kan också använda de Azure Active Directory PowerShell-cmdlets som innehåller [Get-MsolUser](/powershell/module/msonline/get-msoluser?view=azureadps-1.0), [Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0)och [Get-MsolServicePrincipal](/powershell/module/msonline/get-msolserviceprincipal?view=azureadps-1.0).

### <a name="scope"></a>Omfång
Omfattning anger den resurs eller resurs grupp som roll tilldelningen ska gälla för. För resurser är omfånget i formatet: `/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/{provider-namespace}/{resource-type}/{resource-name}`. Mallen använder funktionen `subscription().subscriptionId` för att fylla i `subscription-id`-delen och funktionen `resourceGroup().name`-mall för att fylla i `resource-group-name`s delen. Med hjälp av dessa funktioner kan du använda de labb som du tilldelar en roll i den aktuella prenumerationen och samma resurs grupp som mall distributionen görs till. Den sista delen `resource-name`är namnet på labbet. Det här värdet tas emot via mallparameter i det här exemplet. 

Roll omfånget i mallen: 

```json
"roleScope": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.DevTestLab/labs/', parameters('labName'))]"
```

### <a name="deploying-the-template"></a>Distribuera mallen
Börja med att skapa en parameter fil (till exempel: azuredeploy. Parameters. JSON) som skickar värden för parametrar i Resource Manager-mallen. 

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

Använd sedan PowerShell-cmdleten [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment?view=azurermps-6.13.0) för att distribuera Resource Manager-mallen. I följande exempel kommando tilldelas en person, grupp eller ett huvud namn för tjänsten till användar rollen DevTest Labs för ett labb.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateParameterFile .\azuredeploy.parameters.json -TemplateFile .\azuredeploy.json
```

Det är viktigt att Observera att grupp distributions namnet och roll tilldelningens GUID måste vara unikt. Om du försöker distribuera en resurs tilldelning med ett GUID som inte är unikt får du ett `RoleAssignmentUpdateNotPermitted` fel.

Om du planerar att använda mallen flera gånger för att lägga till flera Active Directory objekt i användar rollen DevTest Labs för ditt labb, bör du överväga att använda dynamiska objekt i PowerShell-kommandot. I följande exempel används cmdleten [New-GUID](/powershell/module/Microsoft.PowerShell.Utility/New-Guid?view=powershell-5.0) för att ange distributions namn och roll tilldelnings-GUID dynamiskt för resurs gruppen.

```powershell
New-AzureRmResourceGroupDeployment -Name "MyLabResourceGroup-$(New-Guid)" -ResourceGroupName 'MyLabResourceGroup' -TemplateFile .\azuredeploy.json -roleAssignmentGuid "$(New-Guid)" -labName "MyLab" -principalId "11111111-1111-1111-1111-111111111111"
```

## <a name="use-azure-powershell"></a>Använda Azure PowerShell
Som beskrivs i introduktionen skapar du en ny roll tilldelning i Azure för att lägga till en användare i **användar rollen DevTest Labs** för labbet. I PowerShell gör du det med hjälp av cmdleten [New-AzureRMRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment?view=azurermps-6.13.0) . Denna cmdlet har många valfria parametrar som gör det möjligt för flexibiliteten. `ObjectId`, `SigninName`eller `ServicePrincipalName` kan anges som det objekt som beviljas behörigheter.  

Här är ett exempel på Azure PowerShell kommandot som lägger till en användare i användar rollen DevTest Labs i det angivna labbet.

```powershell
New-AzureRmRoleAssignment -UserPrincipalName <email@company.com> -RoleDefinitionName 'DevTest Labs User' -ResourceName '<Lab Name>' -ResourceGroupName '<Resource Group Name>' -ResourceType 'Microsoft.DevTestLab/labs'
```

För att ange den resurs som behörigheter beviljas till kan anges med en kombination av `ResourceName`, `ResourceType`, `ResourceGroup` eller av parametern `scope`. Oavsett vilken kombination av parametrar som används, ger du tillräckligt med information till cmdleten för att unikt identifiera Active Directory-objektet (användare, grupp eller tjänstens huvud namn), omfång (resurs grupp eller resurs) och roll definition.

## <a name="use-azure-command-line-interface-cli"></a>Använda kommando rads gränssnittet i Azure (CLI)
I Azure CLI gör du det genom att lägga till en labb användare i ett labb med hjälp av kommandot `az role assignment create`. Mer information om Azure CLI-cmdlets finns i [Hantera åtkomst till Azure-resurser med RBAC och Azure CLI](../role-based-access-control/role-assignments-cli.md).

Objektet som beviljas åtkomst kan anges av `objectId``signInName``spn` parametrar. Labbet som objektet beviljas åtkomst till kan identifieras av `scope` URL eller en kombination av parametrarna `resource-name`, `resource-type`och `resource-group`.

Följande Azure CLI-exempel visar hur du lägger till en person i användar rollen DevTest Labs för det angivna labbet.  

```azurecli
az role assignment create --roleName "DevTest Labs User" --signInName <email@company.com> -–resource-name "<Lab Name>" --resource-type “Microsoft.DevTestLab/labs" --resource-group "<Resource Group Name>"
```

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa och hantera virtuella datorer med DevTest Labs med Azure CLI](devtest-lab-vmcli.md)
- [Skapa en virtuell dator med DevTest Labs med Azure PowerShell](devtest-lab-vm-powershell.md)
- [Använd kommando rads verktyg för att starta och stoppa Azure DevTest Labs virtuella datorer](use-command-line-start-stop-virtual-machines.md)

