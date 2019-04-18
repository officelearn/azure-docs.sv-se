---
title: Exempel – granska om program inte är installerad på virtuella Linux-datorer
description: Det här exemplet gäst principkonfigurationen initiativ och definitioner granska om de angivna programmen inte är installerade i Linux-datorer.
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 03/18/2019
ms.author: dacoulte
ms.openlocfilehash: b432d8557c4244d58c23e7b068874dd747f6249f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59256472"
---
# <a name="sample---audit-if-specified-applications-are-not-installed-inside-linux-vms"></a>Exempel – granska om angivna program inte är installerade i virtuella Linux-datorer

Det här initiativet gäst principkonfigurationen granskar att det angivna programmet är installerad i Linux-datorer. ID för den här inbyggda initiativ är `/providers/Microsoft.Authorization/policySetDefinitions/c937dcb4-4398-4b39-8d63-4a6be432252e`.

> [!IMPORTANT]
> Alla gäster Configuration initiativen består av **granska** och **deployIfNotExists** principdefinitioner. Tilldela bara en av principdefinitionerna orsaka gäst konfiguration inte ska fungera korrekt.

Du kan tilldela det här exemplet med:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="components-of-the-initiative"></a>Komponenter i initiativ

Detta [gäst Configuration](../concepts/guest-configuration.md) initiativ består av följande principer:

