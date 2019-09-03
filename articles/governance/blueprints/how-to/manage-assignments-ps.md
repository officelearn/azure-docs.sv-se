---
title: Hantera tilldelningar med PowerShell
description: Lär dig hur du hanterar skiss tilldelningar med den officiella Azure-ritningen PowerShell-modul, AZ. skiss.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: beaa3f4c5ab272592e7fae5a95b40a9b586aaf65
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232882"
---
# <a name="how-to-manage-assignments-with-powershell"></a>Hantera tilldelningar med PowerShell

En skiss tilldelning kan hanteras med hjälp av modulen **AZ. skiss** Azure PowerShell. Modulen har stöd för att hämta, skapa, uppdatera och ta bort tilldelningar. Modulen kan också hämta information om befintliga skiss definitioner. Den här artikeln beskriver hur du installerar modulen och börjar använda den.

## <a name="add-the-azblueprint-module"></a>Lägg till modulen AZ. skiss

Om du vill aktivera Azure PowerShell hantera skiss tilldelningar måste modulen läggas till. Den här modulen kan användas med lokalt installerat PowerShell, med [Azure Cloud Shell](https://shell.azure.com) eller med [Azure PowerShell Docker-avbildningen](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Grundläggande krav

Modulen Azure-modeller kräver följande program vara:

- Azure PowerShell 1.5.0 eller högre. Om den ännu inte är installerad följer du [de här instruktionerna](/powershell/azure/install-az-ps).
- PowerShellGet 2.0.1 eller högre. Om den inte är installerad eller uppdaterad följer du [de här instruktionerna](/powershell/gallery/installing-psget).

### <a name="install-the-module"></a>Installera modulen

Modulen modeller för PowerShell är **AZ. skissa**.

1. Från en **administrativ** PowerShell-prompt kör du följande kommando:

   ```azurepowershell-interactive
   # Install the Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > Om **AZ. Accounts** redan är installerat kan du behöva använda `-AllowClobber` för att framtvinga installationen.

1. Kontrol lera att modulen har importer ATS och att den är rätt version (0.1.0):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>Hämta skiss definitioner

Det första steget för att arbeta med en tilldelning får ofta en referens till en skiss definition.
`Get-AzBlueprint` Cmdleten hämtar en eller flera skiss definitioner. Cmdleten kan hämta skiss definitioner från en hanterings grupp `-ManagementGroupId {mgId}` med eller en prenumeration `-SubscriptionId {subId}`med. Parametern **Name** hämtar en skiss definition, men den måste användas med **ManagementGroupId** eller **SubscriptionId**. **Version** kan användas med **ett namn** för att vara mer utförlig om vilken skiss definition som returneras. I stället för **version**tar växeln `-LatestPublished` den senast publicerade versionen.

I följande exempel används `Get-AzBlueprint` för att hämta alla versioner av en skiss definition med namnet "101-skisser-definition-Subscription" från en speciell `{subId}`prenumeration som visas som:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

Exempel resultatet för en skiss definition med flera versioner ser ut så här:

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

[Skiss parametrarna](../concepts/parameters.md#blueprint-parameters) i skiss definitionen kan utökas för att ge mer information.

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

## <a name="get-blueprint-assignments"></a>Hämta skiss uppgifter

Om skiss tilldelningen redan finns kan du hämta en referens till den med `Get-AzBlueprintAssignment` cmdleten. Cmdleten använder **SubscriptionId** och **namnet** som valfria parametrar. Om **SubscriptionId** inte anges används den aktuella prenumerations kontexten.

I följande exempel används `Get-AzBlueprintAssignment` för att hämta en enda skiss tilldelning med namnet "tilldelning-lås-resurs-grupper" från en viss prenumeration `{subId}`som visas som:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

Exempel resultatet för en skiss tilldelning ser ut så här:

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

Om skiss tilldelningen inte finns ännu kan du skapa den med `New-AzBlueprintAssignment` cmdleten. Denna cmdlet använder följande parametrar:

- **Namn** kunna
  - Anger namnet på skiss tilldelningen
  - Måste vara unikt och finns inte redan i **SubscriptionId**
- **Skiss** kunna
  - Anger den skiss definition som ska tilldelas
  - Används `Get-AzBlueprint` för att hämta referens objekt
- **Plats** kunna
  - Anger regionen för den systemtilldelade distributions objekt för hanterad identitet och prenumeration som ska skapas i
- **Prenumeration** valfritt
  - Anger den prenumeration som tilldelningen har distribuerats till
  - Om inget värde anges används den aktuella prenumerations kontexten som standard
- **Lås** valfritt
  - Definierar den [skiss resurs låsning](../concepts/resource-locking.md) som ska användas för distribuerade resurser
  - Alternativ som stöds: _None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
  - Om inget värde anges används _ingen_
- **SystemAssignedIdentity** valfritt
  - Välj om du vill skapa en systemtilldelad hanterad identitet för tilldelningen och distribuera resurserna
  - Standardvärdet för parametern "identitet" har angetts
  - Kan inte användas med **UserAssignedIdentity**
- **UserAssignedIdentity** valfritt
  - Anger den användare som tilldelats den hanterade identitet som ska användas för tilldelningen och för att distribuera resurserna
  - En del av parametern "identitet" har angetts
  - Kan inte användas med **SystemAssignedIdentity**
- **Parameter** valfritt
  - En [hash-tabell](/powershell/module/microsoft.powershell.core/about/about_hash_tables) med nyckel/värde-par för att ange [dynamiska parametrar](../concepts/parameters.md#dynamic-parameters) för skiss tilldelningen
  - Standardvärdet för en dynamisk parameter är **DefaultValue** i definitionen
  - Om en parameter inte anges och saknar **Standardvärde**, är parametern inte valfri

    > [!NOTE]
    > **Parametern** stöder inte secureStrings.

- **ResourceGroupParameter** valfritt
  - En [hash-tabell](/powershell/module/microsoft.powershell.core/about/about_hash_tables) för resurs grupps artefakter
  - Varje resurs grupps plats hållare för artefakter har ett nyckel/värde-par för dynamisk inställning av **namn** och/eller **plats** för den här resurs grupps artefakten
  - Om ingen resurs grupps parameter har angetts och saknar **DefaultValue**, är resurs grupps parametern inte valfri

I följande exempel skapas en ny tilldelning av version "1,1" av skiss definitionen "min-skiss" som hämtades med `Get-AzBlueprint`, anger den hanterade identitets-och tilldelnings objekt platsen till "westus2" och låser resurserna med _AllResourcesReadOnly_ , och anger hash-tabeller för både **parameter** -och **ResourceGroupParameter** för en speciell prenumeration `{subId}`som representeras som:

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

Exempel på utdata för att skapa en skiss tilldelning ser ut så här:

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

## <a name="update-blueprint-assignments"></a>Uppdatera skiss tilldelningar

Ibland är det nödvändigt att uppdatera en skiss tilldelning som redan har skapats. `Set-AzBlueprintAssignment` Cmdleten hanterar den här åtgärden. Cmdlet: en tar de flesta av samma parametrar som `New-AzBlueprintAssignment` cmdleten gör, vilket gör att det går att uppdatera allt som har angetts för tilldelningen. Undantagen till detta är _namn_, _skiss_och _SubscriptionId_. Endast de angivna värdena uppdateras.

Information om vad som händer när du uppdaterar en skiss tilldelning finns i [regler för uppdatering av tilldelningar](./update-existing-assignments.md#rules-for-updating-assignments).

- **Namn** kunna
  - Anger namnet på den skiss tilldelning som ska uppdateras
  - Används för att hitta tilldelningen att uppdatera och inte ändra tilldelningen
- **Skiss** kunna
  - Anger skiss definitionen för skiss tilldelningen
  - Används `Get-AzBlueprint` för att hämta referens objekt
  - Används för att hitta tilldelningen att uppdatera och inte ändra tilldelningen
- **Plats** valfritt
  - Anger regionen för den systemtilldelade distributions objekt för hanterad identitet och prenumeration som ska skapas i
- **Prenumeration** valfritt
  - Anger den prenumeration som tilldelningen har distribuerats till
  - Om inget värde anges används den aktuella prenumerations kontexten som standard
  - Används för att hitta tilldelningen att uppdatera och inte ändra tilldelningen
- **Lås** valfritt
  - Definierar den [skiss resurs låsning](../concepts/resource-locking.md) som ska användas för distribuerade resurser
  - Alternativ som stöds: _None_, _AllResourcesReadOnly_, _AllResourcesDoNotDelete_
- **SystemAssignedIdentity** valfritt
  - Välj om du vill skapa en systemtilldelad hanterad identitet för tilldelningen och distribuera resurserna
  - Standardvärdet för parametern "identitet" har angetts
  - Kan inte användas med **UserAssignedIdentity**
- **UserAssignedIdentity** valfritt
  - Anger den användare som tilldelats den hanterade identitet som ska användas för tilldelningen och för att distribuera resurserna
  - En del av parametern "identitet" har angetts
  - Kan inte användas med **SystemAssignedIdentity**
- **Parameter** valfritt
  - En [hash-tabell](/powershell/module/microsoft.powershell.core/about/about_hash_tables) med nyckel/värde-par för att ange [dynamiska parametrar](../concepts/parameters.md#dynamic-parameters) för skiss tilldelningen
  - Standardvärdet för en dynamisk parameter är **DefaultValue** i definitionen
  - Om en parameter inte anges och saknar **Standardvärde**, är parametern inte valfri

    > [!NOTE]
    > **Parametern** stöder inte secureStrings.

- **ResourceGroupParameter** valfritt
  - En [hash-tabell](/powershell/module/microsoft.powershell.core/about/about_hash_tables) för resurs grupps artefakter
  - Varje resurs grupps plats hållare för artefakter har ett nyckel/värde-par för dynamisk inställning av **namn** och/eller **plats** för den här resurs grupps artefakten
  - Om ingen resurs grupps parameter har angetts och saknar **DefaultValue**, är resurs grupps parametern inte valfri

I följande exempel uppdateras tilldelningen av version 1,1 av skiss definitionen My-skiss med `Get-AzBlueprint` genom att ändra lås läget:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

Exempel på utdata för att skapa en skiss tilldelning ser ut så här:

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

## <a name="remove-blueprint-assignments"></a>Ta bort skiss tilldelningar

När det är dags för en skiss tilldelning som ska tas bort, `Remove-AzBlueprintAssignment` hanterar cmdleten den här åtgärden. Cmdlet: en tar antingen **namn** eller **InputObject** för att ange vilken skiss tilldelning som ska tas bort. **SubscriptionId** _krävs_ och måste tillhandahållas i samtliga fall.

Följande exempel hämtar en befintlig skiss tilldelning med `Get-AzBlueprintAssignment` och tar sedan bort den från den aktuella prenumerationen som visas som: `{subId}`

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>Kod exempel från slut punkt till slut punkt

Genom att samla alla steg tillsammans hämtar du skiss definitionen och skapar, uppdaterar och tar bort en skiss tilldelning i den angivna prenumerationen som visas som `{subId}`:

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

- Lär dig mer om [skiss livs cykeln](../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../concepts/resource-locking.md).
- Lös problem som kan uppstå vid tilldelningen av en skiss med [allmän felsökning](../troubleshoot/general.md).