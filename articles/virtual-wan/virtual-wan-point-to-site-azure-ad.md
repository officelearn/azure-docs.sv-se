---
title: 'Konfigurera Azure AD-autentisering för ANVÄNDARE VPN-anslutning: VirtuellT WAN'
description: Lär dig hur du konfigurerar Azure Active Directory-autentisering för Användar-VPN.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: alzam
ms.openlocfilehash: 703b832d58f2374eac131cfd380ba27f2c890618
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059492"
---
# <a name="configure-azure-active-directory-authentication-for-user-vpn"></a>Konfigurera Azure Active Directory-autentisering för användar-VPN

Den här artikeln visar hur du konfigurerar Azure AD-autentisering för Användar-VPN i Virtual WAN för att ansluta till dina resurser i Azure via en OpenVPN VPN-anslutning. Azure Active Directory-autentisering är endast tillgänglig för gateways med OpenVPN-protokollet och klienter som kör Windows.

Den här typen av anslutning kräver att en klient konfigureras på klientdatorn. Mer information om virtuella WAN-nätverk finns i [översikten om virtuellt WAN](virtual-wan-about.md).

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Skapa WAN (Wide Area Network)
> * Skapa en hubb
> * Skapa en P2S-konfiguration
> * Ladda ned en VPN-klientprofil
> * Tillämpa P2S-konfigurationen på en hubb
> * Ansluta ett virtuellt nätverk till en hubb
> * Ladda ned och tillämpa VPN-klientkonfigurationen
> * Visa virtuellt WAN

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>Innan du börjar

Kontrollera att du har uppfyllt följande villkor innan du påbörjar konfigurationen:

* Du har ett virtuellt nätverk som du vill ansluta till. Kontrollera att inget av undernäten i dina lokala nätverk överlappar de virtuella nätverk som du vill ansluta till. Information om hur du skapar ett virtuellt nätverk i Azure-portalen finns i [Snabbstart](../virtual-network/quick-create-portal.md).

* Det virtuella nätverket har inga virtuella nätverksgateways. Om ditt virtuella nätverk har en gateway (antingen VPN eller ExpressRoute) måste du ta bort alla gateways. Den här konfigurationen kräver att virtuella nätverk ansluts i stället till virtual WAN-hubbgatewayen.

* Hämta ett IP-adressintervall för din hubbregion. Navet är ett virtuellt nätverk som skapas och används av Virtual WAN. Det adressintervall som du anger för navet kan inte överlappa något av dina befintliga virtuella nätverk som du ansluter till. Det får inte heller överlappa det adressintervall som du ansluter till lokalt. Om du inte känner till IP-adressintervallen i din lokala nätverkskonfiguration, samordna med någon som kan ange dessa uppgifter åt dig.

* Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Skapa ett virtuellt WAN

