---
title: 'Azure ExpressRoute: Konfigurera MACsec'
description: Den här artikeln hjälper dig att konfigurera MACsec för att skydda anslutningarna mellan dina kantroutrar och Microsofts kantroutrar.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: cherylmc
ms.openlocfilehash: 626302845dfb4b19deb921675601818b35ab8edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083552"
---
# <a name="configure-macsec-on-expressroute-direct-ports"></a>Konfigurera MACsec på ExpressRoute Direct-portar

Den här artikeln hjälper dig att konfigurera MACsec för att skydda anslutningarna mellan dina kantroutrar och Microsofts kantroutrar med PowerShell.

## <a name="before-you-begin"></a>Innan du börjar

Innan du startar konfigurationen bekräftar du följande:

* Du förstår [ExpressRoute Direct-etableringsarbetsflöden](expressroute-erdirect-about.md).
* Du har skapat en [ExpressRoute Direct-portresurs](expressroute-howto-erdirect.md).
* Om du vill köra PowerShell lokalt kontrollerar du att den senaste versionen av Azure PowerShell är installerad på datorn.

### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="sign-in-and-select-the-right-subscription"></a>Logga in och välj rätt prenumeration

Om du vill starta konfigurationen loggar du in på ditt Azure-konto och väljer den prenumeration som du vill använda.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="1-create-azure-key-vault-macsec-secrets-and-user-identity"></a>1. Skapa Azure Key Vault, MACsec hemligheter och användaridentitet

1. Skapa en Key Vault-instans för att lagra MACsec-hemligheter i en ny resursgrupp.

    ```azurepowershell-interactive
    New-AzResourceGroup -Name "your_resource_group" -Location "resource_location"
    $keyVault = New-AzKeyVault -Name "your_key_vault_name" -ResourceGroupName "your_resource_group" -Location "resource_location" -EnableSoftDelete 
    ```

    Om du redan har ett nyckelvalv eller en resursgrupp kan du återanvända dem. Det är dock viktigt att du aktiverar funktionen [ **för mjuk borttagning** ](../key-vault/key-vault-ovw-soft-delete.md) i ditt befintliga nyckelvalv. Om mjuk borttagning inte är aktiverat kan du använda följande kommandon för att aktivera den:

    ```azurepowershell-interactive
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "your_existing_keyvault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```
2. Skapa en användaridentitet.

    ```azurepowershell-interactive
    $identity = New-AzUserAssignedIdentity  -Name "identity_name" -Location "resource_location" -ResourceGroupName "your_resource_group"
    ```

    Om New-AzUserAssignedIdentity inte känns igen som en giltig PowerShell-cmdlet installerar du följande modul (i administratörsläge) och kör kommandot ovan.

    ```azurepowershell-interactive
    Install-Module -Name Az.ManagedServiceIdentity
    ```
3. Skapa en anslutningsassocieringsnyckel (CAK) och ett anslutningsassociationsnyckelnamn (CKN) och lagra dem i nyckelvalvet.

    ```azurepowershell-interactive
    $CAK = ConvertTo-SecureString "your_key" -AsPlainText -Force
    $CKN = ConvertTo-SecureString "your_key_name" -AsPlainText -Force
    $MACsecCAKSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CAK_name" -SecretValue $CAK
    $MACsecCKNSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CKN_name" -SecretValue $CKN
    ```
4. Tilldela GET-behörigheten till användaridentiteten.

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName "your_key_vault_name" -PermissionsToSecrets get -ObjectId $identity.PrincipalId
    ```

   Nu kan den här identiteten hämta hemligheterna, till exempel CAK och CKN, från nyckelvalvet.
5. Ange att den här användaridentiteten ska användas av ExpressRoute.

    ```azurepowershell-interactive
    $erIdentity = New-AzExpressRoutePortIdentity -UserAssignedIdentityId $identity.Id
    ```
 
## <a name="2-configure-macsec-on-expressroute-direct-ports"></a>2. Konfigurera MACsec på ExpressRoute Direct-portar

### <a name="to-enable-macsec"></a>Så här aktiverar du MACsec

Varje ExpressRoute Direct-instans har två fysiska portar. Du kan välja att aktivera MACsec på båda portarna samtidigt eller aktivera MACsec på en port i taget. Att göra det en port i taget (genom att växla trafik till en aktiv port medan service den andra porten) kan bidra till att minimera avbrottet om din ExpressRoute Direct redan är i drift.

1. Ange MACsec hemligheter och chiffer och associera användaridentitet med porten så att ExpressRoute hanteringskoden kan komma åt MACsec hemligheter om det behövs.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[0]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[1]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.identity = $erIdentity
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```
2. (Valfritt) Om portarna är i administrativt nedåtläge kan du köra följande kommandon för att visa portarna.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0].AdminState = "Enabled"
    $erDirect.Links[1].AdminState = "Enabled"
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```

    Nu är MACsec aktiverat på ExpressRoute Direct-portarna på Microsoft-sidan. Om du inte har konfigurerat den på dina kantenheter kan du fortsätta att konfigurera dem med samma MACsec-hemligheter och chiffer.

### <a name="to-disable-macsec"></a>Så här inaktiverar du MACsec

Om MACsec inte längre önskas på din ExpressRoute Direct-instans kan du köra följande kommandon för att inaktivera den.

```azurepowershell-interactive
$erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
$erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.identity = $null
Set-AzExpressRoutePort -ExpressRoutePort $erDirect
```

Nu är MACsec inaktiverat på ExpressRoute Direct-portarna på Microsoft-sidan.

### <a name="test-connectivity"></a>Testa anslutning
När du har konfigurerat MACsec (inklusive MACsec-nyckeluppdatering) på dina ExpressRoute Direct-portar [kontrollerar du](expressroute-troubleshooting-expressroute-overview.md) om BGP-sessionerna för kretsarna är igång. Om du inte har någon krets på portarna ännu, skapa en först och konfigurera Azure Private Peering eller Microsoft Peering av kretsen. Om MACsec är felkonfigurerat, inklusive MACsec-nyckelfel, mellan nätverksenheterna och Microsofts nätverksenheter visas inte ARP-upplösningen vid lager 2 och BGP-etablering på lager 3. Om allt är korrekt konfigurerat bör du se BGP-rutterna som annonseras korrekt i båda riktningarna och ditt programdataflöde i enlighet därmed via ExpressRoute.

## <a name="next-steps"></a>Nästa steg
1. [Skapa en ExpressRoute-krets på ExpressRoute Direct](expressroute-howto-erdirect.md)
2. [Länka en ExpressRoute-krets till ett virtuellt Azure-nätverk](expressroute-howto-linkvnet-arm.md)
3. [Verifiera ExpressRoute-anslutning](expressroute-troubleshooting-expressroute-overview.md)
