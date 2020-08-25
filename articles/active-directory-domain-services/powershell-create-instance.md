---
title: Aktivera Azure DS Domain Services med PowerShell | Microsoft Docs
description: Lär dig hur du konfigurerar och aktiverar Azure Active Directory Domain Services med hjälp av Azure AD PowerShell och Azure PowerShell.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: sample
ms.date: 07/09/2020
ms.author: iainfou
ms.openlocfilehash: 05d4bc8d249d5ac45fd36ce1ae58ca56870e3be6
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/21/2020
ms.locfileid: "88722796"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Aktivera Azure Active Directory Domain Services med PowerShell

Azure Active Directory Domain Services (Azure AD DS) tillhandahåller hanterade domän tjänster som domän anslutning, grup princip, LDAP, Kerberos/NTLM-autentisering som är helt kompatibelt med Windows Server Active Directory. Du använder dessa domän tjänster utan att distribuera, hantera och korrigera domänkontrollanter själv. Azure AD DS integreras med din befintliga Azure AD-klient. Med den här integreringen kan användarna logga in med sina företags uppgifter, och du kan använda befintliga grupper och användar konton för att skydda åtkomsten till resurser.

Den här artikeln visar hur du aktiverar Azure AD DS med hjälp av PowerShell.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här artikeln behöver du följande resurser:

* Installera och konfigurera Azure PowerShell.
    * Om det behövs följer du anvisningarna för att [installera Azure PowerShell-modulen och ansluta till din Azure-prenumeration](/powershell/azure/install-az-ps).
    * Kontrol lera att du loggar in på Azure-prenumerationen med hjälp av cmdleten [Connect-AzAccount][Connect-AzAccount] .
* Installera och konfigurera Azure AD PowerShell.
    * Om det behövs följer du anvisningarna för att [Installera Azure AD PowerShell-modulen och ansluta till Azure AD](/powershell/azure/active-directory/install-adv2).
    * Kontrol lera att du loggar in på Azure AD-klienten med hjälp av cmdleten [Connect-AzureAD][Connect-AzureAD] .
* Du behöver *Global administratörs* behörighet i Azure AD-klienten för att aktivera Azure AD DS.
* Du behöver *deltagar* behörighet i din Azure-prenumeration för att skapa de nödvändiga Azure AD DS-resurserna.

## <a name="create-required-azure-ad-resources"></a>Skapa nödvändiga Azure AD-resurser

Azure AD DS kräver ett huvud namn för tjänsten och en Azure AD-grupp. Dessa resurser låter Azure AD DS-hanterad domän synkronisera data och definiera vilka användare som har administrativa behörigheter i den hanterade domänen.

Börja med att skapa ett Azure AD-tjänstens huvud namn för Azure AD DS för att kommunicera och autentisera sig själv. Ett angivet program-ID används med namnet *domänkontrollant tjänster* med ID *2565bd9d-DA50-47d4-8b85-4c97f669dc36*. Ändra inte det här program-ID: t.

Skapa ett Azure AD-tjänstens huvud namn med cmdleten [New-AzureADServicePrincipal][New-AzureADServicePrincipal] :

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Skapa nu en Azure AD-grupp med namnet *AAD DC-administratörer*. Användare som läggs till i den här gruppen beviljas sedan behörigheter för att utföra administrations uppgifter på den hanterade domänen.

Skapa *Administratörs* gruppen för AAD-domänkontrollanter med cmdleten [New-AzureADGroup][New-AzureADGroup] :

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

Med *Administratörs gruppen för AAD-domänkontrollanten* skapad lägger du till en användare i gruppen med cmdleten [Add-AzureADGroupMember][Add-AzureADGroupMember] . Först får du ett objekt-ID för *AAD DC-administratörer* som använder cmdleten [Get-AzureADGroup][Get-AzureADGroup] och sedan den önskade användarens objekt-ID med hjälp av cmdleten [Get-AzureADUser][Get-AzureADUser] .

I följande exempel är användar objekt-ID: t för kontot med UPN för `admin@contoso.onmicrosoft.com` . Ersätt det här användar kontot med UPN för den användare som du vill lägga till i *Administratörs* gruppen för AAD-domänkontrollant:

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

## <a name="create-supporting-azure-resources"></a>Skapa stöd för Azure-resurser

