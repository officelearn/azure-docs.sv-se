---
title: 'Konfigurera MACsec-ExpressRoute: Azure | Microsoft Docs'
description: Den här artikeln hjälper dig att konfigurera MACsec för att skydda anslutningarna mellan dina gräns-routrar och Microsofts gräns routrar.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: cherylmc
ms.openlocfilehash: 4e07274f7abf87360c054edd3fe0ade1c09907a7
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2019
ms.locfileid: "72178597"
---
# <a name="configure-macsec-on-expressroute-direct-ports"></a>Konfigurera MACsec på ExpressRoute Direct-portar

Den här artikeln hjälper dig att konfigurera MACsec för att skydda anslutningarna mellan dina gräns-routrar och Microsofts gräns routrar med PowerShell.

## <a name="before-you-begin"></a>Innan du börjar

Innan du startar konfigurationen kontrollerar du följande:

* Du förstår [ExpressRoute Direct etablering av arbets flöden](expressroute-erdirect-about.md).
* Du har skapat en [ExpressRoute Direct-port resurs](expressroute-howto-erdirect.md).
* Om du vill köra PowerShell lokalt kontrollerar du att den senaste versionen av Azure PowerShell är installerad på datorn.

### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="sign-in-and-select-the-right-subscription"></a>Logga in och välj rätt prenumeration

För att starta konfigurationen loggar du in på ditt Azure-konto och väljer den prenumeration som du vill använda.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="1-create-azure-key-vault-macsec-secrets-and-user-identity"></a>1. Skapa Azure Key Vault, MACsec hemligheter och användar identitet

1. Skapa en Key Vault-instans för att lagra MACsec hemligheter i en ny resurs grupp.

    ```azurepowershell-interactive
    New-AzResourceGroup -Name "your_resource_group" -Location "resource_location"
    $keyVault = New-AzKeyVault -Name "your_key_vault_name" -ResourceGroupName "your_resource_group" -Location "resource_location" -EnableSoftDelete 
    ```

    Om du redan har ett nyckel valv eller en resurs grupp kan du återanvända dem. Det är dock viktigt att du aktiverar funktionen för [ **mjuk borttagning** ](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-ovw-soft-delete) i ditt befintliga nyckel valv. Om mjuk borttagning inte har Aktiver ATS kan du använda följande kommandon för att aktivera det:

    ```azurepowershell-interactive
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "your_existing_keyvault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```
2. Skapa en användar identitet.

    ```azurepowershell-interactive
    $identity = New-AzUserAssignedIdentity  -Name "identity_name" -Location "resource_location" -ResourceGroupName "your_resource_group"
    ```

    Om New-AzUserAssignedIdentity inte identifieras som en giltig PowerShell-cmdlet installerar du följande modul (i administratörs läge) och kör kommandot ovan igen.

    ```azurepowershell-interactive
    Install-Module -Name Az.ManagedServiceIdentity
    ```
3. Skapa en anslutnings Associations nyckel (CAK) och ett anslutnings Associations nyckel namn (CKN) och lagra dem i nyckel valvet.

    ```azurepowershell-interactive
    $CAK = ConvertTo-SecureString "your_key" -AsPlainText -Force
    $CKN = ConvertTo-SecureString "your_key_name" -AsPlainText -Force
    $MACsecCAKSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CAK_name" -SecretValue $CAK
    $MACsecCKNSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CKN_name" -SecretValue $CKN
    ```
4. Tilldela användar identiteten GET-behörighet.

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName "your_key_vault_name" -PermissionsToSecrets get -ObjectId $identity.PrincipalId
    ```

   Nu kan den här identiteten Hämta hemligheter, till exempel CAK och CKN, från nyckel valvet.
5. Ange att den här användar identiteten ska användas av ExpressRoute.

    ```azurepowershell-interactive
    $erIdentity = New-AzExpressRoutePortIdentity -UserAssignedIdentityId $identity.Id
    ```
 
## <a name="2-configure-macsec-on-expressroute-direct-ports"></a>2. Konfigurera MACsec på ExpressRoute Direct-portar

### <a name="to-enable-macsec"></a>Så här aktiverar du MACsec

Varje ExpressRoute Direct-instans har två fysiska portar. Du kan välja att aktivera MACsec på båda portarna samtidigt eller aktivera MACsec på en port i taget. Genom att göra det en port i taget (genom att växla trafik till en aktiv port samtidigt som den andra porten betjänas) kan du minimera avbrottet om din ExpressRoute Direct redan är i tjänst.

1. Ange MACsec-hemligheter och cipher och koppla användar identiteten till porten så att ExpressRoute kan komma åt MACsec hemligheter om det behövs.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[0]. MacSecConfig.Cipher = "gcm-aes-128"
    $erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[1]. MacSecConfig.Cipher = "gcm-aes-128"
    $erDirect.identity = $erIdentity
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```
2. Valfritt Om portarna är i läget för administrativ avstängning kan du köra följande kommandon för att ta fram portarna.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0].AdminState = “Enabled”
    $erDirect.Links[1].AdminState = “Enabled”
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```

    I det här läget är MACsec aktiverat på ExpressRoute Direct-portarna på Microsoft-sidan. Om du inte har konfigurerat den på dina gräns enheter kan du fortsätta att konfigurera dem med samma MACsec-hemligheter och-chiffer.

### <a name="to-disable-macsec"></a>Så här inaktiverar du MACsec

Om MACsec inte längre behövs för din ExpressRoute-direkta instans kan du köra följande kommandon för att inaktivera det.

```azurepowershell-interactive
$erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
$erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.identity = $null
Set-AzExpressRoutePort -ExpressRoutePort $erDirect
```

I det här läget är MACsec inaktiverat på ExpressRoute Direct-portarna på Microsoft-sidan.

## <a name="next-steps"></a>Nästa steg
1. [Skapa en ExpressRoute-krets på ExpressRoute Direct](expressroute-howto-erdirect.md)
2. [Länka en ExpressRoute-krets till ett virtuellt Azure-nätverk](expressroute-howto-linkvnet-arm.md)
3. [Kontrollera ExpressRoute-anslutningen](expressroute-troubleshooting-expressroute-overview.md)
