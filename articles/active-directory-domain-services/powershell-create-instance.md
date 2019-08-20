---
title: Aktivera Azure Active Directory Domain Services med PowerShell | Microsoft Docs
description: Aktivera Azure Active Directory Domain Services med PowerShell
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: iainfou
ms.openlocfilehash: c6572ab8bc2a10039f327233f983c2e822fba3b0
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617224"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Aktivera Azure Active Directory Domain Services med PowerShell
Den här artikeln visar hur du aktiverar Azure Active Directory (AD) Domain Services med hjälp av PowerShell.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="task-1-install-the-required-powershell-modules"></a>Uppgift 1: Installera de PowerShell-moduler som krävs

### <a name="install-and-configure-azure-ad-powershell"></a>Installera och konfigurera Azure AD PowerShell
Följ anvisningarna i artikeln för att [Installera Azure AD PowerShell-modulen och ansluta till Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Installera och konfigurera Azure PowerShell
Följ anvisningarna i artikeln för att [installera Azure PowerShell-modulen och ansluta till din Azure-prenumeration](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="task-2-create-the-required-service-principal-in-your-azure-ad-directory"></a>Uppgift 2: Skapa tjänstens huvud namn som krävs i Azure AD-katalogen
Skriv följande PowerShell-kommando för att skapa tjänstens huvud namn som krävs för Azure AD Domain Services i din Azure AD-katalog.
```powershell
# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

## <a name="task-3-create-and-configure-the-aad-dc-administrators-group"></a>Uppgift 3: Skapa och konfigurera gruppen "AAD DC-administratörer"
Nästa uppgift är att skapa administratörs gruppen som ska användas för att delegera administrations uppgifter på din hanterade domän.
```powershell
# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Nu när du har skapat gruppen lägger du till några användare i gruppen.
```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="task-4-register-the-azure-ad-domain-services-resource-provider"></a>Uppgift 4: Registrera Azure AD Domain Services Resource Provider
Skriv följande PowerShell-kommando för att registrera resurs leverantören för Azure AD Domain Services:
```powershell
# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

## <a name="task-5-create-a-resource-group"></a>Uppgift 5: Skapa en resursgrupp
Skriv följande PowerShell-kommando för att skapa en resurs grupp:
```powershell
$ResourceGroupName = "ContosoAaddsRg"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Du kan skapa det virtuella nätverket och den Azure AD Domain Services hanterade domänen i den här resurs gruppen.


## <a name="task-6-create-and-configure-the-virtual-network"></a>Uppgift 6: Skapa och konfigurera det virtuella nätverket
Skapa nu det virtuella nätverk där du aktiverar Azure AD Domain Services. Se till att du skapar ett dedikerat undernät för Azure AD Domain Services. Distribuera inte arbets belastnings datorer i det här dedikerade under nätet.

Skriv följande PowerShell-kommandon för att skapa ett virtuellt nätverk med ett dedikerat undernät för Azure AD Domain Services.

```powershell
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```


## <a name="task-7-provision-the-azure-ad-domain-services-managed-domain"></a>Uppgift 7: Etablera den Azure AD Domain Services hanterade domänen
Skriv följande PowerShell-kommando för att aktivera Azure AD Domain Services för din katalog:

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **Glöm inte att lägga till ytterligare konfigurations steg efter etableringen av den hanterade domänen.**
> När din hanterade domän har tillhandahållits måste du ändå utföra följande uppgifter:
> * Uppdatera DNS-inställningarna för det virtuella nätverket så att virtuella datorer kan hitta den hanterade domänen för domän anslutning eller autentisering. Om du vill konfigurera DNS väljer du din Azure AD DS-hanterade domän i portalen. I **översikts** fönstret uppmanas du att konfigurera dessa DNS-inställningar automatiskt.
> * Skapa de regler för nätverks säkerhets grupper som krävs för att begränsa inkommande trafik för den hanterade domänen. Om du vill skapa regler för nätverks säkerhets grupper väljer du din Azure AD DS-hanterade domän i portalen. I **översikts** fönstret uppmanas du att automatiskt skapa lämpliga regler för nätverks säkerhets grupper.
> * **[Aktivera Lösenordssynkronisering till Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)** så att slutanvändarna kan logga in på den hanterade domänen med sina företags uppgifter.

## <a name="powershell-script"></a>PowerShell-skript
PowerShell-skriptet som används för att utföra alla uppgifter som anges i den här artikeln nedan. Kopiera skriptet och spara det i en fil med fil namns tillägget. ps1. Kör skriptet i PowerShell eller med PowerShell (Integrated Scripting Environment).

> [!NOTE]
> **Behörigheter som krävs för att köra det här skriptet** Om du vill aktivera Azure AD Domain Services måste du vara global administratör för Azure AD-katalogen. Du behöver dessutom minst "deltagar"-privilegier i det virtuella nätverk där Azure AD Domain Services aktive ras.
>

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso.onmicrosoft.com"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Connect-AzAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"

# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"

# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq '$AaddsAdminUserUpn'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId

# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $AzureLocation `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **Glöm inte att lägga till ytterligare konfigurations steg efter etableringen av den hanterade domänen.**
> När din hanterade domän har tillhandahållits måste du ändå utföra följande uppgifter:
> * Uppdatera DNS-inställningarna för det virtuella nätverket så att virtuella datorer kan hitta den hanterade domänen för domän anslutning eller autentisering. Om du vill konfigurera DNS väljer du din Azure AD DS-hanterade domän i portalen. I **översikts** fönstret uppmanas du att konfigurera dessa DNS-inställningar automatiskt.
> * Skapa de regler för nätverks säkerhets grupper som krävs för att begränsa inkommande trafik för den hanterade domänen. Om du vill skapa regler för nätverks säkerhets grupper väljer du din Azure AD DS-hanterade domän i portalen. I **översikts** fönstret uppmanas du att automatiskt skapa lämpliga regler för nätverks säkerhets grupper.
> * **[Aktivera Lösenordssynkronisering till Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)** så att slutanvändarna kan logga in på den hanterade domänen med sina företags uppgifter.

## <a name="next-steps"></a>Nästa steg
När du har skapat din hanterade domän utför du följande konfigurations åtgärder så att du kan använda den hanterade domänen:

* [Uppdatera DNS-serverinställningarna för det virtuella nätverket så att det pekar på din hanterade domän](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)
* [Aktivera Lösenordssynkronisering till din hanterade domän](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)