Registrera först Azure AD Domain Services Resource Provider med hjälp av cmdleten [register-AzResourceProvider][Register-AzResourceProvider] :

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Skapa sedan en resurs grupp med cmdleten [New-AzResourceGroup][New-AzResourceGroup] . I följande exempel heter resurs gruppen *myResourceGroup* och skapas i regionen *väst* . Använd ditt eget namn och önskad region:

```powershell
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Skapa det virtuella nätverket och undernät för Azure AD Domain Services. Två undernät skapas – ett för *DomainServices*och ett för *arbets belastningar*. Azure AD DS distribueras till det dedikerade *DomainServices* -undernätet. Distribuera inte andra program eller arbets belastningar i det här under nätet. Använd de separata *arbets belastningarna* eller andra undernät för resten av dina virtuella datorer.

Skapa under näten med cmdleten [New-AzVirtualNetworkSubnetConfig][New-AzVirtualNetworkSubnetConfig] och skapa sedan det virtuella nätverket med cmdleten [New-AzVirtualNetwork][New-AzVirtualNetwork] .

```powershell
$VnetName = "myVnet"

# Create the dedicated subnet for AAD Domain Services.
$AaddsSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name DomainServices `
  -AddressPrefix 10.0.0.0/24

$WorkloadSubnet = New-AzVirtualNetworkSubnetConfig `
  -Name Workloads `
  -AddressPrefix 10.0.1.0/24

# Create the virtual network in which you will enable Azure AD Domain Services.
$Vnet= New-AzVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location westus `
  -Name $VnetName `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $AaddsSubnet,$WorkloadSubnet
```

## <a name="create-a-managed-domain"></a>Skapa en hanterad domän

Nu ska vi skapa en hanterad domän. Ange ditt ID för Azure-prenumerationen och ange sedan ett namn för den hanterade domänen, till exempel *aaddscontoso.com*. Du kan hämta ditt prenumerations-ID med hjälp av cmdleten [Get-AzSubscription][Get-AzSubscription] .

Om du väljer en region som stöder Tillgänglighetszoner fördelas Azure AD DS-resurserna mellan zoner för ytterligare redundans.

Tillgänglighetszoner är unika fysiska platser inom en Azure-region. Varje zon utgörs av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. För att säkerställa återhämtning finns det minst tre separata zoner i alla aktiverade regioner.

Det finns inget som du kan konfigurera för att Azure AD DS ska distribueras mellan zoner. Azure-plattformen hanterar automatiskt zon distributionen av resurser. Mer information och mer information om regions tillgänglighet finns i [Vad är Tillgänglighetszoner i Azure?][availability-zones].

