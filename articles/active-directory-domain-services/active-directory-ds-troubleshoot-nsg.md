---
title: "Azure Active Directory Domain Services: Felsökning Nätverkssäkerhetsgruppen konfiguration | Microsoft Docs"
description: "Felsöka NSG konfigurationen för Azure AD Domain Services"
services: active-directory-ds
documentationcenter: 
author: eringreenlee
manager: 
editor: 
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: ergreenl
ms.openlocfilehash: 447f9119ea379e278be77d8699c7dcb751ea3085
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="azure-ad-domain-services---troubleshooting-network-security-group-configuration"></a>Azure AD Domain Services - felsökning Nätverkssäkerhetsgruppen konfiguration



## <a name="aadds104-network-error"></a>AADDS104: Nätverksfel

**Varningsmeddelande:** *Microsoft kan inte nå domänkontrollanterna för den här hanterade domänen. Detta kan inträffa om en nätverkssäkerhetsgrupp (NSG) som har konfigurerats på din virtuella nätverk blockerar åtkomst till den hanterade domänen. En annan möjlig orsak är att om det finns en användardefinierad väg som blockerar inkommande trafik från internet.*

Den vanligaste orsaken till nätverksfel för Azure AD Domain Services kan bero på felaktig NSG-konfigurationer. För att säkerställa att Microsoft kan tjänsten och underhålla hanteras av domänen, måste du använda en Nätverkssäkerhetsgrupp (NSG) för att tillåta åtkomst till [specifika portar](active-directory-ds-networking.md#ports-required-for-azure-ad-domain-services) i undernätet. Om dessa portar blockeras Microsoft kan inte komma åt resurser som behövs, som kan vara skadligt för din tjänst. När du skapar din NSG att dessa portar hålls öppna så utan avbrott i tjänsten.

## <a name="sample-nsg"></a>Exempel NSG
Följande tabell visar ett exempel på en NSG som vill skydda din hanterade domän samtidigt som Microsoft för att övervaka, hantera och uppdatera informationen.

![exempel NSG](.\media\active-directory-domain-services-alerts\default-nsg.png)

>[!NOTE]
> Azure AD Domain Services kräver obegränsad utgående åtkomst. Vi rekommenderar inte för att skapa någon ytterligare outboud regel till dina NSG: er.

## <a name="adding-a-rule-to-a-network-security-group-using-the-azure-portal"></a>Lägger till en regel till en Nätverkssäkerhetsgrupp med Azure-portalen
Om du inte vill använda PowerShell kan du manuellt lägga till regler som enda NSG: er med hjälp av Azure portal. Följ dessa steg för att skapa regler i din nätverkssäkerhetsgruppen.

1. Navigera till den [Nätverkssäkerhetsgrupper](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.Network%2FNetworkSecurityGroups) sida i Azure-portalen
2. Välj NSG: N som är associerade med domänen från tabellen.
3. Klicka på antingen under inställningar i det vänstra navigeringsfältet **inkommande säkerhetsregler** eller **utgående säkerhetsregler**.
4. Skapa regeln genom att klicka på **Lägg till** och fylla i informationen. Klicka på **OK**.
5. Kontrollera att regeln har skapats genom att söka efter tabellen.


## <a name="create-a-default-nsg-using-powershell"></a>Skapa en standard NSG med hjälp av PowerShell

Föregående finns steg som du kan använda för att skapa en ny NSG med hjälp av PowerShell som håller alla portar som behövs för att köra Azure AD Domain Services öppna medan oönskad åtkomst nekas.


Denna lösning kräver installation och körning av [Azure AD Powershell](https://docs.microsoft.com/en-us/powershell/azure/active-directory/install-adv2?toc=%2Fazure%2Factive-directory-domain-services%2Ftoc.json&view=azureadps-2.0).

1. Ansluta till Azure AD-katalogen.

  ```PowerShell
  # Connect to your Azure AD directory.
  Connect-AzureAD
  ```
2. Logga in på Azure-prenumerationen.

  ```PowerShell
  # Log in to your Azure subscription.
  Login-AzureRmAccount
  ```

3. Skapa en NSG med tre regler. Följande skript definierar tre regler för NSG: N som tillåter åtkomst till de portar som behövs för att köra Azure AD Domain Services. Skriptet skapar sedan en ny NSG som innehåller dessa regler. Är du Välkommen att lägga till ytterligare regler som du vill använda samma format.

  ```PowerShell
  # Create the rules needed
  $rule1 = New-AzureRmNetworkSecurityRuleConfig -Name https-rule -Description "Allow HTTP" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 443
  $rule2 = New-AzureRmNetworkSecurityRuleConfig -Name manage-3389 -Description "Manage domain through port 3389" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
  -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389
  $rule3 = New-AzureRmNetworkSecurityRuleConfig -Name manage-5986 -Description "Manage domain through port 5986" `
  -Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
  -SourceAddressPrefix $serviceIPs -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 5986
  # Create the NSG with the 3 rules above
  $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
  -Name "AADDS-NSG" -SecurityRules $rule1,$rule2,$rule3
  ```

4. Slutligen associerar skriptet NSG: N med vnet och undernät föredrar.

  ```PowerShell
  # Find vnet and subnet
  $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Name $vnetName
  $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName
  # Set the nsg to the subnet and save the changes
  $subnet.NetworkSecurityGroup = $nsg
  Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
  ```

### <a name="full-script"></a>Fullständigt skript

```PowerShell
#Change the following values to match your deployment
$resourceGroup = "ResourceGroupName"
$vnetName = "exampleVnet"
$subnetName = "exampleSubnet"

$serviceIPs = "52.180.183.8, 23.101.0.70, 52.225.184.198, 52.179.126.223, 13.74.249.156, 52.187.117.83, 52.161.13.95, 104.40.156.18, 104.40.87.209, 52.180.179.108, 52.175.18.134, 52.138.68.41, 104.41.159.212, 52.169.218.0, 52.187.120.237, 52.161.110.169, 52.174.189.149, 13.64.151.161"

# Create the rules needed
$rule1 = New-AzureRmNetworkSecurityRuleConfig -Name https-rule -Description "Allow HTTP" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 443

$rule2 = New-AzureRmNetworkSecurityRuleConfig -Name manage-3389 -Description "Manage domain through port 3389" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 102 `
-SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389

$rule3 = New-AzureRmNetworkSecurityRuleConfig -Name manage-5986 -Description "Manage domain through port 5986" `
-Access Allow -Protocol Tcp -Direction Inbound -Priority 103 `
-SourceAddressPrefix $serviceIPs -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 3389


# Connect to your Azure AD directory.
Connect-AzureAD

# Log in to your Azure subscription.
Login-AzureRmAccount

# Create the NSG with the 3 rules above
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location westus `
-Name "NSG-Default" -SecurityRules $rule1,$rule2,$rule3

# Find vnet and subnet
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Name $vnetName
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName

# Set the nsg to the subnet and save the changes
$subnet.NetworkSecurityGroup = $nsg
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

> [!NOTE]
>Den här standardinställningen NSG inte låsa åtkomst till den port som används för säker LDAP. Du hittar information om hur du skapar en regel för den här porten [i den här artikeln](active-directory-ds-troubleshoot-ldaps.md).
>

## <a name="contact-us"></a>Kontakta oss
Kontakta produktteamet Azure Active Directory Domain Services för att [dela feedback eller support](active-directory-ds-contact-us.md).