Öppna en webbläsare, navigera till [Azure Portal](https://portal.azure.com) och logga in med ditt Azure-konto.

1. Navigera till sidan Virtuellt WAN. Klicka på **+Skapa en resurs** i portalen. Skriv **Virtuellt WAN** i sökrutan och välj Retur.
2. Välj **Virtuellt WAN** i resultatet. På sidan Virtuellt WAN klickar du på **Skapa** för att öppna sidan Skapa WAN.
3. Fyll i följande fält på fliken **Skapa** WAN på sidan **Skapa WAN:**

   ![Virtuellt WAN](./media/virtual-wan-point-to-site-azure-ad/vwan.png)

   * **Prenumeration** – Välj vilken prenumeration du vill använda.
   * **Resursgrupp** - Skapa nytt eller använd befintligt.
   * **Plats för resursgrupp** – Välj en resursplats i listrutan. Ett WAN är en global resurs och är inte kopplad till en viss region. Du måste dock välja en region för att lättare att hantera och leta upp WAN-resursen som du skapar.
   * **Namn** - Skriv det namn som du vill kalla ditt WAN.
   * **Typ:** Standard. Om du skapar ett basic WAN kan du bara skapa en Basic-hubb. Grundläggande nav kan endast ansluta från plats till plats.
4. När du har fyllt i fälten väljer du **Granska +Skapa**.
5. När valideringen har gått väljer du **Skapa** för att skapa det virtuella WAN:n.

## <a name="create-an-empty-virtual-hub"></a><a name="site"></a>Skapa en tom virtuell hubb

1. Under det virtuella WAN-datorn väljer du Hubbar och klickar på **+Ny hubb**.

   ![ny webbplats](media/virtual-wan-point-to-site-azure-ad/hub1.jpg)
2. Fyll i följande fält på sidan Skapa virtuellt nav.

   **Region** - Välj den region som du vill distribuera den virtuella hubben i.

   **Namn** - Ange det namn som du vill kalla din virtuella hubb.

   **Navprivat adressutrymme** - Navets adressintervall i CIDR-notation.

   ![ny webbplats](media/virtual-wan-point-to-site-azure-ad/hub2.jpg)  
3. Klicka på **Granska + skapa**.
4. Klicka på **Skapa**på sidan **validerings skickade.**

## <a name="create-a-new-p2s-configuration"></a><a name="site"></a>Skapa en ny P2S-konfiguration

En P2S-konfiguration definierar parametrarna för att ansluta fjärrklienter.

1. Under ditt virtuella WAN väljer du **VPN-konfigurationer för användare**.

   ![ny config](media/virtual-wan-point-to-site-azure-ad/aadportal1.jpg)

2. Klicka på **+Skapa användar-VPN-konfiguration**.

   ![ny config](media/virtual-wan-point-to-site-azure-ad/aadportal2.jpg)

3. Ange informationen och klicka på **Skapa**

   ![ny config](media/virtual-wan-point-to-site-azure-ad/aadportal3.jpg)

## <a name="edit-hub-assignment"></a><a name="hub"></a>Redigera hubbtilldelning

1. Navigera till **hubbarbladet** under det virtuella WAN:et.
2. Välj den hubb som du vill associera vpn-serverkonfigurationen till och klicka på ellipsen (...).

   ![ny webbplats](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Klicka på **Redigera virtuellt nav**.
4. Markera kryssrutan **Inkludera punkt-till-plats-gateway** och välj den **gateway-skalningsenhet** som du vill använda.

   ![ny webbplats](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Ange **den adresspool** som VPN-klienterna ska tilldelas IP-adresser från.
6. Klicka på **Bekräfta**.
7. Åtgärden kan ta upp till 30 minuter att slutföra.

## <a name="download-vpn-profile"></a><a name="device"></a>Ladda ned VPN-profil

Använd VPN-profilen för att konfigurera dina klienter.

1. Klicka på **VPN-konfigurationer**på sidan för ditt virtuella WAN .
2. Högst upp på sidan klickar du på **Hämta användar-VPN-konfiguration**.
3. När filen har skapats klickar du på länken för att ladda ned den.
4. Använd profilfilen för att konfigurera VPN-klienterna.

## <a name="configure-user-vpn-clients"></a>Konfigurera VPN-klienter för användare

För att ansluta måste du hämta Azure VPN-klienten och importera VPN-klientprofilen som hämtades i föregående steg på varje dator som vill ansluta till det virtuella nätverket.

> [!NOTE]
> Azure AD-autentisering stöds endast&reg; för OpenVPN-protokollanslutningar.
>

#### <a name="to-download-the-azure-vpn-client"></a>Så här hämtar du Azure VPN-klienten

Använd den här [länken](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) för att hämta Azure VPN-klienten.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>Så här importerar du en klientprofil

1. Välj **Importera**på sidan .

    ![importera](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Bläddra till profil xml-filen och markera den. När filen är markerad väljer du **Öppna**.

    ![importera](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Ange namnet på profilen och välj **Spara**.

    ![importera](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Välj **Anslut** för att ansluta till VPN.

    ![importera](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. När den är ansluten blir ikonen grön och säger **Ansluten**.

    ![importera](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Så här tar du bort en klientprofil

1. Välj ellipsen (...) bredvid den klientprofil som du vill ta bort. Välj sedan **Ta bort**.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Välj **Ta bort** om du vill ta bort.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose-connection-issues"></a><a name="diagnose"></a>Diagnostisera anslutningsproblem

1. Om du vill diagnostisera anslutningsproblem kan du använda **diagnosverktyget.** Välj ellipsen (...) bredvid den VPN-anslutning som du vill diagnostisera för att visa menyn. Välj sedan **Diagnostisera**.

    ![Diagnostisera](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. På sidan **Anslutningsegenskaper** väljer du **Kör diagnos**.

    ![Diagnostisera](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Logga in med dina autentiseringsuppgifter.

    ![Diagnostisera](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. Visa diagnosresultaten.

    ![Diagnostisera](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>Visa virtuellt WAN

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
