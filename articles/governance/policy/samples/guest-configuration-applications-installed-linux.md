---
title: Exempel – granska om program inte är installerade i virtuella Linux-datorer
description: Detta exempel på principer för gäst konfiguration och definitioner granskar om de angivna programmen inte är installerade på virtuella Linux-datorer.
author: DCtheGeek
ms.service: azure-policy
ms.topic: sample
ms.date: 05/02/2019
ms.author: dacoulte
ms.openlocfilehash: ef2ab4bebf2247b08cdc80ed74bbe17a67c5baae
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2019
ms.locfileid: "71977039"
---
# <a name="sample---audit-if-specified-applications-arent-installed-inside-linux-vms"></a>Exempel – granska om de angivna programmen inte är installerade i virtuella Linux-datorer

Den här principen för konfiguration av gäst konfiguration skapar en gransknings händelse när de angivna programmen inte installeras på virtuella Linux-datorer. ID: t för det här inbyggda initiativet är `/providers/Microsoft.Authorization/policySetDefinitions/c937dcb4-4398-4b39-8d63-4a6be432252e`.

> [!IMPORTANT]
> Alla initiativ för gäst konfiguration består av **gransknings** -och **deployIfNotExists** princip definitioner. Om du bara tilldelar en av princip definitionerna kommer gäst konfigurationen inte att fungera korrekt.

Du kan tilldela det här exemplet med hjälp av:

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="components-of-the-initiative"></a>Initiativets komponenter

Detta [gäst konfigurations](../concepts/guest-configuration.md) initiativ består av följande principer:

