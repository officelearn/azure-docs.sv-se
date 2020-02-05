---
title: 'VPN Gateway: Azure AD-klient för olika användar grupper: Azure AD-autentisering'
description: Du kan använda P2S VPN för att ansluta till ditt VNet med Azure AD-autentisering
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 1/10/2020
ms.author: alzam
ms.openlocfilehash: 90244b9dcf30c2ef01d4e57c9d8e35fa1d71f434
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985653"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Skapa en Azure Active Directory-klient för P2S OpenVPN-protokoll anslutningar

När du ansluter till ditt VNet kan du använda certifikatbaserad autentisering eller RADIUS-autentisering. Men när du använder det öppna VPN-protokollet kan du också använda Azure Active Directory autentisering. Om du vill att en annan uppsättning användare ska kunna ansluta till olika VPN-gatewayer kan du registrera flera appar i AD och länka dem till olika VPN-gatewayer. Den här artikeln hjälper dig att skapa en Azure AD-klient för P2S OpenVPN-autentisering och skapa och registrera flera appar i Azure AD för att ge olika åtkomst till olika användare och grupper.

> [!NOTE]
> Azure AD-autentisering stöds bara för OpenVPN-® protokoll anslutningar.
>

## <a name="tenant"></a>1. Skapa Azure AD-klienten

Skapa en Azure AD-klient med hjälp av stegen i artikeln [skapa en ny klient organisation](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Organisations namn
* Ursprungligt domän namn

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

    ![Godkänt](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

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

## <a name="site"></a>6. skapa en ny P2S-konfiguration

En P2S-konfiguration definierar parametrarna för att ansluta fjärrklienter.

1. Ange följande variabler och ersätt värden efter behov för din miljö.

   ```powershell
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. Kör följande kommandon för att skapa konfigurationen:

   ```powershell
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
   ```

> [!NOTE]
> Använd inte Azure VPN-klientens program-ID i kommandona ovan för att ge alla användare åtkomst till VPN-gatewayen. Använd ID: t för det/de program som du har registrerat.

## <a name="hub"></a>7. redigera Hubbs tilldelning

1. Gå till bladet **hubbar** under det virtuella WAN-nätverket.
2. Välj den hubb som du vill koppla VPN-serverkonfigurationen till och klicka på ellipsen (...).

   ![ny webbplats](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Klicka på **Redigera virtuell hubb**.
4. Markera kryss rutan **Inkludera punkt-till-plats-Gateway** och välj den **enhet för gateway-skalning** som du vill använda.

   ![ny webbplats](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Ange adresspoolen **från vilken** VPN-klienter ska tilldelas IP-adresser.
6. Klicka på **Bekräfta**.
7. Åtgärden kan ta upp till 30 minuter att slutföra.

## <a name="device"></a>8. Ladda ned VPN-profil

Använd VPN-profilen för att konfigurera dina klienter.

1. På sidan för ditt virtuella WAN-nätverk klickar du på **användare VPN-konfigurationer**.
2. Klicka på **Hämta VPN-konfiguration för användare**längst upp på sidan.
3. När filen har skapats klickar du på länken för att ladda ned den.
4. Använd profil filen för att konfigurera VPN-klienterna.

4. Extrahera den hämtade ZIP-filen.

5. Bläddra till mappen unzippad "AzureVPN".

6. Anteckna platsen för filen "azurevpnconfig. xml". Azurevpnconfig. xml innehåller inställningen för VPN-anslutningen och kan importeras direkt till Azure VPN-klientprogrammet. Du kan också distribuera filen till alla användare som behöver ansluta via e-post eller på annat sätt. Användaren måste ha giltiga autentiseringsuppgifter för Azure AD för att kunna ansluta.
## <a name="configure-user-vpn-clients"></a>Konfigurera användares VPN-klienter

För att ansluta måste du ladda ned Azure VPN-klienten (för hands version) och importera VPN-klienttjänsten som hämtades i föregående steg på varje dator som vill ansluta till det virtuella nätverket.

> [!NOTE]
> Azure AD-autentisering stöds bara för OpenVPN-® protokoll anslutningar.
>

#### <a name="to-download-the-azure-vpn-client"></a>Ladda ned Azure VPN-klienten

Använd den här [länken](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) för att ladda ned Azure VPN-klienten (för hands version).

#### <a name="import"></a>Så här importerar du en klient profil

1. På sidan väljer du **Importera**.

    ![export](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Bläddra till profil-XML-filen och markera den. När filen är vald väljer du **Öppna**.

    ![export](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Ange namnet på profilen och välj **Spara**.

    ![export](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Välj **Anslut** för att ansluta till VPN.

    ![export](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. När du har anslutit ikonen blir den grön och säg **ansluten**.

    ![export](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="delete"></a>Ta bort en klient profil

1. Välj ellipsen (...) bredvid den klient profil som du vill ta bort. Välj sedan **ta bort**.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Välj **ta bort** för att ta bort.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose"></a>Diagnostisera anslutnings problem

1. För att diagnostisera anslutnings problem kan du använda verktyget **diagnostisera** . Välj ellipsen (...) bredvid den VPN-anslutning som du vill diagnostisera för att Visa menyn. Välj sedan **diagnostisera**.

    ![diagnostisera](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. På sidan **anslutnings egenskaper** väljer du **Kör diagnostik**.

    ![diagnostisera](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Logga in med dina autentiseringsuppgifter.

    ![diagnostisera](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. Visa diagnos resultatet.

    ![diagnostisera](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="viewwan"></a>Visa ditt virtuella WAN

1. Navigera till det virtuella WAN-nätverket.
2. Varje punkt på kartan på sidan Översikt motsvarar en hubb. För muspekaren över en punkt så visas en sammanfattning av hubbens hälsotillstånd.
3. I avsnittet om hubbar och anslutningar kan du visa hubbstatus, plats, region, VPN-anslutningsstatus och byte in och ut.

## <a name="viewhealth"></a>Visa din resurs hälsa

1. Navigera till ditt WAN.
2. Öppna WAN-sidan. I avsnittet **SUPPORT + felsökning** klickar du på **Hälsa** och visar resursen.


## <a name="cleanup"></a>Rensa resurser

Du kan använda [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) för att ta bort resursgruppen och alla resurser den innehåller när du inte längre behöver dem. Ersätt myResourceGroup med namnet på resursgruppen och kör följande PowerShell-kommando:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

Du hittar mer information om virtuellt WAN i [översikten om virtuellt WAN](virtual-wan-about.md).
