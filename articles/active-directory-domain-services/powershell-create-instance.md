---
title: Aktivera Azure DS-domäntjänster med PowerShell | Microsoft-dokument
description: Lär dig hur du konfigurerar och aktiverar Azure Active Directory Domain Services med Azure AD PowerShell och Azure PowerShell.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: d4bc5583-6537-4cd9-bc4b-7712fdd9272a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: iainfou
ms.openlocfilehash: ee85002aea962dfa675ac6c09a6bfbaeba8e9e79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613231"
---
# <a name="enable-azure-active-directory-domain-services-using-powershell"></a>Aktivera Azure Active Directory Domain Services med PowerShell

Azure Active Directory Domain Services (Azure AD DS) tillhandahåller hanterade domäntjänster som domänanslutning, grupprincip, LDAP, Kerberos/NTLM-autentisering som är helt kompatibel med Active Directory i Windows Server. Du använder dessa domäntjänster utan att distribuera, hantera och korrigera domänkontrollanter själv. Azure AD DS integreras med din befintliga Azure AD-klientorganisation. Med den här integreringen kan användare logga in med sina företagsautentiseringsuppgifter och du kan använda befintliga grupper och användarkonton för att skydda åtkomsten till resurser.

Den här artikeln visar hur du aktiverar Azure AD DS med PowerShell.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

För att kunna slutföra den här artikeln behöver du följande resurser:

* Installera och konfigurera Azure PowerShell.
    * Om det behövs följer du instruktionerna för att [installera Azure PowerShell-modulen och ansluta till din Azure-prenumeration](/powershell/azure/install-az-ps).
    * Se till att du loggar in på din Azure-prenumeration med cmdlet [connect-AzAccount.][Connect-AzAccount]
* Installera och konfigurera Azure AD PowerShell.
    * Om det behövs följer du instruktionerna för att [installera Azure AD PowerShell-modulen och ansluter till Azure AD](/powershell/azure/active-directory/install-adv2).
    * Se till att du loggar in på din Azure AD-klient med hjälp av [Cmdlet Connect-AzureAD.][Connect-AzureAD]
* Du behöver globala administratörsbehörighet i din Azure AD-klient för att aktivera Azure AD DS. *global administrator*
* Du *Contributor* behöver deltagarbehörighet i din Azure-prenumeration för att skapa de nödvändiga Azure AD DS-resurserna.

## <a name="create-required-azure-ad-resources"></a>Skapa nödvändiga Azure AD-resurser

Azure AD DS kräver ett tjänsthuvudnamn och en Azure AD-grupp. Med dessa resurser kan Azure AD DS-hanterade domänen synkronisera data och definiera vilka användare som har administratörsbehörighet i den hanterade domänen.

Skapa först ett Azure AD-tjänsthuvudnamn för Azure AD DS för att kommunicera och autentisera sig själv. Ett specifikt program-ID används med namnet *Domain Controller Services* med ett ID på *2565bd9d-da50-47d4-8b85-4c97f669dc36*. Ändra inte det här program-ID:t.

Skapa ett Azure AD-tjänsthuvudnamn med cmdleten [New-AzureADServicePrincipal:][New-AzureADServicePrincipal]

```powershell
New-AzureADServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
```

Skapa nu en Azure AD-grupp med namnet *AAD DC Administrators*. Användare som läggs till i den här gruppen beviljas sedan behörighet att utföra administrativa uppgifter på azure AD DS-hanterade domänen.

Skapa gruppen *AAD DC-administratörer* med cmdleten [New-AzureADGroup:][New-AzureADGroup]

```powershell
New-AzureADGroup -DisplayName "AAD DC Administrators" `
  -Description "Delegated group to administer Azure AD Domain Services" `
  -SecurityEnabled $true -MailEnabled $false `
  -MailNickName "AADDCAdministrators"
