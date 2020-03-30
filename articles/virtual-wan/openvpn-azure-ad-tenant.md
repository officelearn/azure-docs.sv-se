---
title: 'Azure AD-klient för VPN-anslutningar för användare: Azure AD-autentisering'
description: Du kan använda Azure Virtual WAN User VPN (point-to-site) för att ansluta till ditt virtuella nätverk med Azure AD-autentisering
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: alzam
ms.openlocfilehash: 74347ce969b6a5ffd57f5ca8396517e78590f3f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059447"
---
# <a name="create-an-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>Skapa en Azure Active Directory-klient för VPN OpenVPN-protokollanslutningar för användare

När du ansluter till ditt virtuella nätverk kan du använda certifikatbaserad autentisering eller RADIUS-autentisering. Men när du använder Open VPN-protokollet kan du också använda Azure Active Directory-autentisering. Den här artikeln hjälper dig att konfigurera en Azure AD-klient för Virtual WAN User VPN (point-to-site) Open VPN-autentisering.

> [!NOTE]
> Azure AD-autentisering stöds endast&reg; för OpenVPN-protokollanslutningar.
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Skapa Azure AD-klienten

Skapa en Azure AD-klient med hjälp av stegen i artikeln [Skapa en ny klientorganisation:](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* Organisationsnamn
* Ursprungligt domännamn

Exempel:

   ![Ny Azure AD-klient](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Skapa Azure AD-klientanvändare

Skapa sedan två användarkonton. Skapa ett globalt administratörskonto och ett huvudanvändarkonto. Huvudanvändarkontot används som huvudinbäddningskonto (tjänstkonto). När du skapar ett Azure AD-klientanvändarkonto justerar du katalogrollen för den typ av användare som du vill skapa.

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
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Välj det **globala administratörskontot** om du uppmanas att göra det.

    ![Katalog-ID](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Välj **Acceptera** när du uppmanas att göra det.

    ![Acceptera](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. Under din Azure AD, i **Enterprise-program,** ser du **Azure VPN** listad.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

8. Konfigurera Azure AD-autentisering för Användar-VPN och tilldela den till en virtuell hubb genom att följa stegen i [Konfigurera Azure AD-autentisering för point-to-site-anslutning till Azure](virtual-wan-point-to-site-azure-ad.md)

## <a name="next-steps"></a>Nästa steg

För att kunna ansluta till det virtuella nätverket måste du skapa och konfigurera en VPN-klientprofil och associera den till en virtuell hubb. Se [Konfigurera Azure AD-autentisering för Point-to-Site-anslutning till Azure](virtual-wan-point-to-site-azure-ad.md).
