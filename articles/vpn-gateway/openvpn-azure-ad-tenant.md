---
title: 'VPN Gateway: Azure AD-klient för P2S VPN-anslutningar: Azure AD-autentisering'
description: Du kan använda P2S VPN för att ansluta till ditt VNet med Azure AD-autentisering
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: alzam
ms.openlocfilehash: b9627862002a70dc84b0e268128c53a97df0ebe8
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472306"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Skapa en Azure Active Directory-klient för P2S OpenVPN-protokoll anslutningar

När du ansluter till ditt VNet kan du använda certifikatbaserad autentisering eller RADIUS-autentisering. Men när du använder det öppna VPN-protokollet kan du också använda Azure Active Directory autentisering. Den här artikeln hjälper dig att skapa en Azure AD-klient för P2S Open VPN-autentisering.

> [!NOTE]
> Azure AD-autentisering stöds bara för OpenVPN-® protokoll anslutningar.
>

## <a name="tenant"></a>1. Skapa Azure AD-klienten

Skapa en Azure AD-klient med hjälp av stegen i artikeln [skapa en ny klient organisation](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Organisations namn
* Ursprungligt domän namn

Exempel:

   ![Ny Azure AD-klient](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="users"></a>2. Skapa Azure AD-klient användare

Skapa sedan två användar konton. Skapa ett globalt administratörs konto och ett huvud användar konto. Huvud användar kontot används som ditt huvud inbäddnings konto (tjänst konto). När du skapar ett användar konto för Azure AD-klienten justerar du katalog rollen för den typ av användare som du vill skapa.

Följ stegen i [den här artikeln](../active-directory/fundamentals/add-users-azure-active-directory.md) för att skapa minst två användare för din Azure AD-klient. Se till att ändra **katalog rollen** för att skapa konto typerna:

* Global administratör
* Användare

## <a name="enable-authentication"></a>3. Aktivera Azure AD-autentisering på VPN-gatewayen

1. Leta upp katalog-ID: t för den katalog som du vill använda för autentisering. Den visas i avsnittet Egenskaper på sidan Active Directory.

    ![Katalog-ID](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. Kopiera katalog-ID: t.

3. Logga in på Azure Portal som en användare som har tilldelats rollen som **Global administratör** .

4. Ge sedan administrativt medgivande. Kopiera och klistra in webb adressen som hör till distributions platsen i webbläsarens Adress fält:

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

5. Välj det **globala administratörs** kontot om du uppmanas att göra det.

    ![Katalog-ID](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Välj **acceptera** när du uppmanas till detta.

    ![Godkänn](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. I **företags program**i Azure AD visas **Azure VPN** i listan.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)
    
8. Om du inte redan har en fungerande punkt-till-plats-miljö, följer du anvisningarna för att skapa en. Se [skapa en punkt-till-plats-VPN](vpn-gateway-howto-point-to-site-resource-manager-portal.md) för att skapa och konfigurera en punkt-till-plats-VPN-gateway med intern Azure-certifikatautentisering. 

    > [!IMPORTANT]
    > Bas-SKU: n stöds inte för OpenVPN.

9. Aktivera Azure AD-autentisering på VPN-gatewayen genom att köra följande kommandon, och se till att ändra kommandot så att det motsvarar din egen miljö:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "41b23e61-6c1e-4545-b367-cd054e0ed4b4" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24 -VpnClientProtocol OpenVPN
    ```

   > [!NOTE]
   > Se till att du inkluderar ett avslutande snedstreck i slutet av `AadIssuerUri` svärdet. Annars fungerar inte kommandot.

10. Skapa och ladda ned profilen genom att köra följande kommandon. Ändra värdena-ResourceGroupName och-Name så att de matchar dina egna.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

11. När du har kört kommandona visas ett resultat som liknar det som visas nedan. Kopiera resultat-URL: en till webbläsaren för att ladda ned profil zip-filen.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/profile.png)

12. Extrahera den hämtade ZIP-filen.

13. Bläddra till mappen unzippad "AzureVPN".

14. Anteckna platsen för filen "azurevpnconfig. xml". Azurevpnconfig. xml innehåller inställningen för VPN-anslutningen och kan importeras direkt till Azure VPN-klientprogrammet. Du kan också distribuera filen till alla användare som behöver ansluta via e-post eller på annat sätt. Användaren måste ha giltiga autentiseringsuppgifter för Azure AD för att kunna ansluta.

## <a name="next-steps"></a>Nästa steg

För att kunna ansluta till ditt virtuella nätverk måste du skapa och konfigurera en profil för VPN-klienter. Se [Konfigurera en VPN-klient för P2s VPN-anslutningar](openvpn-azure-ad-client.md).
