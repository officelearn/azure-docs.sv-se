---
title: 'Azure AD-klient för användares VPN-anslutningar: Azure AD-autentisering'
description: Du kan använda Azures virtuella WAN-användare VPN (punkt-till-plats) för att ansluta till ditt VNet med Azure AD-autentisering
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/19/2020
ms.author: alzam
ms.openlocfilehash: 76c65d194d03dd1b7ff4cc2f3b45d84ff7909968
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753361"
---
# <a name="create-an-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>Skapa en Azure Active Directory-klient för användare VPN-OpenVPN protokoll anslutningar

När du ansluter till ditt VNet kan du använda certifikatbaserad autentisering eller RADIUS-autentisering. Men när du använder det öppna VPN-protokollet kan du också använda Azure Active Directory autentisering. Den här artikeln hjälper dig att skapa en Azure AD-klient för virtuella WAN-användare VPN (punkt-till-plats) med öppen VPN-autentisering.

> [!NOTE]
> Azure AD-autentisering stöds bara för OpenVPN- &reg; protokoll anslutningar.
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Skapa Azure AD-klienten

Skapa en Azure AD-klient med hjälp av stegen i artikeln [skapa en ny klient organisation](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Organisations namn
* Ursprungligt domännamn

Exempel:

   ![Ny Azure AD-klient](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Skapa Azure AD-klient användare

Skapa sedan två användar konton. Skapa ett globalt administratörs konto och ett huvud användar konto. Huvud användar kontot används som ditt huvud inbäddnings konto (tjänst konto). När du skapar ett användar konto för Azure AD-klienten justerar du katalog rollen för den typ av användare som du vill skapa.

Följ stegen i [den här artikeln](../active-directory/fundamentals/add-users-azure-active-directory.md) för att skapa minst två användare för din Azure AD-klient. Se till att ändra **katalog rollen** för att skapa konto typerna:

* Global administratör
* Användare

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. Aktivera Azure AD-autentisering på VPN-gatewayen

1. Leta upp katalog-ID: t för den katalog som du vill använda för autentisering. Den visas i avsnittet Egenskaper på sidan Active Directory.

    ![Katalog-ID](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. Kopiera katalog-ID:t.

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

    ![Katalog-ID](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Välj **acceptera** när du uppmanas till detta.

    ![Acceptera](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. I **företags program**i Azure AD visas **Azure VPN** i listan.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

8. Konfigurera Azure AD-autentisering för användar-VPN och tilldela den till en virtuell hubb genom att följa stegen i [Konfigurera Azure AD-autentisering för punkt-till-plats-anslutning till Azure](virtual-wan-point-to-site-azure-ad.md)

## <a name="next-steps"></a>Nästa steg

För att kunna ansluta till ditt virtuella nätverk måste du skapa och konfigurera en profil för VPN-klienter och koppla den till en virtuell hubb. Se [Konfigurera Azure AD-autentisering för punkt-till-plats-anslutning till Azure](virtual-wan-point-to-site-azure-ad.md).
