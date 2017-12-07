---
title: "Aktivera Azure Active Directory Domain Services med hjälp av PowerShell | Microsoft Docs"
description: "Aktivera Azure Active Directory Domain Services med hjälp av PowerShell"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2017
ms.author: maheshu
ms.openlocfilehash: 054d3b3347ef2ce9fdbfa8ff8103cf4aa15bae20
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2017
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Aktivera Azure Active Directory Domain Services med hjälp av PowerShell
Den här artikeln visar hur du aktiverar Azure Active Directory (AD) Domain Services med hjälp av PowerShell.

## <a name="task-1-install-the-required-powershell-modules"></a>Uppgift 1: Installera PowerShell-moduler som krävs

### <a name="install-and-configure-azure-ad-powershell"></a>Installera och konfigurera Azure AD PowerShell
Följ anvisningarna i artikeln [installera Azure AD PowerShell-modulen och ansluta till Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Installera och konfigurera Azure PowerShell
Följ anvisningarna i artikeln [installera Azure PowerShell-modulen och ansluta till din Azure-prenumeration](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="task-2-create-the-required-service-principal-in-your-azure-ad-directory"></a>Uppgift 2: Skapa nödvändiga tjänstens huvudnamn i Azure AD-katalogen
Skriv följande PowerShell-kommando för att skapa tjänstens huvudnamn som krävs för Azure AD Domain Services i Azure AD-katalogen.
```powershell
# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId “2565bd9d-da50-47d4-8b85-4c97f669dc36”
```

## <a name="task-3-create-and-configure-the-aad-dc-administrators-group"></a>Uppgift 3: Skapa och konfigurera gruppen AAD DC-administratörer
Nästa uppgift är att skapa administratörsgruppen som ska användas för att delegera administrationsuppgifter för din hanterade domän.
```powershell
# Create the delegated administration group for AAD Domain Services.
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Nu när du har skapat gruppen kan du lägga till några användare i gruppen.
```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@contoso100.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="task-4-register-the-azure-ad-domain-services-resource-provider"></a>Uppgift 4: Registerresursleverantören Azure AD Domain Services
Skriv följande PowerShell-kommando för att registrera resursprovidern för Azure AD Domain Services:
```powershell
# Register the resource provider for Azure AD Domain Services with Resource Manager.
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AAD
```

## <a name="task-5-create-a-resource-group"></a>Uppgift 5: Skapa en resursgrupp
Skriv följande PowerShell-kommando för att skapa en resursgrupp:
```powershell
$ResourceGroupName = "ContosoAaddsRg"
$AzureLocation = "westus"

# Create the resource group.
New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Du kan skapa det virtuella nätverket och den hanterade domänen med Azure AD Domain Services i den här resursgruppen.


## <a name="task-6-create-and-configure-the-virtual-network"></a>Uppgift 6: Skapa och konfigurera det virtuella nätverket
Nu ska du skapa det virtuella nätverket där du aktiverar Azure AD Domain Services. Se till att du skapar ett dedikerat undernät för Azure AD Domain Services. Distribuera inte arbetsbelastning virtuella datorer i det här dedikerade undernätet.

Skriv följande PowerShell-kommandon för att skapa ett virtuellt nätverk med en dedikerad undernät för Azure AD Domain Services.

```powershell
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```


## <a name="task-7-provision-the-azure-ad-domain-services-managed-domain"></a>Uppgift 7: Etablera den Azure AD Domain Services-hanterad domänen
Skriv följande PowerShell-kommando för att aktivera Azure AD Domain Services för din katalog:

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso100.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Enable Azure AD Domain Services for the directory.
New-AzureRmResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **Glöm inte de ytterligare konfigurationsstegen när du har etablerat din hanterade domän.**
> När din hanterade domän etableras fortfarande måste du utföra följande uppgifter:
> * **[Uppdatera DNS-inställningarna](active-directory-ds-getting-started-dns.md)**  för det virtuella nätverket så att virtuella datorer kan hitta den hanterade domänen för domänanslutning eller autentisering.
* **[Aktivera Lösenordssynkronisering till Azure AD Domain Services](active-directory-ds-getting-started-password-sync.md)**, så att användarna kan logga in på den hanterade domänen med sina företagsuppgifter.
>


## <a name="powershell-script"></a>PowerShell-skript
PowerShell-skriptet som används för att utföra alla aktiviteter i den här artikeln är under. Kopiera skriptet och spara den till en fil med tillägget '.ps1'. Kör skriptet i PowerShell eller med hjälp av PowerShell Integrated Scripting Environment (ISE).

> [!NOTE]
> **Behörigheter som krävs för att köra skriptet** för att aktivera Azure AD Domain Services, du måste vara global administratör för Azure AD-katalog. Dessutom måste minst ”bidragsgivare” privilegier på det virtuella nätverket som aktiverar Azure AD Domain Services.
>

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso100.onmicrosoft.com"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "contoso100.com"
$ResourceGroupName = "ContosoAaddsRg"
$VnetName = "DomainServicesVNet_WUS"
$AzureLocation = "westus"

# Connect to your Azure AD directory.
Connect-AzureAD

# Login to your Azure subscription.
Login-AzureRmAccount

# Create the service principal for Azure AD Domain Services.
New-AzureADServicePrincipal -AppId “2565bd9d-da50-47d4-8b85-4c97f669dc36”

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
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AAD

# Create the resource group.
New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet=New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $AzureLocation `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet

# Enable Azure AD Domain Services for the directory.
New-AzureRmResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -ApiVersion 2017-06-01 -Force -Verbose
```

> [!WARNING]
> **Glöm inte de ytterligare konfigurationsstegen när du har etablerat din hanterade domän.**
> När din hanterade domän etableras fortfarande måste du utföra följande uppgifter:
> * Uppdatera DNS-inställningarna för det virtuella nätverket så att virtuella datorer kan hitta den hanterade domänen för domänanslutning eller autentisering.
* Aktivera Lösenordssynkronisering till Azure AD Domain Services så att användarna kan logga in på den hanterade domänen med sina företagsuppgifter.
>

## <a name="next-steps"></a>Nästa steg
När din hanterade domän har skapats kan du utföra följande konfigurationsåtgärder så att du kan använda den hanterade domänen:

* [Uppdatera DNS-serverinställningarna för det virtuella nätverket så att den pekar till din hanterade domän](active-directory-ds-getting-started-dns.md)
* [Aktivera Lösenordssynkronisering till din hanterade domän](active-directory-ds-getting-started-password-sync.md)
