---
title: Flytta resurser mellan regioner med hjälp av PowerShell i Azure Resource förflyttare
description: Lär dig hur du flyttar resurser mellan regioner med hjälp av PowerShell i Azure Resource överfart.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 11/30/2020
ms.author: raynew
ms.openlocfilehash: 0aca0e49d72025686cf44d434fa7a43ae0c86e0b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461092"
---
# <a name="move-resources-across-regions-in-powershell"></a>Flytta resurser mellan regioner i PowerShell

Lär dig hur du flyttar Azure-resurser till en annan region med PowerShell i Azure Resource-arbetskraften. 

> [!NOTE]
> Azure Resource-arbetskraften är för närvarande en för hands version.



## <a name="before-you-start"></a>Innan du börjar

- Din Azure-prenumeration bör ha åtkomst till resurs förflyttning, och du bör ha behörighet som [ägare](../role-based-access-control/built-in-roles.md#owner) eller [administratör för användar åtkomst](../role-based-access-control/built-in-roles.md#user-access-administrator) för prenumerationen.
- Resurs förflyttning spårar inte ändringar och uppgraderingar, så gör nödvändiga ändringar i resurser innan du börjar flytta dem.
- När du flyttar resurser med PowerShell kan du för närvarande inte redigera några mål regions inställningar. Ändra inställningarna direkt i portalen.
- När du lägger till resurser i en flytt samling, i förberedelser för att flytta dem till en annan region, lagras metadata om flytten i en resurs grupp som skapats för syftet. För närvarande kan den här resurs gruppen finnas i regionerna USA, östra 2 och Europa, norra. Azure-resurser kan flyttas mellan alla offentliga regioner med hjälp av metadata som finns i någon av dessa regioner.

## <a name="sample-values"></a>Exempel värden

Vi använder dessa värden i våra skript exempel:

**Inställning** | **Värde** 
--- | ---
Prenumerations-ID:t | prenumerations-ID
Plats för resurs förflyttnings tjänst | USA, östra 2
Resurs grupp för metadata | RegionMoveRG – centrala-westcentralus
Plats för resurs grupp för metadata | USA, östra 2
Flytta samlings namn | MoveCollection – centrala-westcentralus
Käll region |  USA, centrala
Käll resurs grupp | PSDemoRM
Mål region | USA, västra centrala
Mål resurs grupp | PSDemoRMtgt
Virtuell dator som ska flyttas | PSDemoVM

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration.

```azurepowershell-interactive
# Sign in to an Azure subscription
Connect-AzAccount – Subscription "<subscription-id>"
```

## <a name="create-the-resource-group-for-metadata"></a>Skapa resurs gruppen för metadata

Skapa en resurs grupp för att lagra metadata och konfigurations information för flyttnings samlingen.

```azurepowershell-interactive
# Create the resource group for metadata
New-AzResourceGroup -Name MoveCollection-centralus-westcentralus -Location "East US 2"
```

**Förväntad utdata**:

![Mata ut text när resurs grupp har skapats](./media/move-region-powershell/output-create-resource-group.png)

## <a name="register-the-resource-provider"></a>Registrera resursprovidern

Registrera resurs leverantören Microsoft. Migrate, så att du kan skapa MoveCollection-resursen.

```azurepowershell-interactive
# Register the provider
Register-AzResourceProvider -ProviderNamespace Microsoft.Migrate 

# Wait for registration
While(((Get-AzResourceProvider -ProviderNamespace Microsoft.Migrate)| where {$_.RegistrationState -eq "Registered" -and $_.ResourceTypes.ResourceTypeName -eq "moveCollections"}|measure).Count -eq 0)
  {
      Start-Sleep -Seconds 5
      Write-Output "Waiting for registration to complete."
  }
```

## <a name="create-the-move-collection"></a>Skapa flyttnings samlingen

MoveCollection-objektet lagrar metadata och konfigurations information om resurser som du vill flytta.

- För att MoveCollection-objektet ska få åtkomst till den prenumeration där Azure Resource driven-tjänsten finns, behöver den en [systemtilldelad hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (tidigare kallad HANTERAD TJÄNSTIDENTITET (MSI)) som är betrodd av prenumerationen.
- Identiteten tilldelas rollen deltagare eller administratör för användar åtkomst för käll prenumerationen.
- Om du vill tilldela en roll till identiteten behöver du en roll i prenumerationen (till exempel ägare eller administratör för användar åtkomst) med följande behörigheter:
    -  Microsoft.Authorization/roleAssignments/write
    - Microsoft. Authorization/roleAssignments/Delete


```azurepowershell-interactive
# Create a MoveCollection object
New-AzResourceMoverMoveCollection -Name "MoveCollection-centralus-westcentralus" -ResourceGroupName "RegionMoveRG-centralus-westcentralus" -SubscriptionId "<subscription-id>" -SourceRegion "centralus" -TargetRegion "westcentralus" -Location  "East US 2" -IdentityType SystemAssigned
```
**Förväntad utdata**:

![Utgående text efter skapande av flytta samling](./media/move-region-powershell/output-move-collection.png)


## <a name="assign-a-managed-identity"></a>Tilldela en hanterad identitet 

Ange prenumerations-ID, Hämta identitetens huvud namn för MoveCollection-objektet och tilldela Azure-rollerna till den.


```azurepowershell-interactive
# Set the subscriptionId
$subscriptionId = "<subscription-id>"

# Retrieve the principal managed identity of the MoveCollection
$moveCollection = Get-AzResourceMoverMoveCollection -SubscriptionId $subscriptionId -ResourceGroupName "RegionMoveRG-centralus-westcentralus" -Name "MoveCollection-centralus-westcentralus"

# Set the IdentityPrincipalID
$identityPrincipalId = $moveCollection.IdentityPrincipalId

# Assign the role to the IdentityPrincipalID
New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName Contributor -Scope "/subscriptions/$subscriptionId"

New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName "User Access Administrator" -Scope "/subscriptions/$subscriptionId"
```

## <a name="add-resources-to-the-move-collection"></a>Lägg till resurser i flyttnings samlingen

Hämta ID: t för käll resursen som du vill flytta. Lägg sedan till den i flyttnings samlingen.

```azurepowershell-interactive
# Retrieve the resource ID
Get-AzResource -Name PSDemoVM -ResourceGroupName PSDemoRM
```

**Förväntad utdata**

![Utgående text efter hämtning av resurs-ID](./media/move-region-powershell/output-retrieve-resource.png)


```azurepowershell-interactive
# Add the resource to the move collection
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -SourceId “/subscriptions/e80eb9fa-c996-4435-aa32-5af6f3d3077c/resourceGroups/PSDemoRM/providers/Microsoft.Compute/virtualMachines/PSDemoVM” -Name “PSDemoVM” -ResourceSettingResourceType “Microsoft.Compute/virtualMachines” -ResourceSettingTargetResourceName “PSDemoVM”
```

**Förväntade utdata** 
 ![ Skriva text när resursen har lagts till](./media/move-region-powershell/output-add-resource.png)

## <a name="resolve-dependencies"></a>Matcha beroenden

Verifiera de resurser som du har lagt till och Lös eventuella beroenden för andra resurser.

```azurepowershell-interactive
# Resolve dependencies
Resolve-AzResourceMoverMoveCollectionDependency -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus”
```
**Utdata om beroenden finns**

Om resursen i flyttnings samlingen har beroenden för andra resurser, utfärdas ett meddelande som misslyckats.

![Utgående text efter kontroll av beroenden](./media/move-region-powershell/dependency-error.png)

### <a name="retrieve-dependencies"></a>Hämta beroenden

Om den resurs som du vill flytta är beroende av andra resurser kan du hämta information om de beroenden som saknas.

```azurepowershell-interactive
# Identify dependencies
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -SubscriptionId  “<subscription-id>”
```

**Förväntad utdata**

![Utgående text efter hämtning av beroenden](./media/move-region-powershell/missing-dependencies.png)

I det här exemplet identifieras två beroenden som saknade:

- Käll resurs grupp: PSDemoRM
- NÄTVERKSKORT på virtuell dator: PSDemoVM62

## <a name="add-dependencies-to-collection"></a>Lägg till beroenden i samlingen


Använd de resurs-ID: n som du hämtade, identifiera namnen på de resurser som saknas och Lägg till dem i flyttnings samlingen.

### <a name="add-the-nic"></a>Lägg till NÄTVERKSKORTet

Hämta NÄTVERKSKORTets namn och typ och Lägg till det i samlingen.

```azurepowershell-interactive
# Get the NIC name and resource type
Get-AzResource -ResourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm/providers/microsoft.network/networkinterfaces/psdemovm62”
```

**Förväntad utdata**

![Utgående text efter hämtning av nätverkskort](./media/move-region-powershell/output-retrieve-nic.png)

Lägg nu till NÄTVERKSKORTet i flyttnings samlingen. Det här exemplet ger mål NÄTVERKSKORTet samma namn. Behåll inställningar och Skift läges konsekventa.

```azurepowershell-interactive
# Add the NIC to the collection. 
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -SourceId “/subscriptions/<subscription-id>/resourceGroups/PSDemoRM/providers/Microsoft.Network/networkInterfaces/psdemovm62” -Name “psdemovm62” -ResourceSettingResourceType “Microsoft.Network/networkInterfaces” -ResourceSettingTargetResourceName “psdemovm62”
```

**Förväntad utdata**

![Mata in text efter tillägg av NÄTVERKSKORTet i samlingen](./media/move-region-powershell/add-nic.png)

## <a name="add-the-source-resource-group"></a>Lägg till käll resurs gruppen

```azurepowershell-interactive
# Get the resource group name and resource type
Get-AzResource -ResourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm”

# Add the resource group to the collection. 
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -SourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm” -Name “psdemorm” -ResourceSettingResourceType “resourcegroups” -ResourceSettingTargetResourceName “psdemorm”
```

**Förväntad utdata**

![Utgående text när resurs gruppen har lagts till i samlingen](./media/move-region-powershell/add-source-resource-group.png)

## <a name="recheck-dependencies"></a>Kontrol lera beroenden igen

Kontrol lera igen för saknade beroenden.

```azurepowershell-interactive
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -SubscriptionId  “<subscription-id>”
```

**Resultat**

Vi har ytterligare beroenden som saknas.

![Mata ut text efter kontroll av beroenden](./media/move-region-powershell/recheck-dependencies.png)

## <a name="retrieve-additional-dependencies"></a>Hämta ytterligare beroenden

```azurepowershell-interactive
# Identify dependencies
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -SubscriptionId  “<subscription-id>”
```

**Förväntad utdata**


![Utgående text efter hämtning av ytterligare beroenden](./media/move-region-powershell/additional-missing-dependencies.png)

I det här exemplet identifieras tre fler beroenden som saknade:

- Offentlig IP-adress: psdemovm-IP
- Virtuellt Azure-nätverk: psdemorm-VNet
- Nätverks säkerhets grupp (NSG): psdemovm-NSG

## <a name="add-additional-dependencies"></a>Lägg till ytterligare beroenden

1. [Följ anvisningarna](#add-dependencies-to-collection) för att lägga till dessa resurser i flytt samlingen.
2. När du har lagt till resurser ska du validera igen tills alla beroenden har lagts till.


## <a name="prepare-and-move-the-source-resource-group"></a>Förbereda och flytta käll resurs gruppen

I käll regionen förbereder du resurser innan du flyttar dem. Den förberedande processen beror på vilka resurser du flyttar. För tillstånds lösa resurser förbereder du till exempel att förbereda och exportera en Azure Resource Manager-mall (ARM). Eller för tillstånds känsliga resurser kan replikeringen starta. 

Innan du kan förbereda käll resurserna måste du förbereda och flytta käll resurs gruppen.

### <a name="prepare"></a>Förbereda

```azurepowershell-interactive
# Prepare the source resource group
Invoke-AzResourceMoverPrepare -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -MoveResource “PSDemoRM”
```

**Förväntad utdata**

![Utgående text efter förberedelse av käll resurs grupp](./media/move-region-powershell/prepare-source-resource-group.png)

### <a name="initiate-move"></a>Initiera flytt

```azurepowershell-interactive
# Initiate move
Invoke-AzResourceMoverInitiateMove -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -MoveResource “PSDemoRM”
```

**Förväntad utdata**

![Utgående text efter att käll resurs gruppen har flyttats](./media/move-region-powershell/initiate-move-resource-group.png)


```azurepowershell-interactive
# Commit move
Invoke-AzResourceMoverCommit -SubscriptionId “<subscription-id” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “PS-centralus-
westcentralus-demoRM” -MoveResource “PSDemoRM”
```

**Förväntad utdata**

![Utgående text efter att käll resurs grupp har genomförts](./media/move-region-powershell/commit-move-resource-group.png)


## <a name="prepare-resources"></a>Förbereda resurser

När käll resurs gruppen har flyttats till mål regionen hämtar du en lista över resurserna i flyttnings samlingen och förbereder att flytta dem.

```azurepowershell-interactive
# Retrieve resources in the collection
Get-AzResourceMoverMoveResource  -SubscriptionId “<subscription-id>“ -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus”   | Where-Object {  $_.MoveStatusMoveState -eq “PreparePending” } | Select Name
```
**Förväntad utdata**

![Utgående text efter hämtning av resurser i samlingen](./media/move-region-powershell/collection-resources.png)

Förbered resurserna nu.

```azurepowershell-interactive
# Prepare the resources, using the resource name
Invoke-AzResourceMoverPrepare -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
Du kan också förbereda och flytta resurser med resurs-ID: t, i stället för namnet:

```azurepowershell-interactive
# Prepare the resources using the resource ID
Invoke-AzResourceMoverPrepare -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus  -MoveResourceInputType MoveResourceSourceId  -MoveResource $('/subscriptions/<subscription-id>/resourceGroups/PSDemoRM/providers/Microsoft.Network/networkSecurityGroups/PSDemoVM-nsg')
```

**Förväntad utdata**

![Utgående text efter förberedelse av resurser i samlingen](./media/move-region-powershell/collection-prepare.png)

## <a name="initiate-resource-move"></a>Initiera resurs flytt

När du har förberett resurserna initierar du flytten.

```azurepowershell-interactive
# Initiate the move 
Invoke-AzResourceMoverInitiateMove -SubscriptionId <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**Förväntad utdata**

![Mata ut text efter initiering av resurs flytt](./media/move-region-powershell/initiate-resource-move.png)

## <a name="discard-or-commit-the-move"></a>Ignorera eller genomför flyttningen

Efter den första flyttningen kan du bestämma om du vill att flyttningen ska utföras eller om du vill ta bort den. 

- **Ignorera**: du kan ta bort en flytt om du testar och du inte vill flytta käll resursen. Om du tar bort flytten returneras resursen till ett tillstånd där *initieringen väntar*. Du kan sedan starta flyttningen igen om det behövs.
- **Commit**: commit Slutför flyttningen till mål regionen. När du har gjort det kommer en käll resurs att vara i ett tillstånd där *borttagnings källan väntar*, och du kan välja om du vill ta bort den.

### <a name="discard"></a>Ignorera

Ta bort flytten:

```azurepowershell-interactive
# Discard the move 
Invoke-AzResourceMoverDiscard -SubscriptionId  <subscription-id> `-ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**Förväntad utdata**

![Utgående text efter att flytten har tagits bort](./media/move-region-powershell/discard-move.png)


### <a name="commit"></a>Checka in

Så här genomför du flytten:

```azurepowershell-interactive
# Commit the move 
Invoke-AzResourceMoverCommit -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**Förväntad utdata**

![Mata in text efter att du har genomfört flytten](./media/move-region-powershell/commit-move.png)

## <a name="delete-source-resources"></a>Ta bort käll resurser

När du har gjort flytten och verifierat att resurserna fungerar som förväntat i mål regionen, kan du ta bort käll resurserna i [Azure Portal](../azure-resource-manager/management/manage-resources-portal.md#delete-resources), [med hjälp av POWERSHELL](../azure-resource-manager/management/manage-resources-powershell.md#delete-resources)eller [Azure CLI](../azure-resource-manager/management/manage-resources-cli.md#delete-resources).

## <a name="next-steps"></a>Nästa steg

[Lär dig hur du](remove-move-resources.md) tar bort resurser från en flyttnings samling.
