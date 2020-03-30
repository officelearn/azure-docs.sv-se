---
title: 'VPN Gateway: Azure AD-klient för olika användargrupper: Azure AD-autentisering'
description: Du kan använda P2S VPN för att ansluta till ditt virtuella nätverk med Azure AD-autentisering
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: alzam
ms.openlocfilehash: 118ea21cbdd2e0527659c7c1beb40d8e42fa1d10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77485720"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Skapa en Azure Active Directory-klient för P2S OpenVPN-protokollanslutningar

När du ansluter till ditt virtuella nätverk kan du använda certifikatbaserad autentisering eller RADIUS-autentisering. Men när du använder Open VPN-protokollet kan du också använda Azure Active Directory-autentisering. Om du vill att olika användare ska kunna ansluta till olika VPN-gateways kan du registrera flera appar i AD och länka dem till olika VPN-gateways. Den här artikeln hjälper dig att konfigurera en Azure AD-klient för P2S OpenVPN-autentisering och skapa och registrera flera appar i Azure AD för att tillåta olika åtkomst för olika användare och grupper.

> [!NOTE]
> Azure AD-autentisering stöds endast för OpenVPN® protokollanslutningar.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6. Aktivera autentisering på gatewayen

I det här steget aktiverar du Azure AD-autentisering på VPN-gatewayen.

1. Aktivera Azure AD-autentisering på VPN-gatewayen genom att köra följande kommandon. Var noga med att ändra kommandona så att de återspeglar din egen miljö:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "application ID from previous section" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24
    ```
    > [!NOTE]
    > Använd inte Azure VPN-klientens program-ID i kommandona ovan: Det ger alla användare åtkomst till VPN-gatewayen. Använd ID:t för de program som du registrerade.

2. Skapa och hämta profilen genom att köra följande kommandon. Ändra värdena -ResourcGroupName och -Name så att de matchar dina egna.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

3. När du har kört kommandona visas ett resultat som liknar det nedan. Kopiera resultat-URL:en till webbläsaren för att hämta zip-filen för profilen.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/profile.png)

4. Extrahera den nedladdade zip-filen.

5. Bläddra till mappen "AzureVPN" som inte har uppackats.

6. Anteckna platsen för filen "azurevpnconfig.xml". Azurevpnconfig.xml innehåller inställningen för VPN-anslutningen och kan importeras direkt till Azure VPN-klientprogrammet. Du kan också distribuera den här filen till alla användare som behöver ansluta via e-post eller på annat sätt. Användaren behöver giltiga Azure AD-autentiseringsuppgifter för att kunna ansluta.

## <a name="next-steps"></a>Nästa steg

För att kunna ansluta till det virtuella nätverket måste du skapa och konfigurera en VPN-klientprofil. Se [Konfigurera en VPN-klient för P2S VPN-anslutningar](openvpn-azure-ad-client.md).
