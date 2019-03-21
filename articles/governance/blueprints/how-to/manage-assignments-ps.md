---
title: Så här hanterar du tilldelningar med PowerShell
description: Lär dig hur du hanterar skissen tilldelningar med officiella Azure skisser PowerShell-modulen, Az.Blueprint.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 47026975a5142760f90a1a5712b532519f969fd7
ms.sourcegitcommit: 4133f375862fdbdec07b70de047d70c66ac29d50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/15/2019
ms.locfileid: "57995675"
---
# <a name="how-to-manage-assignments-with-powershell"></a>Så här hanterar du tilldelningar med PowerShell

En tilldelning av skissen kan hanteras med den **Az.Blueprint** Azure PowerShell-modulen. Har stöd för modulen hämtar, skapa, uppdatera och ta bort tilldelningar. Modulen kan också hämta information i befintliga skissdefinitioner. Den här artikeln beskriver hur du installerar modulen och börja använda den.

## <a name="add-the-azblueprint-module"></a>Lägg till modulen Az.Blueprint

Om du vill aktivera Azure PowerShell för att hantera tilldelningar för skissen måste modulen läggas till. Den här modulen kan användas med lokalt installerat PowerShell, med [Azure Cloud Shell](https://shell.azure.com) eller med [Azure PowerShell Docker-avbildningen](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Grundläggande krav

Modulen Azure skisser måste ha följande programvara:

- Azure PowerShell 1.5.0 eller högre. Om den ännu inte är installerad följer du [de här instruktionerna](/powershell/azure/install-az-ps).
- PowerShellGet 2.0.1 eller högre. Om den inte är installerad eller uppdaterad följer du [de här instruktionerna](/powershell/gallery/installing-psget).

### <a name="install-the-module"></a>Installera modulen

Skisser-modulen för PowerShell är **Az.Blueprint**.

1. Från en **administrativ** PowerShell-prompt kör du följande kommando:

   ```azurepowershell-interactive
   # Install the Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > Om **Az.Accounts** är redan installerad kan det vara nödvändigt att använda `-AllowClobber` att verkställa installation.

1. Verifiera att modulen har importerats och är rätt version (0.1.0):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>Hämta skissdefinitioner

Det första steget för att arbeta med en tilldelning är ofta hämtar en referens till en skissdefinitionen.
Den `Get-AzBlueprint` cmdlet hämtar en eller flera skissdefinitioner. Cmdlet: en kan hämta skissdefinitioner från en hanteringsgrupp med `-ManagementGroupId {mgId}` eller en prenumeration med `-SubscriptionId {subId}`. Den **namn** parametern hämtar en skissdefinitionen, men måste användas med **ManagementGroupId** eller **SubscriptionId**. **Version** kan användas med **namn** kan vara mer explicit om vilka skissdefinitionen returneras. I stället för **Version**, växeln `-LatestPublished` grabs de senast publicerade versionen.

I följande exempel används `Get-AzBlueprint` att hämta alla versioner av en skissdefinitionen med namnet ”101-skisser-definition-prenumeration” från en specifik prenumeration visas som `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

Exempelutdata för ett skissdefinitionen med flera versioner ser ut så här:

```output
Name                 : 101-blueprints-definition-subscription
Id                   : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101
                       -blueprints-definition-subscription
DefinitionLocationId : {subId}
Versions             : {1.0, 1.1}
TimeCreated          : 2019-02-25
TargetScope          : Subscription
Parameters           : {storageAccount_storageAccountType, storageAccount_location,
                       allowedlocations_listOfAllowedLocations, [Usergrouporapplicationname]:Reader_RoleAssignmentName}
ResourceGroups       : ResourceGroup
```

Den [skiss parametrar](../concepts/parameters.md#blueprint-parameters) på skissen definition kan utökas för att ge mer information.

```azurepowershell-interactive
$blueprints.Parameters
```

```output
Key                                                    Value
---                                                    -----
storageAccount_storageAccountType                      Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
storageAccount_location                                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
allowedlocations_listOfAllowedLocations                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
[Usergrouporapplicationname]:Reader_RoleAssignmentName Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
```

## <a name="get-blueprint-assignments"></a>Hämta tilldelningar för skissen

Om skisstilldelningen redan finns, kan du hämta en referens till den med den `Get-AzBlueprintAssignment` cmdlet. Cmdlet: en tar **SubscriptionId** och **namn** som valfria parametrar. Om **SubscriptionId** inte anges används den aktuella prenumerationskontexten.

I följande exempel används `Get-AzBlueprintAssignment` att hämta en enda skisstilldelningen med namnet ”tilldelning-Lås-resource-groups” från en viss prenumeration visas som `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

Exempelutdata för ett skisstilldelningen ser ut så här:

```output
Name              : Assignment-lock-resource-groups
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssignme
                    nts/Assignment-lock-resource-groups
Scope             : /subscriptions/{subId}
LastModified      : 2019-02-19
LockMode          : AllResourcesReadOnly
ProvisioningState : Succeeded
Parameters        :
ResourceGroups    : ResourceGroup
```

## <a name="create-blueprint-assignments"></a>Skapa skiss tilldelningar

Om skisstilldelningen ännu inte finns, kan du skapa den med den `New-AzBlueprintAssignment` cmdlet. Den här cmdleten använder följande parametrar:

- **Namn på** [krävs]
  - Anger namnet på skisstilldelningen
  - Måste vara unikt och inte redan finns i **prenumerations-ID**
- **Skissen** [krävs]
  - Anger skissdefinitionen att tilldela
  - Använd `Get-AzBlueprint` att hämta referensobjektet
- **Plats** [krävs]
  - Anger regionen för systemtilldelade hanterade identitets- och prenumeration distributionsobjektet skapas i
- **Prenumeration** (valfritt)
  - Anger den prenumeration som tilldelningen har distribuerats till
  - Om du inte som standard den aktuella prenumerationskontexten
- **Lås** (valfritt)
  - Definierar den [skiss resource låsning](../concepts/resource-locking.md) för distribuerade resurser
  - Alternativ som stöds: _None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
  - Om du inte har angetts som standard _None_
- **SystemAssignedIdentity** (valfritt)
  - Välj att skapa en automatiskt genererad hanterad identitet för tilldelningen och distribuera resurser
  - Standardvärdet för parametern ”identitet”
  - Kan inte användas med **UserAssignedIdentity**
- **UserAssignedIdentity** (valfritt)
  - Anger användartilldelade hanterade identiteten för tilldelningen och distribuera resurser
  - En del av parameteruppsättningen ”identitet”
  - Kan inte användas med **SystemAssignedIdentity**
- **Parametern** (valfritt)
  - En [hashtabell](/powershell/module/microsoft.powershell.core/about/about_hash_tables) av nyckel/värde-par för inställningen [dynamiska parametrar](../concepts/parameters.md#dynamic-parameters) på skiss tilldelning
  - Standardvärde för en dynamisk parameter är den **defaultValue** i definitionen
  - Om en parameter har angetts inte och har någon **defaultValue**, parametern inte är valfritt

    > [!NOTE]
    > **Parametern** stöder inte secureStrings.

- **ResourceGroupParameter** (valfritt)
  - En [hash-tabell](/powershell/module/microsoft.powershell.core/about/about_hash_tables) resource group artefakter
  - Varje resurs grupp artefakt platshållare har en nyckel/värde-par för att ställa in dynamiskt **namn** och/eller **plats** på resurs grupp artefakt
  - Om en resurs grupp-parameter har angetts och inte har någon **defaultValue**, gruppen resursparametern inte är valfritt

I följande exempel skapas en ny tilldelning av versionen '1.1' av 'Mina skissen' skissdefinitionen som hämtas med `Get-AzBlueprint`, ställs hanterade identitets- och tilldelning objekt platsen till 'westus2', låser resurser med  _AllResourcesReadOnly_, och anger hash-tabeller för både **parametern** och **ResourceGroupParameter** på specifik prenumeration visas som `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadyOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

Exempelutdata för att skapa en tilldelning av skissen ser ut så här:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadyOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="update-blueprint-assignments"></a>Uppdatera tilldelningar för skissen

Ibland är det nödvändigt att uppdatera en skisstilldelningen som redan har skapats. Den `Set-AzBlueprintAssignment` cmdleten hanterar den här åtgärden. Cmdlet: en tar de flesta av samma parametrar som den `New-AzBlueprintAssignment` cmdlet gör det, så att allt som har ställts in på tilldelning som ska uppdateras. Undantag till detta är den _namn_, _skissen_, och _SubscriptionId_. De värden som anges uppdateras.

Information om vad som händer när du uppdaterar en skisstilldelningen finns i [regler för uppdatering av tilldelningar](./update-existing-assignments.md#rules-for-updating-assignments).

- **Namn på** [krävs]
  - Anger namnet på skisstilldelningen att uppdatera
  - Används för att hitta tilldelningen att uppdatera, inte att ändra tilldelningen
- **Skissen** [krävs]
  - Anger skissdefinitionen för skisstilldelningen
  - Använd `Get-AzBlueprint` att hämta referensobjektet
  - Används för att hitta tilldelningen att uppdatera, inte att ändra tilldelningen
- **Plats** (valfritt)
  - Anger regionen för systemtilldelade hanterade identitets- och prenumeration distributionsobjektet skapas i
- **Prenumeration** (valfritt)
  - Anger den prenumeration som tilldelningen har distribuerats till
  - Om du inte som standard den aktuella prenumerationskontexten
  - Används för att hitta tilldelningen att uppdatera, inte att ändra tilldelningen
- **Lås** (valfritt)
  - Definierar den [skiss resource låsning](../concepts/resource-locking.md) för distribuerade resurser
  - Alternativ som stöds: _None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
- **SystemAssignedIdentity** (valfritt)
  - Välj att skapa en automatiskt genererad hanterad identitet för tilldelningen och distribuera resurser
  - Standardvärdet för parametern ”identitet”
  - Kan inte användas med **UserAssignedIdentity**
- **UserAssignedIdentity** (valfritt)
  - Anger användartilldelade hanterade identiteten för tilldelningen och distribuera resurser
  - En del av parameteruppsättningen ”identitet”
  - Kan inte användas med **SystemAssignedIdentity**
- **Parametern** (valfritt)
  - En [hashtabell](/powershell/module/microsoft.powershell.core/about/about_hash_tables) av nyckel/värde-par för inställningen [dynamiska parametrar](../concepts/parameters.md#dynamic-parameters) på skiss tilldelning
  - Standardvärde för en dynamisk parameter är den **defaultValue** i definitionen
  - Om en parameter har angetts inte och har någon **defaultValue**, parametern inte är valfritt

    > [!NOTE]
    > **Parametern** stöder inte secureStrings.

- **ResourceGroupParameter** (valfritt)
  - En [hash-tabell](/powershell/module/microsoft.powershell.core/about/about_hash_tables) resource group artefakter
  - Varje resurs grupp artefakt platshållare har en nyckel/värde-par för att ställa in dynamiskt **namn** och/eller **plats** på resurs grupp artefakt
  - Om en resurs grupp-parameter har angetts och inte har någon **defaultValue**, gruppen resursparametern inte är valfritt

I följande exempel uppdateras tilldelningen av version '1.1' av 'Mina skissen' skissdefinitionen som hämtas med `Get-AzBlueprint` genom att ändra lock-läge:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

Exempelutdata för att skapa en tilldelning av skissen ser ut så här:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesDoNotDelete
ProvisioningState : Updating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="remove-blueprint-assignments"></a>Ta bort skissen tilldelningar

När det är dags för en skisstilldelningen som ska tas bort, den `Remove-AzBlueprintAssignment` cmdleten hanterar den här åtgärden. Cmdlet: en tar antingen **namn** eller **InputObject** att ange vilka skiss tilldelning för att ta bort. **SubscriptionId** är _krävs_ och måste anges i samtliga fall.

I följande exempel hämtar en befintlig skisstilldelningen med `Get-AzBlueprintAssignment` och tar sedan bort den från den specifika prenumerationen som `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>Kodexempel från slutpunkt till slutpunkt

Sammanför alla steg i följande exempel hämtar skissdefinitionen, sedan skapar, uppdaterar och tar bort en tilldelning av skissen i den specifika prenumerationen som `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

#region GetBlueprint
# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'
#endregion

#region CreateAssignment
# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadyOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
#endregion CreateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region UpdateAssignment
# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
#endregion UpdateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region RemoveAssignment
# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $bpAssignment -SubscriptionId '{subId}'
#endregion
```

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [livscykeln för en skiss](../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../concepts/resource-locking.md).
- Lös problem som kan uppstå vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md).