```

När gruppen *AAD DC-administratörer* har skapats lägger du till en användare i gruppen med cmdleten [Add-AzureADGroupMember.][Add-AzureADGroupMember] Du får först *AAD DC-administratörer* grupp objekt-ID med hjälp av [Get-AzureADGroup][Get-AzureADGroup] cmdlet, sedan den önskade användarens objekt-ID med hjälp av [Get-AzureADUser][Get-AzureADUser] cmdlet.

I följande exempel är användarobjekt-ID för kontot `admin@aaddscontoso.onmicrosoft.com`med ett UPN i . Ersätt det här användarkontot med UPN för den användare som du vill lägga till i gruppen *AAD DC-administratörer:*

```powershell
# First, retrieve the object ID of the newly created 'AAD DC Administrators' group.
$GroupObjectId = Get-AzureADGroup `
  -Filter "DisplayName eq 'AAD DC Administrators'" | `
  Select-Object ObjectId

# Now, retrieve the object ID of the user you'd like to add to the group.
$UserObjectId = Get-AzureADUser `
  -Filter "UserPrincipalName eq 'admin@aaddscontoso.onmicrosoft.com'" | `
  Select-Object ObjectId

# Add the user to the 'AAD DC Administrators' group.
Add-AzureADGroupMember -ObjectId $GroupObjectId.ObjectId -RefObjectId $UserObjectId.ObjectId
```

## <a name="create-supporting-azure-resources"></a>Skapa stöd för Azure-resurser

Registrera först resursleverantören Azure AD Domain Services med cmdleten [Register-AzResourceProvider:][Register-AzResourceProvider]

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.AAD
```

Skapa sedan en resursgrupp med cmdleten [New-AzResourceGroup.][New-AzResourceGroup] I följande exempel heter resursgruppen *myResourceGroup* och skapas i *regionen westus.* Använd ditt eget namn och önskad region:

```powershell
$ResourceGroupName = "myResourceGroup"
$AzureLocation = "westus"

# Create the resource group.
New-AzResourceGroup `
  -Name $ResourceGroupName `
  -Location $AzureLocation
```

Skapa det virtuella nätverket och undernäten för Azure AD Domain Services. Två undernät skapas - ett för *DomainServices*och ett för *arbetsbelastningar*. Azure AD DS distribueras till det dedikerade *DomainServices-undernätet.* Distribuera inte andra program eller arbetsbelastningar i det här undernätet. Använd de separata *arbetsbelastningarna* eller andra undernät för resten av dina virtuella datorer.

Skapa undernäten med cmdleten [New-AzVirtualNetworkSubnetConfig][New-AzVirtualNetworkSubnetConfig] och skapa sedan det virtuella nätverket med cmdleten [New-AzVirtualNetwork.][New-AzVirtualNetwork]

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

## <a name="create-an-azure-ad-ds-managed-domain"></a>Skapa en Azure AD DS-hanterad domän

Nu ska vi skapa en Azure AD DS-hanterad domän. Ange ditt Azure-prenumerations-ID och ange sedan ett namn för den hanterade domänen, till exempel *aaddscontoso.com*. Du kan få ditt prenumerations-ID med [get-azsubscription][Get-AzSubscription] cmdlet.

Om du väljer en region som stöder tillgänglighetszoner distribueras Azure AD DS-resurserna mellan zoner för ytterligare redundans.

Tillgänglighetszoner är unika fysiska platser inom en Azure-region. Varje zon utgörs av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. För att säkerställa återhämtning finns det minst tre separata zoner i alla aktiverade regioner.

Det finns inget för dig att konfigurera för Azure AD DS som ska distribueras över zoner. Azure-plattformen hanterar automatiskt zonfördelningen av resurser. Mer information och om du vill se regionens tillgänglighet finns [i Vad är tillgänglighetszoner i Azure?][availability-zones].

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

Det tar några minuter att skapa resursen och återställa kontrollen till PowerShell-prompten. Den Hanterade Azure AD DS-domänen fortsätter att etableras i bakgrunden och kan ta upp till en timme att slutföra distributionen. I Azure-portalen visar **översiktssidan** för din Azure AD DS-hanterade domän aktuell status under hela den här distributionsfasen.

När Azure-portalen visar att den Hanterade Azure AD DS-domänen har slutförts måste följande uppgifter slutföras:

