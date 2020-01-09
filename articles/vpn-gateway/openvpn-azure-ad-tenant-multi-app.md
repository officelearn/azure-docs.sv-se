---
title: 'VPN Gateway: Azure AD-klient för olika användar grupper: Azure AD-autentisering'
description: Du kan använda P2S VPN för att ansluta till ditt VNet med Azure AD-autentisering
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: alzam
ms.openlocfilehash: 73a47b20010be0b4eadec790dd8fb254f912ff2a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75477179"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Skapa en Azure Active Directory-klient för P2S OpenVPN-protokoll anslutningar

När du ansluter till ditt VNet kan du använda certifikatbaserad autentisering eller RADIUS-autentisering. Men när du använder det öppna VPN-protokollet kan du också använda Azure Active Directory autentisering. Om du vill att en annan uppsättning användare ska kunna ansluta till olika VPN-gatewayer kan du registrera flera appar i AD och länka dem till olika VPN-gatewayer. Den här artikeln hjälper dig att skapa en Azure AD-klient för P2S OpenVPN-autentisering och skapa och registrera flera appar i Azure AD för att ge olika åtkomst till olika användare och grupper.

> [!NOTE]
> Azure AD-autentisering stöds bara för OpenVPN-® protokoll anslutningar.
>

## <a name="tenant"></a>1. Skapa Azure AD-klienten

Skapa en Azure AD-klient med hjälp av stegen i artikeln [skapa en ny klient organisation](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Organisations namn
* Ursprungligt domännamn

Exempel:

   ![Ny Azure AD-klient](./media/openvpn-azure-ad-tenant-multi-app/newtenant.png)

## <a name="users"></a>2. Skapa Azure AD-klient användare

Skapa sedan två användar konton. Skapa ett globalt administratörs konto och ett huvud användar konto. Huvud användar kontot används som ditt huvud inbäddnings konto (tjänst konto). När du skapar ett användar konto för Azure AD-klienten justerar du katalog rollen för den typ av användare som du vill skapa.

Följ stegen i [den här artikeln](../active-directory/fundamentals/add-users-azure-active-directory.md) för att skapa minst två användare för din Azure AD-klient. Se till att ändra **katalog rollen** för att skapa konto typerna:

* Global administratör
* Användare

## <a name="enable-authentication"></a>3. registrera Azure VPN-klienten i Azure AD-klient

1. Leta upp katalog-ID: t för den katalog som du vill använda för autentisering. Den visas i avsnittet Egenskaper på sidan Active Directory.

    ![Katalog-ID](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

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
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Välj det **globala administratörs** kontot om du uppmanas att göra det.

    ![Katalog-ID](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. Välj **acceptera** när du uppmanas till detta.

    ![Acceptera](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. I **företags program**i Azure AD kommer du att se **Azure VPN** i listan.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azurevpn.png)

## <a name="enable-authentication"></a>4. registrera ytterligare program för olika användare/grupper

1. Klicka på **Appregistreringar** under Azure Active Directory och sedan på **+ ny registrering**

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. I bladet **Registrera ett program** anger du **namnet** och väljer önskade **konto typer som stöds** och klickar på **Registrera**

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. När den nya appen har registrerats klickar du på **exponera ett API** på bladet app

4. Klicka på **+ Lägg till ett omfång**
5. Lämna standard **program-ID-URI: n** och klicka på **Spara och fortsätt**

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app3.png)
6. Fyll i de obligatoriska fälten och kontrol lera att **läget** är **aktiverat**. Klicka på **Lägg till omfattning**

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app4.png)
7. Klicka på **exponera ett API** och sedan **+ Lägg till ett klient program**.  För **klient-ID**anger du följande värden beroende på molnet:
    -   Ange **41b23e61-6c1e-4545-b367-cd054e0ed4b4** för Azure **Public**
    -   Ange **51bb15d4-3a4f-4ebf-9dca-40096fe32426** för Azure **myndigheter**
    -   Ange **538ee9e6-310A-468d-afef-ea97365856a9** för Azure **Germany**
    -   Ange **49f817b6-84ae-4CC0-928c-73f27289b3aa** för Azure **Kina 21Vianet**


8. Klicka på **Lägg till program**

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app5.png)
9. Kopiera **program-ID: t (klient)** från **översikts** sidan. Du behöver den för att konfigurera din VPN-gateway (er)

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. Upprepa stegen i det här avsnittet (4) om du vill skapa så många program som krävs för ditt säkerhets krav. Varje program kommer att associeras med en VPN-gateway och kan ha en annan uppsättning användare. Endast ett program kan associeras med en gateway.

## <a name="enable-authentication"></a>5. tilldela användare till dina program

1. Under Azure AD, **företags program**väljer du det nyligen registrerade programmet och klickar på **Egenskaper**. Se till att **användar tilldelning krävs?** är inställt på **Ja**. Klicka på **Spara**

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. På sidan app klickar du på **användare och grupper** och lägger sedan **till användare**

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user3.png)
3. Under **Lägg till tilldelning**, klickar **du på användare och grupper**. Välj de användare som du vill ska kunna komma åt det här VPN-programmet. Klicka på **Välj**

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user4.png)

## <a name="enable-authentication"></a>6. Aktivera Azure AD-autentisering på VPN-gatewayen

1. Aktivera Azure AD-autentisering på VPN-gatewayen genom att köra följande kommandon, och se till att ändra kommandot så att det motsvarar din egen miljö:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "application ID from previous section" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24
    ```
> [!NOTE]
> Använd inte Azure VPN-klientens program-ID i kommandona ovan för att ge alla användare åtkomst till VPN-gatewayen. Använd ID: t för det/de program som du har registrerat.

2. Skapa och ladda ned profilen genom att köra följande kommandon. Ändra värdena-ResourcGroupName och-Name så att de matchar dina egna.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

3. När du har kört kommandona visas ett resultat som liknar det som visas nedan. Kopiera resultat-URL: en till webbläsaren för att ladda ned profil zip-filen.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/profile.png)

4. Extrahera den hämtade ZIP-filen.

5. Bläddra till mappen unzippad "AzureVPN".

6. Anteckna platsen för filen "azurevpnconfig. xml". Azurevpnconfig. xml innehåller inställningen för VPN-anslutningen och kan importeras direkt till Azure VPN-klientprogrammet. Du kan också distribuera filen till alla användare som behöver ansluta via e-post eller på annat sätt. Användaren måste ha giltiga autentiseringsuppgifter för Azure AD för att kunna ansluta.

## <a name="next-steps"></a>Nästa steg

För att kunna ansluta till ditt virtuella nätverk måste du skapa och konfigurera en profil för VPN-klienter. Se [Konfigurera en VPN-klient för P2s VPN-anslutningar](openvpn-azure-ad-client.md).
