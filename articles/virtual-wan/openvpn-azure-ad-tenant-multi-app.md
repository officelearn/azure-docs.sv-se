---
title: 'Virtuellt WAN: Azure AD-klient för olika användargrupper: Azure AD-autentisering'
description: Du kan använda P2S VPN för att ansluta till ditt virtuella nätverk med Azure AD-autentisering
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: alzam
ms.openlocfilehash: af5ff5817ee9ae7e6d7432fe281ecb440bf25b9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060704"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Skapa en Azure Active Directory-klient för P2S OpenVPN-protokollanslutningar

När du ansluter till ditt virtuella nätverk kan du använda certifikatbaserad autentisering eller RADIUS-autentisering. Men när du använder Open VPN-protokollet kan du också använda Azure Active Directory-autentisering. Om du vill att olika användare ska kunna ansluta till olika gateways kan du registrera flera appar i AD och länka dem till olika gateways.

Den här artikeln hjälper dig att konfigurera en Azure AD-klient för P2S OpenVPN-autentisering och skapa och registrera flera appar i Azure AD för att tillåta olika åtkomst för olika användare och grupper.

> [!NOTE]
> Azure AD-autentisering stöds endast&reg; för OpenVPN-protokollanslutningar.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-create-a-new-p2s-configuration"></a><a name="site"></a>6. Skapa en ny P2S-konfiguration

En P2S-konfiguration definierar parametrarna för att ansluta fjärrklienter.

1. Ange följande variabler och ersätt värden som behövs för din miljö.

   ```azurepowershell-interactive
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. Kör följande kommandon för att skapa konfigurationen:

   ```azurepowershell-interactive
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
   ```

   > [!NOTE]
   > Använd inte Azure VPN-klientens program-ID i kommandona ovan: Det ger alla användare åtkomst till gatewayen. Använd ID:t för de program som du registrerade.

## <a name="7-edit-hub-assignment"></a><a name="hub"></a>7. Redigera hubbtilldelning

1. Navigera till **hubbarbladet** under det virtuella WAN:et.

2. Välj den hubb som du vill associera vpn-serverkonfigurationen till och klicka på ellipsen (...).

    ![ny webbplats](media/openvpn-azure-ad-tenant-multi-app/p2s4.jpg)

3. Klicka på **Redigera virtuellt nav**.

4. Markera kryssrutan **Inkludera punkt-till-plats-gateway** och välj den **gateway-skalningsenhet** som du vill använda.

    ![ny webbplats](media/openvpn-azure-ad-tenant-multi-app/p2s2.jpg)

5. Ange **den adresspool** som VPN-klienterna ska tilldelas IP-adresser från.

6. Klicka på **Bekräfta**.

7. Åtgärden kan ta upp till 30 minuter att slutföra.

## <a name="8-download-vpn-profile"></a><a name="device"></a>8. Ladda ner VPN-profil

Använd VPN-profilen för att konfigurera dina klienter.

1. Klicka på **VPN-konfigurationer**på sidan för ditt virtuella WAN .

2. Högst upp på sidan klickar du på **Hämta användar-VPN-konfiguration**.

3. När filen har skapats klickar du på länken för att ladda ned den.

4. Använd profilfilen för att konfigurera VPN-klienterna.

5. Extrahera den nedladdade zip-filen.

6. Bläddra till mappen "AzureVPN" som inte har uppackats.

7. Anteckna platsen för filen "azurevpnconfig.xml". Azurevpnconfig.xml innehåller inställningen för VPN-anslutningen och kan importeras direkt till Azure VPN-klientprogrammet. Du kan också distribuera den här filen till alla användare som behöver ansluta via e-post eller på annat sätt. Användaren behöver giltiga Azure AD-autentiseringsuppgifter för att kunna ansluta.

## <a name="9-configure-user-vpn-clients"></a>9. Konfigurera VPN-klienter för användare

För att ansluta måste du hämta Azure VPN-klienten och importera VPN-klientprofilen som hämtades i föregående steg på varje dator som vill ansluta till det virtuella nätverket.

> [!NOTE]
> Azure AD-autentisering stöds endast&reg; för OpenVPN-protokollanslutningar.
>

#### <a name="to-download-the-azure-vpn-client"></a>Så här hämtar du Azure VPN-klienten

Använd den här [länken](https://go.microsoft.com/fwlink/?linkid=2117554) för att hämta Azure VPN-klienten.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>Så här importerar du en klientprofil

1. Välj **Importera**på sidan .

    ![importera](./media/openvpn-azure-ad-tenant-multi-app/import/import1.jpg)

2. Bläddra till profil xml-filen och markera den. När filen är markerad väljer du **Öppna**.

    ![importera](./media/openvpn-azure-ad-tenant-multi-app/import/import2.jpg)

3. Ange namnet på profilen och välj **Spara**.

    ![importera](./media/openvpn-azure-ad-tenant-multi-app/import/import3.jpg)

4. Välj **Anslut** för att ansluta till VPN.

    ![importera](./media/openvpn-azure-ad-tenant-multi-app/import/import4.jpg)

5. När den är ansluten blir ikonen grön och säger **Ansluten**.

    ![importera](./media/openvpn-azure-ad-tenant-multi-app/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Så här tar du bort en klientprofil

1. Välj ellipsen (...) bredvid den klientprofil som du vill ta bort. Välj sedan **Ta bort**.

    ![delete](./media/openvpn-azure-ad-tenant-multi-app/delete/delete1.jpg)

2. Välj **Ta bort** om du vill ta bort.

    ![delete](./media/openvpn-azure-ad-tenant-multi-app/delete/delete2.jpg)

#### <a name="to-diagnose-connection-issues"></a><a name="diagnose"></a>Så här diagnostiserar du anslutningsproblem

1. Om du vill diagnostisera anslutningsproblem kan du använda **diagnosverktyget.** Välj ellipsen (...) bredvid den VPN-anslutning som du vill diagnostisera för att visa menyn. Välj sedan **Diagnostisera**.

    ![Diagnostisera](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose1.jpg)

2. På sidan **Anslutningsegenskaper** väljer du **Kör diagnos**.

    ![Diagnostisera](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose2.jpg)

3. Logga in med dina autentiseringsuppgifter.

    ![Diagnostisera](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose3.jpg)

4. Visa diagnosresultaten.

    ![Diagnostisera](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose4.jpg)

## <a name="10-view-your-virtual-wan"></a><a name="viewwan"></a>10. Visa ditt virtuella WAN

1. Navigera till det virtuella WAN-nätverket.

2. Varje punkt på kartan på sidan Översikt motsvarar en hubb.

3. I avsnittet om hubbar och anslutningar kan du visa hubbstatus, plats, region, VPN-anslutningsstatus och byte in och ut.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Rensa resurser

Du kan använda [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) för att ta bort resursgruppen och alla resurser den innehåller när du inte längre behöver dem. Ersätt myResourceGroup med namnet på resursgruppen och kör följande PowerShell-kommando:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

Du hittar mer information om virtuellt WAN i [översikten om virtuellt WAN](virtual-wan-about.md).
