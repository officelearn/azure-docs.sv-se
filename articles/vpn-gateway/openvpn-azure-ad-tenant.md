---
title: 'VPN Gateway: Azure AD-klient för P2S VPN-anslutningar: Azure AD-autentisering'
description: Du kan använda P2S VPN för att ansluta till ditt virtuella nätverk med Azure AD-autentisering
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: alzam
ms.openlocfilehash: f4092f651a3058c8a2e738c81d9db7e296386bfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402891"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Skapa en Azure Active Directory-klient för P2S OpenVPN-protokollanslutningar

När du ansluter till ditt virtuella nätverk kan du använda certifikatbaserad autentisering eller RADIUS-autentisering. Men när du använder Open VPN-protokollet kan du också använda Azure Active Directory-autentisering. Den här artikeln hjälper dig att konfigurera en Azure AD-klient för P2S Open VPN-autentisering.

> [!NOTE]
> Azure AD-autentisering stöds endast för OpenVPN® protokollanslutningar.
>


## <a name="1-verify-azure-ad-tenant"></a><a name="tenant"></a>1. Verifiera Azure AD-klient

Kontrollera att du har en Azure AD-klientorganisation. Om du inte har en Azure AD-klient kan du skapa en med hjälp av stegen i artikeln [Skapa en ny klientorganisation:](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* Organisationsnamn
* Ursprungligt domännamn

Exempel:

   ![Ny Azure AD-klient](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Skapa Azure AD-klientanvändare

Din Azure AD-klient behöver följande konton: ett globalt administratörskonto och ett huvudanvändarkonto. Huvudanvändarkontot används som huvudinbäddningskonto (tjänstkonto). När du skapar ett Azure AD-klientanvändarkonto justerar du katalogrollen för den typ av användare som du vill skapa.

Följ stegen i den [här artikeln](../active-directory/fundamentals/add-users-azure-active-directory.md) för att skapa minst två användare för din Azure AD-klientorganisation. Var noga med att ändra **katalogrollen** för att skapa kontotyperna:

* Global administratör
* Användare

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. Aktivera Azure AD-autentisering på VPN-gatewayen

1. Leta reda på katalog-ID för den katalog som du vill använda för autentisering. Den visas i egenskapsavsnittet på Active Directory-sidan.

    ![Katalog-ID](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. Kopiera katalog-ID:t.

3. Logga in på Azure-portalen som en användare som har tilldelats rollen **Global administratör.**

4. Ge sedan administratörsgodkännande. Kopiera och klistra in webbadressen som gäller din distributionsplats i webbläsarens adressfält:

    Offentlig

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Tyskland

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure Kina 21Vianet

    ```
    https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Välj det **globala administratörskontot** om du uppmanas att göra det.

    ![Katalog-ID](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Välj **Acceptera** när du uppmanas att göra det.

    ![Acceptera](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. Under din Azure AD, i **Enterprise-program,** ser du **Azure VPN** listad.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)
    
8. Om du inte redan har en fungerande point-to-site-miljö följer du instruktionen för att skapa en. Se [Skapa en punkt-till-plats-VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md) för att skapa och konfigurera en punkt-till-plats-VPN-gateway med inbyggd Azure-certifikatautentisering. 

    > [!IMPORTANT]
    > Basic SKU stöds inte för OpenVPN.

9. Aktivera Azure AD-autentisering på VPN-gatewayen genom att köra följande kommandon, se till att ändra kommandot så att det återspeglar din egen miljö:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "41b23e61-6c1e-4545-b367-cd054e0ed4b4" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24 -VpnClientProtocol OpenVPN
    ```

   > [!NOTE]
   > Se till att du inkluderar ett avslutande `AadIssuerUri` snedstreck i slutet av värdet. Annars misslyckas kommandot.

10. Skapa och hämta profilen genom att köra följande kommandon. Ändra värdena -ResourceGroupName och -Name så att de matchar dina egna.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

11. När du har kört kommandona visas ett resultat som liknar det nedan. Kopiera resultat-URL:en till webbläsaren för att hämta zip-filen för profilen.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/profile.png)

12. Extrahera den nedladdade zip-filen.

13. Bläddra till mappen "AzureVPN" som inte har uppackats.

14. Anteckna platsen för filen "azurevpnconfig.xml". Azurevpnconfig.xml innehåller inställningen för VPN-anslutningen och kan importeras direkt till Azure VPN-klientprogrammet. Du kan också distribuera den här filen till alla användare som behöver ansluta via e-post eller på annat sätt. Användaren behöver giltiga Azure AD-autentiseringsuppgifter för att kunna ansluta.

## <a name="next-steps"></a>Nästa steg

För att kunna ansluta till det virtuella nätverket måste du skapa och konfigurera en VPN-klientprofil. Se [Konfigurera en VPN-klient för P2S VPN-anslutningar](openvpn-azure-ad-client.md).
