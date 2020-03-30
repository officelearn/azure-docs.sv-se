---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/18/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e950d194ab48cec1a70c7bd17617332cb858a55d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77485676"
---
## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Skapa Azure AD-klienten

Skapa en Azure AD-klient med hjälp av stegen i artikeln [Skapa en ny klientorganisation:](../articles/active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* Organisationsnamn
* Ursprungligt domännamn

  Exempel:

   ![Ny Azure AD-klient](./media/openvpn-azure-ad-tenant-multi-app/new-tenant.png)

## <a name="2-create-tenant-users"></a><a name="users"></a>2. Skapa klientanvändare

I det här steget skapar du två Azure AD-klientanvändare: Ett globalt administratörskonto och ett huvudanvändarkonto. Huvudanvändarkontot används som huvudinbäddningskonto (tjänstkonto). När du skapar ett Azure AD-klientanvändarkonto justerar du katalogrollen för den typ av användare som du vill skapa. Följ stegen i den [här artikeln](../articles/active-directory/fundamentals/add-users-azure-active-directory.md) för att skapa minst två användare för din Azure AD-klientorganisation. Var noga med att ändra **katalogrollen** för att skapa kontotyperna:

* Global administratör
* Användare

## <a name="3-register-the-vpn-client"></a><a name="register-client"></a>3. Registrera VPN-klienten

Registrera VPN-klienten i Azure AD-klienten.

1. Leta reda på katalog-ID för den katalog som du vill använda för autentisering. Den visas i egenskapsavsnittet på Active Directory-sidan.

    ![Katalog-ID](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

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

    ![Katalog-ID](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. Välj **Acceptera** när du uppmanas att göra det.

    ![Acceptera](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. Under din Azure AD, i **Enterprise-program,** ser du **Azure VPN** i listan.

     ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azure-vpn.png)

## <a name="4-register-additional-applications"></a><a name="register-apps"></a>4. Registrera ytterligare ansökningar

I det här steget registrerar du ytterligare program för olika användare och grupper.

1. Under din Azure Active Directory klickar du på **Appregistreringar** och sedan **+ Ny registrering**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. På sidan **Registrera ett program** anger du **namnet**. Välj önskade **kontotyper som stöds**och klicka sedan på **Registrera**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. När den nya appen har registrerats klickar du på **Exponera ett API** under appbladet.

4. Klicka på **+ Lägg till ett scope**.

5. Lämna **standard-program-ID URI**. Klicka på **Spara och fortsätt**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app3.png)

6. Fyll i de obligatoriska fälten och se till att **tillståndet** är **aktiverat**. Klicka på **Lägg till omfattning**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app4.png)

7. Klicka på **Exponera ett API** och sedan + Lägg till ett **klientprogram**.  För **klient-ID**anger du följande värden beroende på molnet:

    - Ange **41b23e61-6c1e-4545-b367-cd054e0ed4b4** för Azure **Public**
    - Ange **51bb15d4-3a4f-4ebf-9dca-40096fe32426** för Azure **Government**
    - Ange **538ee9e6-310a-468d-afef-ea97365856a9** för Azure **Tyskland**
    - Ange **49f817b6-84ae-4cc0-928c-73f27289b3aaa** för Azure **China 21Vianet**

8. Klicka på **Lägg till program**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app5.png)

9. Kopiera **program-ID:t (klienten)** från sidan **Översikt.** Du behöver den här informationen för att konfigurera dina VPN-gatewayer.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. Upprepa stegen i det här [registret ytterligare program](#register-apps) avsnitt för att skapa så många program som behövs för ditt säkerhetskrav. Varje program kommer att associeras till en VPN-gateway och kan ha olika användare. Endast ett program kan associeras till en gateway.

## <a name="5-assign-users-to-applications"></a><a name="assign-users"></a>5. Tilldela användare till program

Tilldela användarna till dina program.

1. Under **Azure AD -> Enterprise-program**väljer du det nyligen registrerade programmet och klickar på **Egenskaper**. Se till att **användartilldelning krävs?** är inställd på **ja**. Klicka på **Spara**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. Klicka på Användare **och grupper**på appsidan och klicka sedan på **+Lägg till användare**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user3.png)

3. Klicka på **Användare och grupper**under Lägg till **tilldelning.** Välj de användare som du vill kunna komma åt det här VPN-programmet. Klicka på **Markera**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user4.png)