- [Granska](#audit-definition) -granska när program inte är installerade i virtuella Linux-datorer
  - ID: `/providers/Microsoft.Authorization/policyDefinitions/fee5cb2b-9d9b-410e-afe3-2902d90d0004`
- [deployIfNotExists](#deployIfNotExists-definition) – distribuera VM-tillägg till granskning när program inte är installerade i virtuella Linux-datorer
  - ID: `/providers/Microsoft.Authorization/policyDefinitions/4d1c04de-2172-403f-901b-90608c35c721`

### <a name="initiative-definition"></a>Initiativdefinition

Initiativet skapas genom att du ansluter till **gransknings** -och **deployIfNotExists** -definitionerna tillsammans och [initiativ parametrarna](#initiative-parameters). Detta är JSON för definitionen.

[!code-json[initiative-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/azurepolicyset.json "Initiative definition (JSON)")]

### <a name="initiative-parameters"></a>Initiativparametrar

|Name |Typ |Beskrivning |
|---|---|---|
|applicationName |Sträng |Program namn. Exempel: python, PowerShell eller en kommaavgränsad lista, till exempel python, PowerShell. Använd \* för matchning av jokertecken, t. ex. Power @ no__t-1. |

När du skapar en tilldelning via PowerShell eller Azure CLI går det att skicka parametervärdena som JSON antingen i en sträng eller via en fil med hjälp av `-PolicyParameter` (PowerShell) eller `--params` (Azure CLI).
PowerShell stödjer även `-PolicyParameterObject`, vilket kräver att det till cmdlet skickas en Name/Value-hashtabell där **Name** (Namn) är parameternamnet och **Value** (Värde) är det enskilda värde eller den matris med värden som skickas under tilldelningen.

I den här exempel parametern granskas installationen av program _python_ och _PowerShell_ .

```json
{
    "applicationName": {
        "value": "python,powershell"
    }
}
```

Endast **deployIfNotExists** -princip definitionen använder initiativ parametrarna.

### <a name="audit-definition"></a>gransknings definition

JSON som definierar reglerna för **gransknings** princip definitionen.

[!code-json[audit-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/audit/azurepolicy.rules.json "audit policy rules (JSON)")]

### <a name="deployifnotexists-definition"></a>deployIfNotExists-definition

JSON som definierar reglerna för **deployIfNotExists** -princip definitionen.

[!code-json[deployIfNotExists-definition](../../../../policy-templates/samples/GuestConfiguration/installed-application-linux/deployIfNotExists/azurepolicy.rules.json "deployIfNotExists policy rules (JSON)")]

**DeployIfNotExists** princip definition definierar de Azure-avbildningar som principen har verifierats på:

|Utgivare |Erbjudande |SKU |
|-|-|-|
|OpenLogic |CentOS\* |Alla utom 6 @ no__t-0 |
|Redhat |RHEL |Alla utom 6 @ no__t-0 |
|Redhat |osa | Alla |
|credativ |Debian | Alla utom 7 @ no__t-0 |
|SUSE |SLES @ NO__T-0 |Alla utom 11 @ no__t-0 |
|Canonical| UbuntuServer |Alla utom 12 @ no__t-0 |
|microsoft-dsvm |linux-data-science-vm-ubuntu |Alla |
|microsoft-dsvm |azureml |Alla |
|Cloudera |Cloudera-CentOS-OS |Alla utom 6 @ no__t-0 |
|Cloudera |Cloudera-Altus-CentOS-OS |Alla |
|microsoft-ads |linux\* |Alla |
|microsoft-aks |Alla |Alla |
|AzureDatabricks |Alla |Alla |
|qubole-Inc |Alla |Alla |
|datastax |Alla |Alla |
|Couchbase |Alla |Alla |
|scalegrid |Alla |Alla |
|tidszon |Alla |Alla |
|paloaltonetworks |Alla |Alla |

**Distributions** delen av regeln överför _installedApplication_ -parametern till gäst konfigurations agenten på den virtuella datorn. Den här konfigurationen gör att agenten kan utföra verifieringarna och rapportera efterlevnaden genom **gransknings** princip definitionen.

## <a name="azure-portal"></a>Azure Portal

När du har skapat **gransknings** -och **deployIfNotExists** -definitionerna i portalen rekommenderar vi att du grupperar dem till ett [initiativ](../concepts/definition-structure.md#initiatives) för tilldelning.

### <a name="create-copy-of-audit-definition"></a>Skapa en kopia av gransknings definition

[![Deploy till azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)
[![Deploy princip exemplet till Azure gov](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2Faudit%2Fazurepolicy.json)

Genom att använda dessa knappar för att distribuera via portalen skapas en kopia av **gransknings** princip definitionen.
Utan den kopplade **deployIfNotExists** -princip definitionen fungerar inte gäst konfigurationen som den ska.

### <a name="create-copy-of-deployifnotexists-definition"></a>Skapa en kopia av deployIfNotExists-definitionen

[![Deploy till azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)
[![Deploy princip exemplet till Azure gov](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/?#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FGuestConfiguration%2Finstalled-application-linux%2FdeployIfNotExists%2Fazurepolicy.json)

Genom att använda dessa knappar för att distribuera via portalen skapas en kopia av **deployIfNotExists** -princip definitionen. Utan den kopplade **gransknings** princip definitionen fungerar inte gäst konfigurationen som den ska.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Distribuera med Azure PowerShell

#### <a name="copy-and-assign-the-initiative"></a>Kopiera och tilldela initiativet

De här stegen skapar en kopia av initiativet som innehåller de inbyggda principerna för både **gransknings** -och **deployIfNotExists** och tilldelar initiativet till en resurs grupp.

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

#### <a name="copy-and-assign-the-audit-definition"></a>Kopiera och tilldela gransknings definitionen

De här stegen skapar en kopia av **gransknings** definitionen och tilldelar den till en resurs grupp. Den här definitionen fungerar inte som den ska utan att den kopplade **deployIfNotExists** -definitionen också tilldelas.

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

#### <a name="copy-and-assign-the-deployifnotexists-definition"></a>Kopiera och tilldela deployIfNotExists-definitionen

De här stegen skapar en kopia av **deployIfNotExists** -definitionen och tilldelar den till en resurs grupp.
Den här definitionen fungerar inte som den ska utan den kopplade **gransknings** definitionen tilldelas också.

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
| [New-AzPolicySetDefinition](/powershell/module/az.resources/New-AzPolicySetDefinition) | Skapar ett Azure Policy initiativ. |
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-AzPolicyDefinition) | Skapar en Azure Policy-definition. |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup) | Hämtar en enskild resursgrupp. |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-AzPolicyAssignment) | Skapar en ny Azure Policy tilldelning för ett initiativ eller en definition. |
| [New-AzRoleAssignment](/powershell/module/az.resources/New-AzRoleAssignment) | Ger en befintlig roll tilldelning till det särskilda huvud kontot. |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-AzPolicyAssignment) | Tar bort en befintlig Azure Policy-tilldelning. |
| [Remove-AzPolicySetDefinition](/powershell/module/az.resources/Remove-AzPolicySetDefinition) | Tar bort ett initiativ. |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-AzPolicyDefinition) | Tar bort en definition. |

## <a name="next-steps"></a>Nästa steg

- Granska ytterligare [Azure policy exempel](index.md).
- Läs mer om [Azure policy gäst konfiguration](../concepts/guest-configuration.md).
- Granska [Azure policy definitions struktur](../concepts/definition-structure.md).