- [granska](#audit-definition) -gransknings-och att ett program är installerat på virtuella Linux-datorer
  - ID: `/providers/Microsoft.Authorization/policyDefinitions/fee5cb2b-9d9b-410e-afe3-2902d90d0004`
- [deployIfNotExists](#deployIfNotExists-definition) -distribuera VM-tillägget för att granska att ett program är installerat på virtuella Linux-datorer
  - ID: `/providers/Microsoft.Authorization/policyDefinitions/4d1c04de-2172-403f-901b-90608c35c721`

### <a name="initiative-definition"></a>Initiativdefinition

Initiativet har skapats genom att gå med i **granska** och **deployIfNotExists** definitioner tillsammans och [initiativ parametrar](#initiative-parameters). Det här är JSON i definitionen.

[!code-json[initiative-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/azurepolicyset.json "Initiative definition (JSON)")]

### <a name="initiative-parameters"></a>Initiativparametrar

| Namnet | Typ || Beskrivning av | |---|---|| ---| | applicationName | Strängen | Programnamn. Exempel: ”python”, ”powershell” eller en kommaavgränsad lista, till exempel ”python, powershell”. Använd \* för matchning med jokertecken, t.ex ' power\*'. |

När du skapar en tilldelning via PowerShell eller Azure CLI går det att skicka parametervärdena som JSON antingen i en sträng eller via en fil med hjälp av `-PolicyParameter` (PowerShell) eller `--params` (Azure CLI).
PowerShell stödjer även `-PolicyParameterObject`, vilket kräver att det till cmdlet skickas en Name/Value-hashtabell där **Name** (Namn) är parameternamnet och **Value** (Värde) är det enskilda värde eller den matris med värden som skickas under tilldelningen.

I det här exemplet-parametern, installation av program _python_ och _powershell_ granskas.

```json
{
    "applicationName": {
        "value": "python,powershell"
    }
}
```

Endast den **deployIfNotExists** principdefinition använder initiativparametrar.

### <a name="audit-definition"></a>Granska definitionen

JSON definierar reglerna för den **granska** principdefinitionen.

[!code-json[audit-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json "audit policy rules (JSON)")]

### <a name="deployifnotexists-definition"></a>deployIfNotExists-definition

JSON definierar reglerna för den **deployIfNotExists** principdefinitionen.

[!code-json[deployIfNotExists-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json "deployIfNotExists policy rules (JSON)")]

Den **deployIfNotExists** principdefinitionen definierar Azure-avbildningarna principen har verifierats på:

|Utgivare |Erbjudande |SKU |
|-|-|-|
|OpenLogic |CentOS\* |Alla utom 6\* |
|Redhat |RHEL |Alla utom 6\* |
|Redhat |osa | Alla |
|credativ |Debian | Alla utom 7\* |
|SUSE |SLES\* |Alla utom 11\* |
|Canonical| UbuntuServer |Alla utom 12\* |
|microsoft-dsvm |linux-data-science-vm-ubuntu |Alla |
|microsoft-dsvm |azureml |Alla |
|cloudera |cloudera-centos-os |Alla utom 6\* |
|cloudera |cloudera-altus-centos-os |Alla |
|microsoft-ads |linux\* |Alla |
|microsoft-aks |Alla |Alla |
|AzureDatabricks |Alla |Alla |
|qubole inc |Alla |Alla |
|datastax |Alla |Alla |
|Couchbase |Alla |Alla |
|scalegrid |Alla |Alla |
|Kontrollpunkt |Alla |Alla |
|paloaltonetworks |Alla |Alla |

Den **distribution** del av regeln skickar den _installedApplication_ parametern till gäst-konfiguration-agenten på den virtuella datorn. Den här konfigurationen kan agenten validering och rapportera kompatibilitet tillbaka via den **granska** principdefinitionen.

## <a name="azure-portal"></a>Azure Portal

Efter den **granska** och **deployIfNotExists** definitioner skapas i portalen, rekommenderar vi för att gruppera dem till en [initiativ](../concepts/definition-structure.md#initiatives) för tilldelning.

### <a name="create-copy-of-audit-definition"></a>Skapa kopia av audit definition

[![Distribuera princip för exemplet till Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)
[![distribuera princip för exemplet till Azure Government](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)

Med dessa knappar för att distribuera via portalen skapar en kopia av den **granska** principdefinitionen.
Utan den länkade **deployIfNotExists** principdefinition gäst-konfigurationen fungerar inte korrekt.

### <a name="create-copy-of-deployifnotexists-definition"></a>Skapa kopia av deployIfNotExists definition

[![Distribuera princip för exemplet till Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)
[![distribuera princip för exemplet till Azure Government](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)

Med dessa knappar för att distribuera via portalen skapar en kopia av den **deployIfNotExists** principdefinitionen. Utan den länkade **granska** principdefinition gäst-konfigurationen fungerar inte korrekt.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Distribuera med Azure PowerShell

#### <a name="copy-and-assign-the-initiative"></a>Kopiera och tilldela initiativ

De här stegen skapar en kopia av initiativet som innehåller inbyggda principer för både **granska** och **deployIfNotExists** och tilldelar initiativ till en resursgrupp.

```azurepowershell-interactive
# Create the policy initiative (Subscription scope)
$initDef = New-AzPolicySetDefinition -Name 'guestconfig-installed-application-linux' -DisplayName 'GuestConfig - Audit that an application is installed inside Linux VMs' -description 'This initiative will both deploy the policy requirements and audit that the specified application is installed inside Linux virtual machines.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/azurepolicyset.definitions.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/azurepolicyset.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the initiative parameter (JSON format)
$initParam = '{ "applicationName": { "value": "python,powershell" } }'

# Create the initiative assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-assignment' -DisplayName 'GuestConfig - Python and PowerShell apps on Linux' -Scope $scope.ResourceID -PolicySetDefinition $initDef -PolicyParameter $initParam -AssignIdentity -Location 'westus2'

# Get the system-assigned managed identity created by the assignment with -AssignIdentity
$saIdentity = $assignment.Identity.principalId

# Give the system-assigned managed identity the 'Contributor' role on the scope (needed by deployIfNotExists)
$roleAssignment = New-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'
```

Kör följande kommandon för att ta bort föregående tilldelning och definition:

```azurepowershell-interactive
# Remove the initiative assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the 'Contributor' role from the system-assigned managed identity
Remove-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'

# Remove the initiative definition
Remove-AzPolicySetDefinition -Id $initDef
```

#### <a name="copy-and-assign-the-audit-definition"></a>Kopiera och tilldelar definitionen granska

De här stegen skapar en kopia av den **granska** definition och tilldela den till en resursgrupp. Den här definitionen fungerar inte korrekt utan den länkade **deployIfNotExists** definition också tilldelas.

```azurepowershell-interactive
# Create the policy definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'guestconfig-installed-application-linux-audit' -DisplayName 'GuestConfig - Audit that an application is installed inside Linux VMs' -description 'This policy audits that the specified application is installed inside Linux virtual machines. This policy should only be used along with its corresponding deploy policy in an initiative/policy set.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Create the policy assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-audit-assignment' -DisplayName 'GuestConfig - Python and PowerShell apps on Linux' -Scope $scope.ResourceID -PolicyDefinition $definition
```

Kör följande kommandon för att ta bort föregående tilldelning och definition:

```azurepowershell-interactive
# Remove the policy definition
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the policy definition
Remove-AzPolicyDefinition -Id $definition
```

#### <a name="copy-and-assign-the-deployifnotexists-definition"></a>Kopiera och tilldela deployIfNotExists-definition

De här stegen skapar en kopia av den **deployIfNotExists** definition och tilldela den till en resursgrupp.
Den här definitionen fungerar inte korrekt utan den länkade **granska** definition också tilldelas.

```azurepowershell-interactive
# Create the policy definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name 'guestconfig-installed-application-linux-deployIfNotExists' -DisplayName 'GuestConfig - Deploy VM extension to audit that an application is installed inside Linux VMs' -description 'Include this rule to deploy the VM extension for Microsoft Guest Configuration, the VM extension for Microsoft Azure Managed Service Identity, and the content required to audit that an application is installed inside Linux virtual machines. This policy should only be used along with its corresponding audit policy in an initiative/policy set.' -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.parameters.json' -Mode All

# Set the scope to a resource group; may also be a resource, subscription, or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Set the definition parameter (JSON format)
$policyParam  = '{ "applicationName": { "value": "python,powershell" } }'

# Create the policy assignment
$assignment = New-AzPolicyAssignment -Name 'guestconfig-installed-application-linux-deployIfNotExists-assignment' -DisplayName 'GuestConfig - Deploy VM extension to audit that Python and PowerShell are installed inside Linux VMs' -Scope $scope.ResourceID -PolicyDefinition $definition -PolicyParameter $policyParam -AssignIdentity -Location 'westus2'

# Get the system-assigned managed identity created by the assignment with -AssignIdentity
$saIdentity = $assignment.Identity.principalId

# Give the system-assigned managed identity the 'Contributor' role on the scope (needed by deployIfNotExists)
$roleAssignment = New-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'
```

Kör följande kommandon för att ta bort föregående tilldelning och definition:

```azurepowershell-interactive
# Remove the policy assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the 'Contributor' role from the system-assigned managed identity
Remove-AzRoleAssignment -ObjectId $saIdentity -Scope $scope.ResourceId -RoleDefinitionName 'Contributor'

# Remove the policy definition
Remove-AzPolicyDefinition -Id $definition
```

### <a name="azure-powershell-explanation"></a>Azure PowerShell-förklaring

Skripten för distribution och borttagning använder följande kommandon. Varje kommando i följande tabell länkar till kommandospecifik dokumentation:

| Kommando | Anteckningar |
|---|---|
| [New-AzPolicySetDefinition](/powershell/module/az.resources/New-AzPolicySetDefinition) | Skapar ett Azure Policy-initiativ. |
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-AzPolicyDefinition) | Skapar en Azure Policy definition. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup) | Hämtar en enskild resursgrupp. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-AzPolicyAssignment) | Skapar en ny Azure Policy-tilldelning för ett initiativ eller definition. |
| [New-AzRoleAssignment](/powershell/module/az.resources/New-AzRoleAssignment) | Ger en rolltilldelning till specifika huvudnamn. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-AzPolicyAssignment) | Tar bort en befintlig Azure Policy-tilldelning. |
| [Remove-AzPolicySetDefinition](/powershell/module/az.resources/Remove-AzPolicySetDefinition) | Tar bort ett initiativ. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-AzPolicyDefinition) | Tar bort en definition. |

## <a name="next-steps"></a>Nästa steg

- Granska ytterligare [Azure Policy-exempel](index.md).
- Läs mer om [Azure gäst principkonfigurationen](../concepts/guest-configuration.md).
- Granska [Azure Policy-definitionsstruktur](../concepts/definition-structure.md).