```powershell
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

# Enable Azure AD Domain Services for the directory.
New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
  -Location $AzureLocation `
  -Properties @{"DomainName"=$ManagedDomainName; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
  -Force -Verbose
```

Det tar några minuter att skapa resursen och returnera kontrollen till PowerShell-prompten. Den hanterade domänen fortsätter att tillhandahållas i bakgrunden och kan ta upp till en timme att slutföra distributionen. På sidan Azure Portal visar **översikts** sidan för din hanterade domän den aktuella statusen i den här distributions fasen.

När Azure Portal visar att den hanterade domänen har slutfört etableringen måste följande uppgifter utföras:

* Uppdatera DNS-inställningarna för det virtuella nätverket så att virtuella datorer kan hitta den hanterade domänen för domän anslutning eller autentisering.
    * Om du vill konfigurera DNS väljer du din hanterade domän i portalen. I **översikts** fönstret uppmanas du att konfigurera dessa DNS-inställningar automatiskt.
* Skapa en nätverks säkerhets grupp för att begränsa trafiken i det virtuella nätverket för den hanterade domänen. En Azure standard Load Balancer skapas som kräver att dessa regler placeras. Den här nätverks säkerhets gruppen säkrar Azure AD DS och krävs för att den hanterade domänen ska fungera korrekt.
    * Om du vill skapa nätverks säkerhets gruppen och nödvändiga regler måste du först installera `New-AzureAddsNetworkSecurityGroup` skriptet med `Install-Script -Name New-AaddsNetworkSecurityGroup` kommandot och sedan köra `New-AaddsNetworkSecurityGroup` . De regler som krävs för den hanterade domänen skapas åt dig.
* [Aktivera Lösenordssynkronisering till Azure AD DS](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) så att slutanvändarna kan logga in på den hanterade domänen med sina företags uppgifter.

## <a name="complete-powershell-script"></a>Slutför PowerShell-skript

Följande fullständiga PowerShell-skript kombinerar alla uppgifter som visas i den här artikeln. Kopiera skriptet och spara det i en fil med ett `.ps1` fil namns tillägg. Kör skriptet i en lokal PowerShell-konsol eller [Azure Cloud Shell][cloud-shell].

> [!NOTE]
> Om du vill aktivera Azure AD DS måste du vara global administratör för Azure AD-klienten. Du måste också ha minst *deltagar* behörighet i Azure-prenumerationen.

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@contoso.onmicrosoft.com"
$ResourceGroupName = "myResourceGroup"
$VnetName = "myVnet"
$AzureLocation = "westus"
$AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
$ManagedDomainName = "aaddscontoso.com"

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
  -Force -Verbose
```

Det tar några minuter att skapa resursen och returnera kontrollen till PowerShell-prompten. Den hanterade domänen fortsätter att tillhandahållas i bakgrunden och kan ta upp till en timme att slutföra distributionen. På sidan Azure Portal visar **översikts** sidan för din hanterade domän den aktuella statusen i den här distributions fasen.

När Azure Portal visar att den hanterade domänen har slutfört etableringen måste följande uppgifter utföras:

* Uppdatera DNS-inställningarna för det virtuella nätverket så att virtuella datorer kan hitta den hanterade domänen för domän anslutning eller autentisering.
    * Om du vill konfigurera DNS väljer du din hanterade domän i portalen. I **översikts** fönstret uppmanas du att konfigurera dessa DNS-inställningar automatiskt.
* Skapa en nätverks säkerhets grupp för att begränsa trafiken i det virtuella nätverket för den hanterade domänen. En Azure standard Load Balancer skapas som kräver att dessa regler placeras. Den här nätverks säkerhets gruppen säkrar Azure AD DS och krävs för att den hanterade domänen ska fungera korrekt.
    * Om du vill skapa nätverks säkerhets gruppen och nödvändiga regler måste du först installera `New-AzureAddsNetworkSecurityGroup` skriptet med `Install-Script -Name New-AaddsNetworkSecurityGroup` kommandot och sedan köra `New-AaddsNetworkSecurityGroup` . De regler som krävs för den hanterade domänen skapas åt dig.
* [Aktivera Lösenordssynkronisering till Azure AD DS](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) så att slutanvändarna kan logga in på den hanterade domänen med sina företags uppgifter.

## <a name="next-steps"></a>Nästa steg

Om du vill se den hanterade domänen i praktiken kan du [domän ansluta till en virtuell Windows-dator][windows-join], [Konfigurera säker LDAP][tutorial-ldaps]och [Konfigurera hash-synkronisering av lösen ord][tutorial-phs].

<!-- INTERNAL LINKS -->
[windows-join]: join-windows-vm.md
[tutorial-ldaps]: tutorial-configure-ldaps.md
[tutorial-phs]: tutorial-configure-password-hash-sync.md

<!-- EXTERNAL LINKS -->
[Connect-AzAccount]: /powershell/module/Az.Accounts/Connect-AzAccount
[Connect-AzureAD]: /powershell/module/AzureAD/Connect-AzureAD
[New-AzureADServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
[New-AzureADGroup]: /powershell/module/AzureAD/New-AzureADGroup
[Add-AzureADGroupMember]: /powershell/module/AzureAD/Add-AzureADGroupMember
[Get-AzureADGroup]: /powershell/module/AzureAD/Get-AzureADGroup
[Get-AzureADUser]: /powershell/module/AzureAD/Get-AzureADUser
[Register-AzResourceProvider]: /powershell/module/Az.Resources/Register-AzResourceProvider
[New-AzResourceGroup]: /powershell/module/Az.Resources/New-AzResourceGroup
[New-AzVirtualNetworkSubnetConfig]: /powershell/module/Az.Network/New-AzVirtualNetworkSubnetConfig
[New-AzVirtualNetwork]: /powershell/module/Az.Network/New-AzVirtualNetwork
[Get-AzSubscription]: /powershell/module/Az.Accounts/Get-AzSubscription
[cloud-shell]: ../cloud-shell/cloud-shell-windows-users.md
[availability-zones]: ../availability-zones/az-overview.md