---
title: Hantera tilldelningar med PowerShell
description: Lär dig hur du hanterar skisstilldelningar med den officiella Azure Blueprints PowerShell-modulen Az.Blueprint.
ms.date: 09/30/2019
ms.topic: how-to
ms.openlocfilehash: b16cf887ba8bfc51616839db5f4af87944ec686d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247409"
---
# <a name="how-to-manage-assignments-with-powershell"></a>Hantera tilldelningar med PowerShell

En skisstilldelning kan hanteras med **Az.Blueprint** Azure PowerShell-modulen. Modulen stöder hämtning, skapande, uppdatering och borttagning av tilldelningar. Modulen kan också hämta information om befintliga skissdefinitioner. Den här artikeln beskriver hur du installerar modulen och börjar använda den.

## <a name="add-the-azblueprint-module"></a>Lägg till modulen Az.Blueprint

Om du vill att Azure PowerShell ska kunna hantera skisstilldelningar måste modulen läggas till. Den här modulen kan användas med lokalt installerat PowerShell, med [Azure Cloud Shell](https://shell.azure.com) eller med [Azure PowerShell Docker-avbildningen](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Grundläggande krav

Azure Blueprints-modulen kräver följande programvara:

- Azure PowerShell 1.5.0 eller senare. Om den ännu inte är installerad följer du [de här instruktionerna](/powershell/azure/install-az-ps).
- PowerShellGet 2.0.1 eller högre. Om den inte är installerad eller uppdaterad följer du [de här instruktionerna](/powershell/scripting/gallery/installing-psget).

### <a name="install-the-module"></a>Installera modulen

Modulen Skisser för PowerShell är **Az.Blueprint**.

1. Från en **administrativ** PowerShell-prompt kör du följande kommando:

   ```azurepowershell-interactive
   # Install the Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > Om **Az.Accounts** redan är installerat kan `-AllowClobber` det vara nödvändigt att använda för att tvinga fram installationen.

1. Verifiera att modulen har importerats och är rätt version (0.2.6):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>Hämta skissdefinitioner

Det första steget för att arbeta med en tilldelning är ofta att få en referens till en skiss definition.
Cmdlet `Get-AzBlueprint` får en eller flera skissdefinitioner. Cmdleten kan hämta skissdefinitioner från `-ManagementGroupId {mgId}` en hanteringsgrupp med eller en prenumeration med `-SubscriptionId {subId}`. Parametern **Name** får en skissdefinition, men måste användas med **ManagementGroupId** eller **SubscriptionId**. **Versionen** kan användas med **Namn** för att vara tydligare om vilken skissdefinition som returneras. I **Version**stället för `-LatestPublished` Version tar växeln tag i den senast publicerade versionen.

I följande `Get-AzBlueprint` exempel används för att hämta alla versioner av en skissdefinition med namnet "101-blueprints-definition-subscription" från en viss prenumeration representerad som `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

Exempelutdata för en skissdefinition med flera versioner ser ut så här:

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

[Skissparametrarna](../concepts/parameters.md#blueprint-parameters) för skissdefinitionen kan utökas för att ge mer information.

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

## <a name="get-blueprint-assignments"></a>Hämta skisstilldelningar

Om skisstilldelningen redan finns kan du få `Get-AzBlueprintAssignment` en referens till den med cmdleten. Cmdleten tar **SubscriptionId** och **Name** som valfria parametrar. Om **SubscriptionId** inte anges används den aktuella prenumerationskontexten.

I följande `Get-AzBlueprintAssignment` exempel används för att hämta en enda skisstilldelning med namnet "Assignment-lock-resource-groups" från en viss prenumeration som representeras som `{subId}`:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

Exempelutdata för en skisstilldelning ser ut så här:

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

## <a name="create-blueprint-assignments"></a>Skapa skisstilldelningar

Om skisstilldelningen inte finns ännu kan du `New-AzBlueprintAssignment` skapa den med cmdleten. Denna cmdlet använder följande parametrar:

- **Namn** [obligatoriskt]
  - Anger namnet på skisstilldelningen
  - Måste vara unikt och finns inte redan i **SubscriptionId**
- **Skiss** [krävs]
  - Anger skissdefinitionen för att tilldela
  - Används `Get-AzBlueprint` för att hämta referensobjektet
- **Plats** [obligatoriskt]
  - Anger regionen för det systemtilldelade hanterade identitets- och prenumerationsdistributionsobjekt som ska skapas i
- **Prenumeration** (valfritt)
  - Anger den prenumeration som tilldelningen distribueras till
  - Om det inte anges, standardvärden till den aktuella prenumerationskontexten
- **Lås** (valfritt)
  - Definierar [skissresurslåsning](../concepts/resource-locking.md) som ska användas för distribuerade resurser
  - Alternativ som stöds: _Inga_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
  - Om inget annat anges, standardvärdet _Ingen_
- **SystemAssignedIdentity** (valfritt)
  - Välj om du vill skapa en systemtilldelad hanterad identitet för tilldelningen och distribuera resurserna
  - Standard för parameteruppsättningen "identitet"
  - Det går inte att använda med **UserAssignedIdentity**
- **UserAssignedIdentity** (valfritt)
  - Anger den användartilldelade hanterade identitet som ska användas för tilldelningen och att distribuera resurserna
  - En del av parameteruppsättningen "identitet"
  - Det går inte att använda med **SystemAssignedIdentity**
- **Parameter** (valfritt)
  - En [hash-tabell med](/powershell/module/microsoft.powershell.core/about/about_hash_tables) nyckel-/värdepar för att ställa in [dynamiska parametrar](../concepts/parameters.md#dynamic-parameters) på skisstilldelningen
  - Standard för en dynamisk parameter är **standardvärde** i definitionen
  - Om en parameter inte anges och inte har något **standardvärde**är parametern inte valfri

    > [!NOTE]
    > **Parametern** stöder inte secureStrings.

- **ResourceGroupParameter** (valfritt)
  - En [hash-tabell över](/powershell/module/microsoft.powershell.core/about/about_hash_tables) resursgruppsartefakter
  - Varje platshållare för resursgruppsartefakt har nyckel-/värdepar för dynamiskt inställning **av Namn** och **plats** på den resursgruppsartefakten
  - Om en resursgruppsparameter inte anges och inte har någon **standardvärde,** är parametern resursgrupp inte valfri
- **AssignmentFile** (valfritt)
  - Sökvägen till en JSON-filrepresentation av en skisstilldelning
  - Den här parametern är en del av en PowerShell-parameteruppsättning som bara innehåller **Name**, **Blueprint**och **SubscriptionId**, plus de vanliga parametrarna.

### <a name="example-1-provide-parameters"></a>Exempel 1: Ange parametrar

I följande exempel skapas en ny tilldelning av version "1.1" av `Get-AzBlueprint`skissdefinitionen "min skiss" som hämtats med , anger den hanterade platsen för identitet och tilldelningsobjekt till "westus2", låser resurserna med _AllResourcesReadOnly_och anger hash-tabellerna för både **Parameter** och **ResourceGroupParameter** på specifik prenumeration representerad som `{subId}`:

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
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

Exempelutdata för att skapa en skisstilldelning ser ut så här:

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

### <a name="example-2-use-a-json-assignment-definition-file"></a>Exempel 2: Använda en JSON-tilldelningsdefinitionsfil

I följande exempel skapas nästan samma tilldelning som [exempel 1](#example-1-provide-parameters).
I stället för att skicka parametrar till cmdleten visar exemplet användningen av en JSON-tilldelningsdefinitionsfil och **parametern AssignmentFile.** Dessutom konfigureras egenskapen **excludedPrincipals** som en del av **lås**. Det finns inte en PowerShell-parameter för **exkluderadeprinciper** och egenskapen kan bara konfigureras genom att ställa in den via JSON-tilldelningsdefinitionsfilen.

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "westus2",
  "properties": {
    "description": "Assignment of the 101-blueprint-definition-subscription",
    "blueprintId": "/subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101-blueprints-definition-subscription",
    "locks": {
      "mode": "AllResourcesReadOnly",
      "excludedPrincipals": [
          "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
          "38833b56-194d-420b-90ce-cff578296714"
      ]
    },
    "parameters": {
      "storageAccount_storageAccountType": {
        "value": "Standard_GRS"
      }
    },
    "resourceGroups": {
      "ResourceGroup": {
        "name": "storage_rg",
        "location": "westus2"
      }
    }
  }
}
```

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -SubscriptionId '{subId}' `
    -AssignmentFile '.\assignment.json'
```

Ett exempel på JSON-tilldelningsdefinitionsfilen för en användartilldelad hanterad identitet finns i begärandetexten i [Exempel: Tilldelning med användartilldelade hanterade identitet](/rest/api/blueprints/assignments/createorupdate#examples) för REST API.

## <a name="update-blueprint-assignments"></a>Uppdatera skisstilldelningar

Ibland är det nödvändigt att uppdatera en skisstilldelning som redan har skapats. Cmdlet `Set-AzBlueprintAssignment` hanterar denna åtgärd. Cmdleten tar de flesta av `New-AzBlueprintAssignment` samma parametrar som cmdleten gör, vilket gör att allt som har ställts in på tilldelningen kan uppdateras. Undantagen är _Namn,_ _Skiss_och _SubscriptionId_. Endast de angivna värdena uppdateras.

Mer om du vill förstå vad som händer när du uppdaterar en skisstilldelning finns i [regler för uppdatering av tilldelningar](./update-existing-assignments.md#rules-for-updating-assignments).

- **Namn** [obligatoriskt]
  - Anger namnet på skisstilldelningen som ska uppdateras
  - Används för att hitta tilldelningen att uppdatera, inte för att ändra tilldelningen
- **Skiss** [krävs]
  - Anger skissdefinitionen för skisstilldelningen
  - Används `Get-AzBlueprint` för att hämta referensobjektet
  - Används för att hitta tilldelningen att uppdatera, inte för att ändra tilldelningen
- **Plats** (valfritt)
  - Anger regionen för det systemtilldelade hanterade identitets- och prenumerationsdistributionsobjekt som ska skapas i
- **Prenumeration** (valfritt)
  - Anger den prenumeration som tilldelningen distribueras till
  - Om det inte anges, standardvärden till den aktuella prenumerationskontexten
  - Används för att hitta tilldelningen att uppdatera, inte för att ändra tilldelningen
- **Lås** (valfritt)
  - Definierar [skissresurslåsning](../concepts/resource-locking.md) som ska användas för distribuerade resurser
  - Alternativ som stöds: _Inga_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
- **SystemAssignedIdentity** (valfritt)
  - Välj om du vill skapa en systemtilldelad hanterad identitet för tilldelningen och distribuera resurserna
  - Standard för parameteruppsättningen "identitet"
  - Det går inte att använda med **UserAssignedIdentity**
- **UserAssignedIdentity** (valfritt)
  - Anger den användartilldelade hanterade identitet som ska användas för tilldelningen och att distribuera resurserna
  - En del av parameteruppsättningen "identitet"
  - Det går inte att använda med **SystemAssignedIdentity**
- **Parameter** (valfritt)
  - En [hash-tabell med](/powershell/module/microsoft.powershell.core/about/about_hash_tables) nyckel-/värdepar för att ställa in [dynamiska parametrar](../concepts/parameters.md#dynamic-parameters) på skisstilldelningen
  - Standard för en dynamisk parameter är **standardvärde** i definitionen
  - Om en parameter inte anges och inte har något **standardvärde**är parametern inte valfri

    > [!NOTE]
    > **Parametern** stöder inte secureStrings.

- **ResourceGroupParameter** (valfritt)
  - En [hash-tabell över](/powershell/module/microsoft.powershell.core/about/about_hash_tables) resursgruppsartefakter
  - Varje platshållare för resursgruppsartefakt har nyckel-/värdepar för dynamiskt inställning **av Namn** och **plats** på den resursgruppsartefakten
  - Om en resursgruppsparameter inte anges och inte har någon **standardvärde,** är parametern resursgrupp inte valfri

I följande exempel uppdateras tilldelningen av version "1.1" av skissdefinitionen "my-blueprint" som hämtats genom `Get-AzBlueprint` att låsläget ändras:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

Exempelutdata för att skapa en skisstilldelning ser ut så här:

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

## <a name="remove-blueprint-assignments"></a>Ta bort skisstilldelningar

När det är dags för en skisstilldelning att tas bort hanterar cmdlet den `Remove-AzBlueprintAssignment` här åtgärden. Cmdlet tar antingen **Namn** eller **InputObject** för att ange vilken skisstilldelning som ska tas bort. **SubscriptionId** _krävs_ och måste tillhandahållas i samtliga fall.

I följande exempel hämtas en `Get-AzBlueprintAssignment` befintlig skisstilldelning med och tar `{subId}`sedan bort den från den specifika prenumeration som representeras som :

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>Exempel på på kod från slutna till slutna kod

Om du sammanför alla steg får följande exempel skissdefinitionen och skapar, uppdaterar och tar bort `{subId}`en skisstilldelning i den specifika prenumeration som representeras som :

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
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
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

- Läs mer om [skisslivscykeln](../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../concepts/parameters.md).
- Lär dig att anpassa [ordningsföljden för skisssekvensering](../concepts/sequencing-order.md).
- Ta reda på hur du använder [skiss resurs låsning](../concepts/resource-locking.md).
- Lös problem under tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md).