* Uppdatera DNS-inställningar för det virtuella nätverket så att virtuella datorer kan hitta den hanterade domänen för domänanslutning eller autentisering.
    * Om du vill konfigurera DNS väljer du din Azure AD DS-hanterade domän i portalen. I **fönstret Översikt** uppmanas du att automatiskt konfigurera dessa DNS-inställningar.
* Om du har skapat en Azure AD DS-hanterad domän i en region som stöder tillgänglighetszoner skapar du en nätverkssäkerhetsgrupp för att begränsa trafiken i det virtuella nätverket för den Azure AD DS-hanterade domänen. En Azure-standardbelastningsutjämnare skapas som kräver att dessa regler ska placeras. Den här nätverkssäkerhetsgruppen skyddar Azure AD DS och krävs för att den hanterade domänen ska fungera korrekt.
    * Om du vill skapa nätverkssäkerhetsgruppen och nödvändiga regler väljer du din Azure AD DS-hanterade domän i portalen. I **fönstret Översikt** uppmanas du att automatiskt skapa och konfigurera nätverkssäkerhetsgruppen.
* [Aktivera lösenordssynkronisering till Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) så att slutanvändare kan logga in på den hanterade domänen med sina företagsautentiseringsuppgifter.

## <a name="complete-powershell-script"></a>Fullständigt PowerShell-skript

Följande fullständiga PowerShell-skript kombinerar alla uppgifter som visas i den här artikeln. Kopiera skriptet och spara det `.ps1` i en fil med ett tillägg. Kör skriptet i en lokal PowerShell-konsol eller [Azure Cloud Shell][cloud-shell].

> [!NOTE]
> Om du vill aktivera Azure AD DS måste du vara global administratör för Azure AD-klienten. Du behöver också minst *deltagarbehörighet* i Azure-prenumerationen.

```powershell
# Change the following values to match your deployment.
$AaddsAdminUserUpn = "admin@aaddscontoso.onmicrosoft.com"
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

Det tar några minuter att skapa resursen och återställa kontrollen till PowerShell-prompten. Den Hanterade Azure AD DS-domänen fortsätter att etableras i bakgrunden och kan ta upp till en timme att slutföra distributionen. I Azure-portalen visar **översiktssidan** för din Azure AD DS-hanterade domän aktuell status under hela den här distributionsfasen.

När Azure-portalen visar att den Hanterade Azure AD DS-domänen har slutförts måste följande uppgifter slutföras:

* Uppdatera DNS-inställningar för det virtuella nätverket så att virtuella datorer kan hitta den hanterade domänen för domänanslutning eller autentisering.
    * Om du vill konfigurera DNS väljer du din Azure AD DS-hanterade domän i portalen. I **fönstret Översikt** uppmanas du att automatiskt konfigurera dessa DNS-inställningar.
* Om du har skapat en Azure AD DS-hanterad domän i en region som stöder tillgänglighetszoner skapar du en nätverkssäkerhetsgrupp för att begränsa trafiken i det virtuella nätverket för den Azure AD DS-hanterade domänen. En Azure-standardbelastningsutjämnare skapas som kräver att dessa regler ska placeras. Den här nätverkssäkerhetsgruppen skyddar Azure AD DS och krävs för att den hanterade domänen ska fungera korrekt.
    * Om du vill skapa nätverkssäkerhetsgruppen och nödvändiga regler väljer du din Azure AD DS-hanterade domän i portalen. I **fönstret Översikt** uppmanas du att automatiskt skapa och konfigurera nätverkssäkerhetsgruppen.
* [Aktivera lösenordssynkronisering till Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) så att slutanvändare kan logga in på den hanterade domänen med sina företagsautentiseringsuppgifter.

## <a name="next-steps"></a>Nästa steg

Om du vill se den hanterade Azure AD DS-domänen i praktiken kan du [domän ansluta till en Windows-vm,][windows-join]konfigurera säker [LDAP][tutorial-ldaps]och [konfigurera synkronisering av lösenord hash][tutorial-phs].

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
[cloud-shell]: /azure/cloud-shell/cloud-shell-windows-users
[availability-zones]: ../availability-zones/az-overview.md
